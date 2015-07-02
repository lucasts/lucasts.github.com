---
layout: post
title: "Uma olhada no Lotus framework, parte 1"
description: "Descubra como é criar um projeto com o Lotus, parte 1"
date:  2015-07-01 10:00:00
tags: ruby lotus
categories: ruby
---

[Lotus ou Lotus.rb](http://lotusrb.org/) é um web framework ruby lançado por [Luca Guidi](http://lucaguidi.com) em [junho de 2014](http://lucaguidi.com/2014/06/23/introducing-lotus.html)

É um Fullstack framework, composto de pequenas bibliotecas bem forcadas e isoladas.

Muita discussão foi criada desde o lançamento, sobre a capacidade de atingir a maturidade do Rails, se ganharia apoio da comunidade e até mesmo se era necessário. Não vou entrar nesse ponto neste artigo. O fato é que nesses ultimos meses o projeto tem crescido dentro da comunidade e garantido um espaço para seguir evoluindo.

O que vamos ver aqui é uma passada de olho em como é criar um projeto com o Lotus, usando como exemplo uma webapp para votos no programa SuperStar da rede globo de tv. Teremos bandas, jurados e público em geral. Simplificando as regras do programa, teremos voto do público valendo um ponto para cima ou para baixo, e voto dos jurados valendo 10 pontos.

### Instalando o Lotus

Contando com um ambiente ruby moderno(>2) previamente instalado. Instale a gem do Lotus:

``` bash
gem install lotusrb
```

_Não confundir com gem `lotus`_

### Criando o projeto

Use o generator do próprio:
``` bash
lotus new superstar --database=sqlite
```

Tu vai ver um output similar a este:

``` bash
create  superstar/.lotusrc
create  superstar/.env
create  superstar/.env.development
create  superstar/.env.test
create  superstar/Gemfile
create  superstar/config.ru
create  superstar/config/environment.rb
create  superstar/lib/superstar.rb
create  superstar/lib/config/mapping.rb
create  superstar/Rakefile
create  superstar/spec/spec_helper.rb
create  superstar/spec/features_helper.rb
create  superstar/db/schema.sql
create  superstar/lib/superstar/entities/.gitkeep
create  superstar/lib/superstar/repositories/.gitkeep
create  superstar/db/migrations/.gitkeep
create  superstar/spec/superstar/entities/.gitkeep
create  superstar/spec/superstar/repositories/.gitkeep
create  superstar/spec/support/.gitkeep
create  superstar/.gitignore
   run  git init ./superstar from "."
insert  superstar/config/environment.rb
insert  superstar/config/environment.rb
append  superstar/.env.development
append  superstar/.env.test
create  superstar/apps/web/application.rb
create  superstar/apps/web/config/routes.rb
create  superstar/apps/web/views/application_layout.rb
create  superstar/apps/web/templates/application.html.erb
create  superstar/apps/web/controllers/.gitkeep
create  superstar/apps/web/public/javascripts/.gitkeep
create  superstar/apps/web/public/stylesheets/.gitkeep
create  superstar/spec/web/features/.gitkeep
create  superstar/spec/web/controllers/.gitkeep
create  superstar/spec/web/views/.gitkeep
```

Se tu estiver ambientado com projetos Rails, o próximo passo não é muito incomum.
Instale as gems no `Gemfile` usando `bundle`:

``` bash
cd superstar
bundle install
..
Fetching gem metadata from https://rubygems.org/.........
Fetching version metadata from https://rubygems.org/..
Resolving dependencies...
Using rake 10.4.2
Using bundler 1.10.5
Installing mime-types 2.6.1
Installing mini_portile 0.6.2
Installing nokogiri 1.6.6.2 with native extensions
Using rack 1.6.4
Installing rack-test 0.6.3
Installing xpath 2.0.0
Installing capybara 2.4.4
Using dotenv 2.0.2
Using url_mount 0.2.1
Using http_router 0.11.1
Using lotus-utils 0.5.0
Using lotus-validations 0.3.2
Using lotus-controller 0.4.4
Using lotus-helpers 0.2.0
Installing sequel 4.23.0
Installing lotus-model 0.4.0
Using lotus-router 0.4.1
Using tilt 2.0.1
Using lotus-view 0.4.2
Using shotgun 0.9.1
Using thor 0.19.1
Using lotusrb 0.4.0
Installing minitest 5.7.0
Installing sqlite3 1.3.10 with native extensions
Bundle complete! 7 Gemfile dependencies, 26 gems now installed.
Use `bundle show [gemname]` to see where a bundled gem is installed.
```

Antes de testar o web server , de uma olhada no Gemfile

``` ruby
source 'https://rubygems.org'

gem 'bundler'
gem 'rake'

gem 'lotusrb',       '0.4.0'
gem 'lotus-model',   '~> 0.4'

gem 'sqlite3'

group :test do
  gem 'minitest'
  gem 'capybara'
end

group :production do
  # gem 'puma'
end
```

Como tu pode ver, poucos gems são requeridas diretamente, a gem `sqlite3` para acesso ao banco que escolhemos, `lotusrb` que é a gem base do Lotus e `lotus-model`(que é opcional). Ainda uma base para criação de testes com `minitest` e `capybara`.

Mesmo com as gems instaladas em cascata, o total é de apenas 26 gems com o projeto em seu estado inicial.

Antes de criar o primeiro model, um esclarecimento. _Não vamos aqui criar testes e vamos usar arquitetura padrão `container`._

Ambos os tópicos, testes e arquitetura, são centrais ao desenvolvimento com Lotus, mas esse artigo ficaria ainda mais extenso se falarmos deles.  Enquanto testes são similares aos que fariamos em apps Rails e Sinatra, o suporte nativo a diferentes tipos de arquitetura é bem interessante e vale uma leitura com atenção.

Documentação oficial [sobre diferentes arquitetura no Lotus](http://lotusrb.org/guides/architectures/overview/)

### O primeiro model

Vamos iniciar descrevendo um model para as bandas:

``` bash
bundle exec lotus generate model band
  create  lib/superstar/entities/band.rb
  create  lib/superstar/repositories/band_repository.rb
  create  spec/superstar/entities/band_spec.rb
  create  spec/superstar/repositories/band_repository_spec.rb
```

Aqui ja temos duas particularidades do Lotus.

A primeira  é  models e repositories ficarem em `lib/` ao invés do comum `app/models`.

Este é um conceito importante para o Lotus, em `apps/` reside o código responsavel pelas formas de entrega através de uma ou mais aplicações, o modelo de negócios fica no diretório `lib/`.  A forma que o Lotus  armazena as aplicações no diretório `apps` depende da arquitetura Lotus que escolhida ao gerar o projeto. Na arquitetura padrão `Container` pode-se servir inúmeras aplicações Lotus(e/ou Rack). A outra arquitetura, `Application` é dedicada para aplicações menores, focadas em um aspecto só que pode existir sozinha ou agregada em outro projeto que usa a arquitetura `Container`. Mais sobre a arquitetura `Application` em um futuro artigo.

Imagine diversas formas que podemos ver nosso modelo de dados: website, api, site mobile, painel de stats. Todas essas `apps` baseadas no modelo de negócio definido em `libs/`.

O segundo conceito é que o Lotus separa a regra de negócio(models) da persistência dos dados(repositories). É criado um model 'band' que inclui `Lotus::Entity`

``` ruby
class Band
  include Lotus::Entity
end
```

que nos da uma interface simples para identificar uma determinanda entidade usando `#id` e inicializar um instância recebendo `attributes`. Ganha-se ainda o metodo `.attributes` para definir de maneira mais rápida atributos para a entidade/model.

O template do repository gerado é tão simples quanto o model

``` ruby
class BandRepository
  include Lotus::Repository
end
```

que apenas inclui `Lotus::Repository`, que nos da acesso a uma gama de metodos query e [CRUD](https://pt.wikipedia.org/wiki/CRUD) da entidade.

Aqui temos outra decisão interessante do Lotus, todos os metódos de query(ex:  `find`, `where`, `first`, etc..) são privados. A idéia é que nenhuma API seja exposta sem clara intenção do dev e ele deve definir o que vai expor, preferencialmente [escondendo a implemetação e APIs de libs usadas internamente](http://c2.com/cgi/wiki?IntentionRevealingNames).

Até o momento temos o model e seu repository correspondente, falta ainda criar o mapping entre o model(entidade) e o repository(persistência) descrevendo como queremos que a persistência dos dados na base seja feita. Esse mapping usa em seu core a pattern [Data Mapper](https://en.wikipedia.org/wiki/Data_mapper_pattern).

Para informamos o mapeamento de `band`, podemos usar o arquivo configuração da app `lib/superstar.rb` ou no arquivo de mapeamento `lib/config/mapping`(você precisara descomentar a linha que carrega o arquivo de mapping no `lib/superstar.rb`).

Mantendo a simplicidade, usaremos o arquivo de config da app `lib/superstar.rb`. Identifique o block de mapping dentro do arquivo, que deve estar similar a:

``` ruby
mapping do
  # ..
end
```

e atualize o bloco informando os campos que queremos para banda:

``` ruby
mapping do
  collection :bands do
    entity     Band
    repository BandRepository

    attribute :id,   Integer
    attribute :name, String
  end
end
```

volte ao model e adicione o atributo `name`

``` ruby
class Band
  include Lotus::Entity
  attributes :name
end
```

Estamos quase lá, falta apenas criarmos a tabela no banco.
Mas antes disso, ja podemos testar nosso model no console do Lotus.

``` bash
› bundle exec lotus console
irb(main):001:0> metallica = Band.new(name: 'Metallica')
=> #<Band:0x007fe892241080 @name="Metallica">
irb(main):002:0> metallica.id
=> nil
irb(main):003:0> metallica.name
=> "Metallica"
irb(main):004:0> metallica
=> #<Band:0x007fe892241080 @name="Metallica">
irb(main):005:0> metallica.save
NoMethodError: undefined method `save` ..
irb(main):006:0> BandRepository.create(metallica)
Sequel::DatabaseError: SQLite3::SQLException: no such table: bands

```

Nada muito extravagante, mas ja conseguimos instânciar nosso model.
Repare que na instrução 005 `metallica.save` não temos o comum `save`. Na instrução seguinte, 006,
tentamos criar o registro na base usando o repository, mas ele corretamente nos informa que ainda
não criamos a tabela `bands`. Vamos fazer isso!

Crie a migration vazia:

``` bash
bundle exec lotus generate migration create_books
    create  db/migrations/20150630205526_create_bands.rb
```

Edite o arquivo da migration para criar a tabela e o campo `name`:

``` ruby
Lotus::Model.migration do
  change do
    create_table :bands do
      primary_key :id
      column :name, String, null: false
    end
  end
end
```

Rode a migration

``` bash
bundle exec lotus db migrate
```

Agora podemos voltar ao console e testar a persistência:

``` bash
bundle exec lotus console
irb(main):001:0> queen  = Band.new(name: 'Queen')
=> #<Band:0x007fa3d3289208 @name="Queen">
irb(main):002:0> BandRepository.create(queen)
=> #<Band:0x007fa3d324bc50 @name="Queen", @id=1>
irb(main):003:0> BandRepository.first
=> #<Band:0x007fa3d32383f8 @name="Queen", @id=1>
irb(main):004:0> BandRepository.find 1
=> #<Band:0x007fa3d1358730 @name="Queen", @id=1>
irb(main):005:0> BandRepository.all
=> [#<Band:0x007fa3d133bcc0 @name="Queen", @id=1>]
```


Persistência funcionando! Depois de alguns passos e considerações pelo caminho.

Relembrando o que fizemos até aqui:
1. Criamos o projeto
2. Criamos nosso primeiro modelo com seu repositório
3. Informamos como mapear o model/entidade de Banda para persistir no db.
4. Criamos uma migration e a rodamos.

Apesar do artigo ter ficado longo, os passos simples e com alguns detalhes analisados, pode-se um pouco
melhor os conceitos por trás do Lotus.

Este artigo fica por aqui, no próximo da série vamos acelerar o passo e criar o
restante do modelo de dados(lembre-se, tudo em `lib/`) e então partir para
uma aplicação dentro do projeto de exemplo.

Ficou com alguma dúvida? não gostou? Achou um erro?  Comente!
