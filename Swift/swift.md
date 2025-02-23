# Guia de Preparação para Entrevista de Desenvolvedor iOS Pleno/Sênior 🚀

Este guia cobre desde perguntas comuns em entrevistas até conceitos avançados de Swift e iOS. Tudo em um único lugar para facilitar sua preparação!

---

## 📌 Índice

1. [Perguntas e Respostas Comuns em Entrevistas](#-perguntas-e-respostas-comuns-em-entrevistas)
2. [Curso Rápido de Swift](#-curso-rápido-de-swift)
   - [Tipos de Dados Básicos](#tipos-de-dados-básicos)
   - [Funções e Closures](#funções-e-closures)
   - [Protocolos e Extensões](#protocolos-e-extensões)
   - [Generics](#generics)
   - [Error Handling](#error-handling)
   - [Concorrência com async/await](#concorrência-com-asyncawait)
3. [Conceitos Avançados de iOS](#-conceitos-avançados-de-ios)
   - [Ciclo de Vida de ViewControllers](#ciclo-de-vida-de-viewcontrollers)
   - [ARC (Automatic Reference Counting)](#arc-automatic-reference-counting)
   - [Concorrência em iOS](#concorrência-em-ios)
   - [SwiftUI vs UIKit](#swiftui-vs-uikit)
4. [Boas Práticas e Padrões de Design](#-boas-práticas-e-padrões-de-design)
   - [MVC vs MVVM](#mvc-vs-mvvm)
   - [Delegation](#delegation)
   - [Código Limpo e Testável](#código-limpo-e-testável)
5. [Recursos Adicionais](#-recursos-adicionais)

---

## 📌 Perguntas e Respostas Comuns em Entrevistas

### 1. **Qual é a diferença entre `let` e `var` em Swift?**
- **Resposta:** 
  - `let` é usado para declarar constantes, ou seja, valores que não mudam após a atribuição.
  - `var` é usado para declarar variáveis, que podem ter seu valor alterado após a declaração.

### 2. **O que é Optionals em Swift?**
- **Resposta:** 
  - Optionals são usados para representar valores que podem ser `nil` (ausência de valor). Eles são declarados com um `?` após o tipo, por exemplo: `var name: String?`.
  - Para "desembrulhar" um Optional, você pode usar `if let`, `guard let`, ou o operador `!` (force unwrap), mas este último deve ser usado com cuidado para evitar crashes.

### 3. **Explique o ciclo de vida de uma ViewController no iOS.**
- **Resposta:** 
  O ciclo de vida de uma `UIViewController` inclui os seguintes métodos principais:
  - `viewDidLoad()`: Chamado quando a view é carregada na memória.
  - `viewWillAppear(_:)`: Chamado antes da view ser exibida na tela.
  - `viewDidAppear(_:)`: Chamado após a view ser exibida na tela.
  - `viewWillDisappear(_:)`: Chamado antes da view ser removida da tela.
  - `viewDidDisappear(_:)`: Chamado após a view ser removida da tela.
  - `viewWillLayoutSubviews()` e `viewDidLayoutSubviews()`: Chamados antes e depois da view organizar seus subviews.

### 4. **O que é ARC (Automatic Reference Counting)?**
- **Resposta:** 
  - ARC é o mecanismo de gerenciamento de memória usado pelo Swift. Ele automaticamente libera a memória de objetos que não estão mais sendo referenciados.
  - Para evitar ciclos de referência (memory leaks), é importante usar `weak` ou `unowned` para referências que podem criar ciclos.

### 5. **Qual é a diferença entre `struct` e `class` em Swift?**
- **Resposta:** 
  - `struct` é um tipo de valor (value type), ou seja, quando você atribui uma struct a uma nova variável, ela é copiada.
  - `class` é um tipo de referência (reference type), ou seja, quando você atribui uma classe a uma nova variável, ambas apontam para a mesma instância.
  - `struct` não suporta herança, enquanto `class` suporta.

### 6. **O que é o protocolo `Codable`?**
- **Resposta:** 
  - `Codable` é um protocolo que combina `Encodable` e `Decodable`. Ele permite que você serialize e desserialize objetos Swift para formatos como JSON.
  - Exemplo:
    ```swift
    struct User: Codable {
        var name: String
        var age: Int
    }
    ```

### 7. **Como você lida com concorrência em iOS?**
- **Resposta:** 
  - Em iOS, você pode usar Grand Central Dispatch (GCD) ou `OperationQueue` para gerenciar tarefas concorrentes.
  - Com Swift 5.5, você também pode usar `async/await` para escrever código assíncrono de forma mais clara.
  - Exemplo com GCD:
    ```swift
    DispatchQueue.global(qos: .background).async {
        // Tarefa em segundo plano
        DispatchQueue.main.async {
            // Atualizar a UI na thread principal
        }
    }
    ```

### 8. **O que é o padrão Delegation em iOS?**
- **Resposta:** 
  - Delegation é um padrão de design onde um objeto (delegate) age em nome de outro objeto. É comumente usado em UIKit, como em `UITableViewDelegate` e `UITableViewDataSource`.
  - Exemplo:
    ```swift
    class ViewController: UIViewController, UITableViewDelegate {
        // Implementação dos métodos do delegate
    }
    ```

### 9. **Como você testa seu código em iOS?**
- **Resposta:** 
  - Em iOS, você pode usar XCTest para escrever testes unitários e de UI.
  - Exemplo de teste unitário:
    ```swift
    func testExample() {
        let value = 5
        XCTAssertEqual(value, 5, "O valor deve ser 5")
    }
    ```

### 10. **O que é o SwiftUI e como ele difere do UIKit?**
- **Resposta:** 
  - SwiftUI é um framework declarativo para construir interfaces de usuário, introduzido no iOS 13. Ele permite criar UIs de forma mais simples e com menos código.
  - UIKit é um framework imperativo e mais antigo, que requer mais código para criar e gerenciar interfaces.
  - SwiftUI é mais moderno e integrado com Swift, enquanto UIKit é mais maduro e amplamente utilizado em projetos legados.

---

## 🚀 Curso Rápido de Swift

### Tipos de Dados Básicos
- Int, Double, String, Bool, Array, Dictionary, Set.

### Funções e Closures
- Funções são definidas com `func`.
- Closures são blocos de código que podem ser passados como argumentos.
  ```swift
  let greet = { (name: String) in
      print("Hello, \(name)!")
  }
  greet("World")
