---
layout: post
title: Font-face, evitando faux rendering
date:  2014-05-25 01:25:27
tags: css font frontend
categories: css

---


Um dos grandes possibilidades abertas com criação do [CSS level 3](http://www.w3.org/Style/CSS/current-work.en.html) são
as melhorias na tipografia. E sem dúvida a [regra](https://developer.mozilla.org/en-US/docs/CSS/At-rule) [@font-face](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face) foi um dos marcos dessa evolução. 

No entanto, passa despercebido um erro comum em algumas implementações, muitas delas geradas por [ferramentas de automação](http://www.fontsquirrel.com/fonts/open-sans) disseminando ainda mais esse erro.

Ele ocorre quando é dado nomes diferentes para mesma fonte, em declarações diferentes de `@font-face`, com o intuito de criar variantes(ex: regular, italic, bold).

    {% highlight css %}
    @font-face {
      font-family: MyFontNameRegular;
      src: local("OpenSans"),
      url(OpenSans.ttf);
    }    
    @font-face {
      font-family: MyFontNameItalic; 
      src: local("OpenSans"),
      url(OpenSansItalic.ttf);
    }
    {% endhighlight %}
    
Posteriormente, essas definições são usadas conforme:

    {% highlight css %}
    .some-class {
      font-family: MyFontNameRegular;
      font-size: 12px
      /*...*/
    }
    {% endhighlight %}
    
    
O que leva aos problemas:

-  Uso incorreto da regra `@font-face`
-  Manutenção complicada
-  Renderização de [faux-italic e/ou faux-bold ](http://css-snippets.com/web-fonts-faux-bold-and-italic/)
    - Para piorar, cada browser tem um política diferente de renderização desses casos.


Todos estes tem a mesma origem: a regra `@font-face` foi criada com suporte nativo à variantes, sendo desaconselhado a criação de nomes diferentes para mesma fonte(por isso as pessoas criam nomes como MyFontRegular, MyFontItalic, etc).

Quando a declaração é feita corretamente, podemos usar os recursos nativos, tais como `font-weight` e `font-style` para aplicar variações na fonte.

Seguindo a especificação, declaramos a mesma fonte com suas variações em múltiplas declarações `@font-face`, dessa vez mantendo o nome.

    {% highlight css %}
    @font-face {
      font-family: MyFontName;
      src: local("OpenSans"),
      url(OpenSans.ttf);
      font-style: normal;
    }    
    @font-face {
      font-family: MyFontName; 
      src: local("OpenSans"),
      url(OpenSansItalic.ttf);
      font-weight: normal;
      font-style: italic;
    }
    @font-face {
      font-family: MyFontName; 
      src: local("OpenSans"),
      url(OpenSansRegularBold.ttf);
      font-weight: bold;
      font-style: normal;      
    }
    {% endhighlight %}

Podemos agora usar os recursos nativos para acessar as variantes:

    {% highlight css %}
    .some-class {
      font-family: MyFontName;
      font-style: italic
      /*...*/
    }
    {% endhighlight %}
    
    
Evitamos assim todos os problemas citados anteriormente e ainda ganhamos renderização uniforme em browsers modernos.

Como dica final: use geradores/conversores como o font-squirrel para facilitar a criação de tipos de arquivos necessarios para cada sistema, no entanto revise sempre como o css é gerado.

O [módulo de fonts do CSS 3](http://www.w3.org/TR/css3-fonts) ainda esta em densenvolvimento, é sempre bom estarmos atentos as mudanças especificação e em como os browsers estão implementando.


Para uma referencia mais completa, [o tableless tem um artigo](http://tableless.com.br/font-face-fonts-externas-na-web/), onde esse problema já era mencionado em 2010.

### Fontes:
 - http://www.w3.org/TR/css3-fonts/#the-font-face-rule
 - https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face

