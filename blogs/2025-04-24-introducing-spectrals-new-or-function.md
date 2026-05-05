---
title: "Introducing Spectral’s New ‘OR’ Function"
url: "https://blog.stoplight.io/introducting-spectral-or-function"
date: "Thu, 24 Apr 2025 20:54:07 +0000"
author: "​​Frank Kilcommins​"
feed_url: "https://blog.stoplight.io/feed"
---
<p>Spectral continues to evolve to meet the governance needs of API teams looking to enforce quality through customizable linting rules. I’m happy to introduce a new core function—<strong>or</strong>—which adds more power to your rulesets and API style guides. </p>



<h2 class="wp-block-heading">What are Spectral Core Functions? </h2>



<p>Spectral <a href="https://docs.stoplight.io/docs/spectral/cb95cf0d26b83-core-functions" rel="noreferrer noopener" target="_blank">core functions</a> are the building blocks of rules in Spectral. They encapsulate reusable logic like checking for the existence of a property, evaluating a pattern, or comparing values. You pair a core function with a given JSONPath expression and some functionOptions to write powerful, declarative rules for your <a href="https://spec.openapis.org/oas/latest.html" rel="noreferrer noopener" target="_blank">OpenAPI</a>, <a href="https://spec.openapis.org/arazzo/latest.html" rel="noreferrer noopener" target="_blank">Arazzo</a>, or <a href="https://www.asyncapi.com/docs/reference/specification/v3.0.0" rel="noreferrer noopener" target="_blank">AsyncAPI</a> documents. </p>



<p>The current list of core functions is as follows: </p>



<figure class="wp-block-table"><table class="has-fixed-layout"><tbody><tr><td><strong>Function</strong></td><td><strong>Description</strong></td></tr><tr><td><code>alphabetical</code></td><td>Enforce alphabetical content, for simple arrays, or for objects by passing a key.</td></tr><tr><td><code>enumeration</code></td><td>Checks if the field value exist in this set of possible values.</td></tr><tr><td><code>falsy</code></td><td>The value should be <code>false</code>, &#8221;&#8221;, <code>0</code>, <code>null</code> or <code>undefined</code>. </td></tr><tr><td><code>length</code></td><td>Count the length of a string or an array, the number of properties in an object, or a numeric value, and define minimum and/or maximum values.</td></tr><tr><td><code>pattern</code></td><td>Use regular expressions to <code>match</code> or <code>notMatch</code>. </td></tr><tr><td><code>casing</code></td><td>Text must match a certain case, like <code>camelCase</code> or <code>snake_case</code>. </td></tr><tr><td><code>schema</code></td><td>Use JSON Schema (draft 4, 6, 7, 2019-09, or 2020-12) to treat the contents of the $given JSON Path as a JSON instance. </td></tr><tr><td><code>truthy</code></td><td>The value should not be <code>false</code>, &#8221;&#8221;, <code>0</code>, <code>null</code> or <code>undefined</code>. </td></tr><tr><td><code>defined</code></td><td>The value must be defined, meaning it must be anything but <code>undefined</code>.</td></tr><tr><td><code>undefined</code></td><td>The value must be <code>undefined</code>.</td></tr><tr><td><code>unreferencedReusableObject</code></td><td>This function identifies unreferenced objects within a document.</td></tr><tr><td><code>or</code> <strong>**NEW**</strong></td><td>Communicate that one or more of these properties is required to be defined.</td></tr><tr><td><code>xor</code></td><td>Communicate that one of these properties is required, and no more than one is allowed to be defined.</td></tr><tr><td><code>typedEnum</code></td><td>When both a <code>type</code> and <code>enum</code> are defined for a property, the enum values must respect the type.</td></tr></tbody></table></figure>



<h2 class="wp-block-heading">Why is an `or` Function Useful?</h2>



<p>The new or function lets you assert that <strong>at least one </strong>of several specified properties must exist. This is particularly useful when a schema or object can be validated in several ways, and you want to cater for that flexibility across teams or APIs. This can be useful in upholding a strong basis for quality API documentation but remaining practical with respect to the needs of decentralized decision making and use-case nuances.</p>



<p><strong>Function Signature</strong></p>



<pre>
<code class="language-yaml hljs">
function: or
functionOptions: 
   properties: 
   propertyName1 
   propertyName2 
   propertyName3 
</code>
</pre>



<h3 class="wp-block-heading">Example 1: Descriptiveness Text Must Exist on Schemas </h3>



<p>Imagine enforcing documentation/design practices across API schemas. You might require that each schema is documented in some form to express its use (or purpose) for consumers and other API designers – but not necessarily force the same mechanism in expressiveness onto all schema authors. As one might use a <code>title</code>, another a <code>summary</code>, and another a longer <code>description</code>. The <code>or</code> function allows you to express that flexibility. </p>



<pre>
<code class="language-yaml hljs">
rules:
  schemas-descriptive-text-exists:
    description: Defined schemas must have one or more of `title`, `summary`, and/or `description`.
    given: "$.components.schemas.*"
    then:
      function: or
      functionOptions:
        properties:
          - title
          - summary
          - description
</code>
</pre>



<p>The above ruleset ensures that all schemas are at least <em>somewhat</em> documented to express their purpose, without forcing a specific documentation style.</p>



<h3 class="wp-block-heading">Example 2: Ensuring Helpful Hints for String Schemas</h3>



<p>In another common case, you may want to ensure that every <code>type: string</code> schema includes a helpful hint or constraint for developers – via a <code>format</code>, an <code>example</code>, or a <code>pattern</code>. Here again, more than one is acceptable, but at least one should exist: </p>



<pre>
<code class="language-yaml hljs">
rules:
  string-schemas-must-have-hint:
    description: String schemas must include a format, example, or pattern to aid consumers.
    given: "$.components.schemas.*[?(@.type=='string')]"
    then:
      function: or
      functionOptions:
        properties:
          - format
          - example
          - pattern
</code>
</pre>



<h2 class="wp-block-heading">Community Contribution</h2>



<p>Big shout-out to<strong> </strong><a href="https://github.com/cuttingclyde" rel="noreferrer noopener" target="_blank">Clyde Cutting</a><strong> <img alt="💛" class="wp-smiley" src="https://s.w.org/images/core/emoji/17.0.2/72x72/1f49b.png" style="height: 1em;" /></strong> for adding this capability. He had the need for this functionality as part of the great work happening within the <a href="https://financialdataexchange.org/" rel="noreferrer noopener" target="_blank">Financial Data Exchange</a> and their FDX API Style Guide for Open Banking APIs. Community <a href="https://github.com/stoplightio/spectral/pull/2798" rel="noreferrer noopener" target="_blank">contributions</a> like these help Spectral grow into an even more powerful and practical tool. </p>



<h2 class="wp-block-heading">Try It Out</h2>



<p>Update to the latest version of <a href="https://www.npmjs.com/package/@stoplight/spectral-cli" rel="noreferrer noopener" target="_blank">Spectral</a> and give the <code>or</code> function a spin in your own rulesets. Whether you&#8217;re enforcing schema documentation, naming conventions, or structural flexibility, <code>or</code> gives you additional declarative means to express it. </p>
<p>The post <a href="https://blog.stoplight.io/introducting-spectral-or-function">Introducing Spectral’s New ‘OR’ Function </a> appeared first on <a href="https://blog.stoplight.io">Stoplight</a>.</p>
