# Styling

There are many ways in which styling can be applied to a web component that uses shadow DOM:
- Defined by the main page
- Scoped local styles
- Providing hooks in the forms of [custom CSS properties]([https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)) for overriding styles when used. 

## Component-defined styles

Shadow DOM allows each component to have its own defined local styles. This means:
- CSS selector from outer components will not apply to elements inside the host component
- Styles defined within the component will not bleed out and affect outer components. 

This means that within a component, you can define styles without worrying about potential conflicts elsewhere in the main page, as long as there are no styling conflicts within the component. 

Example:

```HTML
# shadow root
<link rel="stylesheet" href="styles.css" />
<style>
	#panels {
		box-shadow: 0 2px 2px rgba(0, 0, 0, .3);
		background: white;
	}
	#tabs {
		display: inline-flex;
	}
</style>

<div id="tabs" />
<div id='panels' />
```
> Referenced style sheets are also scoped to the specific component. 


Components are able to style themselves by using `:host` selector. This selector will select the shadow host of the shadow DOM that uses the CSS styling, and it only works inside shadow DOM. However, `:host` rules defined within an element will be overridden by any styles defined by parent page if they conflict. 

`:host<selector>` will target the host if it matches a `<selector>` criteria. 

```HTML
<style>
	:host {
		opacity: 0.4;
	}
	:host(:hover) {
		opacity: 1;
	}
	:host(.blue) {
		color: blue;
	}
</style>
```

`:host-context<selector>` will match component if it or its ancestor matches the selector. 

Ex: 
```HTML
<body class='darktheme'>
<fancy-tab></fancy-tab>
</body>
```

```CSS

:host-context(.darktheme) {
  color: white;  
  background: black;
 }

```
> In this example, fancy-tab will be selected because its parent matches the class selector. 


## Styling slotted nodes

To style a slotted element, we can use `::slotted(<selector>)` to select elements that are distributed into slots which match the selector. For example:

```HTML
<name-badge>  
	<h2>Eric Bidelman</h2>  
	<span class="title">    
		Digital Jedi, 
		<span class="company">Google</span>  	
	</span>
</name-badge>
```

```CSS
<style>
	::slotted(h2) {  
		margin: 0;  
		font-weight: 300;  
		color: red;
	}
	::slotted(.title) {   
		color: orange;
	}
</style>

/* ::slotted(.title .company) will not work */
```

> slotted styling only works for top-level elements.


## Defining styles from outside

You can directly style a component from outside by defining its styles:

```CSS 
/* styling a custom component from outside */
custom-component {
	width: 300px;
	padding: 20px
} 

custom-component:hover {
	color: red;
}
```

**Outside styles always override styles defined in shadow DOM.** 

But what if the user wants to style the internal of the custom component? By providing styling hooks using [CSS custom properties]([https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)) , this essentially allows users to override styling by giving them a styling placeholder in which they can define their own styles to override. 

Example:

```HTML
<style>
	custom-component {
		margin: 12px; /* defining style for the component from outside */
		--font-color: red; /* defining the custom styling variable */
	}
</style>
<custom-component class='override-style' /> 
```
> Above code defines 2 CSS properties for the `custom-component` element. The first one is to set the margin to 12px, which is applied to the entire component, then it sets the `--font-color` variable to red, which will be applied to the internals of the component. 

Inside the shadow DOM:

```CSS	
:host(.override-style) {
	font-color: var(--font-color, black);
}
```
> The CSS will select the host with class `override-style` and set its font-color to either the custom variable, or black as default. 

## References

1. [https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)
2. [https://developer.mozilla.org/en-US/docs/Web/CSS/:host()](https://developer.mozilla.org/en-US/docs/Web/CSS/:host())
3. [https://developers.google.com/web/fundamentals/web-components/shadowdom#styling](https://developers.google.com/web/fundamentals/web-components/shadowdom#styling)