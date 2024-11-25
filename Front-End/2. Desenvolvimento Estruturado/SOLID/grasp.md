# 🏗️ Princípios GRASP: Guia Completo de Design de Software

## 📌 Princípios GRASP

### 🔍 O que são os Princípios GRASP?

GRASP (General Responsibility Assignment Software Patterns) são padrões para atribuição de responsabilidades em projetos orientados a objetos.

### 🎯 Princípios Fundamentais

1. **Especialista na Informação** 🕵️
   - Atribua responsabilidades a classes que têm as informações necessárias para realizar a tarefa. Este princípio sugere que um objeto deve ser responsável por realizar uma tarefa quando possui todas as informações necessárias para executá-la.

2. **Criador** 🏗️
   - Defina quem deve criar novas instâncias de classes. O criador determina qual classe tem a responsabilidade de instanciar novos objetos, considerando critérios como composição, agregação ou uso próximo.

3. **Baixo Acoplamento** 🔗
   - Minimize dependências entre componentes para criar um sistema mais flexível, fácil de manter e modificar. Componentes com baixo acoplamento são mais independentes uns dos outros.

4. **Alta Coesão** 🎯
   - Mantenha responsabilidades fortemente relacionadas dentro de uma mesma classe ou módulo. Uma classe com alta coesão tem um propósito bem definido e concentrado.

5. **Controlador** 🎛️
   - Gerencie eventos do sistema em classes específicas, representando um ponto de controle para fluxos de trabalho ou casos de uso do sistema.

6. **Polimorfismo** 🔄
   - Use polimorfismo para lidar com variações de tipo, permitindo que diferentes classes implementem métodos de maneira específica, mas com uma interface comum.

7. **Fabricação Pura** 🧩
   - Crie classes artificiais quando necessário para manter a organização e a separação de responsabilidades, mesmo que essas classes não representem conceitos do domínio real.

8. **Indireção** ↔️
   - Use intermediários para reduzir acoplamento entre componentes, criando uma camada adicional de abstração que facilita a comunicação e a flexibilidade.

9. **Proteção contra Variações** 🛡️
   - Encapsule o que varia para minimizar o impacto de mudanças no sistema, criando interfaces estáveis e isolando elementos que podem mudar frequentemente.
