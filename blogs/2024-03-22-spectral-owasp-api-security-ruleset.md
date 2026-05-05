---
title: "Spectral OWASP API Security Ruleset"
url: "https://blog.stoplight.io/spectral-owasp-api-2023-security-ruleset"
date: "Fri, 22 Mar 2024 16:40:29 +0000"
author: "Phil Sturgeon"
feed_url: "https://blog.stoplight.io/feed"
---
<p>The OWASP API Security Project is a research and education project helping API teams discover, define, and categorize security risks. Within that, the API Security Top 10 list ranks the most important risks you should really care about.</p>



<p>To make following the advice easier, Stoplight created the <a href="https://github.com/stoplightio/spectral-owasp-ruleset">Spectral OWASP ruleset</a>, allowing you to benefit from the advice as you work in Spectral CLI, Stoplight Studio, or many of the other places Spectral runs, initially using the 2019 edition. In July 2023 the OWASP team released a new edition of their API Security Top 10 to cover the biggest threats in 2023, so we have updated our ruleset to match these changes and added some excellent new rules in the process.</p>



<h2 class="wp-block-heading" id="h-api-security-top-10">API Security Top 10</h2>



<ol class="wp-block-list">
<li><strong>API1:2023 &#8211; Broken Object Level Authorization: </strong>APIs tend to expose endpoints that handle object identifiers, creating a wide attack surface of Object Level Access Control issues. Object level authorization checks should be considered in every function that accesses a data source using an ID from the user. This hasn’t changed since 2019.</li>



<li><strong>API2:2023 &#8211; Broken Authentication: </strong>Authentication mechanisms are often implemented incorrectly, either hand-crafted or misconfigured. This allows attackers to compromise authentication tokens or to exploit implementation flaws to assume other users&#8217; identities temporarily or permanently. Compromising a system&#8217;s ability to identify the client/user, compromises API security overall. This hasn’t changed since 2019.</li>



<li><strong>API3:2023 &#8211; Broken Object Property Level Authorization:</strong> This updated category combines two previous categories: <strong>API3:2019 Excessive Data Exposure</strong> and <strong>API6:2019 &#8211; Mass Assignment</strong>. It’s focused on the lack of or improper authorization validation at the object property level.</li>



<li><strong>API4:2023 &#8211; Unrestricted Resource Consumption:</strong> Satisfying API requests requires resources such as network bandwidth, CPU, memory, storage, or integration with third-party services, which charge per email/call/SMS. Successful attacks can lead to Denial of Service or an increase in operational costs. This category recently had a name change.</li>



<li><strong>API5:2023 &#8211; Broken Function Level Authorization: </strong>Complex access control policies with different hierarchies, groups, roles, and an unclear separation between administrative and regular functions, tend to lead to authorization flaws. By exploiting these issues, attackers can gain access to other users’ resources and/or administrative functions.</li>



<li><strong>API6:2023 &#8211; Unrestricted Access to Sensitive Business Flows:</strong> APIs vulnerable to this risk expose a business flow &#8211; such as buying a ticket, or posting a comment &#8211; without compensating for how the functionality could harm the business if used excessively in an automated manner.</li>



<li><strong>API7:2023 &#8211; Server-Side Request Forgery:</strong> Server-Side Request Forgery (SSRF) flaws can occur when an API fetches a remote resource without validating the user-supplied URI. This enables an attacker to coerce the application to send a crafted request to an unexpected destination, even when protected by a firewall or a VPN.</li>



<li><strong>API8:2023 &#8211; Security Misconfiguration: </strong>APIs and the systems supporting them typically contain complex configurations, meant to make the APIs more customizable. Software and DevOps engineers can miss these configurations or choose not to follow security best practices when it comes to configuration, opening the door for different types of attacks.</li>



<li><strong>API9:2023 &#8211; Improper Inventory Management:</strong> APIs tend to expose more endpoints than traditional web applications, making proper and updated documentation highly important. A proper inventory of hosts and deployed API versions is also important to mitigate issues such as deprecated API versions and exposed debug endpoints.</li>



<li><strong>API10:2023 &#8211; Unsafe Consumption of APIs: </strong>Developers tend to trust data received from third-party APIs more than user input, thus adopting weaker security standards. To compromise APIs, attackers go after integrated third-party services instead of trying to compromise the target API directly.</li>
</ol>



<h2 class="wp-block-heading" id="h-added-changes">Added Changes</h2>



<p>Let&#8217;s focus on some of the changes we&#8217;ve made for this new version.</p>



<h3 class="wp-block-heading" id="h-new-rule-owasp-api2-2023-short-lived-access-tokens">New Rule: <code>owasp:api2:2023-short-lived-access-tokens</code></h3>



<p>Both 2019 and 2023 have been asking people to use strong tokens that rotate, so now if you are using an OAuth 2.x flow which does not mention a <code>refreshUrl</code>, Spectral will ask you for one. Perhaps you are missing it from OpenAPI, but perhaps you are using long-lived tokens that could be hacked. Either way, this rule will check.</p>



<pre><code class="language-yaml hljs">components:
  securitySchemes:
    OAuth2:
      type: oauth2
      flows:
        authorizationCode:
          authorizationUrl: https://example.com/oauth/authorize
          tokenUrl: https://example.com/oauth/token

          # error if this is missing
          refreshUrl: https://example.com/oauth/refresh
</code></pre>



<h3 class="wp-block-heading" id="h-new-rule-owasp-api5-2023-admin-security-unique"><strong>New Rule: <code>owasp:api5:2023-admin-security-unique</code></strong></h3>



<p>If you have public endpoints and admin endpoints with the same security requirements, you&#8217;ve got a problem. This rule aims to spot anything in the `/admin` namespace that uses any of the same security schemes as the public API, because you do not want users to be able to sneakily work on your admin endpoints.</p>



<pre><code class="language-yaml hljs">openapi: "3.1.0"
info:
  version: "1.0"
  title: "My API"
paths:
  "/public/export":
    get:
      security:
      - oauth2: []

  "/admin/export":
    get:
      security:
      - oauth2: [] # this will throw an error

  "/admin/other":
    get:
      security:
      - oauth2: [admin] # this is good

components:
  securitySchemes:
    oauth2:
      type: oauth2
      flows:
        authorizatonCode:
          authorizationUrl: https://example.com/oauth2/authorize
          tokenUrl: https://example.com/oauth2/token
          scopes: {}
</code></pre>



<h3 class="wp-block-heading" id="h-new-rule-owasp-api7-2023-concerning-url-parameter"><strong>New Rule: <code>owasp:api7:2023-concerning-url-parameter</code></strong></h3>



<p>Using external resources based on user input for webhooks, file fetching from URLs, custom SSO, URL previews, or redirects, can lead to a category of security issues that fall under Server-Side Request Forgery. Safe servers can be whitelisted, and URLs can be sanitized and scanned for malicious requests.</p>



<p>This rule will tell Spectral to keep an eye out for any parameters passing URLs to the API. As Spectral can’t tell what you&#8217;re doing with those URLs, this rule’s severity is set to Information, which you can choose to ignore either with Overrides or Extends.</p>



<pre><code class="language-yaml hljs">    paths:
    "/foo":
        get:
        parameters:
            - name: callback
            in: query
            - name: redirect
            in: query
            - name: redirect_url
            in: query
</code></pre>



<p>All of these will trigger the info message.</p>



<h3 class="wp-block-heading" id="h-new-rules-owasp-api3-2023-no-unevaluatedproperties-and-owasp-api3-2023-constrained-unevaluatedproperties"><strong>New Rules: <code>owasp:api3:2023-no-unevaluatedProperties</code> and <code>owasp:api3:2023-constrained-unevaluatedProperties</code></strong> </h3>



<p>This rule is less about a change in OWASP rules. It&#8217;s handy for OpenAPI v3.1 users who want to use <code>unevaluatedProperties</code> instead of <code>additionalProperties</code>, as it’s similar and doesn’t have a lot of the <a href="https://json-schema.org/understanding-json-schema/reference/object#unevaluated-properties">confusing drawbacks</a> that stump people when they try to use <code>additionalProperties</code> with <code>allOf</code>.</p>



<h3 class="wp-block-heading" id="h-new-rule-owasp-api8-2023-define-cors-origin">New Rule: <code>owasp:api8:2023-define-cors-origin</code></h3>



<p>The new API8:2023 — Security Misconfiguration section has collected a lot of the old &#8220;make sure you define error responses&#8221; and &#8220;use HTTPS over HTTP&#8221; but this is a net new rule to help remind people to work on their <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS">Cross-Origin Resource Sharing (CORS)</a> headers!</p>



<p>The goal of this rule is to get people to define an <code>Access-Control-Allow-Origin</code> header on all responses, whether it is set to <code>*</code>, <code>null</code>, or <code>dash.example.com</code>.</p>



<pre><code class="language-yaml hljs">"/":
  get:
    responses:
      "200":
        description: OK
        headers:
          Access-Control-Allow-Origin:
            schema:
              type: string
              examples: ["*"]
</code></pre>



<p>CORS is a tricky topic, and there is a whole lot more to it than this one header, but it&#8217;s a good place to start.</p>



<ul class="wp-block-list">
<li>Learn more about <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS">CORS</a>.</li>



<li>Learn more about <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Origin">Access-Control-Allow-Origin</a>.</li>
</ul>



<h3 class="wp-block-heading" id="h-new-rule-owasp-api9-2023-inventory-access">New rule: <code>owasp:api9:2023-inventory-access</code></h3>



<p>The first entry for <strong>API9 &#8211; Improper Inventory Management</strong>. This rule aims to help people figure out what the intended audience is for a given API, utilizing the increasingly popular <code>x-internal: true/false</code> vendor extension.</p>



<pre><code class="language-yaml hljs">servers:

- url: https://api.example.com/
  x-internal: false

- url: https://api-private.example.com/
  x-internal: true
</code></pre>



<p>While it might not be a core part of the OpenAPI Specification, it works with most documentation tools, and it won&#8217;t hurt to have it in there as anything starting with an <code>x-</code> is ignored by tools that don&#8217;t understand it.</p>



<h3 class="wp-block-heading" id="h-new-rule-owasp-api9-2023-inventory-environment">New Rule: <code>owasp:api9:2023-inventory-environment</code></h3>



<p>Another new entry for <strong>API9 &#8211; Improper Inventory Management</strong>, this rule asks API developers to be clear about the intended environment of any given server.</p>



<pre><code class="language-yaml hljs">servers:

- url: https://api.example.com/
  description: Production

- url: https://preprod.example.com/
  description: Staging
</code></pre>



<p>You can call it <code>staging</code>, <code>test</code>, <code>testing</code>, <code>preprod</code>, all the usual names, but if we&#8217;ve forgotten anything please do put in a quick <a href="https://github.com/stoplightio/spectral-owasp-ruleset">pull request</a>.</p>



<h2 class="wp-block-heading" id="h-limitations">Limitations</h2>



<p>Some of you may have thought of this, but some of those OWASP security concerns are not something Spectral could detect, as it looks at OpenAPI and only describes the surface level of the API. Anything happening inside the code, or SysOps concerns like using an old version of TLS, would all be inaccessible to Spectral.</p>



<p>Spectral can identify issues described in the OpenAPI or highlight what’s missing from the definition, but using it alone will not guarantee your API is completely safe from all forms of attack. Consider it to be one tool in your toolkit for spotting problems rather than a single litmus test.</p>



<p>To learn more about all the Top 10 risks, watch this brilliant <a href="https://www.youtube.com/watch?v=nIWBp_nvzq4&amp;list=PLrA5ciulugn8nydmfvt9cGBgDFqg8XbEt">YouTube playlist</a> covering the ten categories, put together by <a href="https://www.linkedin.com/in/frank-kilcommins">Frank Kilcommins</a> and <a href="https://www.linkedin.com/in/jose-haro-peralta/">José Haro Peralta</a> for SmartBear last year.</p>



<h2 class="wp-block-heading" id="h-using-the-spectral-owasp-ruleset">Using the Spectral OWASP Ruleset</h2>



<p>If you have not used the Spectral OWASP API Security Ruleset before, you can set it up in a few ways.</p>



<h3 class="wp-block-heading" id="h-spectral-cli-installed-via-npm">Spectral CLI installed via NPM</h3>



<pre><code class="language-plaintext hljs">npm install --save -D @stoplight/spectral-owasp-ruleset@^2.0.0
npm install --save -D @stoplight/spectral-cli
</code></pre>



<p>Pop over to your terminal to the working directory that contains your OpenAPI documents and use this command to create a new file that extends the NPM module we just installed.</p>



<pre><code class="language-plaintext hljs">echo 'extends: ["@stoplight/spectral-owasp-ruleset"]' > .spectral.yaml
</code></pre>



<p>Creating this local file acts as a &#8220;local ruleset&#8221; that extends the distributed ruleset. In its most basic form, this tells Spectral what ruleset you want to use, but it will allow you to customize things, add your own rules, and turn bits off that are causing trouble.</p>



<p>Once the file is in place, you can run <code>spectral lint {doc}</code> to see how your API is faring against these security checks.</p>



<pre><code class="language-plaintext hljs">spectral lint api/openapi.yaml 
</code></pre>



<h2 class="wp-block-heading" id="h-quick-start-with-stoplight-platform">Quick Start with Stoplight Platform</h2>



<p>Stoplight Platform comes with a set of public style guides that can be enabled within your Stoplight workspace with a single click. The OWASP Top 10 is one of those and can be enabled through the user interface.</p>



<ol class="wp-block-list">
<li>Go to your Stoplight workspace.</li>



<li>Create a style guide project OR edit a project that has an API.</li>



<li>Select Manage Style Guides.</li>



<li>Enable <code>OWASP Top 10 2023</code> from a list of public style guides.</li>
</ol>



<figure class="wp-block-image size-full"><img alt="" class="wp-image-1252" height="306" src="https://blog.stoplight.io/wp-content/uploads/2024/03/spectral1.png" width="953" /></figure>



<p>These rules will then start showing up in Stoplight Studio as you work, letting you know about issues in real time before you even commit anything back. </p>



<figure class="wp-block-image size-large"><img alt="" class="wp-image-1253" height="510" src="https://blog.stoplight.io/wp-content/uploads/2024/03/spectral2-1024x510.png" width="1024" /></figure>



<h2 class="wp-block-heading" id="h-upgrading">Upgrading</h2>



<p>If you have already set the OWASP ruleset up via NPM, you can update to this new v2.0.0 version by updating your <code>package.json</code> and running <code>npm update</code>, or run the following command:</p>



<pre><code class="language-plaintext hljs">npm install --save -D @stoplight/spectral-owasp-ruleset@^2.0.0
</code></pre>



<p>For Stoplight Platform users, enable the <code>OWASP Top 10 2023</code> style guide from the Manage Style Guides option within your workspace. </p>



<h2 class="wp-block-heading" id="h-other-changes">Other Changes</h2>



<p>Other than the above updates, there are a few other changes to existing rules, mostly focused on changing names to match the categories changing around. These will mostly only affect people who were customizing, extending, or disabling any of the following rules, but keep an eye out for the severity changes.</p>



<ul class="wp-block-list">
<li>Renamed <code>owasp:api1:2019-no-numeric-ids</code> to <code>owasp:api1:2019-no-numeric-ids</code>. </li>



<li>Renamed <code>owasp:api2:2019-protection-global-unsafe-strict</code> to <code>owasp:api2:2023-write-restricted</code>. </li>



<li>Renamed <code>owasp:api2:2019-protection-global-safe</code> to <code>owasp:api2:2023-read-restricted</code> and increased severity from <code>info</code> to <code>warn</code>. </li>



<li>Renamed <code>owasp:api2:2019-auth-insecure-schemes</code> to <code>owasp:api2:2023-auth-insecure-schemes</code>. </li>



<li>Renamed <code>owasp:api2:2019-jwt-best-practices</code> to <code>owasp:api2:2023-jwt-best-practices</code>. </li>



<li>Renamed <code>owasp:api2:2019-no-api-keys-in-url</code> to <code>owasp:api2:2023-no-api-keys-in-url</code>. </li>



<li>Renamed <code>owasp:api2:2019-no-credentials-in-url</code> to <code>owasp:api2:2023-no-credentials-in-url</code>. </li>



<li>Renamed <code>owasp:api2:2019-no-http-basic</code> to <code>owasp:api2:2023-no-http-basic</code>. </li>



<li>Renamed <code>owasp:api3:2019-define-error-validation</code> to <code>owasp:api8:2023-define-error-validation</code>. </li>



<li>Renamed <code>owasp:api3:2019-define-error-responses-401</code> to <code>owasp:api8:2023-define-error-responses-401</code>. </li>



<li>Renamed <code>owasp:api3:2019-define-error-responses-500</code> to <code>owasp:api8:2023-define-error-responses-500</code>. </li>



<li>Renamed <code>owasp:api4:2019-rate-limit</code> to <code>owasp:api4:2023-rate-limit</code>. </li>



<li>Renamed <code>owasp:api4:2019-rate-limit-retry-after</code> to <code>owasp:api4:2023-rate-limit-retry-after</code>. </li>



<li>Renamed <code>owasp:api4:2019-rate-limit-responses-429</code> to <code>owasp:api4:2023-rate-limit-responses-429</code>. </li>



<li>Renamed <code>owasp:api4:2019-array-limit</code> to <code>owasp:api4:2023-array-limit</code>. </li>



<li>Renamed <code>owasp:api4:2019-string-limit</code> to <code>owasp:api4:2023-string-limit</code>. </li>



<li>Renamed <code>owasp:api4:2019-string-restricted</code> to <code>owasp:api4:2023-string-restricted</code> and downgraded from <code>error</code> to <code>warn</code>. </li>



<li>Renamed <code>owasp:api4:2019-integer-limit</code> to <code>owasp:api4:2023-integer-limit</code>. </li>



<li>Renamed <code>owasp:api4:2019-integer-limit-legacy</code> to <code>owasp:api4:2023-integer-limit-legacy</code>. </li>



<li>Renamed <code>owasp:api4:2019-integer-format</code> to <code>owasp:api4:2023-integer-format</code>. </li>



<li>Renamed <code>owasp:api6:2019-no-additionalProperties</code> to <code>owasp:api3:2023-no-additionalProperties</code> and restricted rule to only run the <code>oas3_0</code> format. </li>



<li>Renamed <code>owasp:api6:2019-constrained-additionalProperties</code> to <code>owasp:api3:2023-constrained-additionalProperties</code>  and restricted rule to only run the <code>oas3_0</code> format. </li>



<li>Renamed <code>owasp:api7:2023-security-hosts-https-oas2</code> to <code>owasp:api8:2023-no-scheme-http</code>. </li>



<li>Renamed <code>owasp:api7:2023-security-hosts-https-oas3</code> to <code>owasp:api8:2023-no-server-http</code>. </li>
</ul>



<p>See the <a href="https://github.com/stoplightio/spectral-owasp-ruleset/blob/b3174a90fbe80f5693cb13f8a5bd41bb6b70d7c6/CHANGELOG.md" rel="noreferrer noopener" target="_blank">CHANGELOG.md</a> for the entire list of changes.&nbsp;</p>
<p>The post <a href="https://blog.stoplight.io/spectral-owasp-api-2023-security-ruleset">Spectral OWASP API Security Ruleset</a> appeared first on <a href="https://blog.stoplight.io">Stoplight</a>.</p>
