---
layout: post
title: "Ruby Drops #3: include vs extend"
description: "Quais as diferenças e melhoras praticas no uso de include e extend"
date:  2015-01-07 22:36:27
tags: ruby drops
categories: ruby

---

### Origens

Uma explicação TL;DR normalmente ficaria assim:

>`include` adiciona métodos a uma instancia, enquanto `extend` adiciona
métodos de classe.

Certo? Nem tanto. Aqui estamos misturando um idioma comum no desenvolvimento
ruby com a definição real desses dois métodos.

`include` [[ruby-doc](http://ruby-doc.org/core-2.2.0/Module.html#method-i-include)]
 inclui(sic) nas instancias da classe destino constantes, métodos e variaveis
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

`extend` [[ruby-doc](http://ruby-doc.org/core-2.2.0/Object.html#method-i-extend)]
de forma similar, adiciona métodos a instancia, mas somente uma instancia, isso
fica claro ao saber que `extend` é parte da ruby core class `Object` a qual
todos os objetos ruby herdam, por padrão.

usando extend:


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

Em suas implementações, `include` adiciona a definição de uma módulo a todas as
instancias de uma classe em sua própria definição enquanto `extend` adiciona o
módulo diretamente a um objeto.

Aquela definição inicial, como dito esta quase certa, podemos usar `extend`
para definir metodos de classe assim:

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

Assim descobrimos a meia-verdade na explicação anterior, `extend` pode ser
usada para adicionar métodos de classe, mas isso é uma parte de seu uso,
onde podemos até mesmo imitar o funcionamento do `include`.


chegamos então a ...

### Um idioma ruby

Provalvelmente você ja topou com variações de código assim:

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

Esse código usa um hook provido na definição de `Module`, o `included`.
Ele é chamado quando o módulo é incluído a uma classe.

Através do argumento `base`, temos acesso essa classe alvo, onde o módulo
esta sendo incluido, o que permite usarmos o extend para adicionar metódos
de classe.

Se você estiver se perguntando "Mas calma, o extend não era um método de `Object`?"
Sim, mas temos que lembrar que o argumento `base` é a propria classe, seu objeto
e não uma instancia.

Lembre-se ainda que em Ruby tudo são objetos, incluindo as proprias classes e modulós.
Por isso, ao extendermos o objeto da classe, estamos adicionado métodos a classe
e não as suas instancias

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

`extend`, por estar definido no pai de todos `Object` funciona
em tudo que é lugar, módulos, instancias, classes.


### Active Support Concern

O módulo rails `ActiveSupport::Concern` facilita a criação desse módulos,
com um dsl simplifada e resolução de dependencias integrada.

``` ruby
require 'active_support/concern'

module NightWatcher
  extend ActiveSupport::Concern

  included do
     # aqui tu pode chamar metodos de classe diretamente
     # mas  nao precisa mais incluir ClassMethods
     # basta definir, como feito abaixo, que o AS::Concern
     # cuida do resto.
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

Em caso de dependencias entre modulos, tu não precisa incluir diretamente na
classe destino, Concern vai resolver essas  dependencias.

Ao invés disso:

``` ruby
module Um
end

module Dois
  include Um
end

class Preciosa
  include Um
  include Dois
end
```

Faz assim:
Ao invés disso:
``` ruby
require 'active_support/concern'

module Um
  extend ActiveSupport::Concern
end

module Dois
  extend ActiveSupport::Concern
  include Um
end

class Preciosa
  include Dois # note que agora podemos incluir somente o que precisamos
               # e o Concern vai resolver a dependencia.
end
```

Existe algum movimento [contra](http://blog.coreyhaines.com/2012/12/why-i-dont-use-activesupportconcern.html)
o [uso](http://mcdowall.info/posts/the-great-satan-rails-concerns/) de concerns, principalmente por seu
gerenciamento implicito de dependencias, que traz um code smell para a forma
que estamos usando mixins no ruby.

Deve-se evitar modúlos que dependam de outros módulos, e o concerns acaba
incentivando/facilitando esse tipo de uso.

Existem bons casos de uso para concerns, como mostrado pelo DHH em [ Put chubby models on a diet with concerns ](https://signalvnoise.com/posts/3372-put-chubby-models-on-a-diet-with-concerns)

Avalie com calma quando e onde usar cada, evite dependencia entre módulos.

Include/Extend e Concerns trazem muitos recursos interessantes, sendo a base
de Ruby Mixins.

