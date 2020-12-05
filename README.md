# Conceitos SOLID

> Os princípios:

- S - Single Responsability Principle (Princípio da Responsabilidade Única);

- O - Open-Closed Principle (Princípio Aberto-Fechado);
- L - Liskov Substitution Principle (Princípio da Substituição de Liskov);
- I - Interface Segregation Principle (Princípio da Segregação da Interface);
- D - Dependency Inversion Principle (Princípio da Inversão de Dependência)

## S

> Toda Classe DEVE ter um e SOMENTE um motivo para mudar

Em outras palavras, uma Classe, tem que ter apenas uma responsabilidade, e não múltiplas.
> Exemplo de classe com MAIS de UMA responsabilidade

```php
class Usuario
{
    public function getNome () {}
    public function setNome() {}

    public function add () {}
    public function update () {}
    public function delete () {}
}
```

Este exemplo viola o princípio de responsabilidade única, essa classe está fazendo, ao mesmo tempo, um gerenciamento do usuário (setando nome, email, etc) e uma manipulação do usuário, no banco de dados.

> Uma abordagem a ser feita para adaptação seria:

```php
class Usuario
{
    public function getNome () {}
    public function setNome () {}
}
```

Deste modo, essa classe tem apenas a responsabilidade de gerenciar o usuário.
Coisas relacionadas ao próprio usuário.

> Então faríamos uma outra classe, apenas para manipular o usuário com o banco de dados, com a responsabilidade de manipulação dos dados:

```php
class UsuarioDb
{
    public function add (Usuario $user) {}
    public function update (Usuario $user) {}
    public function delete (int $id) {}
}
```

## Problemas ao se violar este Princípio

Exemplo de violação ilustrado anteriormente em algumas imagens acima.

- Falta de coesão (Você não entenderá qual o princípio / objetivo daquela classe em questão).

- Auto-Acoplamento (Você irá ter tudo sobre o usuário, junto ali no mesmo lugar. Você terá que puxar toda a classe, um exemplo, você quer apenas criar um usuário, definir nome, idade, etc... e recuperar estes dados, junto a isso você irá trazer todos os métodos de manipulação de dados. O código tenderá a ficar cada vez mais bagunçado).
