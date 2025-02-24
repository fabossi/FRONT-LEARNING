# Guia de Entrevista Sênior para iOS

Este documento contém perguntas comuns para entrevistas sênior de iOS, com respostas explicadas de forma objetiva.

---

## 1. Qual a diferença entre `strong`, `weak`, `unowned` em Swift?
- **`strong`**: Mantém uma referência forte ao objeto, impedindo que ele seja desalocado.
- **`weak`**: Não impede a desalocação do objeto, usado para evitar **retain cycles**. Deve ser opcional (`weak var delegate: MyDelegate?`).
- **`unowned`**: Semelhante ao `weak`, mas assume que o objeto sempre existirá (não pode ser opcional). Se acessar um objeto desalocado, a app **crasha**.

---

## 2. Como funciona o ARC (Automatic Reference Counting)?
O **ARC** gerencia a memória automaticamente, contando quantas referências existem para um objeto.
- Se o contador chega a **zero**, o objeto é desalocado.
- **Problema**: Ciclos de referência podem impedir a liberação de memória (por isso usamos `weak` e `unowned`).

---

## 3. O que é `escaping` e `non-escaping` em closures?
- **`@escaping`**: A closure **sobrevive** à execução da função, usada quando queremos armazená-la ou chamá-la depois.
- **`non-escaping`** (padrão): A closure **deve ser executada dentro da função** e não pode ser armazenada para uso futuro.

Exemplo:
```swift
func fetchData(completion: @escaping () -> Void) {  
    DispatchQueue.global().async {  
        completion() // Chamado depois  
    }  
}
```

---

## 4. Como evitar retenção cíclica em closures?
Usamos `[weak self]` dentro da closure para evitar que ela mantenha uma referência forte ao `self`.

```swift
class MyClass {
    var name = "Teste"
    
    func doSomething() {
        someAsyncMethod { [weak self] in
            print(self?.name ?? "Sem nome")  
        }
    }
}
```

---

## 5. O que é GCD (Grand Central Dispatch)?
API para gerenciar **threads** no iOS.

- `DispatchQueue.main.async {}` → Executa no **main thread** (UI).
- `DispatchQueue.global().async {}` → Executa em **background** (tarefas pesadas).
- `DispatchQueue.concurrentPerform` → Para executar **várias tarefas ao mesmo tempo**.

---

## 6. Como funciona o Combine?
Combine é um framework de **programação reativa**.

Exemplo:
```swift
import Combine

class ViewModel {
    @Published var text = "Hello"
    var cancellable: AnyCancellable?

    init() {
        cancellable = $text.sink { newText in
            print("Novo valor: \(newText)")
        }
    }
}
```

---

## 7. Qual a diferença entre Struct e Class em Swift?

| **Struct** | **Class** |
|------------|-----------|
| Tipo **valor** (cópia) | Tipo **referência** (ponteiro) |
| Mais leve e eficiente | Útil para objetos complexos |
| Imutável por padrão | Mutável |
| Sem herança | Suporta herança |

Geralmente usamos **Struct** para **modelos de dados simples** e **Class** quando precisamos de herança ou referência compartilhada.

---

## 8. O que é MVVM e como implementá-lo?

**MVVM (Model-View-ViewModel)** separa responsabilidades para manter o código limpo.

- **Model**: Dados (ex.: `User`, `Post`).
- **ViewModel**: Processa dados e expõe para a View.
- **View**: Interface gráfica (UIKit/SwiftUI).

Exemplo de **ViewModel**:
```swift
class UserViewModel {
    @Published var username: String = ""

    func fetchUser() {
        self.username = "John Doe"
    }
}
```

---

## 9. Como funciona o Dependency Injection (DI) em iOS?

**Dependency Injection** é um padrão para injetar dependências em vez de criá-las dentro da classe.

```swift
class Service {
    func fetchData() { print("Buscando dados...") }
}

class ViewModel {
    private let service: Service
    
    init(service: Service) {
        self.service = service
    }
    
    func loadData() {
        service.fetchData()
    }
}
```

---

## 10. O que é Codable e como funciona?

O protocolo `Codable` permite codificar/decodificar JSON automaticamente.

```swift
struct User: Codable {
    let name: String
    let age: Int
}

let json = """
{"name": "Alice", "age": 25}
""".data(using: .utf8)!

let user = try? JSONDecoder().decode(User.self, from: json)
print(user?.name) // Alice
```

---

## 11. O que é um Retain Cycle e como evitar?

Um **Retain Cycle** ocorre quando dois objetos seguram uma referência forte um ao outro.

**Solução usando `[weak self]`**:
```swift
func fetchData() {
    someAsyncCall { [weak self] in
        print(self?.name ?? "Sem nome")
    }
}
```

---

## 12. Como funciona a herança de protocolos em Swift?

Swift permite que um protocolo herde outro.

```swift
protocol Animal {
    var name: String { get }
    func makeSound()
}

protocol Flyable: Animal {
    func fly()
}

struct Bird: Flyable {
    let name: String

    func makeSound() {
        print("Piu piu")
    }

    func fly() {
        print("\(name) está voando!")
    }
}
```

---

Essas são algumas das principais perguntas para entrevistas iOS Sênior. Boa sorte! 🚀

