# Element Upgrades

## Progressive Enhancement
We use `customElements.define()` to register a custom element. But elements can be used before it's even defined/registered, and this feature is **progressive enhancement**.

Example:
```HTML
<custom-element></custom-element>
<script>
	customElements.define("custom-elements", class CustomElement extends HTMLElement { constructor() { super(); } })
</script>
```
`window.customElements.whenDefined()` can be used to determine when a tag becomes defined. It returns a promise that resolves when the element definition is registered. 

```JavaScript
customElements.whenDefined('app-drawer').then(() => {  console.log('app-drawer defined');});
```

**Another Example:**
```HTML
<share-buttons>
	<social-button type="twitter"><a href="...">Twitter</a></social-button>  			
	<social-button type="fb"><a href="...">Facebook</a></social-button>  		
	<social-button type="plus"><a href="...">G+</a></social-button> 
</share-buttons>
```
```JavaScript
// Fetch all the children of <share-buttons> that are not defined yet.
let undefinedButtons = buttons.querySelectorAll(':not(:defined)');

let promises = [...undefinedButtons].map(socialButton => {
    return customElements.whenDefined(socialButton.localName);
));

// Wait for all the social-buttons to be upgraded.
Promise.all(promises).then(() => {
    // All social-button children are ready.
});
```
> Note: a custom element is essentially just a tag that is used to wrap around a single or multiple HTML pre-defined elements. It's almost like a React element.

# Element Defined Content

Custom elements can define their own HTML markup content. 

```JavaScript
customElements.define('x-foo-with-markup', class extends HTMLElement {
    connectedCallback() {
            this.innerHTML = "<b>I'm an x-foo-with-markup!</b>";
        }
        ...
});
```
> Generally, it's not a good idea to override an element's children like this. 

## Shadow DOM

For shadow DOM 101: [Shadow DOM v1]()
This section only talks about how to create an element that uses Shadow DOM, which is a better way to add element-defined content. 

Shadow DOM allows an element to render and style its own chunk of DOM that is separated from the rest of the page. To use Shadow DOM, call `this.attachShadow` inside the element constructor. 

```JavaScript
let tmpl = document.createElement('template');
tmpl.innerHTML = `
  <style>:host { ... }</style> <!-- look ma, scoped styles -->
  <b>I'm in shadow dom!</b>
  <slot></slot>
`;

customElements.define('x-foo-shadowdom', class extends HTMLElement {
    constructor() {
            super(); // always call super() first in the constructor.
            // Attach a shadow root to the element.
            let shadowRoot = this.attachShadow({
                mode: 'open'
            });
            shadowRoot.appendChild(tmpl.content.cloneNode(true));
        }
        ...
});
```

> In the above snippet we use a `template` element to clone DOM, instead of setting the `innerHTML` of the `shadowRoot`. This technique cuts down on HTML parse costs because the content of the template is only parsed once, whereas calling `innerHTML` on the `shadowRoot` will parse the HTML for each instance. We'll talk more about templates in the next section.

Example usage:

```HTML
<x-foo-shadowdom> 
 <p><b>User's</b> custom text</p>
 </x-foo-shadowdom><!-- renders as -->
 <x-foo-shadowdom> 
  #shadow-root  
    <b>I'm in shadow dom!</b>  
      <slot></slot>
       <!-- slotted content appears here -->
 </x-foo-shadowdom>
```
The children of the custom element will appear at `<slot><slot/>`


## Template element

`<template>` element allows users to define a custom fragment of DOM, which can be parsed, inserted at page load and used later in run time. Templates are ideal placeholder for declaring the structure of aa custom element. 

Example:

```HTML
<template id="x-foo-from-template">
	<style>
		p { color: green; }
	</style>
	<p>I'm in Shadow DOM. My markup was stamped from a &lt;template&gt;.
	</p>
</template>
``` 

```JavaScript
let tmpl = document.querySelector('#x-foo-from-template');
// If your code is inside of an HTML Import you'll need to change the above line to:
// let tmpl = document.currentScript.ownerDocument.querySelector('#x-foo-from-template');

customElements.define('x-foo-from-template', class extends HTMLElement {
    constructor() {
            super(); // always call super() first in the constructor.
            let shadowRoot = this.attachShadow({
                mode: 'open'
            });
            shadowRoot.appendChild(tmpl.content.cloneNode(true));
        }
        ...
});
```

# Styling Custom Elements

Elements can be styled before it's defined using `:defined` selector. 

Example: hiding `<app-drawer>` when it's undefined:

```CSS
app-drawer:not(:defined) { 
/* Pre-style, give layout, replicate app-drawer's eventual styles, etc. */  
	display: inline-block; 
	height: 100vh;
	opacity: 0; 
	transition: opacity 0.3s ease-in-out; }
```

# Extending Custom Elements

Custom element API allows the extension of other custom elements as well as HTML built-in elements.

## Extending a custom element

Elements that don't inherits from standard HTML elements are called **Autonomous custom elements**. 
> Note: Autonomous custom elements still extends `HTMLElement`

```JavaScript
class FancyDrawer extends AppDrawer {
    constructor() {
        super(); // always call super() first in the constructor. This also calls the extended class' constructor.
        ...
    }

    toggleDrawer() {
        // Possibly different toggle implementation?
        // Use ES2015 if you need to call the parent method.
        // super.toggleDrawer()
    }

    anotherMethod() {
        ...
    }
}

customElements.define('fancy-app-drawer', FancyDrawer);
```

By extending a custom element, we can define additional behaviors on top of a parent custom element.

## Extending HTML elements

These elements are called **Customized built-in elements**.
> Note: not supported by safari

```JavaScript
// See https://html.spec.whatwg.org/multipage/indices.html#element-interfaces
// for the list of other DOM interfaces.
class FancyButton extends HTMLButtonElement {
    constructor() {
        super(); // always call super() first in the constructor.
        this.addEventListener('click', e => this.drawRipple(e.offsetX, e.offsetY));
    }

    // Material design ripple animation.
    drawRipple(x, y) {
        let div = document.createElement('div');
        div.classList.add('ripple');
        this.appendChild(div);
        div.style.top = `${y - div.clientHeight/2}px`;
        div.style.left = `${x - div.clientWidth/2}px`;
        div.style.backgroundColor = 'currentColor';
        div.classList.add('run');
        div.addEventListener('transitionend', e => div.remove());
    }
}

customElements.define('fancy-button', FancyButton, {
    extends: 'button'
});
```

> Notice that in `customElements.define()` there is a third parameter that tells HTML exactly which element it is extending. This is necessary because many HTML tags share the same DOM interface. `<section>`, `<address>`, and `<em>` (among others) all share `HTMLElement`; both `<q>` and `<blockquote>` share `HTMLQuoteElement`

The custom built-in elements can be used in several ways:

1. Using `is` attribute: 
```HTML 
<button is="fancy-button" disabled>Fancy button!</button>
```
2. Create an instance of it using JavaScript
```JavaScript
// Custom elements overload createElement() to support the is="" attribute.
let button = document.createElement('button', {
    is: 'fancy-button'
});
button.textContent = 'Fancy button!';
button.disabled = true;
document.body.appendChild(button);
```
3. Using the `new` operator
```JavaScript
let button = new FancyButton();
button.textContent = 'Fancy button!';
button.disabled = true;
```