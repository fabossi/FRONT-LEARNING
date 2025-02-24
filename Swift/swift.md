# 📱 Guia Completo para Desenvolvedores iOS - Pleno/Sênior

Um guia abrangente cobrindo desde conceitos básicos até tópicos avançados de desenvolvimento iOS. Perfeito para preparação de entrevistas e consulta rápida!

## 📑 Índice

1. [Fundamentos do Swift](#fundamentos-do-swift)
2. [Conceitos Essenciais do iOS](#conceitos-essenciais-do-ios)
3. [Padrões de Arquitetura](#padrões-de-arquitetura)
4. [Gerenciamento de Memória](#gerenciamento-de-memória)
5. [Concorrência](#concorrência)
6. [Testes e Qualidade](#testes-e-qualidade)
7. [Perguntas Comuns de Entrevista](#perguntas-comuns-de-entrevista)
8. [Boas Práticas](#boas-práticas)

## Fundamentos do Swift

### Tipos de Valor vs Tipos de Referência
- **Structs (Tipos de Valor)**
  ```swift
  struct Usuario {
      let id: UUID
      var nome: String
      var idade: Int
      
      mutating func atualizarNome(_ novoNome: String) {
          nome = novoNome
      }
  }
  ```
  - Copiados quando atribuídos ou passados
  - Thread-safe por padrão
  - Sem herança
  - Preferidos para modelos de dados

- **Classes (Tipos de Referência)**
  ```swift
  class Pessoa {
      var nome: String
      weak var supervisor: Pessoa?
      
      init(nome: String) {
          self.nome = nome
      }
  }
  ```
  - Compartilham a mesma referência
  - Suportam herança
  - Requerem cuidado com referências circulares

### Optionals e Tratamento
```swift
// Unwrap seguro
if let nome = usuario.nome {
    print("Nome: \(nome)")
}

// Guard let
guard let idade = usuario.idade else {
    return
}

// Optional chaining
let supervisorNome = pessoa.supervisor?.nome
```

### Tratamento de Erros
```swift
enum APIError: Error {
    case networkError
    case invalidData
    case decodingError
    case serverError(code: Int)
    
    var mensagem: String {
        switch self {
        case .networkError:
            return "Erro de conexão"
        case .invalidData:
            return "Dados inválidos"
        case .decodingError:
            return "Erro ao processar dados"
        case .serverError(let code):
            return "Erro do servidor: \(code)"
        }
    }
}

func buscarDados() throws -> [Usuario] {
    // Implementação
}

// Uso
do {
    let usuarios = try buscarDados()
} catch let erro as APIError {
    print(erro.mensagem)
} catch {
    print("Erro desconhecido: \(error)")
}
```

## Conceitos Essenciais do iOS

### Ciclo de Vida de ViewControllers
```swift
class ExemploViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        // View foi carregada na memória
    }
    
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        // View vai aparecer
    }
    
    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        // View apareceu
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        // View vai desaparecer
    }
    
    override func viewDidDisappear(_ animated: Bool) {
        super.viewDidDisappear(animated)
        // View desapareceu
    }
}
```

## Padrões de Arquitetura

### MVC (Model-View-Controller)
```swift
// Model
struct Produto {
    let id: Int
    var nome: String
    var preco: Double
}

// View
class ProdutoView: UIView {
    let nomeLabel: UILabel
    let precoLabel: UILabel
    
    func configurar(com produto: Produto) {
        nomeLabel.text = produto.nome
        precoLabel.text = "R$ \(produto.preco)"
    }
}

// Controller
class ProdutoViewController: UIViewController {
    private let produtoView: ProdutoView
    private var produto: Produto
    
    override func viewDidLoad() {
        super.viewDidLoad()
        produtoView.configurar(com: produto)
    }
}
```

### MVVM (Model-View-ViewModel)
```swift
// Model
struct Usuario {
    let id: UUID
    var nome: String
    var email: String
}

// ViewModel
class UsuarioViewModel {
    private var usuario: Usuario
    
    var nomeExibicao: String {
        return "Nome: \(usuario.nome)"
    }
    
    var emailExibicao: String {
        return "Email: \(usuario.email)"
    }
    
    func atualizar(nome: String) {
        usuario.nome = nome
        // Notificar view sobre mudanças
    }
}

// View
class UsuarioViewController: UIViewController {
    private let viewModel: UsuarioViewModel
    
    private func configurarBindings() {
        // Implementar bindings com viewModel
    }
}
```

## Gerenciamento de Memória

### ARC (Automatic Reference Counting)
```swift
class Delegado {
    weak var viewController: UIViewController?  // weak evita ciclo de referência
}

class ServicoDownload {
    // Closure de completion com [weak self] para evitar retenção
    func baixarDados(completion: @escaping () -> Void) {
        DispatchQueue.global().async { [weak self] in
            // Processar download
            completion()
        }
    }
}
```

## Concorrência

### GCD (Grand Central Dispatch)
```swift
// Operações assíncronas
DispatchQueue.global(qos: .background).async {
    // Trabalho pesado em background
    DispatchQueue.main.async {
        // Atualizar UI na main thread
    }
}

// Grupos de dispatch
let grupo = DispatchGroup()
grupo.enter()
baixarImagem { _ in
    grupo.leave()
}

grupo.notify(queue: .main) {
    // Todas as operações completadas
}
```

### async/await (iOS 15+)
```swift
func buscarDados() async throws -> [Usuario] {
    let (data, _) = try await URLSession.shared.data(from: url)
    return try JSONDecoder().decode([Usuario].self, from: data)
}

// Uso
Task {
    do {
        let usuarios = try await buscarDados()
        // Usar dados
    } catch {
        // Tratar erro
    }
}
```

## Testes e Qualidade

### Testes Unitários
```swift
class CalculadoraTests: XCTestCase {
    var calculadora: Calculadora!
    
    override func setUp() {
        super.setUp()
        calculadora = Calculadora()
    }
    
    func testSoma() {
        let resultado = calculadora.somar(2, 3)
        XCTAssertEqual(resultado, 5)
    }
    
    func testDivisaoPorZero() {
        XCTAssertThrowsError(try calculadora.dividir(10, por: 0))
    }
}
```

### Mocks e Stubs
```swift
protocol ServicoAPI {
    func buscarUsuarios() async throws -> [Usuario]
}

class MockServicoAPI: ServicoAPI {
    var usuariosMock: [Usuario] = []
    var deveLancarErro = false
    
    func buscarUsuarios() async throws -> [Usuario] {
        if deveLancarErro {
            throw APIError.networkError
        }
        return usuariosMock
    }
}
```

## Boas Práticas

### Injeção de Dependência
```swift
protocol ServicoDados {
    func buscarDados() async throws -> [Dado]
}

class ViewController {
    private let servico: ServicoDados
    
    init(servico: ServicoDados) {
        self.servico = servico
    }
}
```

### SOLID
- **S**: Single Responsibility
  ```swift
  // Bom: Classe com única responsabilidade
  class GerenciadorAutenticacao {
      func autenticar(usuario: String, senha: String) -> Bool
  }
  ```

- **O**: Open/Closed
  ```swift
  protocol Validador {
      func validar(_ valor: String) -> Bool
  }
  
  // Novas validações podem ser adicionadas sem modificar código existente
  struct ValidadorEmail: Validador {
      func validar(_ valor: String) -> Bool
  }
  ```

### Clean Code
```swift
// Nomes significativos
func calcularDescontoTotal(valor: Double, percentual: Double) -> Double

// Funções pequenas e focadas
extension String {
    var isEmailValido: Bool {
        // Validação de email
    }
}
```

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

## Recursos Adicionais

- [Documentação Oficial Apple](https://developer.apple.com/documentation)
- [Swift.org](https://swift.org)
- [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines)
