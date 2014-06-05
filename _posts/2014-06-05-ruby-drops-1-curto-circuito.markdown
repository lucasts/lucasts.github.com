---
layout: post
title: Ruby Drops #1: Curto Circuito(Short Circuit)
date:  2014-06-5 01:25:27
tags: ruby drops
categories: ruby

---

Curto circuito, ou em inglês _short circuit_ é um interessante recurso de
escrita de código, que permite refatoração, simples e eficiente.

Em ruby ele  é uma instrução condicional com duas expressões, onde a segunda só é executada caso
a primeira seja verdadeira, insuficiente para saber o resultado completo da instrução

No código a seguir, a segunda parte da expressão(`puts 'Oi'`) não sera executada:
    {% highlight ruby %}
      var_ou_exp_falsa = false
      var_ou_exp_falsa and puts('Oi')
    {% endhighlight %}

Logo, em código com encadeamento de instruções condicionais como a seguir:

    {% highlight ruby %}
      has_ring = false
      at_mordor = false

      if has_ring
        if at_mordor
          throw_ring_on_lava
        end
      end
    {% endhighlight %}

Pode se reescrito desta forma:

    {% highlight ruby %}
      has_ring = false
      at_mordor = false

      if has_ring && at_mordor
        throw_ring_on_lava
      end
    {% endhighlight %}


Isso também pode aplicado aos operadores `||`/`or`.

Assim, short-circuit:

 - `&&`/`and`: segunda expressão é executada/verificada caso a primeira seja verdadeira
 - `||`/ `or`: segunda expressão é executada/verificada caso a primeira seja falsa


O rubyista atento notou o uso de `and` para controlar o fluxo no primeiro snippet e `&&` na instrução condicional do terceiro.
Isso se deve a recomendação comum de uso, vinda da diferença de precedência entre as versões textuais e simbólicas

Mais sobre isso no próximo _Drop_.




