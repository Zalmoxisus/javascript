# Mithril Style Guide

*A mostly reasonable approach to Mithril*

## Table of Contents

  1. [To read](#to-read)
  1. [Basic Rules](#basic-rules)
  1. [Elements](#elements)
  1. [Components](#components)

## To Read

  - [1.1](#1.1) <a name='1.1'></a> For beginners:
    - [What is MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)
    - [Dave's guide to Mithril.js](http://ratfactor.com/daves-guide-to-mithril-js?/shire)
    - [A Tutorial Introduction (part 1)](http://gilbert.ghost.io/mithril-js-tutorial-1/)
    - [A Tutorial Introduction (part 2)](http://gilbert.ghost.io/mithril-js-tutorial-2/)
    - [Getting Started](https://lhorie.github.io/mithril/getting-started.html)
    - [Introductory Slideshow (in Japanese, but Mithril code clear)](http://www.slideshare.net/ShoyoKyo/js-framework-mithril)
    - [FAQ](https://github.com/lhorie/mithril.js/wiki/FAQ)
  - [1.2](#1.2) <a name='1.2'></a> [Keeping views declarative](http://lhorie.github.io/mithril-blog/getting-over-a-fear-of-turing-complete-templates.html)
  - [1.3](#1.3) <a name='1.3'></a> Integrating with other libraries:
    - [JQuery Select2 Example](http://mithril.js.org/integration.html)
    - [JQuert Modal Example](https://gist.github.com/lhorie/07c61012aaa2f196c9d4)

**[⬆ back to top](#table-of-contents)**

## Basic Rules

  - [2.1](#2.1) <a name='2.1'></a> Use ternary operators instead of if statements whenever possible.

    ```javascript
    // bad
    var widget
    if (someCondition) {
      widget = m('div', 'widget here')
    }

    m('div', [
       widget
    ])

    // good
    m('div', [
       someCondition ? m('div', 'widget here') : ''
    ])
    ```
  - [2.2](#2.2) <a name='2.2'></a> Use `Array.prototype.map()` instead of loops for lists.

    ```javascript
    // bad
    var items = [];
    for (var i = 0; i < someList.length; i++) {
       var tpl = m('div', 'list item ' + i)
       items.push(tpl);
    };

    m('div', items)

    // good
    m('div', [
       someList.map(function(item, i) {
         return m('div', 'list item ' + i)
       })
    ])
    ```

  - [2.3](#2.3) <a name='2.3'></a> Respect the Mithril's mantra: "if something is too noisy or repetitive, you can put the verbosity in a function and call that instead".

    ```javascript
    //data stores
    var firstName = m.prop('');
    var lastName = m.prop('');

    // bad
    m('fieldset', [
      m('input', {oninput: m.withAttr('value', firstName), value: firstName()}),
      m('input', {oninput: m.withAttr('value', lastName), value: lastName()})
    ]);

    // better, but still bad in this case
    //refactor the binding to a simple helper
    var binds = function(prop) {
        return {oninput: m.withAttr('value', prop), value: prop()}
    }
    //binding the data store in a view
    m('fieldset', [
      m('input', binds(firstName)),
      m('input', binds(lastName))
    ]);

    // almost good
    //refactor the binding to a simple helper
    var input = function(prop) {
        return m('input', {oninput: m.withAttr('value', prop), value: prop()});
    };
    //binding the data store in a view
    m('fieldset', [
      input(firstName),
      input(lastName)
    ]);

    // good
    m('fieldset',
      [firstName,lastName].map(function(prop) {
        return m('input', {oninput: m.withAttr('value', prop), value: prop()});
      })
    );
    ```
    > For more details: [Extending the view language](http://lhorie.github.io/mithril-blog/extending-the-view-language.html).

**[⬆ back to top](#table-of-contents)**

## Elements

  - [3.1](#3.1) <a name='3.1'></a> The CSS selector syntax (e.g. `a#google.external[href='http://google.com'])` is meant to be used for declaring static attributes in the element, i.e. attribute values that don't change dynamically when the user interacts with the app. The attributes argument (i.e. the second parameter in the `m('div', {class: 'container'}, 'Hello')` example) is meant to be used for attributes whose values we want to dynamically populate.

    ```javascript
    // bad
    m('div', {class: 'container'}, 'Hello');

    //good
    m('div.container', 'Hello');

    //assume the variable `link` came from a web service
    var link = {url: 'http://google.com', title: 'Google'};

    // bad
    m('a[href="'+link.url+'"]'}, link.title);

    //good
    m('a', {href: link.url}, link.title); //yields <a href="http://google.com">Google</a>

    ```

  - [3.2](#3.2) <a name='3.2'></a> Use JSON syntax whenever the attribute name you are setting has non-alphanumeric characters.

    ```javascript
    // good
    m('div', {'data-index': 1}); //yields <div data-index="1"></div>

    // bad
    m('div', {data-index: 1});
    ```

  - [3.3](#3.3) <a name='3.2'></a> Use Javascript's property name instead of HTML's whenver you set a value.

  > Why? If an attribute has the same name in Javascript and in HTML, then Mithril assumes you're setting the Javascript property. Mithril does not auto-camel-case CSS properties.

    ```javascript
    // good
    //note this uses the Javascript syntax (uppercase "O") for `readonly`
    //in order to set the boolean javascript property instead of the HTML attribute
    m('input', {readOnly: true}); //yields <input readonly />

    // bad
    //using the HTML attribute name will call `setAttribute`, which may not be what you want
    m('input', {readonly: false}); //yields <input readonly="false" />, which is still readonly
    ```

  - [3.4](#3.4) <a name='3.4'></a> Use the Javascript syntax when setting CSS properties on inline style attributes via Javascript objects.
  You can find the [Javascript syntax for all the CSS rules here](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Properties_Reference).

    ```javascript
    // good
    m('div', {style: {textAlign: 'center'}}); //yields <div style="text-align:center;"></div>
    m('div', {style: {cssFloat: 'left'}}); //yields <div style="float:left;"></div>

    // bad
    //this does not work
    m('div', {style: {'text-align': 'center'}});
    m('div', {style: {float: 'left'}});
    ```

  - [3.5](#3.5) <a name='3.5'></a> Add units like px or % to any CSS values, which requires them.

  > In order to keep the framework lean, Mithril does not auto-append them.

  ```javascript
  // good
  m('div', {style: {borderWidth: '1px'}});

  // bad
  m('div', {style: {borderWidth: '1'}});
  ```

  - [3.6](#3.6) <a name='3.6'></a> Unescape trusted HTML strings by using m.trust.

  > Why? By default, Mithril escapes HTML strings in order to help prevent XSS attacks.

  ```javascript
  // bad
  m('div', '&times;') //becomes <div>&amp;times;</div>

  // good
  m('div', m.trust('&times;')) //becomes <div>&times;</div>
  ```

  - [3.7](#3.7) <a name='3.7'></a> To avoid surprises, be sure to change only attribute values, using undefined or null as values if appropriate, rather than conditionally substituting attribute dictionaries altogether.

  > Why? Elements are recreated if either their node name changes, or if the list of attribute names change, or if the ID attribute changes.

  ```javascript
  // bad
  m('li', selected ? {class: 'active'} : {})

  // good
  m('li', {class: selected ? 'active' : ''})
  ```

  > If you want to retain the element state across DOM context changes, you must track and bind it explicitly in the application ([example](http://jsfiddle.net/Zalmoxisus/r2dt1ppk/)).

  - [3.8](#3.8) <a name='3.8'></a> Always use keys if you need to sort lists, remove items from them or splice them in any way. To keep focus of input in a sortable list add a key property to the direct children of the array that you're planning to modify. The key for each child must be unique among a list of sibling DOM elements, but it does not need to be globally unique.

  > Why? UI state like input focus is not tracked correctly if the focused element moves around, and likewise, state for 3rd party plugins that are added via config can also end up in the wrong element.

  ```javascript
  // bad
  m('ul', [
    items.map(function(item) {
        return m('li', [
            m('input')
        ]);
    })
]);

  // good
  m('ul', [
    items.map(function(item) {
        return m('li', {key: item.id}, [
            m('input')
        ]);
    })
]);
  ```

  - [3.9](#3.9) <a name='3.9'></a> Be sure to update the value of the getter-setter.

  > Mithril always considers the model layer data to be canonical. This means that in the code below, the input on screen will overwritten by the model data any time a redraw happens.

  ```javascript
  //a data store
  var name = m.prop('')

  // bad
  //note that we are not updating the value of the `name` getter-setter via an event handler
  //redraws will always overwrite the current UI value with the value of `name()`
  m('input', {value: name()})

  // good
  m('input', {oninput: m.withAttr('value', name), value: name()})
  ```
  > See [2.3](#2.3) for the expressiveness.

**[⬆ back to top](#table-of-contents)**

## Components

  - [4.1](#4.1) <a name='4.1'></a> You cannot call Mithril's redrawing methods (m.startComputation / m.endComputation and m.redraw) or m.request from templates.

  > Why? Doing so will trigger another redraw, which will result in an infinite loop.

  ```javascript
  //model
  var User = {}
  User.listEven = function() {
      return m.request({method: 'GET', url: '/user'}).then(function(list) {
          return list.filter(function(user) {return user.id % 2 == 0});
      });
  }

  // bad
  var MyComponent = {
    view: function() {
        return m('ul', User.listEven().map( function(user) {
            return m('li', user.name);
        });
    }
  };

  // good
  var MyComponent = {
    controller: function() {
        return {users: User.listEven()}
    },
    view: function(ctrl) {
        return m('ul', ctrl.users.map( function(item) {
            return m('li', user.name);
        });
    }
  };
  ```

  - [4.2](#4.2) <a name='4.2'></a> When nesting components, the root DOM element in a component's view must not be changed during the lifecycle of the component, otherwise undefined behavior will occur.

  ```javascript
  // bad
  var MyComponent = {
    view: function() {
        return someCondition ? m('a') : m('b');
    }
  };

  // good
  var MyComponent = {
    view: function() {
        return m('div', someCondition ? m('a') : m('b'));
    }
  };

  var MyApp = {
    view: function() {
        return m('div', m.component(MyComponent));
    }
  };
  ```

  - [4.3](#4.3) <a name='4.3'></a> As for elements, if you have components inside a sortable list or when the elements move around, you should add key attributes to your components. Keys must be unique within a list of sibling DOM elements, and they must be either a string or a number.

  > Why? To ensure that DOM elements are not recreated from scratch, but merely moved when possible.

  ```javascript
  var App = {
    ctrl: function() {
        return {data: [1, 2, 3]}
    }
    view: function(ctrl) {
        return m('.app', [
            //pressing the button reverses the list
            m('button[type=button]', {onclick: function() {ctrl.data.reverse()}}, 'My App'),

            // bad
            ctrl.data.map(function(item) {
                return m.component(MyComponent, {message: 'Hello ' + item})
            })

            // good
            ctrl.data.map(function(item) {
                //the key ensures the components aren't recreated from scratch, if they merely exchanged places
                return m.component(MyComponent, {message: 'Hello ' + item, key: item})
            })
        ])
    }
  }

  var MyComponent = {
      controller: function(args) {
          return {greeting: args.message}
      },
      view: function(ctrl) {
          return m('h2', ctrl.greeting)
      }
  }

  m.mount(document.body, App)
  ```
  > Example when the elements move around: [bad (without keys))](http://jsfiddle.net/Zalmoxisus/L7woL6cp/) / [good](http://jsfiddle.net/Zalmoxisus/o4anq7dh/).

  - [4.4](#4.4) <a name='4.4'></a> For moving either elements or components to the different parents, emulate it at the controller instance level as there's no support for that out of the box.
  > Use as in [1](http://jsfiddle.net/y4qgh6e8/11/) or [2](https://jsbin.com/mujere/edit?js,output).


**[⬆ back to top](#table-of-contents)**