# 💉 Dependency Injection (DI) no Angular

## 📚 Índice
- [Conceito Fundamental](#-conceito-fundamental)
- [Tipos de Injetores](#-tipos-de-injetores)
- [Hierarquia de Injeção](#-hierarquia-de-injeção)
- [Modificadores de Resolução](#-modificadores-de-resolução)
- [Providers](#-providers)
- [Exemplos Práticos](#-exemplos-práticos)

## 🎯 Conceito Fundamental

### O que é Dependency Injection?
Dependency Injection é um padrão de design onde uma classe delega a responsabilidade de criar e fornecer suas dependências a uma fonte externa. No Angular, é um sistema fundamental que permite que componentes, diretivas e serviços recebam as dependências que precisam sem precisar criá-las.

### Por que usar DI?
- ✨ Melhor manutenibilidade
- 🔄 Reutilização de código
- 🧪 Facilita testes unitários
- 📦 Modularidade aprimorada

## 🏗 Tipos de Injetores

### 1. Module Injector (NgModule)
```typescript
@NgModule({
  providers: [
    MeuServico
  ]
})
export class MeuModulo { }
```
- Configurado em nível de módulo
- Disponível para todo o módulo
- Definido através do decorator `@NgModule`

### 2. Element Injector
```typescript
@Component({
  providers: [
    MeuServico
  ]
})
export class MeuComponente { }
```
- Criado para cada elemento DOM
- Configurado em componentes/diretivas
- Escopo limitado ao componente e seus filhos

## 🌳 Hierarquia de Injeção

### Processo de Resolução
1. Verifica o Element Injector do componente atual
2. Sobe na árvore de Element Injectors
3. Volta ao ponto inicial
4. Verifica o Module Injector
5. Sobe na hierarquia de Module Injectors

```typescript
// Exemplo de hierarquia
@Injectable({
  providedIn: 'root' // Disponível em toda aplicação
})
class ServicoGlobal { }

@Component({
  providers: [ServicoLocal] // Disponível apenas neste componente
})
class MeuComponente { }
```

## 🎮 Modificadores de Resolução

### @Optional
```typescript
constructor(@Optional() private servico: MeuServico) { }
```
- Permite que a dependência seja opcional
- Retorna null se não encontrada

### @Self
```typescript
constructor(@Self() private servico: MeuServico) { }
```
- Procura apenas no injector atual
- Lança erro se não encontrar

### @SkipSelf
```typescript
constructor(@SkipSelf() private servico: MeuServico) { }
```
- Pula o injector atual
- Procura apenas nos ancestrais

### @Host
```typescript
constructor(@Host() private servico: MeuServico) { }
```
- Limita a busca ao component host
- Útil em diretivas

## 🛠 Providers

### useClass
```typescript
providers: [
  { provide: MeuServico, useClass: MinhaImplementacao }
]
```
- Fornece uma implementação específica

### useValue
```typescript
providers: [
  { provide: CONFIG, useValue: { api: 'http://api.exemplo.com' } }
]
```
- Fornece um valor literal

### useFactory
```typescript
providers: [
  {
    provide: MeuServico,
    useFactory: (http: HttpClient) => {
      return new MeuServico(http);
    },
    deps: [HttpClient]
  }
]
```
- Cria instância dinamicamente
- Permite lógica personalizada

### useExisting
```typescript
providers: [
  { provide: NovaInterface, useExisting: ServicoExistente }
]
```
- Usa uma instância existente
- Cria um alias

## 💻 Exemplos Práticos

### Serviço Básico
```typescript
@Injectable({
  providedIn: 'root'
})
export class LogService {
  log(message: string) {
    console.log(message);
  }
}
```

### Injeção em Componente
```typescript
@Component({
  selector: 'app-exemplo',
  template: `<div>{{dados}}</div>`,
  providers: [DadosService]
})
export class ExemploComponent {
  constructor(
    private dadosService: DadosService,
    @Optional() private logService?: LogService
  ) { }
}
```

### Provider com Factory
```typescript
@NgModule({
  providers: [
    {
      provide: 'API_CONFIG',
      useFactory: (environment: any) => {
        return {
          apiUrl: environment.production 
            ? 'https://api.prod.com' 
            : 'https://api.dev.com'
        };
      },
      deps: ['ENV']
    }
  ]
})
```

## 🔍 Debugging e Boas Práticas

### Dicas de Debugging
```typescript
// Visualizar árvore de injeção
constructor(injector: Injector) {
  console.log('Hierarquia:', injector);
}
```

### Boas Práticas
1. **Use `providedIn: 'root'` para serviços singleton**
2. **Prefira `useClass` e `useFactory` sobre `useValue`**
3. **Mantenha a hierarquia de injeção clara**
4. **Documente providers complexos**

## ⚠️ Armadilhas Comuns

### Circular Dependencies
```typescript
// ❌ Evite isso
@Injectable()
class ServicoA {
  constructor(private b: ServicoB) {}
}

@Injectable()
class ServicoB {
  constructor(private a: ServicoA) {}
}
```

### Multiple Instances
```typescript
// ⚠️ Cuidado com providers em múltiplos níveis
@Component({
  providers: [MeuServico] // Nova instância
})
@Component({
  providers: [MeuServico] // Outra instância
})
```

## 📈 Performance

### Lazy Loading
```typescript
@Injectable({
  providedIn: 'any' // Lazy loaded
})
export class MeuServicoLazy { }
```

### Tree-Shakeable Providers
```typescript
@Injectable({
  providedIn: 'root',
  useFactory: () => new MeuServico()
})
export class MeuServico { }
```

---

⭐ **Dicas Finais**:
- Mantenha a hierarquia de injeção simples e clara
- Use modificadores de resolução com sabedoria
- Documente decisões de design complexas
- Teste diferentes cenários de injeção

📚 **Recursos Adicionais**:
- [Documentação Oficial do Angular DI](https://angular.io/guide/dependency-injection)
- [Angular DI Providers](https://angular.io/guide/dependency-injection-providers)
- [Hierarchical Dependency Injection](https://angular.io/guide/hierarchical-dependency-injection)
