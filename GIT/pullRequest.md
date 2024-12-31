# Template de Review de Pull Request

## Estrutura do Código

### Imports
- Ordem de imports:
  1. Angular core
  2. Angular modules
  3. Third-party libraries
  4. Local modules
  5. Components/Services/etc
- Linha em branco entre grupos de imports
- Evitar imports com `*`

```typescript
import { Component, OnInit } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';

import { SharedModule } from '@shared/shared.module';

import { UserService } from './user.service';
```

### Espaçamento
- Uma linha em branco entre:
  - Métodos
  - Propriedades diferentes
  - Decorators e suas classes/propriedades
- Duas linhas em branco entre:
  - Classes
  - Interfaces principais
  - Enums

### Organização de Arquivos
```
feature/
├── components/
│   ├── feature.component.ts
│   ├── feature.component.html
│   ├── feature.component.scss
│   └── feature.component.spec.ts
├── services/
├── models/
└── feature.module.ts
```

### Lógica
- Componentes:
  ```typescript
  @Component({...})
  export class FeatureComponent implements OnInit, OnDestroy {
    // 1. Decorators
    @Input() data: Data;
    
    // 2. Propriedades públicas
    isLoading = false;
    
    // 3. Propriedades privadas
    private destroy$ = new Subject<void>();
    
    // 4. Getters/Setters
    get isValid(): boolean {
      return this.form.valid;
    }
    
    // 5. Constructor
    constructor(private service: Service) {}
    
    // 6. Lifecycle hooks
    ngOnInit(): void {
      this.initialize();
    }
    
    // 7. Métodos públicos
    submit(): void {
      // lógica
    }
    
    // 8. Métodos privados
    private initialize(): void {
      // lógica
    }
  }
  ```

### Boas Práticas
- Métodos com responsabilidade única
- Máximo 400 linhas por arquivo
- Máximo 3 níveis de indentação
- Evitar else após return
- RxJS: 
  - Usar pipe para transformações
  - takeUntil para unsubscribe
  - catchError para tratamento de erros

### Nomenclatura
- Componentes: `feature.component.ts`
- Serviços: `feature.service.ts`
- Interfaces: `IFeature` ou `Feature`
- Enums: `FeatureType`
- Observables: sufixo `$`
- Métodos: verbo + substantivo
- Private properties: prefixo `_`

## Padrões TypeScript
```typescript
// Interfaces vs Types
interface User {
  id: number;
  name: string;
}

type UserResponse = User & {
  token: string;
}

// Enums
enum Status {
  Active = 'ACTIVE',
  Inactive = 'INACTIVE'
}

// Generics
function transform<T>(items: T[]): T[] {
  return items.map(item => ({...item}));
}
```

### 💖 Feito com carinho para todos os devs

*Última atualização: Dezembro 2024*
