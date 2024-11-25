# 🔄 Change Detection no Angular

## 📚 Índice
- [Visão Geral](#-visão-geral)
- [Gatilhos de Detecção](#-gatilhos-de-detecção)
- [Estratégias](#-estratégias)
- [NgZone](#-ngzone)
- [Boas Práticas](#-boas-práticas)
- [Otimizações](#-otimizações)
- [Exemplos Práticos](#-exemplos-práticos)
- [Debugging](#-debugging)

## 🎯 Visão Geral
Change Detection é o mecanismo do Angular responsável por garantir que seu estado (dados/modelo) esteja sempre sincronizado com a interface do usuário (UI). É como um vigilante que monitora mudanças e atualiza a tela quando necessário.

## 🔔 Gatilhos de Detecção

### 🖱️ Eventos do Usuário
- Cliques
- Movimentos do mouse
- Digitação
- Submissão de formulários

### 🌐 Chamadas Assíncronas
- Requisições HTTP
- Promises
- Observables

### ⏰ Timers
- setTimeout/setInterval
- requestAnimationFrame

## 💡 Estratégias

### ⚡ Default (Padrão)
```typescript
@Component({
  selector: 'app-exemplo',
  template: '...'
})
```
- Verifica toda a árvore de componentes
- Mais seguro, porém menos performático

### 🚀 OnPush
```typescript
@Component({
  selector: 'app-exemplo',
  template: '...',
  changeDetection: ChangeDetectionStrategy.OnPush
})
```
Só é acionado quando:
- ✨ Referências @Input mudam
- 🎮 Eventos são disparados
- 🔄 Change Detection manual
- 📡 Async pipe emite valores

## 🔄 NgZone

### O que é?
- Serviço que encapsula a aplicação
- Monitora operações assíncronas
- Gerencia ciclos de Change Detection

### Exemplo Básico
```typescript
constructor(private ngZone: NgZone) {
  // Executar fora do Angular
  this.ngZone.runOutsideAngular(() => {
    // Operações pesadas aqui
  });
}
```

## 👍 Boas Práticas

### 📌 Use OnPush quando possível
```typescript
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush
})
```

### 🔒 Mantenha dados imutáveis
```typescript
// ❌ Evite
this.dados.propriedade = novoValor;

// ✅ Faça
this.dados = { ...this.dados, propriedade: novoValor };
```

### 🎯 Controle manual quando necessário
```typescript
constructor(private cd: ChangeDetectorRef) {
  // Detectar mudanças manualmente
  this.cd.detectChanges();
}
```

## 🚀 Otimizações

### 💪 Performance
1. **Desacople quando não precisar**
```typescript
this.cd.detach(); // Desacopla do Change Detection
```

2. **Use runOutsideAngular para operações pesadas**
```typescript
this.ngZone.runOutsideAngular(() => {
  // Animações ou cálculos intensivos
});
```

### 🔍 Monitoramento
- Use Angular DevTools
- Monitore ciclos de detecção
- Profile a performance

## 💻 Exemplos Práticos

### 🔄 Componente Otimizado
```typescript
@Component({
  selector: 'app-otimizado',
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <div>{{ dados$ | async }}</div>
  `
})
export class ComponenteOtimizado {
  dados$ = new BehaviorSubject<any>(valorInicial);
}
```

## 🐞 Debugging

### 🔍 Ferramentas
- Chrome DevTools
- Angular DevTools Extension
- Console logging estratégico

### 📊 Performance
```typescript
// Medindo tempo de Change Detection
console.time('change-detection');
this.cd.detectChanges();
console.timeEnd('change-detection');
```

---

## 📝 Notas Importantes
- Sempre teste a performance
- Monitore o número de detecções
- Use DevTools para debugging
- Mantenha-se atualizado com as melhores práticas

## 🔗 Links Úteis
- [Documentação Oficial do Angular](https://angular.io/guide/change-detection)
- [Angular DevTools](https://angular.io/guide/devtools)
- [Guia de Performance](https://angular.io/guide/performance)

---

⭐ **Dica**: Mantenha este guia como referência ao implementar estratégias de Change Detection em seus projetos Angular.
