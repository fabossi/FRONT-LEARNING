# 🛠️ Tipos Utilitários do TypeScript

<div align="center">

![TypeScript Logo](https://img.shields.io/badge/TypeScript-007ACC?style=for-the-badge&logo=typescript&logoColor=white)

</div>

## 📚 Sumário
- [Sobre](#-sobre)
- [Tipos Utilitários](#-tipos-utilitários)
  - [Partial<T>](#-partialt)
  - [Required<T>](#-requiredt)
  - [Readonly<T>](#-readonlyt)
  - [Record<K,V>](#-recordkv)
  - [Pick<T,K>](#-picktk)
  - [Omit<T,K>](#-omittk)
  - [Exclude<T,U>](#-excludetu)
  - [Extract<T,U>](#-extracttu)
  - [NonNullable<T>](#-nonnullablet)
  - [ReturnType<T>](#-returntypet)
  - [InstanceType<T>](#-instancetypet)
  - [ThisType<T>](#-thistypet)
- [Boas Práticas](#-boas-práticas)
- [Contribuição](#-contribuição)

## 🎯 Sobre

Os tipos utilitários do TypeScript são ferramentas poderosas que nos permitem transformar e manipular tipos existentes para criar novos tipos. Este guia fornece uma visão detalhada de cada tipo utilitário, incluindo exemplos práticos e casos de uso.

## 🔧 Tipos Utilitários

### 🔄 Partial<T>

**O que é:**
- Torna todas as propriedades de um tipo opcional.

**Quando Usar:**
- Ao criar funções de atualização parcial de objetos
- Em formulários onde nem todos os campos precisam ser preenchidos
- Em estados iniciais de componentes

```typescript
interface Usuario {
  nome: string;
  idade: number;
  email: string;
}

// Todas as propriedades se tornam opcionais
type UsuarioParcial = Partial<Usuario>;

function atualizarUsuario(id: number, usuario: Partial<Usuario>) {
  // Atualiza apenas as propriedades fornecidas
}
```

### ⚡ Required<T>

**O que é:**
- Torna todas as propriedades de um tipo obrigatórias.

**Quando Usar:**
- Ao validar dados antes de operações críticas
- Em interfaces de configuração que precisam estar completas
- Em modelos de dados para persistência

```typescript
interface ConfiguracaoOpcional {
  tema?: string;
  idioma?: string;
  notificacoes?: boolean;
}

// Todas as propriedades se tornam obrigatórias
type ConfiguracaoCompleta = Required<ConfiguracaoOpcional>;
```

### 🔒 Readonly<T>

**O que é:**
- Torna todas as propriedades de um tipo somente leitura.

**Quando Usar:**
- Em dados que não devem ser modificados após a criação
- Em configurações imutáveis
- Em props de componentes React

```typescript
interface Produto {
  id: number;
  nome: string;
  preco: number;
}

// Todas as propriedades se tornam readonly
type ProdutoImutavel = Readonly<Produto>;
```

### 📝 Record<K,V>

**O que é:**
- Cria um tipo com um conjunto de propriedades de tipo K com valores de tipo V.

**Quando Usar:**
- Para criar mapas/dicionários
- Para objetos com chaves dinâmicas
- Para coleções de dados indexados

```typescript
// Mapa de IDs para Usuários
type MapaUsuarios = Record<string, Usuario>;

// Dicionário de traduções
type Traducoes = Record<'pt' | 'en' | 'es', string>;
```

### ✂️ Pick<T,K>

**O que é:**
- Cria um tipo selecionando um conjunto de propriedades K de T.

**Quando Usar:**
- Para criar DTOs
- Para criar versões simplificadas de tipos
- Em APIs que precisam de apenas algumas propriedades

```typescript
interface Usuario {
  id: number;
  nome: string;
  email: string;
  senha: string;
  dataCriacao: Date;
}

// Apenas dados públicos
type UsuarioPublico = Pick<Usuario, 'id' | 'nome'>;
```

### 🗑️ Omit<T,K>

**O que é:**
- Cria um tipo excluindo um conjunto de propriedades K de T.

**Quando Usar:**
- Para remover propriedades sensíveis
- Para criar interfaces derivadas
- Em respostas de API sem dados internos

```typescript
interface Post {
  id: number;
  titulo: string;
  conteudo: string;
  autorId: number;
  draft: boolean;
}

// Remove propriedades internas
type PostPublicado = Omit<Post, 'draft' | 'autorId'>;
```

### ❌ Exclude<T,U>

**O que é:**
- Remove tipos de T que são atribuíveis a U.

**Quando Usar:**
- Para filtrar tipos de união
- Para remover casos específicos
- Em sistemas de tipo customizados

```typescript
type Cores = 'vermelho' | 'azul' | 'verde' | 'amarelo';
type CoresQuentes = 'vermelho' | 'amarelo';

// Remove cores quentes
type CoresFrias = Exclude<Cores, CoresQuentes>;
```

### 🎯 Extract<T,U>

**O que é:**
- Extrai tipos de T que são atribuíveis a U.

**Quando Usar:**
- Para filtrar tipos específicos
- Para criar subconjuntos de tipos
- Em validações de tipo

```typescript
type Dados = string | number | undefined | null;

// Extrai apenas tipos não-nulos
type DadosValidos = Extract<Dados, string | number>;
```

### ⚠️ NonNullable<T>

**O que é:**
- Remove null e undefined de um tipo.

**Quando Usar:**
- Em validações de dados
- Para garantir valores não-nulos
- Em operações que requerem dados válidos

```typescript
type Resposta = string | null | undefined;

// Remove null e undefined
type RespostaValida = NonNullable<Resposta>;
```

### 📤 ReturnType<T>

**O que é:**
- Extrai o tipo de retorno de um tipo função.

**Quando Usar:**
- Para trabalhar com tipos de retorno de funções
- Em utilitários de tipo genéricos
- Em decorators

```typescript
function getUsuario() {
  return { id: 1, nome: 'João' };
}

// Extrai o tipo de retorno
type Usuario = ReturnType<typeof getUsuario>;
```

### 🏭 InstanceType<T>

**O que é:**
- Constrói um tipo consistindo do tipo de instância de uma função construtora.

**Quando Usar:**
- Com classes e construtores
- Em padrões factory
- Em sistemas de injeção de dependência

```typescript
class MinhaClasse {
  constructor(public nome: string) {}
}

// Tipo da instância
type InstanciaMinhaClasse = InstanceType<typeof MinhaClasse>;
```

### 👉 ThisType<T>

**O que é:**
- Marca um contexto de this em um objeto.

**Quando Usar:**
- Em mixins
- Em objetos com métodos
- Em padrões de design que manipulam o contexto

```typescript
interface Estado {
  nome: string;
  idade: number;
}

interface Acoes {
  atualizarNome(nome: string): void;
  atualizarIdade(idade: number): void;
}

type ObjetoComEstado = {
  acoes: Acoes;
} & ThisType<Estado & Acoes>;
```

## 📚 Boas Práticas

1. **Nomeação Clara:**
   - Use nomes descritivos para tipos derivados
   - Mantenha um padrão de nomenclatura consistente

2. **Composição de Tipos:**
   - Combine tipos utilitários para casos mais complexos
   - Mantenha a hierarquia de tipos organizada

3. **Documentação:**
   - Documente o propósito de cada tipo customizado
   - Inclua exemplos de uso em comentários

4. **Performance:**
   - Evite criar tipos muito complexos
   - Use tipos utilitários apenas quando necessário

## 🤝 Contribuição

Sinta-se à vontade para contribuir com este guia! 

1. Faça um Fork do projeto
2. Crie uma Branch para sua Feature
3. Adicione suas mudanças
4. Faça um Commit
5. Faça um Push
6. Abra um Pull Request

---

<div align="center">

⭐ Se este guia foi útil, considere dar uma estrela no repositório!

</div>
