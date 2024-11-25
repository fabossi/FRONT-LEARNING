# 🔄 Guia Completo de Operadores RxJS

## 📋 Sumário
- [Introdução](#-introdução)
- [Categorias de Operadores](#-categorias-de-operadores)
  - [Operadores de Criação](#-operadores-de-criação)
  - [Operadores de Transformação](#-operadores-de-transformação)
  - [Operadores de Filtragem](#-operadores-de-filtragem)
  - [Operadores de Combinação](#-operadores-de-combinação)
  - [Operadores de Tratamento de Erro](#-operadores-de-tratamento-de-erro)
  - [Operadores Utilitários](#-operadores-utilitários)

## 📝 Introdução

O RxJS oferece uma vasta gama de operadores que nos permitem manipular streams de dados de forma declarativa. Este guia explica os principais operadores e suas diferenças.

## 🔍 Categorias de Operadores

### Operadores de Criação
Criam novos Observables a partir de diferentes tipos de fontes.

| Operador | Descrição | Exemplo |
|----------|-----------|---------|
| `of` | Cria um Observable a partir de valores estáticos | `of(1, 2, 3)` |
| `from` | Converte arrays, promises ou iterables em Observable | `from([1, 2, 3])` |
| `interval` | Emite números sequenciais em intervalos específicos | `interval(1000)` |
| `fromEvent` | Cria Observable a partir de eventos do DOM | `fromEvent(button, 'click')` |

### Operadores de Transformação
Modificam os valores emitidos pelo Observable.

| Operador | Descrição | Exemplo |
|----------|-----------|---------|
| `map` | Transforma cada valor emitido | `map(x => x * 2)` |
| `mergeMap` | Transforma e achata Observables aninhados | `mergeMap(x => http.get(url))` |
| `switchMap` | Cancela Observable anterior ao receber novo valor | `switchMap(term => search(term))` |
| `concatMap` | Processa Observables em sequência | `concatMap(x => saveData(x))` |

### Operadores de Filtragem
Controlam quais valores são emitidos.

| Operador | Descrição | Exemplo |
|----------|-----------|---------|
| `filter` | Emite apenas valores que passam na condição | `filter(x => x > 10)` |
| `debounceTime` | Espera um tempo antes de emitir | `debounceTime(300)` |
| `distinctUntilChanged` | Emite apenas quando o valor muda | `distinctUntilChanged()` |
| `take` | Pega N primeiros valores | `take(5)` |

### Operadores de Combinação
Trabalham com múltiplos Observables.

| Operador | Descrição | Exemplo |
|----------|-----------|---------|
| `combineLatest` | Combina últimos valores de múltiplos Observables | `combineLatest([obs1, obs2])` |
| `merge` | Une múltiplos Observables em um | `merge(obs1, obs2)` |
| `concat` | Concatena Observables em sequência | `concat(obs1, obs2)` |
| `zip` | Combina valores correspondentes | `zip(obs1, obs2)` |

### Operadores de Tratamento de Erro
Lidam com erros no stream.

| Operador | Descrição | Exemplo |
|----------|-----------|---------|
| `catchError` | Trata erros no Observable | `catchError(err => of('fallback'))` |
| `retry` | Tenta novamente em caso de erro | `retry(3)` |
| `retryWhen` | Retry personalizado baseado em lógica | `retryWhen(errors => errors.pipe(delay(1000)))` |

### Operadores Utilitários
Auxiliam no debug e controle do fluxo.

| Operador | Descrição | Exemplo |
|----------|-----------|---------|
| `tap` | Executa side effects sem modificar valores | `tap(x => console.log(x))` |
| `delay` | Atrasa a emissão de valores | `delay(1000)` |
| `timeout` | Emite erro se não receber valor em tempo | `timeout(5000)` |

## 🎯 Diferenças Importantes

### map vs flatMap vs mergeMap vs switchMap vs concatMap

#### `map`
- Transformação 1:1 simples de valores
- Não lida com Observables aninhados
- Use quando: Precisa transformar dados de forma direta
```typescript
// Exemplo: Dobrar números
of(1, 2, 3).pipe(
  map(x => x * 2)
) // Resultado: 2, 4, 6
```

#### `mergeMap` (alias para flatMap)
- Processa Observables internos em paralelo
- Mantém múltiplas subscrições ativas simultaneamente
- Ordem de chegada não é garantida
- Use quando: Precisa processar múltiplas requisições simultâneas
```typescript
// Exemplo: Múltiplas requisições HTTP simultâneas
users$.pipe(
  mergeMap(user => http.get(`/api/details/${user.id}`))
) // Processa todas requisições em paralelo
```

#### `switchMap`
- Cancela a subscrição anterior quando recebe novo valor
- Mantém apenas uma subscrição ativa
- Use quando: Precisa cancelar requisições obsoletas (ex: autocomplete)
```typescript
// Exemplo: Campo de busca
searchInput$.pipe(
  switchMap(term => http.get(`/api/search?q=${term}`))
) // Cancela requisições anteriores quando usuário digita novo termo
```

#### `concatMap`
- Processa Observables em sequência estrita
- Espera cada Observable completar antes de iniciar o próximo
- Use quando: Precisa garantir ordem de execução
```typescript
// Exemplo: Salvar dados em ordem
actions$.pipe(
  concatMap(action => saveToDatabase(action))
) // Garante que cada save termine antes do próximo começar
```

### merge vs concat vs combineLatest vs zip vs forkJoin

#### `merge`
- Combina múltiplos Observables em um
- Emite valores assim que chegam de qualquer fonte
- Não mantém ordem ou relacionamento entre streams
```typescript
// Exemplo: Combinando cliques de diferentes botões
merge(
  fromEvent(button1, 'click'),
  fromEvent(button2, 'click')
) // Emite quando qualquer botão for clicado
```

#### `concat`
- Concatena Observables em sequência
- Espera um completar antes de começar o próximo
- Mantém ordem estrita
```typescript
// Exemplo: Executar animações em sequência
concat(
  fadeOut(element),
  delay(1000),
  fadeIn(element)
) // Garante que fadeOut complete antes do fadeIn começar
```

#### `combineLatest`
- Emite array com valores mais recentes quando qualquer Observable emite
- Precisa de pelo menos um valor de cada Observable
- Use quando: Precisa reagir a mudanças em múltiplos streams
```typescript
// Exemplo: Filtros combinados
combineLatest([
  priceFilter$,
  categoryFilter$,
  searchTerm$
]).pipe(
  map(([price, category, term]) => ({price, category, term}))
) // Emite objeto com estado atual de todos filtros
```

#### `zip`
- Combina valores correspondentes por índice
- Espera valor de todos Observables antes de emitir
- Mantém sincronização estrita entre streams
```typescript
// Exemplo: Combinando dados relacionados
zip(
  userIds$,
  userNames$
).pipe(
  map(([id, name]) => ({id, name}))
) // Combina IDs com nomes na ordem correta
```

#### `forkJoin`
- Espera todos Observables completarem
- Emite último valor de cada Observable
- Use quando: Precisa esperar múltiplas requisições completarem
```typescript
// Exemplo: Carregar dados iniciais
forkJoin({
  user: getUserProfile(),
  preferences: getUserPreferences(),
  settings: getUserSettings()
}).pipe(
  map(({user, preferences, settings}) => ({
    ...user,
    ...preferences,
    ...settings
  }))
) // Emite apenas quando todas requisições completarem
```

### debounceTime vs throttleTime vs sampleTime vs auditTime

#### `debounceTime`
- Espera um período de silêncio antes de emitir
- Cancela emissões durante o período de espera
- Use quando: Precisa esperar o usuário "parar" de agir
```typescript
// Exemplo: Campo de busca com autocomplete
searchInput$.pipe(
  debounceTime(300),
  switchMap(term => searchApi(term))
) // Espera usuário parar de digitar
```

#### `throttleTime`
- Limita emissões a uma por período
- Emite primeiro valor e ignora outros durante o período
- Use quando: Precisa limitar frequência de ações
```typescript
// Exemplo: Limitando eventos de scroll
fromEvent(window, 'scroll').pipe(
  throttleTime(200)
) // Processa no máximo 5 eventos por segundo
```

#### `sampleTime`
- Emite valor mais recente em intervalos regulares
- Pode pular valores entre intervalos
- Use quando: Precisa amostragem periódica
```typescript
// Exemplo: Monitoramento de progresso
progress$.pipe(
  sampleTime(1000)
) // Atualiza progresso a cada segundo
```

#### `auditTime`
- Similar ao throttleTime, mas emite último valor do período
- Espera período completo antes de emitir
- Use quando: Precisa último valor de um período
```typescript
// Exemplo: Atualização de posição
mousemove$.pipe(
  auditTime(1000)
) // Emite última posição a cada segundo
```

## 💡 Dicas de Uso

1. Use `switchMap` para requests HTTP em campos de busca
2. Use `debounceTime` para controlar input do usuário
3. Use `catchError` para fallbacks em caso de erro
4. Use `retry` para tentativas automáticas em caso de falha
5. Use `tap` para debugging sem afetar o stream

## 🚀 Exemplo Prático

```typescript
fromEvent(searchInput, 'input').pipe(
  map(event => event.target.value),
  debounceTime(300),
  distinctUntilChanged(),
  switchMap(term => searchApi(term)),
  catchError(err => of([]))
).subscribe(results => {
  displayResults(results);
});
```

Este exemplo mostra um campo de busca com:
- Debounce para evitar muitas requisições
- Verificação de valor diferente
- Cancelamento de requests anteriores
- Tratamento de erro
