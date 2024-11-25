# 🏗️ Princípios SOLID e GRASP: Guia Completo de Design de Software

## 📌 Princípios SOLID

### 🔍 O que são os Princípios SOLID?

SOLID é um acrônimo criado por Robert C. Martin (Uncle Bob) que representa cinco princípios fundamentais da Programação Orientada a Objetos (POO). Esses princípios visam tornar os sistemas de software mais:

- 🧩 Compreensíveis
- 🔬 Flexíveis
- 🛠️ Fáceis de manter

### 🚦 Os Cinco Princípios

| Letra | Princípio | Descrição Resumida |
|-------|-----------|---------------------|
| **S** | Single Responsibility Principle (SRP) | 📝 Uma classe deve ter apenas uma razão para mudar |
| **O** | Open/Closed Principle (OCP) | 🔓 Aberto para extensão, fechado para modificação |
| **L** | Liskov Substitution Principle (LSP) | 🔄 Objetos de uma superclasse devem ser substituíveis por objetos de suas subclasses |
| **I** | Interface Segregation Principle (ISP) | 🧩 Muitas interfaces específicas são melhores que uma interface geral |
| **D** | Dependency Inversion Principle (DIP) | 🔀 Dependa de abstrações, não de implementações concretas |

### 🔬 Detalhamento de Cada Princípio

#### 1️⃣ Single Responsibility Principle (SRP) 📝
**Conceito:** Cada classe deve ter uma única responsabilidade bem definida.

**Exemplo Prático:**
```typescript
// ❌ Classe com múltiplas responsabilidades
class Usuario {
  salvar() { /* Salvar no banco */ }
  validarEmail() { /* Validar email */ }
  enviarNotificacao() { /* Enviar notificação */ }
}

// ✅ Classes com responsabilidades únicas
class UsuarioRepositorio {
  salvar(usuario) { /* Salvar no banco */ }
}

class ValidadorEmail {
  validar(email) { /* Validar email */ }
}

class ServicoNotificacao {
  enviar(mensagem) { /* Enviar notificação */ }
}
```

#### 2️⃣ Open/Closed Principle (OCP) 🔓
**Conceito:** Classes devem ser abertas para extensão, mas fechadas para modificação.

**Exemplo Prático:**
```typescript
interface Desconto {
  calcular(valor: number): number;
}

class DescontoPadrao implements Desconto {
  calcular(valor: number) {
    return valor * 0.9; // 10% de desconto
  }
}

class DescontoBlackFriday implements Desconto {
  calcular(valor: number) {
    return valor * 0.5; // 50% de desconto
  }
}
```

#### 3️⃣ Liskov Substitution Principle (LSP) 🔄
**Conceito:** Objetos de uma classe pai devem ser substituíveis por objetos de suas classes filhas sem quebrar a aplicação.

**Exemplo Prático:**
```typescript
interface FormaGeometrica {
  calcularArea(): number;
}

class Retangulo implements FormaGeometrica {
  calcularArea() {
    return this.largura * this.altura;
  }
}

class Quadrado implements FormaGeometrica {
  calcularArea() {
    return this.lado * this.lado;
  }
}
```

#### 4️⃣ Interface Segregation Principle (ISP) 🧩
**Conceito:** Clientes não devem ser forçados a depender de interfaces que não usam.

**Exemplo Prático:**
```typescript
interface Trabalhador {
  trabalhar(): void;
}

interface Alimentavel {
  comer(): void;
}

class Humano implements Trabalhador, Alimentavel {
  trabalhar() { /* Trabalhar */ }
  comer() { /* Comer */ }
}

class Robo implements Trabalhador {
  trabalhar() { /* Trabalhar */ }
}
```

#### 5️⃣ Dependency Inversion Principle (DIP) 🔀
**Conceito:** Dependa de abstrações, não de implementações concretas.

**Exemplo Prático:**
```typescript
interface Repositorio {
  salvar(dados: any): void;
}

class RepositorioMySQL implements Repositorio {
  salvar(dados: any) { /* Salvar no MySQL */ }
}

class RepositorioMongoDB implements Repositorio {
  salvar(dados: any) { /* Salvar no MongoDB */ }
}

class ServicoUsuario {
  constructor(private repositorio: Repositorio) {}
  
  salvarUsuario(usuario: any) {
    this.repositorio.salvar(usuario);
  }
}
```

## 🧠 Princípios GRASP

### 📝 Visão Geral
GRASP (General Responsibility Assignment Software Patterns) são padrões para atribuição de responsabilidades em projetos orientados a objetos.

### 🎯 Princípios Fundamentais

1. **Especialista na Informação** 🕵️
   - Atribua responsabilidades a classes que têm as informações necessárias

2. **Criador** 🏗️
   - Defina quem deve criar novas instâncias de classes

3. **Baixo Acoplamento** 🔗
   - Minimize dependências entre componentes

4. **Alta Coesão** 🎯
   - Mantenha responsabilidades fortemente relacionadas

5. **Controlador** 🎛️
   - Gerencie eventos do sistema em classes específicas

6. **Polimorfismo** 🔄
   - Use polimorfismo para lidar com variações de tipo

7. **Fabricação Pura** 🧩
   - Crie classes artificiais quando necessário

8. **Indireção** ↔️
   - Use intermediários para reduzir acoplamento

9. **Proteção contra Variações** 🛡️
   - Encapsule o que varia

## 🚀 Benefícios

- ✅ Código mais manutenível
- ✅ Melhor organização das responsabilidades
- ✅ Redução de acoplamento
- ✅ Aumento da coesão
- ✅ Facilidade de testes
- ✅ Maior reusabilidade

## 📚 Referências
- Livro: "Princípios, Padrões e Práticas de Arquitetura de Software" - Robert C. Martin
- Clean Code - Robert C. Martin

## 🤝 Contribuições
Contribuições são bem-vindas! Sinta-se à vontade para abrir issues ou pull requests.

## 📄 Licença
Este guia é distribuído sob a licença MIT.
