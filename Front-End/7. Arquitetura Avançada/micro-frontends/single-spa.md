# 🅰️ Single-SPA com Angular 18: Microfrontends Definitivo

## 📘 Introdução ao Single-SPA no Angular

### O que é Single-SPA para Angular?
Single-SPA no Angular permite criar aplicações modulares compostas por múltiplos microfrontends, todos construídos com Angular 18.

## 🛠 Configuração do Ambiente

### Pré-requisitos
- Node.js 18+
- Angular CLI 18
- Conhecimento intermediário de Angular

### Instalação das Dependências

```bash
# Instalar pacotes essenciais
npm install single-spa single-spa-angular @angular-architects/module-federation

# Instalar Angular CLI
npm install -g @angular/cli@18
```

## 🧩 Arquitetura de Microfrontends no Angular

### Estrutura Básica
```
meu-projeto/
├── root-config/
├── shell-app/
├── micro-frontend-1/
├── micro-frontend-2/
└── shared/
```

## 🔧 Configuração Passo a Passo

### 1. Configuração do Root Config

```typescript
// main.single-spa.ts
import { registerApplication, start } from 'single-spa';

registerApplication({
  name: '@meu-org/shell',
  app: () => System.import('@meu-org/shell'),
  activeWhen: ['/']
});

registerApplication({
  name: '@meu-org/produtos',
  app: () => System.import('@meu-org/produtos'),
  activeWhen: ['/produtos']
});

start();
```

### 2. Configuração do Módulo Angular

```typescript
// app.module.ts
import { NgModule } from '@angular/core';
import { singleSpaAngular } from 'single-spa-angular';

@NgModule({
  // Configurações do módulo
})
export class AppModule {
  static singleSpaModule = singleSpaAngular({
    bootstrapFunction: () => platformBrowserDynamic().bootstrapModule(AppModule)
  });
}
```

### 3. Webpack Module Federation

```javascript
// webpack.config.js
const ModuleFederationPlugin = require('webpack/lib/container/ModuleFederationPlugin');

module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'meu_microfrontend',
      filename: 'remoteEntry.js',
      exposes: {
        './Component': './src/app/app.component.ts'
      }
    })
  ]
};
```

## 🚦 Estratégias de Roteamento

### Roteamento Dinâmico
- Use `activeWhen` para definir rotas específicas
- Suporte a rotas exatas e prefixadas
- Navegação fluida entre microfrontends

## 🔍 Comunicação entre Microfrontends

### Estratégias no Angular
- Serviços compartilhados
- RxJS para comunicação de estado
- Events bus personalizado

### Exemplo de Serviço Compartilhado

```typescript
@Injectable({
  providedIn: 'root'
})
export class MicrofrontendCommunicationService {
  private eventSubject = new Subject<any>();

  sendEvent(event: any) {
    this.eventSubject.next(event);
  }

  getEvents() {
    return this.eventSubject.asObservable();
  }
}
```

## ⚙️ Configurações Avançadas

### Dicas de Performance
- Lazy loading de módulos
- Code splitting
- Otimização de chunks
- Estratégias de pré-carregamento

## 🚨 Desafios Comuns

- Gerenciamento de estado global
- Versionamento de dependências
- Overhead de performance
- Complexidade de configuração inicial

## 🛡️ Boas Práticas

- Mantenha componentes desacoplados
- Use interfaces bem definidas
- Centralize estado global
- Documente interfaces entre microfrontends

## 📊 Monitoramento

### Ferramentas Recomendadas
- Angular DevTools
- Chrome Performance Tab
- Sentry para rastreamento de erros

## 🤝 Compartilhamento de Dependências

```typescript
// shared-dependencies.module.ts
@NgModule({
  exports: [
    CommonModule,
    HttpClientModule,
    // Outros módulos compartilhados
  ]
})
export class SharedDependenciesModule {}
```

## 🔗 Referências Úteis
- [Documentação Oficial Single-SPA](https://single-spa.js.org/)
- [Angular Module Federation](https://www.angulararchitects.io/en/aktuelles/module-federation-with-angular/)

## 📄 Licença
MIT License
