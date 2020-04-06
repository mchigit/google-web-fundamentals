# Introduction

Shadow DOM is 1 of 4 Web Component standards:
- Shadow DOM
- Custom Elements
- HTML templates
- HTML imports

Shadow DOM allows us to create **self-contained** components with its own CSS styles using Vanilla JavaScript without any tooling. 

Some benefits of using Shadow DOM includes:
- Isolated DOM 
- Scoped CSS
- Simplifies DOM composition

It's very similar to React components, where each component's implementation is hidden from the higher level, and each component can have its own defined CSS rules. 

## What is Shadow DOM

To understand how web pages work, it's important to understand the generic idea of [Document Object Model]([https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction)) and how the browser parses the static HTML code we write into a dynamic data model of objects and nodes.

Shadow DOM is the ability to create a scoped DOM tree, and attaching it to an element that is separate from its children. This scoped tree is called **Shadow Tree** and the element the shadow tree attaches to is the **shadow host**. This allows the shadow tree to have its own scoped styling and properties, since any added styling are local to the host. 

## Creating a shadow DOM

To create a shadow DOM, we create a shadow root (a document fragment) using `element.attachShadow` method, and use `appendChild` or `innerHTML` to add markup to the shadow root. 

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


> Notice that `attachShadow` takes in an object/dictionary, it contains `mode` field, which specifies the encapsulation mode of the shadow DOM tree, and can be either open or closed. By setting the mode to open, the shadow DOM is accessible by JavaScript outside of the root using `element.shadowRoot`, setting it to closed will deny access (`element.shadowRoot` will return null). 

Some elements don't support shadow DOM because the browser may already have defined their own internal shadow DOM for some elements like `<textarea>` or `<input>`. 


## Shadow DOM for custom element

We can also create shadow DOM for a custom element, and it can be attached in the constructor. The following example creates a shadow root and attaches it to itself to encapsulate its inner HTML. 

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

Composition is how applications are constructed using different HTML building blocks (elements). Some tags know how to handle certain child tags, such as `<select>` and `<options>`. Before understanding the concept of composition, some new terminologies need to be understood: 

- **Light DOM:** refers to an element's actual children. In case below, the light DOM refers to `<img>` and `<span>`, which are written by the component user. 
```HTML
<better-button> 
	<img src="gear.svg" slot="icon" />
	<span>Settings</span>
</better-button>
```
- **Shadow DOM:** refers to the DOM that a component authors creates, hidden to component user. 
- **Flattened DOM Tree:** refers to what is rendered, both light and shadow DOM. 
	- Slot is used to render light DOM, else shadow DOM will overwrites light DOM.

Slots are placeholders for light DOM, allows component users to fill with their own markup. Slots can be provided with a fallback markup, which will get rendered if users don't pass one in. 

```HTML
<slot> <!-- default slot entire DOM tree as fallback -->
 <h2>title</h2>
 <summary>Description text</summary>
</slot>
```

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

The flattened DOM tree looks like the following:

```HTML
<fancy-tabs>  
  #shadow-root
  <div id="tabs">
	  <slot id="tabsSlot" name="title">
	    <button slot="title">Title</button>        
	    <button slot="title" selected>Title 2</button>  
	    <button slot="title">Title 3</button>      
	  </slot>
	</div>
	<div id="panels">      
		<slot id="panelsSlot">        
			<section>content panel 1</section>
		    <section>content panel 2</section>   
		    <section>content panel 3</section>      
		</slot>
	</div>
</fancy-tabs>
```
> The shadow root declares a slot with name title, therefore when using the component, any elements with attribute `slot="title"` will be rendered at the first declared slot. The rest of the children are rendered under the div with `id="panels"`. 









## References

1. [https://developers.google.com/web/fundamentals/web-components/shadowdom](https://developers.google.com/web/fundamentals/web-components/shadowdom)
2. [https://gist.github.com/ebidel/2d2bb0cdec3f2a16cf519dbaa791ce1b](https://gist.github.com/ebidel/2d2bb0cdec3f2a16cf519dbaa791ce1b)
3. [https://developer.mozilla.org/en-US/docs/Web/API/Element/attachShadow](https://developer.mozilla.org/en-US/docs/Web/API/Element/attachShadow)