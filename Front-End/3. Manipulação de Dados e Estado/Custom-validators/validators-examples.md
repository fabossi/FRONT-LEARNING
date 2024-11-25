# 📘 Guia Completo de Custom Validators no Angular (Typed Forms)

## 📋 Sumário
- [Introdução](#introdução)
- [Setup Inicial](#setup-inicial)
- [Validadores Síncronos](#validadores-síncronos)
- [Validadores Assíncronos](#validadores-assíncronos)
- [Validadores Compostos](#validadores-compostos)
- [Casos de Uso Comuns](#casos-de-uso-comuns)
- [Boas Práticas](#boas-práticas)

## 📝 Introdução

Este guia apresenta uma abordagem completa para criar e utilizar validadores customizados no Angular usando Typed Reactive Forms.

## 🚀 Setup Inicial

### Interfaces Base
```typescript
// interfaces/form.interfaces.ts
export interface UserFormModel {
  cpf: string;
  email: string;
  senha: string;
  confirmacaoSenha: string;
  dataNascimento: Date | null;
  dataInicio: Date | null;
  dataFim: Date | null;
  cep: string;
  tipoPessoa: 'PF' | 'PJ';
  documento: string;
  telefones: TelefoneModel[];
}

export interface TelefoneModel {
  numero: string;
  tipo: 'CELULAR' | 'FIXO';
}

export interface EnderecoModel {
  cep: string;
  logradouro: string;
  numero: string;
  complemento?: string;
  bairro: string;
  cidade: string;
  estado: string;
}
```

### Tipos de Erro
```typescript
// interfaces/validator.interfaces.ts
export interface SenhaErrors {
  senhaFraca: {
    maiuscula: boolean;
    minuscula: boolean;
    numero: boolean;
    especial: boolean;
  }
}

export interface DateRangeErrors {
  dateRange: {
    inicio: Date;
    fim: Date;
    message: string;
  }
}
```

## 🔧 Validadores Síncronos

### Custom Validators Class
```typescript
// validators/custom.validators.ts
import { AbstractControl, ValidationErrors, ValidatorFn, FormGroup } from '@angular/forms';
import { SenhaErrors, DateRangeErrors } from '../interfaces/validator.interfaces';

export class CustomValidators {
  static cpf(): ValidatorFn {
    return (control: AbstractControl<string>): ValidationErrors | null => {
      if (!control.value) return null;

      const cpf = control.value.replace(/[^\d]/g, '');

      if (cpf.length !== 11) {
        return { cpf: true };
      }

      if (/^(\d)\1{10}$/.test(cpf)) {
        return { cpf: true };
      }

      let soma = 0;
      for (let i = 0; i < 9; i++) {
        soma += parseInt(cpf.charAt(i)) * (10 - i);
      }

      let digito = 11 - (soma % 11);
      if (digito > 9) digito = 0;

      if (digito !== parseInt(cpf.charAt(9))) {
        return { cpf: true };
      }

      soma = 0;
      for (let i = 0; i < 10; i++) {
        soma += parseInt(cpf.charAt(i)) * (11 - i);
      }

      digito = 11 - (soma % 11);
      if (digito > 9) digito = 0;

      if (digito !== parseInt(cpf.charAt(10))) {
        return { cpf: true };
      }

      return null;
    };
  }

  static senhaForte(): ValidatorFn {
    return (control: AbstractControl<string>): SenhaErrors | null => {
      if (!control.value) return null;

      const hasUpperCase = /[A-Z]/.test(control.value);
      const hasLowerCase = /[a-z]/.test(control.value);
      const hasNumeric = /[0-9]/.test(control.value);
      const hasSpecialChar = /[!@#$%^&*]/.test(control.value);

      const passwordValid = hasUpperCase && hasLowerCase && 
                          hasNumeric && hasSpecialChar;

      return !passwordValid ? {
        senhaFraca: {
          maiuscula: !hasUpperCase,
          minuscula: !hasLowerCase,
          numero: !hasNumeric,
          especial: !hasSpecialChar
        }
      } : null;
    };
  }

  static dateRange(): ValidatorFn {
    return (formGroup: AbstractControl): DateRangeErrors | null => {
      if (!(formGroup instanceof FormGroup)) return null;

      const inicio = formGroup.get('dataInicio')?.value;
      const fim = formGroup.get('dataFim')?.value;

      if (!inicio || !fim) return null;

      const startDate = new Date(inicio);
      const endDate = new Date(fim);

      if (endDate <= startDate) {
        return {
          dateRange: {
            inicio: startDate,
            fim: endDate,
            message: 'Data final deve ser maior que a data inicial'
          }
        };
      }

      return null;
    };
  }

  static senhasIguais(): ValidatorFn {
    return (formGroup: AbstractControl): ValidationErrors | null => {
      if (!(formGroup instanceof FormGroup)) return null;

      const senha = formGroup.get('senha')?.value;
      const confirmacao = formGroup.get('confirmacaoSenha')?.value;

      if (!senha || !confirmacao) return null;

      return senha !== confirmacao ? { senhasNaoConferem: true } : null;
    };
  }
}
```

## 🔄 Validadores Assíncronos

```typescript
// validators/async.validators.ts
import { AbstractControl, AsyncValidatorFn, ValidationErrors } from '@angular/forms';
import { Observable, timer, of } from 'rxjs';
import { map, switchMap, catchError } from 'rxjs/operators';
import { UserService } from '../services/user.service';
import { CepService } from '../services/cep.service';

export class AsyncValidators {
  static emailDisponivel(userService: UserService): AsyncValidatorFn {
    return (control: AbstractControl<string>): Observable<ValidationErrors | null> => {
      if (!control.value) return of(null);

      return timer(300).pipe(
        switchMap(() => userService.checkEmail(control.value)),
        map(isAvailable => !isAvailable ? { emailEmUso: true } : null),
        catchError(() => of(null))
      );
    };
  }

  static cepValido(cepService: CepService): AsyncValidatorFn {
    return (control: AbstractControl<string>): Observable<ValidationErrors | null> => {
      if (!control.value) return of(null);

      const cep = control.value.replace(/[^0-9]/g, '');

      if (cep.length !== 8) {
        return of({ cepInvalido: true });
      }

      return timer(300).pipe(
        switchMap(() => cepService.consultaCep(cep)),
        map(endereco => endereco ? null : { cepInexistente: true }),
        catchError(() => of({ cepError: true }))
      );
    };
  }
}
```

## 📱 Componente Exemplo Completo

```typescript
// components/cadastro.component.ts
import { Component, OnInit } from '@angular/core';
import { FormBuilder, FormArray, Validators } from '@angular/forms';
import { CustomValidators } from '../validators/custom.validators';
import { AsyncValidators } from '../validators/async.validators';
import { UserService } from '../services/user.service';
import { CepService } from '../services/cep.service';
import { UserFormModel, TelefoneModel } from '../interfaces/form.interfaces';

@Component({
  selector: 'app-cadastro',
  template: `
    <form [formGroup]="form" (ngSubmit)="onSubmit()">
      <!-- CPF -->
      <div>
        <label for="cpf">CPF:</label>
        <input id="cpf" type="text" formControlName="cpf">
        <span *ngIf="form.controls.cpf.errors?.['cpf']">
          CPF inválido
        </span>
      </div>

      <!-- Email -->
      <div>
        <label for="email">Email:</label>
        <input id="email" type="email" formControlName="email">
        <span *ngIf="form.controls.email.errors?.['email']">
          Email inválido
        </span>
        <span *ngIf="form.controls.email.errors?.['emailEmUso']">
          Email já está em uso
        </span>
      </div>

      <!-- Senha -->
      <div formGroupName="senhas">
        <div>
          <label for="senha">Senha:</label>
          <input id="senha" type="password" formControlName="senha">
          <ng-container *ngIf="form.get('senhas.senha')?.errors?.['senhaFraca'] as senhaErros">
            <p *ngIf="senhaErros.maiuscula">Necessário letra maiúscula</p>
            <p *ngIf="senhaErros.minuscula">Necessário letra minúscula</p>
            <p *ngIf="senhaErros.numero">Necessário número</p>
            <p *ngIf="senhaErros.especial">Necessário caractere especial</p>
          </ng-container>
        </div>

        <div>
          <label for="confirmacaoSenha">Confirmar Senha:</label>
          <input id="confirmacaoSenha" type="password" formControlName="confirmacaoSenha">
          <span *ngIf="form.get('senhas')?.errors?.['senhasNaoConferem']">
            Senhas não conferem
          </span>
        </div>
      </div>

      <!-- Datas -->
      <div formGroupName="periodo">
        <div>
          <label for="dataInicio">Data Início:</label>
          <input id="dataInicio" type="date" formControlName="dataInicio">
        </div>
        <div>
          <label for="dataFim">Data Fim:</label>
          <input id="dataFim" type="date" formControlName="dataFim">
        </div>
        <span *ngIf="form.get('periodo')?.errors?.['dateRange'] as dateError">
          {{dateError.message}}
        </span>
      </div>

      <!-- Telefones -->
      <div formArrayName="telefones">
        <button type="button" (click)="adicionarTelefone()">
          Adicionar Telefone
        </button>

        <div *ngFor="let telefone of telefones.controls; let i = index"
             [formGroupName]="i">
          <input formControlName="numero" placeholder="Número">
          <select formControlName="tipo">
            <option value="CELULAR">Celular</option>
            <option value="FIXO">Fixo</option>
          </select>
          <button type="button" (click)="removerTelefone(i)">
            Remover
          </button>
        </div>
      </div>

      <button type="submit" [disabled]="form.invalid || form.pending">
        Cadastrar
      </button>
    </form>
  `
})
export class CadastroComponent implements OnInit {
  form = this.fb.group({
    cpf: ['', [Validators.required, CustomValidators.cpf()]],
    email: ['', {
      validators: [Validators.required, Validators.email],
      asyncValidators: [AsyncValidators.emailDisponivel(this.userService)],
      updateOn: 'blur'
    }],
    senhas: this.fb.group({
      senha: ['', [Validators.required, CustomValidators.senhaForte()]],
      confirmacaoSenha: ['', Validators.required]
    }, { validators: CustomValidators.senhasIguais() }),
    periodo: this.fb.group({
      dataInicio: [null as Date | null, Validators.required],
      dataFim: [null as Date | null, Validators.required]
    }, { validators: CustomValidators.dateRange() }),
    telefones: this.fb.array<FormGroup<TelefoneModel>>([])
  });

  get telefones() {
    return this.form.controls.telefones;
  }

  constructor(
    private fb: FormBuilder,
    private userService: UserService,
    private cepService: CepService
  ) {}

  ngOnInit(): void {
    this.adicionarTelefone(); // Adiciona primeiro telefone
  }

  adicionarTelefone(): void {
    const telefoneForm = this.fb.group<TelefoneModel>({
      numero: ['', [Validators.required, Validators.pattern(/^\d{10,11}$/)]],
      tipo: ['CELULAR', Validators.required]
    });

    this.telefones.push(telefoneForm);
  }

  removerTelefone(index: number): void {
    this.telefones.removeAt(index);
  }

  onSubmit(): void {
    if (this.form.valid) {
      const formValue = this.form.getRawValue(); // Tipado automaticamente!
      console.log(formValue);
      // Implementar lógica de submit
    }
  }
}
```

## 🎯 Casos de Uso Comuns

### 1. Validação de Documentos
```typescript
static cnpj(): ValidatorFn {
  return (control: AbstractControl<string>): ValidationErrors | null => {
    // Implementação da validação de CNPJ
  };
}
```

### 2. Validação de Cartão de Crédito
```typescript
static cartaoCredito(): ValidatorFn {
  return (control: AbstractControl<string>): ValidationErrors | null => {
    // Implementação do algoritmo de Luhn
  };
}
```

## 💡 Boas Práticas

1. **Type Safety**
   - Sempre defina interfaces para seus formulários
   - Use union types para campos opcionais
   - Aproveite a inferência de tipos do TypeScript

2. **Performance**
   - Use `updateOn: 'blur'` para validações assíncronas
   - Implemente `distinctUntilChanged()` em `valueChanges`
   - Evite validações pesadas no `valueChanges`

3. **Organização**
   - Mantenha validadores em um módulo separado
   - Agrupe validadores relacionados
   - Use namespaces para organizar validadores por domínio

4. **Reutilização**
   - Crie validadores genéricos e reutilizáveis
   - Use composição de validadores quando possível
   - Documente os casos de uso dos validadores

5. **Error Handling**
   - Defina interfaces para seus erros
   - Use mensagens de erro claras e específicas
   - Implemente tratamento de erros consistente

## 🤝 Contribuindo

Sinta-se à vontade para contribuir com mais validadores úteis ou melhorias nos existentes através de Pull Requests.

## 📝 Notas

- Sempre considere a performance ao criar validadores assíncronos
- Use debounceTime/distinctUntilChanged para chamadas à API
- Mantenha os validadores simples e reutilizáveis
- Documente bem os casos de uso e parâmetros esperados
