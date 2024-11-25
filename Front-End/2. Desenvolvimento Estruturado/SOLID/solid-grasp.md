Módulo 2: Princípios SOLID e Padrões de Projeto

## 📌 SOLID

SOLID é um acrônimo criado por Robert C. Martin (Uncle Bob) que representa cinco princípios fundamentais da programação orientada a objetos.

Os princípios SOLID são um conjunto de boas práticas de design de software que tornam os sistemas mais compreensíveis, flexíveis e fáceis de manter. A sigla SOLID refere-se a cinco princípios:

- **S**: Single Responsibility Principle (SRP)
- **O**: Open/Closed Principle (OCP)
- **L**: Liskov Substitution Principle (LSP)
- **I**: Interface Segregation Principle (ISP)
- **D**: Dependency Inversion Principle (DIP)

## 1. Single Responsibility Principle (SRP)

Cada classe deve ter uma única responsabilidade. Isso significa que uma classe deve ter apenas uma razão para mudar.

**Exemplo:**

```typescript
// Violando o SRP: Uma classe que faz muitas coisas.
class User {
  constructor(public name: string, public email: string) {}

  validateEmail() {
    // Lógica de validação de email
  }

  saveUser() {
    // Lógica de salvar o usuário
  }

  sendEmail() {
    // Lógica para enviar email
  }
}

// Aplicando o SRP: Cada classe tem uma responsabilidade única.
class User {
  constructor(public name: string, public email: string) {}
}

class EmailValidator {
  validate(email: string) {
    // Lógica de validação de email
  }
}

class UserRepository {
  save(user: User) {
    // Lógica para salvar o usuário
  }
}

class EmailService {
  send(email: string) {
    // Lógica para enviar email
  }
}
```

## 2. Open/Closed Principle (OCP)

Uma classe deve estar aberta para extensão, mas fechada para modificação. Isso significa que você deve ser capaz de adicionar novos comportamentos sem alterar o código existente.

**Exemplo:**

```typescript
// Violando o OCP: Modificando a classe para cada novo tipo de desconto.
class Discount {
  calculate(price: number, discountType: string): number {
    if (discountType === 'seasonal') {
      return price * 0.9;
    } else if (discountType === 'clearance') {
      return price * 0.5;
    }
    return price;
  }
}

// Aplicando o OCP: Adicionando novos descontos sem modificar a classe base.
interface DiscountStrategy {
  calculate(price: number): number;
}

class SeasonalDiscount implements DiscountStrategy {
  calculate(price: number): number {
    return price * 0.9;
  }
}

class ClearanceDiscount implements DiscountStrategy {
  calculate(price: number): number {
    return price * 0.5;
  }
}

class Discount {
  apply(price: number, discount: DiscountStrategy): number {
    return discount.calculate(price);
  }
}
```

// Violando LSP
class Desconto {
    calcular(valorProduto: number): number {
        return valorProduto * 0.9; // 10% de desconto
    }
}

class DescontoBlackFriday extends Desconto {
    calcular(valorProduto: number): number {
        if (valorProduto > 1000) {
            throw new Error("Desconto não permitido para valores acima de 1000"); // Viola LSP
        }
        return valorProduto * 0.5; // 50% de desconto
    }
}

// Uso problemático:
const calcularPrecoFinal = (produto: Desconto, valor: number) => {
    return produto.calcular(valor); // Pode quebrar com DescontoBlackFriday
}


// Aplicando LSP corretamente
interface Desconto {
    calcular(valorProduto: number): number;
}

class DescontoComum implements Desconto {
    calcular(valorProduto: number): number {
        return valorProduto * 0.9; // 10% de desconto
    }
}

class DescontoBlackFriday implements Desconto {
    calcular(valorProduto: number): number {
        const percentualDesconto = valorProduto > 1000 ? 0.2 : 0.5;  // 20% ou 50%
        return valorProduto * (1 - percentualDesconto);
    }
}

// Uso correto:
const calcularPrecoFinal = (desconto: Desconto, valor: number) => {
    return desconto.calcular(valor); // Funciona com qualquer tipo de desconto
}

## 4. Interface Segregation Principle (ISP)

Os clientes não devem ser forçados a depender de interfaces que não utilizam. Em vez de usar interfaces grandes, crie várias interfaces pequenas e específicas.

**Exemplo:**

```typescript
// Violando o ISP: Forçando classes a implementar métodos que não precisam.
interface Worker {
  work(): void;
  eat(): void;
}

class HumanWorker implements Worker {
  work() {
    console.log("Working...");
  }

  eat() {
    console.log("Eating...");
  }
}

class RobotWorker implements Worker {
  work() {
    console.log("Working...");
  }

  eat() {
    throw new Error("Robots don't eat!");
  }
}

// Aplicando o ISP: Interfaces segregadas com responsabilidades específicas.
interface Workable {
  work(): void;
}

interface Eatable {
  eat(): void;
}

class HumanWorker implements Workable, Eatable {
  work() {
    console.log("Working...");
  }

  eat() {
    console.log("Eating...");
  }
}

class RobotWorker implements Workable {
  work() {
    console.log("Working...");
  }
}
```

### 5. D - Dependency Inversion Principle (Princípio da Inversão de Dependência)

Princípio da Inversão de Dependência — Dependa de abstrações e não de implementações.

De acordo com Uncle Bob, esse princípio pode ser definido da seguinte forma:

Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender da abstração.

```javascript
// ❌ RUIM: Dependência direta de implementações
class MySQLDatabase {
    save(data) {
        console.log('Salvando no MySQL:', data);
    }
}

class UserService {
    constructor() {
        this.database = new MySQLDatabase(); // Dependência direta!
    }

    saveUser(user) {
        this.database.save(user);
    }
}

// ✅ BOM: Usando injeção de dependência
class Database {
    save(data) {
        throw new Error('Método save deve ser implementado');
    }
}

class MySQLDatabase extends Database {
    save(data) {
        console.log('Salvando no MySQL:', data);
    }
}

class MongoDatabase extends Database {
    save(data) {
        console.log('Salvando no MongoDB:', data);
    }
}

class UserService {
    constructor(database) {
        this.database = database;
    }

    saveUser(user) {
        this.database.save(user);
    }
}

// Uso:
const mysqlService = new UserService(new MySQLDatabase());
const mongoService = new UserService(new MongoDatabase());

mysqlService.saveUser({name: 'João'});
mongoService.saveUser({name: 'Maria'});
```
---

# Princípios GRASP (General Responsibility Assignment Software Patterns)

## 📝 Descrição
GRASP são padrões fundamentais para atribuição de responsabilidades em projetos orientados a objetos. Estes princípios ajudam desenvolvedores a criar software mais manutenível e com melhor design orientado a objetos.

## 🎯 Princípios Fundamentais

### 1. Especialista na Informação (Information Expert)
#### Conceito
- Atribua uma responsabilidade à classe que tem as informações necessárias para realizá-la
#### Exemplo Prático
```java
class Pedido {
    private List<ItemPedido> itens;
    
    public double calcularTotal() {  // Responsabilidade adequada pois Pedido tem os dados
        return itens.stream()
                   .mapToDouble(ItemPedido::getSubtotal)
                   .sum();
    }
}
```

### 2. Criador (Creator)
#### Conceito
- Define quem deve ser responsável por criar uma nova instância de uma classe
#### Exemplo Prático
```java
class Pedido {
    public ItemPedido criarItem(Produto produto, int quantidade) {
        return new ItemPedido(produto, quantidade, this);
    }
}
```

### 3. Baixo Acoplamento (Low Coupling)
#### Conceito
- Mantenha o acoplamento entre classes baixo
- Reduza dependências entre componentes
#### Boas Práticas
- Use interfaces ao invés de classes concretas
- Evite dependências desnecessárias
- Prefira composição à herança

### 4. Alta Coesão (High Cohesion)
#### Conceito
- Classes devem ter responsabilidades fortemente relacionadas
- Cada classe deve fazer uma única coisa bem feita
#### Exemplo de Má Coesão
```java
class Usuario {
    public void salvarNoBanco() { }
    public void enviarEmail() { }
    public void gerarRelatorio() { }
    public void processarPagamento() { }  // Muitas responsabilidades diferentes!
}
```

### 5. Controlador (Controller)
#### Conceito
- Atribua a responsabilidade de gerenciar eventos do sistema a uma classe específica
#### Exemplo
```java
class PedidoController {
    public void criarPedido(DadosPedido dados) { }
    public void cancelarPedido(long id) { }
    public void confirmarPagamento(long id) { }
}
```

### 6. Polimorfismo (Polymorphism)
#### Conceito
- Use polimorfismo para lidar com alternativas baseadas em tipo
#### Exemplo
```java
interface FormaDePagamento {
    void processar(double valor);
}

class PagamentoCartao implements FormaDePagamento {
    public void processar(double valor) {
        // Processamento específico para cartão
    }
}

class PagamentoPix implements FormaDePagamento {
    public void processar(double valor) {
        // Processamento específico para PIX
    }
}
```

### 7. Fabricação Pura (Pure Fabrication)
#### Conceito
- Crie uma classe artificial quando necessário
- Útil quando não há uma classe natural para certas responsabilidades
#### Exemplo
```java
class GerenciadorDeArquivos {
    public void salvar(String conteudo, String caminho) { }
    public String ler(String caminho) { }
}
```

### 8. Indireção (Indirection)
#### Conceito
- Reduza acoplamento usando intermediários
#### Exemplo
```java
interface ServicoExterno { }

class Adaptador implements ServicoExterno {
    private ServicoTerceiro servico;
    // Adapta a interface do ServicoTerceiro
}
```

### 9. Proteção contra Variações (Protected Variations)
#### Conceito
- Encapsule o que varia
- Crie interfaces estáveis
#### Exemplo
```java
interface Notificador {
    void enviar(String mensagem);
}

class NotificadorEmail implements Notificador { }
class NotificadorSMS implements Notificador { }
class NotificadorWhatsApp implements Notificador { }
```

## 🚀 Benefícios
- Código mais manutenível
- Melhor organização das responsabilidades
- Redução de acoplamento
- Aumento da coesão
- Facilidade de testes
- Maior reusabilidade
