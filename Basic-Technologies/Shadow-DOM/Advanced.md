# Advanced Topics

## Creating closed shadow root

Recalling you can create a **closed** shadow root by setting its mode to "closed", which means it cannot be accessed using JavaScript by outside components. Directly accessing the shadow root of such element will return null. 

However, creating a closed shadow root should be avoided, and here is why:

1. Accessing a closed shadow root can still be done using `Element.prototype.attachShadow`.
2. Closed mode also prevents defining element code to access its own shadow DOM. In order to do so, the shadow root needs to be stashed during creation (inside constructor),  so it can be referenced later. This defeats the purpose of creating a closed shadow root. 
3. Less flexible for component users. 

## Working with Slots in JS

Shadow DOM API has a lot of utility functions to work with slots which can be very handy:

### slotchange event
`slotchange` event is fired when the nodes of a slot changes, for example when user remove or add new children to the slot. This event will not fire when the element is first initialized. 

```JavaScript
const slot = custom_element.shadowRoot.querySelector('selector');
slot.addEventListener('slotchange', callbackFn);
```

### Elements rendered in slot
`assignedNodes()` will return the elements rendered by the slot, but will not render fallback content if nothing is passed into the slot. Using `{flatten: true}` will display the fallback content as well as rendered element. 

You can also find out what slot the element is assigned to by running `element.assignedSlot`. 

## Shadow DOM event model

Not all events are propagated outside of the shadow DOM. For events that do, they are re-targeted to look like as if the event came from the component, not its internals. For events that do propagate out of the shadow DOM, they are listed here: https://developers.google.com/web/fundamentals/web-components/shadowdom#events. 

### Custom events
You can define internal nodes to dispatch custom events, but they will not propagate outside the shadow DOM unless the event is created using `{composed: true}`

```JavaScript
onSelect() {
	const element = this.shadowRoot.querySelector('selector');
	element.dispatchEvent(new Event('event-name', {bubbles: true, composed: true}));
}

// if composed: false, callback will never fire
element.addEventListener('event-name', callback);
```

## Handling focus

Since events are re-targeted as if they come from the hosting component, `document.activeElement` will also be targeting hosting element instead of the activated internal DOM. To access the activated internal, you can use `document.activeElement.shadowRoot.activeElement` if the mode is open. 

This means if the shadow DOM tree has several layers, finding the activeElement involves recursively traversing the tree. 

```JavaScript
function findActive() {
	let a = document.activeElement;
	while (a && a.shadowRoot && a.shadowRoot.activeElement) {
		a = a.shadowRoot.activeElement;
	}
	return a;
}	
```
> The loop will stop running when a does not have a shadowRoot attached anymore, which means it is at the last layer of the DOM tree. 

## References
1. [https://developers.google.com/web/fundamentals/web-components/shadowdom#advanced](https://developers.google.com/web/fundamentals/web-components/shadowdom#advanced)

