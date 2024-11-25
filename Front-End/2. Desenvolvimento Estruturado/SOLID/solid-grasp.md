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

### 2️⃣ Open/Closed Principle (OCP) 🔓
**Conceito:** Classes devem ser abertas para extensão, mas fechadas para modificação.

**Exemplo Prático:**
```typescript
// ❌ Violando OCP: Modificando a classe para cada novo tipo de desconto
class Desconto {
  calcular(valor: number, tipoDesconto: string): number {
    if (tipoDesconto === 'padrao') {
      return valor * 0.9; // 10% de desconto
    } else if (tipoDesconto === 'blackFriday') {
      return valor * 0.5; // 50% de desconto
    }
    return valor;
  }
}

// ✅ Respeitando OCP: Usando polimorfismo para estender comportamentos
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

class Carrinho {
  aplicarDesconto(valor: number, desconto: Desconto) {
    return desconto.calcular(valor);
  }
}
```

### 3️⃣ Liskov Substitution Principle (LSP) 🔄
**Conceito:** Objetos de uma classe pai devem ser substituíveis por objetos de suas classes filhas sem quebrar a aplicação.

**Exemplo Prático:**
```typescript
// ❌ Violando LSP: Subclasse altera o comportamento de forma inesperada
class Retangulo {
  protected largura: number;
  protected altura: number;

  definirDimensoes(largura: number, altura: number) {
    this.largura = largura;
    this.altura = altura;
  }

  calcularArea(): number {
    return this.largura * this.altura;
  }
}

class Quadrado extends Retangulo {
  definirDimensoes(lado: number) {
    this.largura = lado;
    this.altura = lado;
  }

  // Comportamento diferente do pai, potencialmente quebrando código
}

// ✅ Respeitando LSP: Usando composição e interfaces
interface Forma {
  calcularArea(): number;
}

class Retangulo implements Forma {
  constructor(
    private largura: number, 
    private altura: number
  ) {}

  calcularArea(): number {
    return this.largura * this.altura;
  }
}

class Quadrado implements Forma {
  constructor(private lado: number) {}

  calcularArea(): number {
    return this.lado * this.lado;
  }
}
```

### 4️⃣ Interface Segregation Principle (ISP) 🧩
**Conceito:** Clientes não devem ser forçados a depender de interfaces que não usam.

**Exemplo Prático:**
```typescript
// ❌ Violando ISP: Interface grande e com responsabilidades múltiplas
interface Trabalhador {
  trabalhar(): void;
  comer(): void;
  dormir(): void;
  programar(): void;
}

class Humano implements Trabalhador {
  trabalhar() { /* Trabalhar */ }
  comer() { /* Comer */ }
  dormir() { /* Dormir */ }
  programar() { /* Programar */ }
}

class Robo implements Trabalhador {
  trabalhar() { /* Trabalhar */ }
  comer() { throw new Error("Robôs não comem!"); }
  dormir() { throw new Error("Robôs não dormem!"); }
  programar() { /* Programar */ }
}

// ✅ Respeitando ISP: Interfaces menores e específicas
interface Trabalhavel {
  trabalhar(): void;
}

interface Programavel {
  programar(): void;
}

interface Alimentavel {
  comer(): void;
}

interface Dormivel {
  dormir(): void;
}

class Humano implements Trabalhavel, Programavel, Alimentavel, Dormivel {
  trabalhar() { /* Trabalhar */ }
  programar() { /* Programar */ }
  comer() { /* Comer */ }
  dormir() { /* Dormir */ }
}

class Robo implements Trabalhavel, Programavel {
  trabalhar() { /* Trabalhar */ }
  programar() { /* Programar */ }
}
```

### 5️⃣ Dependency Inversion Principle (DIP) 🔀
**Conceito:** Dependa de abstrações, não de implementações concretas.

**Exemplo Prático:**
```typescript
// ❌ Violando DIP: Dependência direta de implementações concretas
class ServicoNotificacao {
  private emailService = new ServicoEmail();
  private smsService = new ServicoSMS();

  notificarPorEmail(mensagem: string) {
    this.emailService.enviar(mensagem);
  }

  notificarPorSMS(mensagem: string) {
    this.smsService.enviar(mensagem);
  }
}

class ServicoEmail {
  enviar(mensagem: string) { /* Enviar email */ }
}

class ServicoSMS {
  enviar(mensagem: string) { /* Enviar SMS */ }
}

// ✅ Respeitando DIP: Dependendo de abstrações
interface ServicoNotificacao {
  enviar(mensagem: string): void;
}

class ServicoEmail implements ServicoNotificacao {
  enviar(mensagem: string) { /* Enviar email */ }
}

class ServicoSMS implements ServicoNotificacao {
  enviar(mensagem: string) { /* Enviar SMS */ }
}

class Notificador {
  constructor(private servicoNotificacao: ServicoNotificacao) {}

  notificar(mensagem: string) {
    this.servicoNotificacao.enviar(mensagem);
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
