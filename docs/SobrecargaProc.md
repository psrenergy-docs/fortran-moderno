---
title: Sobrecarga de procedimentos
nav_order: 1
---

# Sobrecarga de procedimentos
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Interfaces

Interfaces em Fortran são blocos que podem ser usados para diversas finalidades em uma aplicação. Entretanto, um dos principais objetivos da interface é definir a características de um procedimento, ou seja, especificar seu identificador, seus parâmetros, identificando quais são somente de leitura (`intent(in)`) e quais podem ser alterados (`intent(inout)`, `intent(out)`), bem como se é uma subrotina ou função. Abaixo é possível ver um exemplo de um bloco de interface.

```Fortran 
interface
    subroutine trocar_valores(x, y)
        integer, intent(intout) :: x, y
    end subroutine
end interface
```

Repare que a definição da subrotina não foi fornecida, somente seu protótipo, ou interface.

## Sobrecarga de procedimentos

Um dos usos dos blocos de interface é para realizar a sobrecarga de procedimentos. A sobrecarga ocorre quando um identificador é usado para se referir a procedimentos com diferentes implementações. Quando este identicador é invocado no código, o compilador saberá a qual implementação utilizar com base na lista de argumentos informados e em seus tipos. Observe abaixo um exemplo de uso deste bloco:

```Fortran 
interface alocar
    subroutine alocar_vetor_int (vetor, tamanho)
        integer, intent(inout)  :: vetor(:)
        integer, intent(in)     :: tamanho
    end subroutine alocar_vetor

    subroutine alocar_vetor_real (vetor, tamanho)
        real, intent(inout)  :: vetor(:)
        real, intent(in)     :: tamanho
    end subroutine alocar_vetor
end interface
```

Uma vez definido o bloco de interface e as definições das subrotinas, aqui omitidas, o seguinte uso pode ser feito:

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