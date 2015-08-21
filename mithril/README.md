# Mithril Style Guide

*A mostly reasonable approach to Mithril*

## Table of Contents

  1. [Articles to read](#to-read)
  1. [Basic Rules](#basic-rules)

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
      widget = m("div", "widget here")
    }

    m("div", [
       widget
    ])

    // good
    m("div", [
       someCondition ? m("div", "widget here") : ""
    ])
    ```
  - [2.2](#2.2) <a name='2.2'></a> Use `Array.prototype.map()` instead of loops for lists.

    ```javascript
    // bad
    var items = [];
    for (var i = 0; i < someList.length; i++) {
       var tpl = m("div", "list item " + i)
       items.push(tpl);
    };

    m("div", items)

    // good
    m("div", [
       someList.map(function(item, i) {
         return m("div", "list item " + i)
       })
    ])
    ```

**[⬆ back to top](#table-of-contents)**
