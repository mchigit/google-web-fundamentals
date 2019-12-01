---


---

<h1 id="properties-and-attributes">Properties and Attributes</h1>
<h2 id="reflecting-properties-to-attributes">Reflecting properties to attributes</h2>
<p>Definition:<br>
<a href="https://html.spec.whatwg.org/multipage/common-dom-interfaces.html#reflecting-content-attributes-in-idl-attributes">https://html.spec.whatwg.org/multipage/common-dom-interfaces.html#reflecting-content-attributes-in-idl-attributes</a></p>
<p>Every element property that is changed by JS will be reflected back to HTML DOM as element attribute. This is useful because some DOM APIs rely on attributes to work. This is used to keep HTML elements’ DOM representation and JavaScript states in sync.</p>
<h2 id="reacting-to-attribute-changes">Reacting to attribute changes</h2>
<p><code>attributeChangedCallback</code> is called every time an attribute in <code>observedAttributes</code> array changes, and it’s a method that can be defined by users.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token keyword">class</span> <span class="token class-name">AppDrawer</span> <span class="token keyword">extends</span> <span class="token class-name">HTMLElement</span> <span class="token punctuation">{</span>
    <span class="token operator">...</span>

    <span class="token keyword">static</span> <span class="token keyword">get</span> <span class="token function">observedAttributes</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token punctuation">[</span><span class="token string">'disabled'</span><span class="token punctuation">,</span> <span class="token string">'open'</span><span class="token punctuation">]</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">get</span> <span class="token function">disabled</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">hasAttribute</span><span class="token punctuation">(</span><span class="token string">'disabled'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">set</span> <span class="token function">disabled</span><span class="token punctuation">(</span>val<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>val<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">setAttribute</span><span class="token punctuation">(</span><span class="token string">'disabled'</span><span class="token punctuation">,</span> <span class="token string">''</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
            <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">removeAttribute</span><span class="token punctuation">(</span><span class="token string">'disabled'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>

    <span class="token comment">// Only called for the disabled and open attributes due to observedAttributes</span>
    <span class="token function">attributeChangedCallback</span><span class="token punctuation">(</span>name<span class="token punctuation">,</span> oldValue<span class="token punctuation">,</span> newValue<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// When the drawer is disabled, update keyboard/screen reader behavior.</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span>disabled<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">setAttribute</span><span class="token punctuation">(</span><span class="token string">'tabindex'</span><span class="token punctuation">,</span> <span class="token string">'-1'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">setAttribute</span><span class="token punctuation">(</span><span class="token string">'aria-disabled'</span><span class="token punctuation">,</span> <span class="token string">'true'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
            <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">setAttribute</span><span class="token punctuation">(</span><span class="token string">'tabindex'</span><span class="token punctuation">,</span> <span class="token string">'0'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">setAttribute</span><span class="token punctuation">(</span><span class="token string">'aria-disabled'</span><span class="token punctuation">,</span> <span class="token string">'false'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token comment">// TODO: also react to the open attribute changing.</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<blockquote>
<p><code>observedAttributes</code> is a getter that returns an array of attributes. Only these attributes change will cause <code>attributeChangedCallback</code> to fire.<br>
<code>attributeChangedCallback</code> takes in 3 parameters: attribute name, old value and the new value.</p>
</blockquote>

