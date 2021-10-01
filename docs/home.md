---
layout: default
title: Home
nav_order: 1
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
permalink: /
---

## Bem vindo a documentação sobre Fortran moderno da PSR

Fortran é uma linguagem de programação desenvolvidade pela IBM na década de 50. Nos últimos anos, a linguagem tem sido atualizada, recebendo novas funcionalidades que a ajudaram a se adequar aos novos paradigmas de desenvolvimento de *software*, como a orientação a objetos. Apesar disso, as documentações disponiveis na internet não são tão detalhadas quanto as de outras linguagens como C, C++ ou Python. Esta documentação tem como objetivo cobrir os principais tópicos da linguagem necessários para utilizar o Fortran sob a ótica da programação orientada a objetos.


## Introdução à programação orientada a objetos

A programação orientada à objetos (POO) é um padrão de programação que se baseia na construção e organização de estruturas de dados que são formadas por **atributos** e **métodos**. Os atributos representam características, ou propriedades, de um determinado sistema que a estrutura procura descrever. Os métodos são procedimentos associados à essas estruturas e são capazes de operar sobre os atributos da mesma para executar determinadas instruções. No contexto da POO, a estrutura de dados é denominada **classe**. Os atributos e métodos de uma classe são referidos como **membros**, por fazer parte de sua estrutura. Para ilustrar estes conceitos, observe o exemplo abaixo.

```Fortran
! Classe onibus
type :: onibus
    ! Atributos
    integer       :: capacidade_maxima
    integer       :: numero_de_passageiros
    logical       :: estado_da_porta
    character(20) :: proxima_parada

    ! Métodos
    contains
        procedure :: andar
        procedure :: parar
        procedure :: abrir_porta
        procedure :: verificar_numero_passageiros
        procedure :: anunciar_proxima_parada
end type onibus
```

No exemplo acima, foi definida uma classe, em Fortran, para representar um ônibus para transporte. Foram definidos alguns atributos que caracterizam um ônibus em sua qualidade de transporte público, tal como `capacidade_maxima`
e `proxima_parada`. Além dos atributos, foram declarados métodos para representar ações típicas de um ônibus como `andar` e `abrir_porta_embarque`.

Uma das principais características da POO é possibilidade de restringir o acesso do usuário da classe a determinados atributos e métodos, disponibilizando somente o essencial a ele. Este conceito é conhecido como **encapsulamento**, o qual também está relacionado a outro, conhecido como **abstração**. A abstração é ato de esconder do usuário detalhes internos da implementação de um código, fornecendo somente informações sobre a funcionalidade desse conjunto de instruções.

 No Fortran, um aliado importante das classes é o bloco `module`. A estrutura `module` permite declarar variáveis, procedimentos e classes dentro do bloco definido por ela. Entretanto, o que a torna uma excelente companheira das classes é a possibilidade que controlar o acesso dos dados declarados dentro dela com o uso das palavras `private` e `public`. **Em um módulo, os dados são públicos por padrão**. Logo, é possível realizar o encapsulamento de dados, bem como construir camadas de abstração de forma organizada e modular com o uso da estrutura `module`. Um exemplo de uso do `module` para a classe `onibus` definida anteriormente é:

 ```Fortran

module transporte
    implicit none

    ! Tornar dados e procedimentos do módulo privados
    private
    
    ! Tornar somente classe onibus como pública
    public :: onibus
    
    ! Classe onibus
    type :: onibus
        ! Atributos
        integer       :: capacidade_maxima
        integer       :: numero_de_passageiros
        logical       :: estado_da_porta
        character(20) :: proxima_parada

        ! Métodos
        contains
            procedure :: andar
            procedure :: parar
            procedure :: abrir_porta
            procedure :: verificar_numero_passageiros
            procedure :: anunciar_proxima_parada
    end type onibus

    contains
        subroutine andar (self)
            <definição do procedimento>
        end subroutine andar

        subroutine parar (self)
            <definição do procedimento>
        end subroutine parar

        subroutine abrir_porta (self)
            <definição do procedimento>
        end subroutine abrir_porta

        subroutine verificar_numero_passageiros (self)
            <definição do procedimento>
        end subroutine verificar_numero_passageiros

        subroutine anunciar_proxima_parada (self)
            <definição do procedimento>
        end subroutine anunciar_proxima_parada

end module transporte
```

Existem diversas formas de escrever um módulo e uma classe em Fortran. Algumas delas serão mostradas nesta documentação, entretanto com o que foi mostrado acima é possível perceber qua o Fortran possui as funcionalidades necessárias para uma implementação baseada em POO.
