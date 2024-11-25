# Design Patterns no Angular
> Guia completo de padrões de projeto aplicados ao Angular

## Sumário
- [Padrões Estruturais](#padrões-estruturais)
- [Padrões de Serviço](#padrões-de-serviço)
- [Padrões de Componente](#padrões-de-componente)
- [Padrões de Estado](#padrões-de-estado)
- [Boas Práticas](#boas-práticas)

## Padrões Estruturais

### 1. Module Pattern
O Angular já utiliza módulos nativamente através do `NgModule`.

```typescript
@NgModule({
  declarations: [
    AppComponent,
    HomeComponent
  ],
  imports: [
    BrowserModule,
    HttpClientModule
  ],
  providers: [
    AuthService
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### 2. Feature Modules
Organização de módulos por funcionalidade.

```typescript
// user.module.ts
@NgModule({
  declarations: [
    UserListComponent,
    UserDetailComponent,
    UserFormComponent
  ],
  imports: [
    CommonModule,
    UserRoutingModule
  ],
  providers: [
    UserService
  ]
})
export class UserModule { }
```

## Padrões de Serviço

### 1. Singleton Service
Serviços são singletons por padrão no Angular quando providenciados no root.

```typescript
@Injectable({
  providedIn: 'root'
})
export class AuthService {
  private currentUser: User | null = null;

  login(credentials: Credentials): Observable<User> {
    return this.http.post<User>('/api/login', credentials)
      .pipe(
        tap(user => this.currentUser = user)
      );
  }

  getCurrentUser(): User | null {
    return this.currentUser;
  }
}
```

### 2. Repository Pattern
Abstrai a lógica de acesso a dados.

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserRepository {
  private apiUrl = 'api/users';

  constructor(private http: HttpClient) {}

  getAll(): Observable<User[]> {
    return this.http.get<User[]>(this.apiUrl);
  }

  getById(id: number): Observable<User> {
    return this.http.get<User>(`${this.apiUrl}/${id}`);
  }

  create(user: User): Observable<User> {
    return this.http.post<User>(this.apiUrl, user);
  }
}
```

## Padrões de Componente

### 1. Container/Presenter Pattern (Smart/Dumb Components)
Separa componentes com lógica de negócios dos componentes de apresentação.

```typescript
// container component (smart)
@Component({
  selector: 'app-user-list-container',
  template: `
    <app-user-list
      [users]="users$ | async"
      (userSelected)="onUserSelected($event)">
    </app-user-list>
  `
})
export class UserListContainerComponent {
  users$ = this.userService.getUsers();

  constructor(private userService: UserService) {}

  onUserSelected(user: User): void {
    // lógica de negócios aqui
  }
}

// presenter component (dumb)
@Component({
  selector: 'app-user-list',
  template: `
    <div *ngFor="let user of users">
      <div (click)="select(user)">
        {{ user.name }}
      </div>
    </div>
  `
})
export class UserListComponent {
  @Input() users: User[] = [];
  @Output() userSelected = new EventEmitter<User>();

  select(user: User): void {
    this.userSelected.emit(user);
  }
}
```

### 2. Observer Pattern (com RxJS)
Implementação de observables para gerenciar fluxos de dados.

```typescript
@Component({
  selector: 'app-search',
  template: `
    <input [formControl]="searchControl">
    <div *ngFor="let result of searchResults$ | async">
      {{ result.name }}
    </div>
  `
})
export class SearchComponent implements OnInit {
  searchControl = new FormControl('');
  searchResults$: Observable<SearchResult[]>;

  constructor(private searchService: SearchService) {
    this.searchResults$ = this.searchControl.valueChanges.pipe(
      debounceTime(300),
      distinctUntilChanged(),
      switchMap(term => this.searchService.search(term))
    );
  }
}
```

## Padrões de Estado

### 1. State Management Pattern
Usando services para gerenciar estado.

```typescript
@Injectable({
  providedIn: 'root'
})
export class CartStore {
  private state = new BehaviorSubject<CartState>({
    items: [],
    total: 0
  });

  state$ = this.state.asObservable();
  items$ = this.state$.pipe(map(state => state.items));
  total$ = this.state$.pipe(map(state => state.total));

  addItem(item: CartItem): void {
    const currentState = this.state.getValue();
    this.state.next({
      items: [...currentState.items, item],
      total: currentState.total + item.price
    });
  }
}
```

### 2. Facade Pattern
Simplifica interfaces complexas.

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserFacade {
  private userState = new BehaviorSubject<UserState>({
    user: null,
    loading: false,
    error: null
  });

  user$ = this.userState.pipe(map(state => state.user));
  loading$ = this.userState.pipe(map(state => state.loading));
  error$ = this.userState.pipe(map(state => state.error));

  constructor(
    private userService: UserService,
    private authService: AuthService
  ) {}

  loadUser(id: string): void {
    this.userState.next({ ...this.userState.getValue(), loading: true });
    this.userService.getUser(id).pipe(
      tap(user => this.userState.next({
        user,
        loading: false,
        error: null
      })),
      catchError(error => {
        this.userState.next({
          user: null,
          loading: false,
          error
        });
        return EMPTY;
      })
    ).subscribe();
  }
}
```

## Padrões de Forms

### 1. Form Builder Pattern
```typescript
@Component({
  selector: 'app-user-form',
  template: `
    <form [formGroup]="userForm" (ngSubmit)="onSubmit()">
      <input formControlName="name">
      <input formControlName="email">
      <button type="submit">Save</button>
    </form>
  `
})
export class UserFormComponent {
  userForm = this.fb.group({
    name: ['', Validators.required],
    email: ['', [Validators.required, Validators.email]]
  });

  constructor(private fb: FormBuilder) {}

  onSubmit(): void {
    if (this.userForm.valid) {
      console.log(this.userForm.value);
    }
  }
}
```

## Boas Práticas

### 1. Dependency Injection
O Angular usa DI nativamente.

```typescript
@Injectable({
  providedIn: 'root'
})
export class ProductService {
  constructor(
    private http: HttpClient,
    private authService: AuthService
  ) {}
}
```

### 2. Interceptors
Pattern para interceptar requisições HTTP.

```typescript
@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  constructor(private authService: AuthService) {}

  intercept(
    req: HttpRequest<any>,
    next: HttpHandler
  ): Observable<HttpEvent<any>> {
    const token = this.authService.getToken();
    if (token) {
      const authReq = req.clone({
        headers: req.headers.set('Authorization', `Bearer ${token}`)
      });
      return next.handle(authReq);
    }
    return next.handle(req);
  }
}
```
