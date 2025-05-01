# TG4 - Orientações

Do proejto escolhido, cada equipe deverá desenvolver o diagrama de classes completo (nível implementação) e ao menos um diagrama de sequencia de mensagens e explicar qual caso de uso ele representa. 


---

## Exemplo

Vamos modelar um sistema simples de biblioteca com as seguintes entidades:

- Livro
- Usuario
- Biblioteca
- Empréstimo

Vamos representar a estrutura do sistema e simular a sequência de chamadas para realizar um empréstimo.

### DCL:

```yaml
@startuml
class Livro {
  -isbn: String
  -titulo: String
  -autor: String
  -disponivel: boolean
  +emprestar(): void
  +devolver(): void
  +isDisponivel(): boolean
}

class Usuario {
  -id: int
  -nome: String
  +solicitarEmprestimo(livro: Livro, biblioteca: Biblioteca): void
}

class Biblioteca {
  -livros: List<Livro>
  -emprestimos: List<Emprestimo>
  +registrarEmprestimo(usuario: Usuario, livro: Livro): void
  +adicionarLivro(livro: Livro): void
}

class Emprestimo {
  -data: Date
  +getResumo(): String
}

' COMPOSIÇÃO: Biblioteca compõe Livro
Biblioteca "1" *-- "0..*" Livro 

' AGREGAÇÃO: Biblioteca agrega Emprestimos
Biblioteca "1" o-- "0..*" Emprestimo

' AGREGAÇÃO: Emprestimo envolve Livro
Emprestimo "1" o-- "1" Livro 

' Usuario pode fazer vários empréstimos (opcional, se desejar detalhar mais)
Usuario "1" o-- "0..*" Emprestimo

@enduml
```

### DSM:


```yaml
@startuml
autonumber
actor Cliente
participant usuario
participant biblioteca 
participant livro 
participant emprestimo

Cliente -> usuario : solicitarEmprestimo(livro, biblioteca)
activate usuario
usuario -> biblioteca : registrarEmprestimo(this, livro)
activate biblioteca
biblioteca -> livro : isDisponivel()
activate livro
livro --> biblioteca : true
deactivate livro

alt Livro disponível
    biblioteca -> livro : emprestar()
    activate livro
    livro --> biblioteca
    deactivate livro

    biblioteca -> emprestimo : new(usuario, livro, data)
    activate emprestimo
    emprestimo --> biblioteca
    deactivate emprestimo

    biblioteca --> usuario : empréstimo registrado
else Livro indisponível
    biblioteca --> usuario : livro não disponível
end
deactivate biblioteca
deactivate usuario
@enduml
```