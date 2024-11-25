# 🧪 Guia Completo: Jest no Angular

## 📚 Índice
- [Introdução ao Jest](#-introdução-ao-jest)
- [Configuração Inicial](#-configuração-inicial)
- [Estrutura dos Testes](#-estrutura-dos-testes)
- [Testando Componentes](#-testando-componentes)
- [Testando Serviços](#-testando-serviços)
- [Testando Pipes](#-testando-pipes)
- [Mocks e Spies](#-mocks-e-spies)
- [Matchers do Jest](#-matchers-do-jest)
- [Boas Práticas](#-boas-práticas)
- [Depuração e Execução](#-depuração-e-execução)

## 🎯 Introdução ao Jest

### O que é Jest?
Jest é um poderoso framework de testes em JavaScript desenvolvido pelo Facebook. Ele se destaca por:

✨ **Principais Características:**
- 🚀 Execução rápida de testes
- 🎨 Sintaxe limpa e intuitiva
- 📸 Snapshot testing
- 📊 Cobertura de código integrada
- 🎭 Sistema de mocking poderoso
- 👀 Watch mode inteligente

## ⚙️ Configuração Inicial

### Instalação
```bash
# Remover Karma e instalar Jest
ng add @briebug/jest-schematic
```

### Configuração do package.json
```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  }
}
```

## 🏗️ Estrutura dos Testes

### Anatomia de um Teste
```typescript
describe('📦 Nome do Módulo', () => {
  // Configuração global
  beforeAll(() => {
    // Executado uma vez antes de todos os testes
  });

  // Configuração por teste
  beforeEach(() => {
    // Executado antes de cada teste
  });

  // 🧪 Teste individual
  it('deve realizar uma ação específica', () => {
    // Arrange (Preparação)
    const valor = 'teste';

    // Act (Ação)
    const resultado = fazerAlgo(valor);

    // Assert (Verificação)
    expect(resultado).toBe('TESTE');
  });
});
```

## 🎨 Testando Componentes

### Exemplo de Componente
```typescript
// 📄 profile.component.ts
@Component({
  selector: 'app-profile',
  template: `
    <div class="profile">
      <h1>{{ userName }}</h1>
      <button (click)="updateProfile()">Atualizar</button>
    </div>
  `
})
export class ProfileComponent {
  userName = 'João Silva';

  updateProfile() {
    this.userName = 'João Silva Atualizado';
  }
}

// 🧪 profile.component.spec.ts
describe('ProfileComponent', () => {
  let component: ProfileComponent;
  let fixture: ComponentFixture<ProfileComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ ProfileComponent ]
    }).compileComponents();

    fixture = TestBed.createComponent(ProfileComponent);
    component = fixture.componentInstance;
  });

  it('🎯 deve criar o componente', () => {
    expect(component).toBeTruthy();
  });

  it('🔄 deve atualizar o nome do usuário', () => {
    // Arrange
    fixture.detectChanges();
    const button = fixture.debugElement.query(By.css('button'));

    // Act
    button.triggerEventHandler('click', null);
    fixture.detectChanges();

    // Assert
    const h1 = fixture.debugElement.query(By.css('h1'));
    expect(h1.nativeElement.textContent).toContain('Atualizado');
  });
});
```

## 🔧 Testando Serviços

### Exemplo de Serviço HTTP
```typescript
// 📄 user.service.ts
@Injectable({
  providedIn: 'root'
})
export class UserService {
  constructor(private http: HttpClient) {}

  getUsers(): Observable<User[]> {
    return this.http.get<User[]>('/api/users');
  }
}

// 🧪 user.service.spec.ts
describe('UserService', () => {
  let service: UserService;
  let httpMock: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [UserService]
    });

    service = TestBed.inject(UserService);
    httpMock = TestBed.inject(HttpTestingController);
  });

  it('📡 deve buscar usuários', () => {
    const mockUsers = [
      { id: 1, name: 'Ana' },
      { id: 2, name: 'Carlos' }
    ];

    service.getUsers().subscribe(users => {
      expect(users).toEqual(mockUsers);
    });

    const req = httpMock.expectOne('/api/users');
    req.flush(mockUsers);
  });
});
```

## 🔄 Testando Pipes

### Exemplo de Pipe Personalizado
```typescript
// 📄 format-text.pipe.ts
@Pipe({ name: 'formatText' })
export class FormatTextPipe implements PipeTransform {
  transform(value: string): string {
    return value?.toUpperCase() || '';
  }
}

// 🧪 format-text.pipe.spec.ts
describe('FormatTextPipe', () => {
  const pipe = new FormatTextPipe();

  it('✨ deve transformar texto para maiúsculas', () => {
    expect(pipe.transform('hello')).toBe('HELLO');
  });

  it('⚡ deve lidar com valores nulos', () => {
    expect(pipe.transform(null)).toBe('');
  });
});
```

## 🎭 Mocks e Spies

### Exemplos de Mocks
```typescript
// 🎭 Mock de Serviço
const mockUserService = {
  getUsers: jest.fn().mockReturnValue(['User1', 'User2'])
};

// 👀 Spy em Método
const spy = jest.spyOn(component, 'metodo');
component.metodo();
expect(spy).toHaveBeenCalled();
```

## 🎯 Matchers do Jest

### Matchers Comuns
```typescript
// ✅ Verificações Básicas
expect(valor).toBe(esperado);        // Igualdade estrita
expect(valor).toEqual(esperado);     // Igualdade profunda
expect(valor).toBeTruthy();          // Valor verdadeiro
expect(valor).toBeFalsy();           // Valor falso

// 📐 Números
expect(numero).toBeGreaterThan(3);   // Maior que
expect(numero).toBeLessThan(5);      // Menor que

// 📝 Strings
expect(texto).toMatch(/padrão/);     // Regex
expect(texto).toContain('valor');    // Contém

// 📦 Arrays
expect(array).toContain(item);       // Contém item
expect(array).toHaveLength(2);       // Tamanho

// 🎲 Objetos
expect(objeto).toHaveProperty('prop');// Tem propriedade
```

## 📋 Boas Práticas

### 1. 🎯 Organização
- Use descrições claras nos testes
- Agrupe testes relacionados
- Mantenha testes independentes

### 2. 🏗️ Padrão AAA
```typescript
it('deve fazer algo', () => {
  // 🎨 Arrange
  const input = 'teste';

  // ⚡ Act
  const resultado = funcao(input);

  // ✅ Assert
  expect(resultado).toBe('TESTE');
});
```

### 3. 🔍 Cobertura de Código
```bash
# Gerar relatório de cobertura
ng test --coverage
```

## 🛠️ Depuração e Execução

### Comandos Úteis
```bash
# 🏃‍♂️ Executar todos os testes
ng test

# 👀 Modo watch
ng test --watch

# 📊 Relatório de cobertura
ng test --coverage

# 🎯 Testes específicos
ng test --testPathPattern=user
```

### 🐛 Depuração
1. Adicione `debugger;` no código
2. Execute `ng test --debug`
3. Abra Chrome DevTools (F12)

## 📚 Recursos Adicionais

### 🔗 Links Úteis
- [Documentação Oficial do Jest](https://jestjs.io/)
- [Angular Testing Guide](https://angular.io/guide/testing)
- [Jest Cheat Sheet](https://github.com/sapegin/jest-cheat-sheet)

### 💡 Dicas Finais
- Mantenha os testes simples e focados
- Atualize os testes junto com o código
- Use mocks para isolar dependências
- Prefira testes pequenos e específicos
- Mantenha cobertura de código adequada
