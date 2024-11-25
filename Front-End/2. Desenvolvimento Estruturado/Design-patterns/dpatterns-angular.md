# 🏗️ Design Patterns no Angular

> Arquitetura Inteligente, Código Elegante 🚀

## 🌟 Introdução

Design Patterns são soluções comprovadas para problemas recorrentes no desenvolvimento de software. No Angular, esses padrões nos ajudam a criar aplicações:
- 🧩 Modulares
- 🔒 Escaláveis
- 🚀 Performáticas
- 🤝 Fáceis de manter

## 🗂️ Categorias de Design Patterns

### 1. 🏗️ Padrões Estruturais

```mermaid
graph TD
    A[Padrões Estruturais] --> B[Module Pattern]
    A --> C[Feature Modules]
    A --> D[Composition]
    
    B --> E[Organização de Código]
    C --> F[Modularização]
    D --> G[Reutilização de Componentes]
```

#### Module Pattern
- 🔑 Organização nativa do Angular
- 📦 Agrupamento de funcionalidades relacionadas
- 🧩 Controle de escopo e dependências

```typescript
@NgModule({
  declarations: [ComponentesRelacionados],
  imports: [DependênciasNecessárias],
  providers: [ServiçosEspecíficos]
})
export class MeuModuloEspecifico { }
```

### 2. 🤝 Padrões de Serviço

```mermaid
graph TD
    A[Padrões de Serviço] --> B[Singleton]
    A --> C[Repository]
    A --> D[Facade]
    
    B --> E[Instância Única]
    C --> F[Abstração de Dados]
    D --> G[Simplificação de Interfaces]
```

#### Singleton Service
- 🔒 Instância única globalmente
- 💾 Gerenciamento de estado centralizado
- 🌐 Acessível em toda aplicação

```typescript
@Injectable({ providedIn: 'root' })
export class AuthService {
  private currentUser: User | null = null;
  
  login(credentials: Credentials): Observable<User> {
    return this.http.post<User>('/login', credentials)
      .pipe(tap(user => this.currentUser = user));
  }
}
```

### 3. 🖥️ Padrões de Componente

```mermaid
graph TD
    A[Padrões de Componente] --> B[Container/Presenter]
    A --> C[Observer]
    A --> D[Composition]
    
    B --> E[Separação de Responsabilidades]
    C --> F[Gerenciamento Reativo]
    D --> G[Componentes Reutilizáveis]
```

#### Container/Presenter Pattern
- 🧠 Componentes inteligentes (Container)
- 🎨 Componentes de apresentação (Presenter)
- 🔀 Separação clara de responsabilidades

```typescript
// Container (Smart Component)
@Component({
  template: `<app-user-list [users]="users$ | async"></app-user-list>`
})
export class UserListContainerComponent {
  users$ = this.userService.getUsers();
}

// Presenter (Dumb Component)
@Component({
  template: `
    <div *ngFor="let user of users">
      {{ user.name }}
    </div>
  `
})
export class UserListComponent {
  @Input() users: User[] = [];
}
```

### 4. 🔄 Padrões de Estado

```mermaid
graph TD
    A[Padrões de Estado] --> B[State Management]
    A --> C[Facade]
    A --> D[Store]
    
    B --> E[Gerenciamento Centralizado]
    C --> F[Simplificação de Complexidade]
    D --> G[Fluxo de Dados Previsível]
```

#### State Management Pattern
- 📊 Gerenciamento de estado
- 🔄 Fluxo de dados controlado
- 🧩 Previsibilidade

```typescript
@Injectable({ providedIn: 'root' })
export class CartStore {
  private state = new BehaviorSubject<CartState>({
    items: [],
    total: 0
  });

  state$ = this.state.asObservable();

  addItem(item: CartItem): void {
    const currentState = this.state.getValue();
    this.state.next({
      items: [...currentState.items, item],
      total: currentState.total + item.price
    });
  }
}
```

## 🛡️ Boas Práticas

### 1. Dependency Injection
- 🔌 Desacoplamento de dependências
- 🧩 Injeção automática de serviços
- 🚀 Facilita testes e manutenção

### 2. HTTP Interceptors
- 🌐 Interceptação de requisições
- 🔒 Segurança e transformação de requests
- 📡 Manipulação global de chamadas HTTP

## 🏆 Benefícios dos Design Patterns

- 📐 Arquitetura consistente
- 🚀 Código mais limpo e legível
- 🤝 Facilita colaboração
- 💡 Soluções testadas e aprovadas

## 🚦 Conclusão

Design Patterns não são apenas técnicas, são **filosofias de desenvolvimento**. No Angular, eles nos ajudam a criar aplicações robustas, escaláveis e de fácil manutenção.

**Lembre-se**: Padrões são guias, não correntes. Use-os com sabedoria! 🧠✨
