# Introduction

Shadow DOM is 1 of 4 Web Component standards:
- Shadow DOM
- Custom Elements
- HTML templates
- HTML imports

Shadow DOM is used with Custom elements to provide self-contained HTML markup and CSS for a custom element. 

Some benefits of using Shadow DOM includes:
- Isolated DOM
- Scoped CSS
- Simplifies DOM composition

It's very similar to React components, where each component's implementation is hidden from the higher level, and each component can have its own defined CSS rules. 

## What is Shadow DOM

To understand how web pages work, it's important to understand the generic idea of [Document Object Model]([https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction)) and how the browser parses the static HTML code we write into a dynamic data model.

Shadow DOM is the ability to include a DOM subtree to render in the document, but it doesn't exist in the actual DOM. This scoped tree is called **Shadow Tree** and the element the shadow tree attaches to is the **shadow host**. This allows the shadow tree to have its own scoped styling and properties. 

## Creating a shadow DOM

To create a shadow DOM, we create a shadow root using `element.attachShadow` method, and append child or use `innerHTML` to add markup to the shadow root. 

```JavaScript
const header = document.createElement('header');
const shadowRoot = header.attachShadow({
    mode: 'open'
});
shadowRoot.innerHTML = '<h1>Hello Shadow DOM</h1>'; // Could also use appendChild().

// header.shadowRoot === shadowRoot
// shadowRoot.host === header
```

> So we created an HTML element header, then created a shadow root by using `attachShadow` method. The markup and property of the shadow root can be changed using JavaScript like any other elements. 

Some elements don't support shadow DOM. 
We can also create shadow DOM for a custom element, and it can be attached in the constructor. 

```JavaScript
constructor() {
    super(); // always call super() first in the constructor.

    // Attach a shadow root to <fancy-tabs>.
    const shadowRoot = this.attachShadow({
        mode: 'open'
    });
    shadowRoot.innerHTML = `
      <style>#tabs { ... }</style> <!-- styles are scoped to fancy-tabs! -->
      <div id="tabs">...</div>
      <div id="panels">...</div>
    `;
}
```

An element's shadow DOM is rendered and will replace the element's children. Using `<slot>` will allow the element to render both shadow DOM and its children. 


## Composition and slots

- **Light DOM:** refers to an element's actual children:
```HTML
<better-button> 
	<img src="gear.svg" slot="icon"
	<span>Settings</span>
</better-button>
```
- **Shadow DOM:** refers to the DOM that a component authors creates, hidden to component user. 
- **Flattened DOM Tree:** refers to what is rendered, both light and shadow DOM. 
	- Slot is used to render light DOM, else shadow DOM will overwrites light DOM.

Slots are placeholders for light DOM, allows component users to fill with their own markup. Slots can be provided with aa fallback markup, which will get rendered if users don't pass one in. 

Slots can also be named, very useful when users are passing in multiple children. 

```HTML
#shadow-root  
<div id="tabs">
	<slot id="tabsSlot" name="title"></slot> <!-- named slot -->
</div>
<div id="panels">
	<slot id="panelsSlot"></slot>
</div>
```
```HTML
<fancy-tabs> 
	<button slot="title">Title</button>
	<button slot="title" selected>Title 2</button>
	<button slot="title">Title 3</button>
	<section>content panel 1</section>
	<section>content panel 2</section>
	<section>content panel 3</section>
</fancy-tabs>
```
> The shadow root declares a slot with name title, therefore when using the component, any elements with attribute `slot="title"` will be rendered at the first declared slot. The rest of the children are rendered under the div with `id="panels"`. 