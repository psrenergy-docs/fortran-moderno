---
title: Sobrecarga de procedimentos
nav_order: 3
---

# Sobrecarga de procedimentos
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

A sobrecarga de procedimentos, sejam eles subrotinas ou funções, ocorre quando um procedimento possui diferentes implementações dependendo do contexto onde é aplicado. No Fortran, a sobrecarga de procedimentos pode ser alcançada de algumas formas, as quais serão mostradas nesta parte da documentação.

## Interfaces

Interfaces em Fortran são blocos que podem ser usados para diferentes finalidades em uma aplicação. Entretanto, um dos principais objetivos da interface é definir as características de um procedimento, ou seja, especificar seu identificador (nome), seus parâmetros, indicando seus tipos, bem como se é uma subrotina ou função. Abaixo é possível ver um exemplo de um bloco de interface.

```Fortran 
interface
    subroutine trocar_valores(x, y)
        integer, intent(intout) :: x, y
    end subroutine trocar_valores
end interface
```

```Fortran 
interface
    real function soma(x, y) result(z)
        integer, intent(in) :: x, y
        real                :: z
    end function soma
end interface
```

Repare que as definições da subrotina e da função não foram fornecidas, somente seus protótipos, ou interfaces.

## Sobrecarga de procedimentos

Outro uso dos blocos de interface é para realizar a sobrecarga de procedimentos. Como dito anteriormente, a sobrecarga é feita quando um identificador é usado para se referir a procedimentos com diferentes implementações. Quando este identicador é invocado no código, o compilador saberá a qual implementação utilizar com base na lista de argumentos informados na interface e nos usados na chamada do procedimentos. Observe abaixo um exemplo de uso deste bloco em um módulo:

```Fortran 
module sobrecarga
    implicit none

    ! Tornar privados procedimentos de módulo
    private

    ! Tornar público identidador da interface
    public :: alocar

    interface alocar
        module procedure :: alocar_vetor_int
        module procedure :: alocar_vetor_real
    end interface alocar

    contains

    subroutine alocar_vetor_int (vetor, tamanho)
        integer, intent(inout)  :: vetor(:)
        integer, intent(in)     :: tamanho

        allocate(vetor(tamanho))
    end subroutine alocar_vetor

    subroutine alocar_vetor_real (vetor, tamanho)
        real, intent(inout)  :: vetor(:)
        real, intent(in)     :: tamanho

        allocate(vetor(tamanho))
    end subroutine alocar_vetor
end module sobrecarga
```

A expressão `module procedure` usado dentro da interface serve para indicar os procedimentos de módulo que estão associados à interface genérica `alocar` e somente devem ser usados dentro desses blocos. Uma vez definido o bloco de interface e as implementações das subrotinas, o seguinte uso pode ser feito:

```Fortran
program sobrecarga
    implicit none

    integer :: vetor1(:)
    real    :: vetor2(:)
    integer :: tamanho

    ! Tamanho a ser alocado
    tamanho = 3

    ! Chamará alocar_vetor_int
    call alocar(vetor1, tamanho)

    ! Chamará alocar_vetor_real
    call alocar(vetor2, tamanho)

end program sobrecarga
```

## Sobrecarga de procedimentos de classe  (type-bound procedures)

Quando a sobrecarga é desejada em um procedimento de classe, chamado de ***type-bound procedure***, é feito o uso da palavra `generic`. O `generic` define uma interface para o procedimento de classe, da mesma forma que o bloco `interface` da seção 
anterior. Um exemplo de uso pode ser visto abaixo:

```Fortran
module complexo
    implicit none

    private

    public :: cmplx

    type :: cmplx
        real :: re
        real :: im

        contains
            generic :: operator(+) => add_cmplx
    end type cmplx

    contains

        function add_cmplx(a, b) result(z)
            class(cmplx), intent(in) :: a, b
            type(cmplx)              :: z

            z%re = a%re + b%re
            z%im = a%im + b%im
        end function add_cmplx

end module complexo
```

No exemplo, o operador `+` é sobrecarregado para implementar a soma de números complexos, formados por uma parte real e outra imaginária. Com a sobrecarga, o operador pode ser usado da seguinte forma:

```Fortran
program exemplo
    use complexo
    implicit none

    type(cmplx) :: a, b, c

    ! Definindo valores
    a%re = 1.0
    a%im = 1.0

    b%re = 2.0
    b%im = 2.0

    ! Chamada do operador
    c = a + b
    
    ! c = 3 + j3
end program exemplo

```


 Dos exemplos mostrados, é possível perceber que as interfaces no Fortran permitem criar um novo nível de abstração no código, uma vez que criadas, tornam desnecessária o conhecimento e a manipulação dos dados por trás das classes ou procedimentos.


