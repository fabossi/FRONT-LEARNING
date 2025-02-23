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

## Perguntas Comuns de Entrevista

1. **Como você lida com memory leaks?**
   - Uso de `weak` e `unowned`
   - Ferramentas de debug como Instruments
   - Boas práticas de closure capture lists

2. **Diferença entre frame e bounds?**
   - Frame: posição e tamanho relativos ao superview
   - Bounds: posição e tamanho no próprio sistema de coordenadas

3. **Como implementar persistência de dados?**
   - UserDefaults para dados simples
   - CoreData para dados complexos
   - FileManager para arquivos
   - Keychain para dados sensíveis

4. **Padrões de comunicação entre ViewControllers?**
   - Delegate pattern
   - Notification Center
   - Closure callbacks
   - Combine (iOS 13+)

5. **Como otimizar performance?**
   - Reutilização de células
   - Operações pesadas em background
   - Lazy loading de imagens
   - Caching apropriado

## Recursos Adicionais

- [Documentação Oficial Apple](https://developer.apple.com/documentation)
- [Swift.org](https://swift.org)
- [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines)
