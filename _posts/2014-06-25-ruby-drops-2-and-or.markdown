---
layout: post
title: "Ruby Drops #2: And, or,  && e || "
description: "Qual a diferença entre and e &&, or e ||."
date:  2014-06-25 22:36:27
tags: ruby drops
categories: ruby

---

Um daqueles curiosos casos, gerador de dúvidas e problemas, certamente é a diferença entre os operadores `&&`/`and` e `||`/`or`.

Ja li inúmeros artigos esclarecendo o uso correto, mas tendendo a complicar demais algo que por si só já gera bastante problemas.

Fazendo jus ao nome, *ruby drop*, aqui vai uma resposta simples direta:

* `&&` e `||` são operadores booleanos/lógicos, tem precedência alta.
* `and` e `or` são operadores de fluxo, tem precedência baixa.


O tipo de operador e sua precedência definem como devemos usar cada um. 

As versões textuais(`and` e `or`) deve ser usadas para controlar o fluxo de execução, então se eu quero que algo seja executado em seqüência(`and`) ou de modo alternativo(`or`). Uso as versões textuas:

    {% highlight ruby %}
      # rodo a segunda se a primeira for bem sucedida
      esse_metodo and essa_outra
      
      # roda a segnda se a primeira nao foi bem sucedida(nil ou false) 
      essa_metodo or essa_outra 
    {% endhighlight %}
    

As versões simbólicas(`&&` e `||`) deve ser usadas para operações lógicas, também conhecidas como comparações booleans. 

    {% highlight ruby %}
      if algo && alguma coisa
      
      if algo || alguma coisa        
    {% endhighlight %}
    

Essa explicação é simples mas eficiente, use uma forma para o fluxo da aplicação e outra operações lógicas.


O que achou desse Drop? Quer mais exemplos? Que outros partes do Ruby você tem interesse? Comente aqui embaixo!





 






