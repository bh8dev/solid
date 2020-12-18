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

## L

> "Se para cada objeto O1 do tipo S existe um objeto O2 do tipo T, tal que, para todos os programas P definidos em termos de T, o comportamento de P fica inalterado quando O1 é substituído por O2, então S é um subtipo de T.”

Em outras palavras, um objeto que utilize uma classe base deve continuar a funcionar corretamente quando um objeto derivado dessa classe base for passado para ele.

Ou, basicamente, LISKOV (1988) está dizendo que se uma classe S herda de uma classe T, então a classe S deve se comportar igual a classe T.

Ou seja, a T é a classe base e ela deve responder por todas as classes filhas dela, inclusive a S.

## Exemplo na prática

```php
class A
{
    public function getNome ()
    {
        return 'Meu nome é A';
    }
}

class B extends A
{
    public function getNome ()
    {
        return 'Meu nome é B';
    }
}

function imprimeNome (A $objeto) // injeção de independência
{
    return $objeto->getNome();
}

$objeto1 = new A();
$objeto2 = new B();
echo imprimeNome($objeto1) . '<br/>';
echo imprimeNome($objeto2);
```

> Resultado ao executar este código:

Meu nome é A

Meu nome é B

## Situação onde o princípio seria violado

Se mudassemos o retorno do método **getNome()** da Classe B, para 30, ele não retornaria mais uma string, que é o original de seu Pai (A), e sim um inteiro.
Ao realizar esta alteração, nós mudamos o objetivo desse método. Ele funcionar normalemnte, contudo, nós violamos o princípio ao realizar tal alteração.

```php
class B extends A
{
    public function getNome ()
    {
        return 30;
    }
}
```

## I

> o princípio de segregação da interface afirma que nenhum cliente deve ser forçado a depender dos métodos que não usa.

Simplificando: interfaces maiores devem ser divididas em menores. Ao fazer isso, podemos garantir que as classes de implementação só precisam se preocupar com os métodos que são do seu interesse.

## Exemplo de violação deste princípio

```php
interface IAves
{
    public function setLocation ($latitude, $longitude);
    public function setLatitude ($latitude);
    public function render ();
}

class Papagaio implements IAves
{
    public function setLocation ($latitude, $longitude)
    {

    }

    public function setLatitude ($latitude)
    {

    }

    public function render ()
    {

    }
}

class Pinguim implements IAves
{
    public function setLocation ($latitude, $longitude)
    {

    }

    public function setLatitude ($latitude)
    {
        return 0;
    }

    public function render ()
    {

    }
}
```

Um Pinguim não possui Altitude, é um tipo de Ave que nao voa, então nesse caso o **setAltitude()** vai estar sempre um valor fixo, ele nao vai ser utilizado, a altitude sempre será a mesma, pois ele estará no chão.

## Abordagem correta

> Uma abordagem correta seria, criar a interface Aves e estendê-la para interfaces mais específicas.

```php
interface IAves
{
    public function setLocation ($latitude, $longitude);
    public function render ();
}

interface IAvesQueVoam extends IAves
{
    public function setLatitude ($latitude);
}

class Papagaio implements IAvesQueVoam
{
    public function setLocation ($latitude, $longitude)
    {

    }

    public function setLatitude ($latitude)
    {

    }

    public function render ()
    {

    }
}

class Pinguim implements IAves
{
    public function setLocation ($latitude, $longitude)
    {

    }

    public function render ()
    {

    }
}
```

No exemplo acima criamos interfaces onde realmente usaremos apenas o necessário em quem implementa-las, assim não deixando nenhum método lá de bobeira e sim apenas os itens que VÃO ser realmente utilizados nas classes que os implementarem.
