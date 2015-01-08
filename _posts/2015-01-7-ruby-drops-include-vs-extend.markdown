---
layout: post
title: "Ruby Drops #3: include vs extend"
description: "Quais as diferenças e melhoras praticas no uso de include e extend"
date:  2015-01-07 22:36:27
tags: ruby drops
categories: ruby

---

### TL;DR

>`include` adiciona métodos a uma instancia, enquanto `extend` adiciona
métodos de classe.

Certo? Nem tanto. Aqui estamos misturando um idioma comum no desenvolvimento
ruby com a definição real desses dois métodos.

### include [[ruby-doc](http://ruby-doc.org/core-2.2.0/Module.html#method-i-include)]

inclui(sic) nas instancias da classe destino constantes, métodos e variáveis
de módulo.

``` ruby
module Jumps
  def jump
    puts 'Jumped'
  end
end

class WildFox
  include Jumps
end

a_fox = WildFox.new
a.jump #> Jumped
```


### extend [[ruby-doc](http://ruby-doc.org/core-2.2.0/Object.html#method-i-extend)]
adiciona métodos um objeto, `extend` é parte da ruby core class `Object` a qual
todos os objetos ruby herdam, por padrão.

um exemplo usando extend:

``` ruby
module Jumps
  def jump
    puts 'Jumped'
  end
end

class WildFox
end

a_fox = WildFox.new
another_fox = WildFox.new
a.extend Jumps
a.jump #> Jumped
another_fox.jump #> NoMethodError
```

### Expandindo o entendimento do `extend`

Aquela definição inicial, como dito esta quase certa, podemos sim usar `extend`
para definir métodos de classe:

``` ruby
module Jumps
  def jump
    puts 'Jumped'
  end
end

class WildFox
  extend Jumps
end

a_fox = WildFox.new
# note que agora não precisamos usar extend na instancia
a.jump #> NoMethodError pois jump agora esta incluida na classe
WildFox.jump #> 'Jumped'
```

Mas isso é só parte de sua aplicação, `extend` pode ser até mesmo imitar o
funcionamento do `include`.

chegamos então a ...

### Um idioma ruby

Provavelmente você já topou com variações de código assim:

``` ruby
module Kind
  def self.included(base)
    base.extend ClassMethods
  end

  module ClassMethods
    def type
      puts 'mammal'
    end
  end
end

class WildCat
  include Kind
end

a_cat = WildCat.new
a.cat.type #> NoMethodError
WildCat.type
```

Usando o hook `included` de `Module` podemos interceptar o momento que o módulo
é incluído a uma classe.

E através do argumento `base`, temos acesso essa classe alvo, onde o módulo
esta sendo incluído, o que então permite usarmos o extend para adicionar métodos
de classe.

Se você estiver se perguntando "Mas calma, o extend não era um método de `Object`?"
Sim, mas temos que lembrar que o argumento `base` é a própria classe, seu objeto
e não uma instancia.

Lembre-se ainda que em Ruby tudo é um objeto, incluindo as próprias classes e módulos.
Por isso, ao fazermos `extend` no objeto da classe, estamos adicionado métodos
a classe e não as suas instancias.

Esse idioma permite definirmos módulos mais completos, com métodos de
instancia e de classe no mesmo módulo.

``` ruby
module NightWatcher
  def self.included(base)
    base.extend ClassMethods
  end

  def instance_level_method
    puts 'instance level'
  end

  module ClassMethods
    def class_level_method
      puts 'class level'
    end
  end
end

class Watcher
  include NightWatcher
end

Watcher.class_level_method #> class level
watcher = Watcher.new
watcher.instance_level_method #> instance level
```
E ai esta o pulo do gato dessa expressão idiomatíca e que mostra o poder do
`extend`, que por estar definido no pai de todos `Object` funciona
em tudo que é lugar: módulos, instancias, classes, etc.

### Active Support Concern

O módulo rails `ActiveSupport::Concern` facilita a criação desse módulos,
com um dsl simplificada e resolução de dependências integrada.

``` ruby
require 'active_support/concern'

module NightWatcher
  extend ActiveSupport::Concern

  included do
     # aqui tu pode chamar metodos de classe diretamente
     # mas  nao precisa mais incluir diretamente ClassMethods
     # basta definir, como feito abaixo, que o AS::Concern
     # cuida do resto.

     # exemplo se fosse um concern para um model Rails
     # has_many :tags
  end

  def instance_level_method
    puts 'instance level'
  end

  module ClassMethods
    def class_level_method
      puts 'class level'
    end
  end
end

class Watcher
  include NightWatcher
end

Watcher.class_level_method #> class level
watcher = Watcher.new
watcher.instance_level_method #> instance level
```

No entanto, existem alguns que defendem
[não](http://blog.coreyhaines.com/2012/12/why-i-dont-use-activesupportconcern.html)
[usar](http://mcdowall.info/posts/the-great-satan-rails-concerns/)
concerns, principalmente por seu gerenciamento implícito de dependências,
que traz um code smell, mas também bons argumentos a favor, como mostrado pelo
DHH em [ Put chubby models on a diet with concerns ](https://signalvnoise.com/posts/3372-put-chubby-models-on-a-diet-with-concerns).

Avalie com calma quando e onde usar. Não a regra de outro, contexto e o projeto
irão podem mudar o ponto de vista de aplicação.

### Por fim
Saber quando usar include e extend, conhecer suas possibilidades e expressões
idiomáticas nos dá uma série de recursos uteis para mantermos em nosso cinto de
utilidades. Entenda seu funcionamento interno.

O Ruby core nos prove uma API poderosa e simples de usar. Use-o, estude-o.
