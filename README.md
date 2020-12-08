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

## O

> Objetos, classes, entidades, etc... DEVEM estar abertos para extensão mas fechadas para modificação.

Ou seja, possível de se extender, de inserir novas funcionalidades.

> Exemplo na prática, SEM o princípio Open-Closed:

```php
class ContratoClt
{
    public function calcularSalario () {}
}

class Estagio
{
    public function bolsaAuxilio () {}
}

class ContratoPj
{
    public function calcularPagamento () {}
}

class FolhaDePagamento
{
    public function calcular ($funcionario)
    {

        $salario = 0;

        if ($funcionario instanceof ContratoClt)
        {
            $salario = $funcionario->calcularSalario();
        }
        else if ($funcionario instanceof Estagio)
        {
            $salario = $funcionario->bolsaAuxilio();
        }
        else if ($funcionario instanceof ContratoPj)
        {
            $salario = $funcionario->calcularPagamento();
        }
        return $salario;
    }
}
```

O exemplo acima, viola o princípio de Open-Closed, cada vez que surgir um novo tipo de contrato, teriamos que modificar a classe FolhaDePagamento, violando o fechado para modificação do princípio.
Se temos uma situação como essa, não vamos modificar a classe para adicionar uma funcionalidade, e sim extender essa classe.

> A solução seria utilizar uma Interface, para realizar este "contrato", de certo modo, onde ela diz o que precisa ser implementado, para implementar tal funcionalidade.

Exemplo com a interface, mantendo o princípio:

```php
interface Remuneravel
{
    public function remuneracao ();
}
```

Toda classe que implementar essa Interface, terá que ter, obrigatoriamente, esse método.

```php
class ContratoClt implements Remuneravel
{
    public function remuneracao () {}
}

class Estagio implements Remuneravel
{
    public function remuneracao () {}
}

class ContratoPj implements Remuneravel
{
    public function remuneracao () {}
}

class FolhaDePagamento
{
    public function calcular (Remuneravel $funcionario)
    {
        return $funcionario->remuneracao();
    }
}
```

E se quisermos adicionar um novo tipo de contrato, nós EXTENDEMOS a funcionalidade e não alteramos nada na classe de pagamento e nem na interface.

> Exemplo:

```php
class ContratoInternacional implements Remuneravel
{
    public function remuneracao () {}
}
```
