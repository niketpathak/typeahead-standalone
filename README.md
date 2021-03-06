# Typeahead-standalone.js 

[![npm version](https://img.shields.io/npm/v/typeahead-standalone.svg)](https://www.npmjs.com/package/typeahead-standalone)
[![Build Status](https://travis-ci.org/niketpathak/typeahead-standalone.svg?branch=master)](https://travis-ci.org/niketpathak/typeahead-standalone) 
[![code style](https://img.shields.io/badge/code_style-prettier-ff69b4.svg)](https://github.com/prettier/prettier)
![Downloads](https://img.shields.io/npm/dt/typeahead-standalone) 
![maintained](https://img.shields.io/badge/maintained-yes-blueviolet) 
[![License](https://img.shields.io/badge/license-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![speed blazing](https://img.shields.io/badge/speed-blazing%20%F0%9F%94%A5-9cf.svg)](https://twitter.com/acdlite/status/974390255393505280)

---
A fast fully-featured standalone autocomplete library

**Typeahead-standalone.js**

- is a blazing fast autocomplete library in pure javascript with **ZERO DEPENDENCIES**!
- is a highly customizable light-weight library [![](http://img.badgesize.io/https://cdn.jsdelivr.net/npm/typeahead-standalone?compression=gzip)](https://cdn.jsdelivr.net/npm/typeahead-standalone)
- inbuilt support for multiple data sources - Local, Prefetch and Remote
- suggestions calculated via a very efficient trie algorithm
- remote requests are rate-limited by default
- supports all major browsers (sorry IE, no support for you)
---

### Demo

Here is a **[Live Demo](https://typeahead.niketpathak.com/)** of **typeahead-standalone.js** in action.

## Install

```shell script
# you can install typeahead with npm
$ npm install --save typeahead-standalone

# Alternatively you can use Yarn
$ yarn add typeahead-standalone
```
Then include the library in your App/Page.

**As a module,** 
```javascript
// using ES6 modules
import { typeahead } from 'typeahead-standalone';

// using CommonJS modules
var typeahead = require('typeahead-standalone');
```

**In the browser context,**
```html
<!-- Include the library -->
<script src="./node_modules/typeahead-standalone/dist/typeahead-standalone.js"></script>

<!-- Alternatively, you can use a CDN with jsdelivr -->
<script src="https://cdn.jsdelivr.net/npm/typeahead-standalone"></script>
<!-- or with unpkg.com -->
<script src="https://unpkg.com/typeahead-standalone@2.2.0/dist/typeahead-standalone.js"></script>
```
The library will be available as a global object at `window.typeahead`

## Usage

Typeahead requires an `input` Element to attach itself to and a `Data source` (local/remote) to display suggestions. 

Here is a very basic example (See [demo](#demo) for advanced examples)

#### Html

```html
<!-- include the library -->
<script src="..." async></script>

<!-- Html markup -->
<input type="search" id="searchInput" autocomplete="off" placeholder="Search...">
```
#### Javascript

```javascript
// local Data
const colors = ['Grey', 'Brown', 'Black', 'Blue']

// input element to attach to
const inputElement = document.getElementById("searchInput");

typeahead({
    input: inputElement,
    source: {
      local: colors,
      // remote: {...}
    }
});
```

## Configuration

You can pass the following config options to `typeahead-standalone`:

| Parameter | Description | Default |
| --------- | ----------- | ------- |
|`input`|DOM input element must be passed with this parameter and typeahead will attach itself to this field. |`-` (Required)|
|`source`|This is the source of Data from which suggestions will be calculated. The source can be local, prefetched or retrieved from a remote endpoint. [Details](#source) |`-` (Required)|
|`minLength`|Specify the minimum length, when suggestions should appear on the screen.|`1`|
|`limit`|Specify the maximum number of suggestions that should be displayed.|`5`|
|`highlight`| If set to true, the matched letters are highlighted in the list of suggestions. A class `tt-highlight` is added to facilitate styling|`undefined`|
|`hint`| Updates the input placeholder to be equal to the first matched suggestion. A class `tt-hint` is added to facilitate styling|`true`|
|`className`|The typeahead-standalone container will have this class name (in addition to the default class `typeahead-standalone`)|`undefined`|
|`templates`|An object containing templates for header, footer, suggestion, ground and notFound state. See [templates section](#templates) for clarification |`undefined`|
|`debounceRemote`|Delays execution of making Ajax requests (in milliseconds) |`100`|
|`preventSubmit`|Prevents automatic form submit when ENTER is pressed.|`false`|
|`onSelect`|This method will be called when the user chooses an item from the suggestions. The selected item will be passed as first parameter.|Sets labels text as input's value|
|`normalizer`| The Normalizer function tries to convert/normalize the source data into the expected format => **array of objects with a label property**. For example, `['hello world']` is converted to `[{ label: 'hello world'}]`. Receives the list of suggestions `listItems: []` and the `identifier: string` as its arguments.|Normlizes source data|

---

### Source

This is the source of data from which suggestions will be provided. This is the expected format of the source object.
```
source: {
  local: [];
  remote: {
    url: 'https://remoteapi.com/%QUERY';
    wildcard: '%QUERY';
  };
  prefetch: {
    url: 'https://remoteapi.com/%QUERY';
    startEvent: ''; // defaults to 'onInit' 
  };
  identifier: '';
  transform: function (data) {
    // modify remote data if needed
    return data;
  };
}
```
- **Local**: The `local` data source is used when you want to provide suggestions from a local variable.
- **Prefetch**: The `prefetch` data source is used when you want to preload suggestions from a remote endpoint in advance. You can also provide an optional `startEvent` parameter. Its value defaults to `onInit` meaning that suggestions will be loaded as soon as typeahead gets initialized. You can set it to `onFocus` which will cause suggestions to be preloaded as soon as the user focuses the search input box.
- **Remote**: The `remote` data source is used when you want to interrogate a remote endpoint to fetch data.
- **Wildcard**: While using the `remote` data source, you must set the `url` and the `wildcard` options. `wildcard` will be replaced with the search string while executing the request.
- **Transform**: You can provide an optional `transform` function which gets called immediately after the remote endpoint returns a response. You can modify the remote response before it gets processed by typeahead. The transformed data is passed to the `normalizer` to ensure that the data is normalized.
- **Identifier**: An `identifier` is used to identify which property of the object should be used as the label. For example, assuming the data source returns the following:
```javascript
/* Data source returns */
[
  { id: 1, color: "Yellow" }, 
  { id: 2, color: "Green" },
  ...
]
 ```
 Now if we wish to use the `label`(suggestions) as the text defined in the `color` property , then the **identifier** must be set to **color**. (i.e. `identifier: "color"`)
```javascript
/* When the identifier is set, the default normalizer will produce the following expected data format */
[
  { id: 1, color: "Yellow", label: "Yellow" }, 
  { id: 2, color: "Green", label: "Green" },
  ...
]
```
The identifier is optional and must be used **only if the data needs to be normalized**.

## Styling (css)

Some basic styling is added to typeahead by default. However the UI is completely upto you and is customizable to the very pixel. You can use the following classes to add/override styles. 

- The entire html is wrapped in a container with a class `typeahead-standalone`.
- The input element gets an additional `tt-input` class.
- The list of suggestions is wrapped in a container with a `tt-list` class. (A class `tt-hide` is added when no suggestions are available)
- Each suggestion has a class `tt-suggestion` and if the suggestion is selected, then it has a `tt-selected` class additionally.

```css
/* set background color for each suggestion */
.typeahead-standalone .tt-list .tt-suggestion {
  background-color: green;
}
```

<strong>Note</strong>: To override default styling, set the config option `className` and use it as a selector. Lets say you set `className: "my-typeahead"`, then to override style on hovering/selecting a suggestion, you could use: 
 ```css
/* override styles */
.typeahead-standalone.my-typeahead .tt-list .tt-suggestion:hover, 
.typeahead-standalone.my-typeahead .tt-list .tt-suggestion.tt-selected {
  color: black;
  background-color: white;
}
```
You can also use templates to add a header, footer and further style each suggestion.

### Templates

Templates can be used to customize the rendering of the List. Their usage is completely optional.

```javascript
templates: { 
  header: '<h1>List of Countries</h1>', /* Rendered at the top of the dataset */
  footer: '<div>See more</div>', /* Rendered at the bottom of the dataset */
  suggestion: function(item) { /* Used to render a single suggestion */
    return '<div class="custom-suggestion">' + item.label + '</div>'; 
  },
  group: function(groupName) { /* Used to render a group */
    return '<div class="custom-group">' + groupName + '</div>'; 
  },
  notFound: '<div>Nothing Found</div>', /* Rendered if 0 suggestions are available */
}
```

Each template gets wrapped in a `div` with its corresponding class. i.e.
- `header` => class `tt-header`
- `footer` => class `tt-footer`
- `suggestion` => class `tt-suggestion`
- `group` => class `tt-group`
- `notFound` => class `tt-notFound`
---

### API

* [`typeahead.destroy()`](#typeaheaddestroy)

#### <a id="typeaheaddestroy">typeahead.`destroy()`</a>

Destroys the typeahead instance, removes all event handlers and cleans up the DOM. Can be used once when you no more want to have suggestions.

```javascript
var typeaheadInstance = typeahead({ /* options */ });
typeaheadInstance.destroy();
```

### Contribute

Interested in contributing features and fixes?

[Read more on contributing](./contributing.md).

### Changelog

See the [Changelog](https://github.com/niketpathak/typeahead-standalone/wiki/Changelog)

### License

[MIT](LICENSE) © [Niket Pathak](https://niketpathak.com)