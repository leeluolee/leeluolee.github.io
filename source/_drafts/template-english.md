
Templating is a technology that help us to represent data in different forms. 


In the old days, choosing a appropriate template engine for client templating is not easy, beacuse you were left with little other choice. Nowadays, choosing templating engine is still a big probelm, beacuse there are so many template engines and most of them seems do nothing different([Template-Engine-Chooser!](http://garann.github.io/template-chooser/) comes).


In this article, from a principle perspective ,we will have __A comprehensive comparison of front-end templating solutions__. there are some distinct types of solution that will be mentioned.

1. String-based templating (String-based parser & compiler)
2. Dom-based templating (Dom-based link & compiler)
3. Living templating (combine String-based parser with Dom-based compiler)
4. Other:  [Coffeekup: Inner DSL based on coffeescript syntax](https://github.com/mauricemach/coffeekup)

The article won't  take the fourth type into detail, except for [React](https://github.com/facebook/react). But you will find that react is very similiar with Living templating. beacuse all them is compeletely independent with `innerHTML`.


Before diving into detail, let's talking about __`innerHTML`__ first.

## innerHTML

`innerHTML` is the key through this post，so we need to have a brief review on it. But i don't think there is any necessity to bring `innerHTML` into detail, since we are all familiar with it. so let's talk about the pros &amp; cons directly.

### innerHTML is good, without doubt

Before `innerHTML` becomes [web standard](https://domparsing.spec.whatwg.org/#innerhtml), it has been a conventional "practical standard" for serveral years beacuse its irreplaceable advantages. for example.

__1 . easy to code and intuitive to view__

imagine that you need to append a html like that.
```html
<h2 title="header">title</h2>
<p>content</p>
```

use `innerHTML`

```javascript
node.innerHTML = "<h2 title="header">title</h2><p>content</p>"
```
compare with the way using `Dom API`

```javascript
var header = document.createElement('h2');
var content = document.createElement('p');
h2.setAttribute('title', 'header');
h2.textContent = 'title';
p.textContent = 'content';
node.appendChild(header);
node.appendChild(content);
```

`innerHTML` obviously win the game.

Although some frameworks like [mootools:Element](http://mootools.net/docs/core/Element/Element#Element:constructor) provide some more efficient way to constructing HTML with `Dom API`, `innerHTML` is still the most intuitive way.

__2 . it is faster，especially [in old IE](http://www.quirksmode.org/dom/innerhtml.html)__

   &gt; the test maybe out of date in modern browser, the difference between `innerHTML` and `Dom Level 1` is become smaller and smaller.

But we also learned: _The recommended way to modify the DOM is to use the DOM Level 1 API._

A great reference to this is Chapter 15 of "Javascript: The Definitive Guide". Why?

### innerHTML is disobedient, sometimes

__1. security issues__
`innerHTML` has more security issues , especially if you don't sanitize what you're putting into it. for example
```
document.body.innerHTML = "<img src=x onerror='alert(xss)'/>"
```
I know you won't code like this, but if the HTML is not compeltely controlled by yourself(for example : from remote server), it will be a big issue.

__2. it is slow__
Indeed, I mentioned `innerHTML` is fast. if you just need change a attribute but replacing all DOM nodes with `innerHTML` completely, it would be obviously inefficient.

_Context is everything_

__3. not smart enough__
    it disconnect all existing DOM nodes and rerendering again, all events and state in     previous DOM nodes is gone.
    
__4. potential for generating invalid markup with invalid markup.__
    html parser is so "friendly", it even [accpet invalid html](http://stackoverflow.com/questions/25559999/why-arent-browsers-strict-about-html),  but developer wont get any 'parse error 'during the parsing.


_Maybe it's not innerHTML that is the problem, but constructing HTML with string operations is._


we have thought through `innerHTML` already, it is time to talk about "templating solutions" now.

## String-based templating

> It is essentially a way to address the need to populate an HTML view with data in a better way than having to write a big, ugly string concatenation expression.
--- cited from [http://www.dehats.com/drupal/?q=node/107](http://www.dehats.com/drupal/?q=node/107)

String-based templating is the most common solution we ever used. beacuse frontend templating is derivatived from backend,  in server side the output must be a string , so the browser can render it. 


__Example__

1. mustache: less-logic support
2. Dust.js: rich-logic support
3. doT.js: super fast

__The basic process__

<a href="http://modernweb.com/wp-content/uploads/2014/09/String-based-Template.png"><img src="http://modernweb.com/wp-content/uploads/2014/09/String-based-Template.png" alt="String-based Template" width="540" class="alignnone size-medium wp-image-3150" /></a>

As shown above, string-based templating is tightly coupled with `innerHTML` (for Rendering).

__pros__

1. Faster initialize time
2. Isomorphic: support rendering on server-side and client-side.
beacuse this solution is compeletely dom-independent.
3. More powerful template-logic support (all depend on the design of your DSL )

__cons__

1. security issue: see `innerHTML` section
2. not smart enough: see `innerHTML` section
3. performance issue when updating.
Although the string-based template is become more and more faster beacuse of the intense competition,we need also to take into account the time taken to load the template output into the DOM which is actually [the real bottleneck](https://github.com/leonidas/transparency/wiki/Defining-template-engine-performance).

## Dom-based Template Engine

In recent years, dom-based begun to pop up, the prime example is Angular that earned almost 28000 stars in github. 


__Example__

1. Angularjs: most popular one
2. Knockout: the early one
3. Vuejs: upstart, more concise and aiming to build interactive UI, enough is as good as a feast.



__The basic process__


<a href="http://modernweb.com/wp-content/uploads/2014/09/Dom-based-Template.png"><img class="alignnone size-medium wp-image-3143" src="http://modernweb.com/wp-content/uploads/2014/09/Dom-based-Template.png" alt="Dom-based Template" width="540" /></a>

dom-based template doesn't have their own parser, so if you need creating view from a template string, you have to use `innerHTML` to convert the string to dom(parsing), then walk the dom tree using the Dom API(`attributes`, `getAttribute`, `firstChild`... etc). All information like directives is hold by the dom node and its attributes.

In fact, the whole process is more like reshaping than rendering. 

__pros__

1. output dom is Living.
2. is __runtime__ efficient.
2. using directive(or other similar concepts), the coding  style is pure declarative, just like you writing html. 

__cons__

1. have no parser themselves, syntax is restricted by dom and is hard to embed logic in it.
2. also have security issues beacuse using of `innerHTML`.
3. will have some useless placeholder on generated dom , beacuse dom-based template need this information to act operation. for example, if you [inspect the angular's todomvc](http://todomvc.com/architecture-examples/angularjs/)， you can some placeholder (`ng-show`, etc) in every nodes.





## Living Template Engine


string-based and dom-based template are all tightly coupled with `innerHTML`, the differrence is: String-based template use `innerHTML` for __Rendering__ and Dom-based use it for __Parsing__.


__Why not combining String-based parser and Dom-based compiler to abate the dependence on `innerHTML` ?__


In fact, there have been servaral templates that realized in this way .

__Example__

1. [htmlbar](https://github.com/tildeio/htmlbars): built on top of Handlebars template compiler.
2. [ractivejs](https://github.com/ractivejs/ractive): standalone
3. [Regularjs](https://github.com/regularjs/regular) standalone


__The basic process__

<a href="http://modernweb.com/wp-content/uploads/2014/09/Living-Template.png"><img class="alignnone size-medium wp-image-3144" src="http://modernweb.com/wp-content/uploads/2014/09/Living-Template.png" alt="Living Template" width="540" /></a>

As shown in the picture above, parsing and compiling are similar with String-based template and dom-based template respectively

### 1 . Parsing

First. it use a builtin parser to parse the template string then output a AST. 

for example, template string(syntax base on [regularjs](https://github.com/regularjs/regular))

```html
<button {{#if !isLogin}} on-click={{this.login()}} {{/if}}>
  {{isLogin? 'Login': 'Wellcome'}}
</button>'
```

will be parsed to:

```javascript
[
  {
    "type": "element",
    "tag": "button",
    "attrs": [
      {
        "type": "if",
        "test": {
          "type": "expression",
          "body": "(!_d_['isLogin'])",
          "constant": false,
          "setbody": false
        },
        "consequent": [
          [
            {
              "type": "attribute",
              "name": "on-click",
              "value": {
                "type": "expression",
                "body": "_c_['login']()",
                "constant": false,
                "setbody": false
              }
            }
          ]
        ],
        "alternate": []
      }
    ],
    "children": [
      {
        "type": "expression",
        "body": "_d_['isLogin']?'Login':'Wellcome'",
        "constant": false,
        "setbody": false
      }
    ]
  }
]

```

1. it is very similar with the string-based template, so we can use more powerful syntax (it is all depend on the DSL you defined). 
2. string-based templates only parse the "dsl element" and consider the "xml element" as the "text". but in living template, we need parse the "xml" together with the "dsl element" to make it be __dom-aware__. beacuse we need the infomation for creating living dom.
3. unlike Dom-based templating, instead of real dom, the AST holds the all information we needed(statement, directive, attributes and tagname...etc). 
  1. it is more **lightweight**, setter and getter on dom is expensive.
  2. it is **reusable**.
  3. it can **be serialized** , so you can preparse it on server.
4. only output the necessary part.
  compare with dom-based template, living template's output is more clean. [inspect regularjs's todomvc on codepen.io](http://codepen.io/leeluolee/pen/dGxCb)

  ```javascript
  <ul id="todo-list">
    <!--Regular list-->
    <li class="completed">
      <div class="view">
        
        <label>sleep</label>
        <button class="destroy"></button>
      </div>
      
    </li>
  </ul>

  ```

  
### 2 Compiler

with spcified model (in regularjs, it is a plain object), template engine walks the AST and generating the dom recursively, meanwhile, according to the directive and other binder(event, inteplation... etc), it also create the binding between model and dom to **make the dom living**. 

for example, just like the inteplation `{{isLogin? 'Login': 'Wellcome'}}` showed above.  once the compiler seen it, the `expression` walker will be called.

```javascript
// some source from regularjs

walkers.expression = function(ast){
  var node = document.createTextNode("");
  this.$watch(ast, function(newval){
    dom.text(node, "" + (newval == null? "": String(newval)));
  })
  return node;
}

```

as shown above, once the expression changed, node.textContent(or innerText) will changes synchronous.

Compare to string-based template, instead of `innerHTML`, it use `DOM` api(createElement, setAttribute, createTextNode...etc ) to generate the dom. so it is safe.

In fact, in compiling phase, the most difference between Living template and dom-based template is: __dom-based template act a reshaping on dom nodes, But living template is building that according to the resuable AST.



### living template's clever brother —— React

React can be considered as a templating solution，it avoid be coupled with `innerHTML` by using virtual dom which is created by nested function call(you can also use jsx syntax)

__Example__

```
var MyComponent = React.createClass({
 render: function() {
   if (this.props.first) {
     return <div className="first"><span>A Span</span></div>;
   } else {
     return <div className="second"><p>A Paragraph</p></div>;
   }
 }
});

```

which in regularjs

```html
{{#if first}}
  <div className="first"><span>A Span</span></div>
{{#else}}
  <div className="second"><p>A Paragraph</p></div>;
{{/if}}

```

__Every one thinks in his way__, And I prefer using template to describe my structure, do you?


## A comparison table

__Warning: __


| Contrast /Solutions  |  String-based templating | Dom-based templating  | Living templating|
| -------------|-------------             | ---------| ------------- |
|Example       |  Mustache,Dustjs|Angularjs, Vuejs  |Regularjs 、Ractivejs、htmlbars|
|Syntax| &diams;&diams;&diams;| &diams; | &diams;&diams;&diams; |
|Living Dom| X | &diams;&diams;&diams; | &diams;&diams;&diams; |
|Security| &diams;| &diams;&diams; | &diams;&diams;&diams; |
|SVG support(*1)| X | &diams;&diams; | &diams;&diams;&diams; |
|Dom independent| &diams;&diams;&diams; | X | &diams;&diams; |
|Server Rendering | &diams;&diams;&diams; | &diams; | &diams; |

1. no one can compeletely replace another one. 
2. They are not necessarily incompatible, for example, you can use string-based template engine to generate template string for dom-based template.





### Reference
1. [Template Engines by @Sendhil](http://codingarchitect.wordpress.com/2012/10/22/template-engines/)
2. [string-templating-considered-harmful](http://modernweb.com/2014/03/24/string-templating-considered-harmful/)


