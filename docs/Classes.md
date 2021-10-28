---
title: Classes
nav_order: 2
---

# Classes
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

Classes são estruturas formadas por atributos e métodos capazes de representar conceitos presentes em nosso mundo. Nesta seção, são descritas as principais caracteríticas e a forma de uso das classes na linguagem Fortran.

## Criando uma classe em Fortran

Classes, no contexto do Fortran, são estruturas `type` às quais podem ser associadas variáveis, que irão representar atributos, e procedimentos, que são os métodos da classe. É boa prática definir classes dentro de estruturas `module` do Fortran, pois elas permitem o **controle de acesso dos dados**, o qual é feito através das palavras `public` e `private`. Para criar uma classe, a sintaxe é a seguinte:

```Fortran
type :: <nome da classe>
    <declaração dos atributos>
    contains
        <declaração dos métodos>
end type <nome da classe>
```

Para instaciar um objeto da classe criada, a sintaxe é:
```Fortran
type(<nome da classe>) :: <nome do objeto>
```

As declarações dos membros da classe, além de seus identificadores e tipos, podem conter os especificadores de acesso e outros argumentos para especificar a declaração desses membros. As variáveis dentro de uma classe são declaradas como em qualquer lugar no Fortran. Segue um exemplo

```Fortran
module Geo

! Torna os dados do módulo privados por padrão
private

! Torna público a classe PONTO_R2
public :: PONTO_R2

    type :: PONTO_R2
        ! Atributos da classe são privados
        real, private :: x
        real, private :: y
    end type PONTO_R2

end module Geo
```

Como a palavra `private` foi usada no início do módulo, todos os dados, sejam eles classes, variáveis ou procedimentos, tornam-se privados e só podem ser acessados de dentro do módulo. A classe `PONTO_R2` foi especificada como `public`, logo ela e todos os seus membros não `private` serão disponibilizados aos usuários do módulo. Como os atributos `x` e `y` são `private`, eles não serão disponibilizados.


Os métodos de uma classe são chamados de ***type-bound procedure*** e uma forma para declará-los em uma classe é:

```Fortran
    procedure :: <nome do procedimento de classe> => <nome do procedimento de módulo>
```

O procedimento de classe é o *type-bound procedure* e tem a função de associar o procedimento de módulo à classe. Segue um exemplo:

```Fortran
module Geo
    implicit none

    private

    public :: PONTO_R2 ! Método norma é acessível

    type :: PONTO_R2
        real, private :: x
        real, private :: y

        contains
            procedure, pass(self) :: norma => PONTO_R2_norma
    end type PONTO_R2

    contains
        function PONTO_R2_norma(self) result(modulo)
            implicit none
            class(PONTO_R2), intent(in) :: self
            real                        :: modulo ! como é result, modulo é intent(out) por default

            modulo = sqrt(self%x * self%x + self%y * self%y)
        end function PONTO_R2_calc_area
end module Geo
```

Na declaração do procedimento de classe `norma`, foi usado o especificador `pass(self)`. Ele informa para o compilador que um dos argumentos do procedimento é um objeto da classe `PONTO_R2`, ou de uma classe derivada desta, e seu nome é `self`. Na declaração de `self` foi utilizado o termo `class(PONTO_R2)`. Ele permite que, além de objetos da classe `PONTO_R2`, objetos de classes derivadas deste tipo sejam passadas. Os argumentos de procedimentos de classe que são objetos da própria classe devem ser declarados com o `class`.

## Classes derivadas

O Fortran permite que classes sejam criadas a partir de outras já existentes. Neste processo, a nova classe herda os membros da classe usada como base. As classes derivadas são chamadas de ***extended types***. Uma classe derivada pode ser criada da seguinte forma:

```Fortran
module Geo
    implicit none

    private

    public :: PONTO_R2, PONTO_R3 ! Método norma é acessível

    type :: PONTO_R2
        real, private :: x
        real, private :: y
        contains
            procedure, pass(self) :: norma => PONTO_R2_norma
    end type PONTO_R2
    
    type, extends(PONTO_R2) :: PONTO_R3
        real, private :: z
        contains
            procedure, pass(self) :: norma => PONTO_R3_norma
    end type quadrado

    contains
        <implementacao dos procedimentos>
end module Geo
```

A classe `PONTO_R3` é uma classe derivada da classe `PONTO_R2`. Os membros de `PONTO_R2` também são membros de `PONTO_R3`, portanto os atributos de `PONTOS_R3` são `x`, `y` e `z`, bem como o método `norma`. O método `norma` é reimplementado em `PONTO_R3` e deve ter as mesmas características que o método da classe base, ou seja, mesmo nome, tipo (função ou subrotina) e tipos dos argumentos (nome e tipo de dado).
Caso não se deseja que um método seja reimplementado por uma classe derivada, o atributo `non_overridable` deve ser usado na declaração do procedimento. Logo, a declaração de `norma` em `PONTO_R2`, por exemplo, seria:

```Fortran
 procedure, non_overridable, pass(self) :: norma => PONTO_R2_norma
```

É comum criar rotinas de inicialização dos objetos além dos contrutores padrões que o Fortran oferece. Neste exemplo, poderia-se encontrar justamente o problema de incompatibilidade entre procedimentos de mesmo nome entre a classe base e derivada. Suponha que deseja-se criar um *type-bound procedure* chamado `init`. No caso da classe `PONTO_R2`, ela teria, além do argumento referente ao objeto da classe, dois argumentos referentes ao atributos `x` e `y` da classe. Já a rotina de inicialização da classe `PONTO_R3` terá argumentos para os atributos `x`, `y` e `z`, além do `self` para o objeto da classe. Uma forma de contornar este problema é com o uso do atributo `optional` na declaração do atributo em excesso para a classe base, `z` neste caso. Então, teria-se:

```Fortran
module Geo
    implicit none
    type :: PONTO_R2
        real, private :: x
        real, private :: y

        contains
            procedure, pass(self) :: init  => PONTO_R2_init
            procedure, pass(self) :: norma => PONTO_R2_norma
    end type PONTO_R2

    type, extends(PONTO_R2) :: PONTO_R3
        real, private :: z

        contains
            procedure, pass(self) :: init  => PONTO_R3_init
            procedure, pass(self) :: norma => PONTO_R3_norma
    end type PONTO_R3

    contains

        subroutine PONTO_R2_init(self, x, y, z)
            implicit none
            class(PONTO_R2), intent(inout) :: self
            real,            intent(in)    :: x
            real,            intent(in)    :: y
            real, optional,  intent(in)    :: z

            self%x = x
            self%y = y
        end subroutine PONTO_R2_init

        subroutine PONTO_R3_init(self, x, y, z)
            implicit none
            class(PONTO_R3), intent(inout) :: self
            real,            intent(in)    :: x
            real,            intent(in)    :: y
            real, optional,  intent(in)    :: z

            self%x = x
            self%y = y
            self%z = z
        end subroutine PONTO_R3_init
end module Geo
```

Assim, a chamada para as rotinas de inicialização seriam:

```Fortran
    ! Declaração
    type(PONTO_R2) :: vetor_r2
    type(PONTO_R3) :: vetor_r3
    real :: x = 1, y = 0, z = 1
    real :: norma1, norma2

    ! Inicializando objetos
    call vetor_r2%init(x, y)
    call vetor_r3%init(x, y, z)
```

## Classes abstratas

As classes abstratas no Fortran são análogas às classes abstratas de linguagens como C++. Elas padronizam determinados atributos e métodos para suas classes derivadas. As classes derivadas, por sua vez, além de herdar os atributos da classe abstrata, têm a função de implementar os métodos virtuais que foram declarados na abstrata. As classes abstratas são criadas com o atributo `abstract` e sua sintaxe geral é a seguinte:

```Fortran
    type, abstract :: <nome da classe>
        <declaração dos atributos>
        contains
            <declaração dos métodos>
    end type <nome da classe>
```

Um exemplo de uso de classes abstratas pode ser conferido abaixo.

```Fortran
module Poly
    implicit none

    private

    !Definindo classe abstrata
    type, abstract :: poligono
        contains
            !Procedimento abstrato deve ser definido nas classes derivadas
            procedure(area_inter), pass(self), deferred :: area
    end type poligono

    abstract interface
        real function area_inter(self) result(area)
            !Import necessário para trazer a classe poligono para o escopo da interface
            import poligono
            class(poligono), intent(in) :: self

        end function area_inter
    end interface
end module Poly
```

No exemplo, é definida uma classe abstrata `poligono`. Repare na generalidade do termo, já que um polígono é uma figura geométrica pode possuir diversas formas, entretanto sempre possuindo características comuns. Dentro da classe abstrata, é declarado o procedimento `area`, no qual é usado o atributo `deferred`. Este atributo só pode ser usado em classes abstratas e indica que o procedimento deve ser implementado pelas classes derivadas. Além disso, deve ser fornecida uma interface para o procedimento virtual, de modo a padronizar suas características entre as implementações das classes derivadas.
