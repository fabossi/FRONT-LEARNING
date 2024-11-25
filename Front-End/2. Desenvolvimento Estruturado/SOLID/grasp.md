# 🏗️ Princípios GRASP: Guia Prático de Design de Software com TypeScript

## 📌 Introdução aos Princípios GRASP

GRASP (General Responsibility Assignment Software Patterns) são padrões que nos ajudam a atribuir responsabilidades de forma inteligente em projetos orientados a objetos, promovendo um design de software mais flexível, manutenível e compreensível.

## 🎯 Princípios Fundamentais com Exemplos

### 1. 🕵️ Especialista na Informação (Information Expert)

#### Código Ruim (Violação do Princípio)
```typescript
class Order {
  items: any[];
  
  calculateTotal(taxRate: number) {
    // A classe Order não deveria calcular o total, 
    // pois não possui todas as informações necessárias
    let total = 0;
    for (let item of this.items) {
      total += item.price;
    }
    return total * (1 + taxRate);
  }
}
```

#### Código Correto
```typescript
class Item {
  constructor(
    public name: string, 
    public price: number
  ) {}

  calculateItemTotal(taxRate: number): number {
    return this.price * (1 + taxRate);
  }
}

class Order {
  constructor(public items: Item[]) {}

  calculateTotal(taxRate: number): number {
    return this.items.reduce((total, item) => 
      total + item.calculateItemTotal(taxRate), 0);
  }
}
```

### 2. 🏗️ Criador (Creator)

#### Código Ruim
```typescript
class User {
  static createOrder(user: User) {
    // Uma classe externa criando objetos de outra classe
    return new Order(user);
  }
}

class Order {
  constructor(public user: User) {}
}
```

#### Código Correto
```typescript
class User {
  createOrder(): Order {
    // A própria classe User cria sua instância de Order
    return new Order(this);
  }
}

class Order {
  constructor(public user: User) {}
}
```

### 3. 🔗 Baixo Acoplamento (Low Coupling)

#### Código Ruim (Alto Acoplamento)
```typescript
class PaymentProcessor {
  processPaypal(details: any) {
    // Método fortemente acoplado a detalhes específicos do Paypal
    const paypal = new PaypalGateway();
    paypal.sendRequest(details);
  }

  processStripe(details: any) {
    const stripe = new StripeGateway();
    stripe.sendRequest(details);
  }
}
```

#### Código Correto
```typescript
interface PaymentGateway {
  processPayment(amount: number): boolean;
}

class PaypalGateway implements PaymentGateway {
  processPayment(amount: number): boolean {
    // Implementação específica do Paypal
    return true;
  }
}

class StripeGateway implements PaymentGateway {
  processPayment(amount: number): boolean {
    // Implementação específica do Stripe
    return true;
  }
}

class PaymentProcessor {
  constructor(private gateway: PaymentGateway) {}

  processPayment(amount: number): boolean {
    return this.gateway.processPayment(amount);
  }
}
```

### 4. 🎯 Alta Coesão (High Cohesion)

#### Código Ruim (Baixa Coesão)
```typescript
class UserManager {
  // Mistura responsabilidades de autenticação, validação, log e notificação
  register(username: string, password: string) {
    // Validação
    if (username.length < 3) throw new Error('Invalid username');
    
    // Autenticação
    const user = new User(username, password);
    
    // Logging
    console.log(`User ${username} registered`);
    
    // Notificação
    this.sendWelcomeEmail(user);
  }
}
```

#### Código Correto
```typescript
class UserValidator {
  static validate(username: string): boolean {
    return username.length >= 3;
  }
}

class UserRegistration {
  register(username: string, password: string): User {
    if (!UserValidator.validate(username)) {
      throw new Error('Invalid username');
    }
    return new User(username, password);
  }
}

class UserLogger {
  static log(username: string) {
    console.log(`User ${username} registered`);
  }
}

class UserNotification {
  static sendWelcomeEmail(user: User) {
    // Lógica de envio de e-mail
  }
}
```

### 5. 🎛️ Controlador (Controller)

#### Código Ruim
```typescript
// Lógica de sistema espalhada em várias classes
class ProductView {
  onAddToCart() {
    // Lógica de negócio misturada com interface
    const product = this.selectedProduct;
    const cart = new Cart();
    cart.addItem(product);
  }
}
```

#### Código Correto
```typescript
class CartController {
  constructor(
    private cart: Cart, 
    private inventoryService: InventoryService
  ) {}

  addToCart(product: Product): void {
    if (this.inventoryService.checkAvailability(product)) {
      this.cart.addItem(product);
    }
  }
}

class ProductView {
  constructor(private cartController: CartController) {}

  onAddToCart(product: Product) {
    this.cartController.addToCart(product);
  }
}
```

### 6. 🔄 Polimorfismo (Polymorphism)

#### Código Ruim
```typescript
class ReportGenerator {
  generateReport(type: string, data: any) {
    if (type === 'PDF') {
      // Lógica específica para PDF
    } else if (type === 'CSV') {
      // Lógica específica para CSV
    }
  }
}
```

#### Código Correto
```typescript
interface ReportGenerator {
  generate(data: any): string;
}

class PDFReportGenerator implements ReportGenerator {
  generate(data: any): string {
    // Lógica específica para PDF
    return 'PDF Report';
  }
}

class CSVReportGenerator implements ReportGenerator {
  generate(data: any): string {
    // Lógica específica para CSV
    return 'CSV Report';
  }
}

class ReportService {
  constructor(private generator: ReportGenerator) {}

  createReport(data: any): string {
    return this.generator.generate(data);
  }
}
```

### 7. 🧩 Fabricação Pura (Pure Fabrication)

#### Código Ruim
```typescript
class Order {
  // Sobrecarregando a classe Order com responsabilidades que não são suas
  calculateTax(): number {
    // Lógica de cálculo de imposto
  }

  sendEmailConfirmation(): void {
    // Lógica de envio de e-mail
  }
}
```

#### Código Correto
```typescript
class TaxCalculator {
  static calculateTax(order: Order): number {
    // Classe artificial para cálculo de impostos
    return order.total * 0.1;
  }
}

class OrderNotification {
  static sendConfirmation(order: Order): void {
    // Classe artificial para envio de notificações
  }
}

class Order {
  // Mantém apenas responsabilidades essenciais
}
```

### 8. ↔️ Indireção (Indirection)

#### Código Ruim
```typescript
class ProductRepository {
  saveProduct(product: Product) {
    // Acoplamento direto com banco de dados
    const database = new Database();
    database.save(product);
  }
}
```

#### Código Correto
```typescript
interface DatabaseAdapter {
  save(data: any): void;
}

class MySQLAdapter implements DatabaseAdapter {
  save(data: any): void {
    // Lógica específica do MySQL
  }
}

class ProductRepository {
  constructor(private database: DatabaseAdapter) {}

  saveProduct(product: Product): void {
    this.database.save(product);
  }
}
```

### 9. 🛡️ Proteção contra Variações (Protected Variations)

#### Código Ruim
```typescript
class PaymentProcessor {
  processPayment(method: string, amount: number) {
    // Processamento de pagamento hardcoded
    if (method === 'credit') {
      // Lógica de cartão de crédito
    } else if (method === 'debit') {
      // Lógica de cartão de débito
    }
  }
}
```

#### Código Correto
```typescript
interface PaymentStrategy {
  process(amount: number): boolean;
}

class CreditCardPayment implements PaymentStrategy {
  process(amount: number): boolean {
    // Lógica específica de cartão de crédito
    return true;
  }
}

class DebitCardPayment implements PaymentStrategy {
  process(amount: number): boolean {
    // Lógica específica de cartão de débito
    return true;
  }
}

class PaymentProcessor {
  constructor(private strategy: PaymentStrategy) {}

  processPayment(amount: number): boolean {
    return this.strategy.process(amount);
  }
}
```

## 🚀 Conclusão

Os Princípios GRASP são ferramentas poderosas para criar designs de software mais robustos, flexíveis e manuteníveis. Ao aplicá-los consistentemente, você estará no caminho para criar sistemas de alta qualidade.

**Dicas Finais:**
- Não aplique todos os princípios de uma vez
- Use bom senso e julgamento
- Sempre refatore e melhore continuamente
- Pratique, pratique, pratique!
