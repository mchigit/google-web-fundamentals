# Custom Components

Custom components allow the creation of custom HTML tags, modify existing tags or extend tags created by other developers. Custom components can be created using just Vanilla HTML/CSS/JS

Creating a custom component needs to extend `HTMLElement` to inherit DOM API

Code Snippet for creating a custom element:

```JavaScript
class AppDrawer extends HTMLElement {

    // A getter/setter for an open property.
    get open() {
        return this.hasAttribute('open');
    }

    set open(val) {
        // Reflect the value of the open property as an HTML attribute.
        if (val) {
            this.setAttribute('open', '');
        } else {
            this.removeAttribute('open');
        }
        this.toggleDrawer();
    }

    // A getter/setter for a disabled property.
    get disabled() {
        return this.hasAttribute('disabled');
    }

    set disabled(val) {
        // Reflect the value of the disabled property as an HTML attribute.
        if (val) {
            this.setAttribute('disabled', '');
        } else {
            this.removeAttribute('disabled');
        }
    }

    // Can define constructor arguments if you wish.
    constructor() {
        // If you define a constructor, always call super() first!
        // This is specific to CE and required by the spec.
        super();

        // Setup a click listener on <app-drawer> itself.
        this.addEventListener('click', e => {
            // Don't toggle the drawer if it's disabled.
            if (this.disabled) {
                return;
            }
            this.toggleDrawer();
        });
    }

    toggleDrawer() {
        ...
    }
}

customElements.define('app-drawer', AppDrawer);
```

`this` keyword refers to the actual DOM element itself, therefore it can access the entire DOM API such as `this.children` or `this.addEventListener`

## Rules for creating a custom element

1. Names must contain a dash to allow HTML parser to distinguish between pre-defined elements and custom elements
2. The same tag cannot be registered more than once. Its behaviour is defined once and cannot be modified.
3. Custom elements cannot be self-closing. Ex: `<custom-component />` will not work.

## Custom element reactions

Custom element reactions are lifecycle hooks that can be defined to run specified code during different scenarios, and these hook functions are ran **synchronously**.
|Name |Called When |
|:--|:--|
|constructor |Called when an instance of the custom element has been created or upgraded. Usually used for initiating states, adding event listeners. See the [spec](<[https://html.spec.whatwg.org/multipage/custom-elements.html#custom-element-conformance](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-element-conformance)>) for restrictions |
|connectedCallback|Called every time the element is inserted into DOM. Can be used for setup code|
|disconnectedCallback|Called when element is removed from the DOM|
|attributeChangedCallback(attrName, oldVal, newVal)|Called when an observed attribute (only attributes listed in the `observedAttributes` property will receive this callback.) is modified. |
|adoptedCallback|The custom element has been moved into a new `document` (e.g. someone called `document.adoptNode(el)`).|

> For attributeChangedCallback, only observed attributes are whitelisted due to performance optimization. Common attribute changes like `class` or `style` should not cause callback.
> disconnectedCallback() will **NOT** be called when user closes the tab, therefore developers should never rely on lifecycle hooks for cleaning up.

```JavaScript
class AppDrawer extends HTMLElement {
    constructor() {
        super(); // always call super() first in the constructor.
        ...
    }
    connectedCallback() {
        ...
    }
    disconnectedCallback() {
        ...
    }
    attributeChangedCallback(attrName, oldVal, newVal) {
        ...
    }
}
```
