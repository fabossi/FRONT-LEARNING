# 🚀 Guia Definitivo de Métodos e Funções em JavaScript

## 📘 Introdução

Este guia abrangente explora todos os métodos e funções em JavaScript puro, organizados por tipo de dado e funcionalidade.

## 🔢 Métodos de Array

### Métodos de Transformação
```javascript
// map(): Transforma cada elemento
const dobrados = [1, 2, 3].map(x => x * 2); 

// filter(): Filtra elementos
const pares = [1, 2, 3, 4].filter(x => x % 2 === 0);

// reduce(): Reduz array a um valor
const soma = [1, 2, 3].reduce((acc, curr) => acc + curr, 0);
```

### Métodos de Busca e Verificação
```javascript
// find(): Encontra primeiro elemento que satisfaz condição
const primeiro = [1, 2, 3].find(x => x > 1);

// findIndex(): Retorna índice do primeiro elemento
const indice = [1, 2, 3].findIndex(x => x === 2);

// some(): Verifica se pelo menos um elemento satisfaz condição
const temPar = [1, 2, 3].some(x => x % 2 === 0);

// every(): Verifica se todos elementos satisfazem condição
const todosPares = [2, 4, 6].every(x => x % 2 === 0);
```

### Métodos de Manipulação
```javascript
// push(): Adiciona elemento ao final
let arr = [1, 2];
arr.push(3); // [1, 2, 3]

// pop(): Remove último elemento
arr.pop(); // retorna 3, arr é [1, 2]

// shift(): Remove primeiro elemento
arr.shift(); // retorna 1, arr é [2]

// unshift(): Adiciona elemento no início
arr.unshift(0); // arr é [0, 2]

// splice(): Adiciona/remove elementos em posição específica
arr.splice(1, 0, 1); // insere 1 na posição 1
arr.splice(1, 1); // remove 1 elemento na posição 1

// slice(): Extrai parte do array
const parte = [1, 2, 3, 4].slice(1, 3); // [2, 3]
```

### Métodos de Ordenação
```javascript
// sort(): Ordena array
const numeros = [3, 1, 4].sort((a, b) => a - b);
const alfabetica = ['b', 'a', 'c'].sort();

// reverse(): Inverte ordem
const invertido = [1, 2, 3].reverse();
```

### Métodos de Junção
```javascript
// join(): Transforma array em string
const texto = ['a', 'b', 'c'].join('-'); // "a-b-c"

// concat(): Concatena arrays
const combinado = [1, 2].concat([3, 4]);
```

## 🧩 Métodos de String

### Métodos de Busca
```javascript
// indexOf(): Encontra posição de substring
"hello".indexOf('e'); // 1

// includes(): Verifica existência
"hello".includes('ell'); // true

// startsWith(), endsWith(): Verifica início/fim
"hello".startsWith('he'); // true
"hello".endsWith('lo'); // true
```

### Métodos de Transformação
```javascript
// toLowerCase(), toUpperCase(): Conversão de case
"Hello".toLowerCase(); // "hello"
"hello".toUpperCase(); // "HELLO"

// trim(): Remove espaços
"  hello  ".trim(); // "hello"

// replace(): Substitui substring
"hello".replace('l', 'x'); // "hexlo"

// split(): Divide string
"a,b,c".split(','); // ["a", "b", "c"]
```

### Métodos de Extração
```javascript
// substring(): Extrai parte da string
"hello".substring(1, 3); // "el"

// slice(): Similar ao substring
"hello".slice(1, 3); // "el"
```

## 🔠 Métodos de Objeto

### Métodos Estáticos
```javascript
// Object.keys(): Chaves do objeto
const chaves = Object.keys({a: 1, b: 2}); // ["a", "b"]

// Object.values(): Valores do objeto
const valores = Object.values({a: 1, b: 2}); // [1, 2]

// Object.entries(): Pares chave-valor
const entradas = Object.entries({a: 1, b: 2}); 
// [["a", 1], ["b", 2]]

// Object.assign(): Copia propriedades
const merged = Object.assign({}, {a: 1}, {b: 2});

// Object.create(): Cria objeto com protótipo
const proto = {greet() { console.log('Olá'); }};
const obj = Object.create(proto);
```

## 🧮 Métodos Matemáticos

```javascript
// Math.round(): Arredonda
Math.round(4.7); // 5

// Math.floor(): Arredonda para baixo
Math.floor(4.7); // 4

// Math.ceil(): Arredonda para cima
Math.ceil(4.3); // 5

// Math.max(), Math.min(): Valor máximo/mínimo
Math.max(1, 2, 3); // 3
Math.min(1, 2, 3); // 1

// Math.random(): Gera número aleatório
Math.random(); // 0 a 1
```

## 🕰️ Métodos de Data

```javascript
// Criando datas
const data = new Date();

// Métodos de obtenção
data.getFullYear(); // Ano
data.getMonth(); // Mês (0-11)
data.getDate(); // Dia do mês
data.getDay(); // Dia da semana
data.getHours(); // Horas
data.getMinutes(); // Minutos
```

## 📍 Funções Globais

```javascript
// parseInt(): Converte para inteiro
parseInt("123"); // 123

// parseFloat(): Converte para float
parseFloat("123.45"); // 123.45

// isNaN(): Verifica se não é número
isNaN(NaN); // true

// isFinite(): Verifica se é número finito
isFinite(123); // true
```

## 🌐 Métodos de Conversão

```javascript
// toString(): Converte para string
(123).toString(); // "123"

// Number(): Converte para número
Number("123"); // 123

// String(): Converte para string
String(123); // "123"

// Boolean(): Converte para booleano
Boolean(0); // false
```

## 💡 Dicas Finais

- Use métodos nativos para código mais limpo e eficiente
- Pratique combinando diferentes métodos
- Explore a documentação oficial para detalhes completos

**🚀 Continue Aprendendo!**
