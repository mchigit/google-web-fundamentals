# Properties and Attributes

## Reflecting properties to attributes

Definition:
[https://html.spec.whatwg.org/multipage/common-dom-interfaces.html#reflecting-content-attributes-in-idl-attributes](https://html.spec.whatwg.org/multipage/common-dom-interfaces.html#reflecting-content-attributes-in-idl-attributes)

Every element property that is changed by JS will be reflected back to HTML DOM as element attribute. This is useful because some DOM APIs rely on attributes to work. This is used to keep HTML elements' DOM representation and JavaScript states in sync.

## Reacting to attribute changes

`attributeChangedCallback` is called every time an attribute in `observedAttributes` array changes, and it's a method that can be defined by users.

```JavaScript
class AppDrawer extends HTMLElement {
    ...

    static get observedAttributes() {
        return ['disabled', 'open'];
    }

    get disabled() {
        return this.hasAttribute('disabled');
    }

    set disabled(val) {
        if (val) {
            this.setAttribute('disabled', '');
        } else {
            this.removeAttribute('disabled');
        }
    }

    // Only called for the disabled and open attributes due to observedAttributes
    attributeChangedCallback(name, oldValue, newValue) {
        // When the drawer is disabled, update keyboard/screen reader behavior.
        if (this.disabled) {
            this.setAttribute('tabindex', '-1');
            this.setAttribute('aria-disabled', 'true');
        } else {
            this.setAttribute('tabindex', '0');
            this.setAttribute('aria-disabled', 'false');
        }
        // TODO: also react to the open attribute changing.
    }
}
```

> `observedAttributes` is a getter that returns an array of attributes. Only these attributes change will cause `attributeChangedCallback` to fire.
> `attributeChangedCallback` takes in 3 parameters: attribute name, old value and the new value.
