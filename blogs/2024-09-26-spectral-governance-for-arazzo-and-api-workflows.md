---
title: "Spectral Governance for Arazzo and API Workflows"
url: "https://blog.stoplight.io/spectral-governance-for-arazzo-and-api-workflows"
date: "Thu, 26 Sep 2024 12:00:00 +0000"
author: "​​Frank Kilcommins​"
feed_url: "https://blog.stoplight.io/feed"
---
<h2 class="wp-block-heading">Spectral adds Support for the Arazzo Specification</h2>



<p>At SmartBear and Stoplight, we’ve always had our finger close to the pulse of the API specifications landscape, and with that, we seek to ensure we’re delivering tools to help your organization achieve consistent quality.</p>



<p>Standardization remains a top concern for companies. With developer experience weighing heavy in differentiating your offerings from the competition, we advocate for treating API descriptions the same way as code. This means that APIs (and related artefacts) should have style guides with rules; they should be reviewed to ensure they are concise, yet readable and descriptive for the developers who use them.</p>



<p>In the rapidly evolving API landscape, increasing levels of abstraction are creating new challenges. APIs are no longer just consumed by human developers, but by AI-agents and other automated systems, which brings unique demands. These new consumers benefit from deterministic, predictable interactions that can be directly understood and executed by machines. This shift is driving the need for new API specifications, such as the Arazzo Specification, which is designed to define API-oriented use cases in a way that is not only human-readable but also semantically rich for agentic consumption. Arazzo enables interoperability across diverse systems while ensuring workflows are clear, assertable, and reusable for both humans and AI alike.</p>



<h2 class="wp-block-heading">Why Arazzo?</h2>



<p>The <a href="https://www.openapis.org/arazzo">Arazzo Specification</a> defines a standard, programming language-agnostic mechanism to express sequences of calls and articulates the dependencies between them to achieve a particular outcome, or set of outcomes, when dealing with API descriptions (such as OpenAPI descriptions).</p>



<p>The Arazzo Specification can articulate these workflows in a deterministic human-readable and machine-readable manner, thus improving provider and consumer experiences when working with APIs. Like what OpenAPI has done for describing HTTP interfaces, the Arazzo Specification enables the ability to articulate the functional use cases offered by an API (or group of APIs) thus removing the guesswork for both human and machine consumers.</p>



<p>Use cases for machine-readable API workflow definition documents include, but are not limited to:</p>



<ul class="wp-block-list">
<li>interactive living workflow documentation</li>



<li>automated documentation generation (e.g. Developer Portal documentation)</li>



<li>code and SDK generation driven by functional use cases</li>



<li>automation of test cases</li>



<li>automated regulatory compliance checks</li>



<li>deterministic API invocation by AI-based LLMs</li>
</ul>



<h2 class="wp-block-heading">Arazzo Support is a Game Changer</h2>



<p>As the market heats up with Arazzo-backed tooling, we want to be there to ensure that authors have the confidence that they can craft Arazzo Descriptions, either by hand, or valid- generated Arazzo Documents from AI-augmented tooling.</p>



<h2 class="wp-block-heading">Introducing Native Arazzo Support within Spectral</h2>



<p>And with that… we’re delighted to announce that Spectral has launched native support for the Arazzo Specification with the Arazzo ruleset. This milestone adds on top of previous launches of AsyncAPI support, giving greater flexibility to Spectral users to ensure consistency and quality of their APIs across OpenAPI, AsyncAPI, and now Arazzo.</p>



<p>For the Spectral community there is no big lift, all you need to do to enable Arazzo linting is update to the latest CLI version and add <code>spectral:arazzo</code> to your ruleset file. That’s it!</p>



<p class="has-text-align-center"><img alt="" class="wp-image-1270" height="195" src="https://blog.stoplight.io/wp-content/uploads/2024/09/spectral-arazzo-ruleset.png" style="width: 550px;" width="550" /></p>



<h2 class="wp-block-heading">Getting Started with Arazzo &amp; Spectral</h2>



<p>Linting Arazzo Descriptions is like linting OpenAPI Description or AsyncAPI Documents, meaning that most instructions or documentation you’ll find out there in the wild about Spectral will also apply to linting Arazzo with Spectral.</p>



<p>Together with the maintainers of the Arazzo Specification, we maintain a core Arazzo ruleset containing a suite of useful rules that you can leverage with just the above <code>extends</code> option to get started with Arazzo governance within your workflow.</p>



<h2 class="wp-block-heading">Step 1: Install Spectral</h2>



<p>To get started, you need to <a href="https://meta.stoplight.io/docs/spectral/docs/getting-started/2-installation.md">install Spectral first</a>. Note that you need to have <a href="https://docs.npmjs.com/downloading-and-installing-node-js-and-npm">npm</a> or <a href="https://yarnpkg.com/getting-started/install">Yarn</a> installed, running <code>npm install -g @stoplight/spectral-cli</code> or <code>yarn global add @stoplight/spectral-cli</code> is sufficient to get Spectral.</p>



<p>The CLI package bundles <a href="https://www.npmjs.com/package/@stoplight/spectral-rulesets">@stoplight/spectral-rulesets</a> which contains the actual ruleset we’ll use. If you intend to use an older version of the Arazzo ruleset, you could additionally install a different version of <code>@stoplight/spectral-rulesets</code> (this depends on the date you’re reading this blog). However, it’s an optional step, and it&#8217;s generally recommended to stick with the latest versions where possible.</p>



<h2 class="wp-block-heading">Step 2: Create a Ruleset</h2>



<p>Once you’ve got all the required dependencies installed, the next item on the agenda is to create a simple ruleset. To do so, create a file called <code>.spectral.json</code> (normally in the location of the API artefacts, but you can specify during linting commands so don’t worry). The following template acts as a decent baseline.</p>



<pre class="wp-block-code"><code>{ 
    //step 2 
    // This makes sure our rules apply only to Arazzo documents.
    // It might be handy in case you have other specs in the directory you intend to lint.
    "formats": &#91;"arazzo1_0"],
    // this includes the ruleset linked below
    // https://docs.stoplight.io/docs/spectral/96c7245b504b1-arazzo-rules
    // Note that by default, only recommended rules are enabled.
    // Some rules listed in the article above may not be a fit for you,
    // therefore we don’t enable them by default.
    "extends": "spectral:arazzo",
    "rules": {
      // you can add your own rules here
    }
  }</code></pre>



<h2 class="wp-block-heading">Step 3: Create Custom Rules</h2>



<p>The native Arazzo ruleset acts as a great (slightly opinionated) base ruleset, that focuses primarily on ensuring validity of the Arazzo Description being linted. However, to better facilitate your unique company requirements or definition of what “good” looks like, Spectral allows you to create your own rules, and ultimately form your own style guide for how Arazzo Descriptions should be crafted to meet your goals.</p>



<p>There’s plenty of documentation out there on how to craft Spectral rules, but for the purposes of this blog, let’s create a simple custom rule for our Arazzo ruleset within the <code>rules</code> property of the template. The rule will verify the following:</p>



<ul class="wp-block-list">
<li>A <code>version</code> property is defined within the Arazzo Info object.</li>



<li>The <code>version</code> property following a versioning pattern (e.g. 0.x.x).</li>
</ul>



<pre class="language-json"><code>{ 
  "formats": &#91;"arazzo1_0"],
  "extends": "spectral:arazzo",
  "rules": {
    "valid-document-version": {
      "message": "Version must match 0.x.x",
      "severity": "error",
      "given": "$.info",
      "then": &#91;
        {
          "field": "version",
          "function": "defined"
        },
        {
          "field": "version",
          "function": "pattern",
          "functionOptions": {
            "match": "^0(\\.&#91;0-9]+){2}$"
          }
        }
      ]
    } 
  }
}</code></pre>




<h2 class="wp-block-heading">Step 4: Lint Arazzo with Spectral</h2>



<p>Now that we have all the pieces together, we can run Spectral to lint Arazzo Descriptions that we create. Let’s take the <code>LoginAndRetrievePets</code> example from the <a href="https://github.com/OAI/Arazzo-Specification/blob/main/examples/1.0.0/LoginAndRetrievePets.arazzo.yaml">Arazzo Specification repository</a>.</p>



<p>Here’s the content of that example at the time of writing:</p>



<pre class="wp-block-code"><code>arazzo: 1.0.0
info:
    title: A pet purchasing workflow
    summary: This workflow showcases how to purchase a pet through a sequence of API calls
    description: |
        This workflow walks you through the steps of `searching` for, `selecting`, and `purchasing` an available pet.
    version: 1.0.1
sourceDescriptions:
- name: petStoreDescription
  url: https://github.com/swagger-api/swagger-petstore/blob/master/src/main/resources/openapi.yaml
  type: openapi

workflows:
- workflowId: loginUserRetrievePet
  summary: Login User and then retrieve pets
  description: This procedure lays out the steps to login a user and then retrieve pets
  inputs:
      type: object
      properties:
          username:
              type: string
          password:
              type: string
  steps:
  - stepId: loginStep
    description: This step demonstrates the user login step
    operationId: petStoreDescription.loginUser
    parameters:
      # parameters to inject into the loginUser operation (parameter name must be resolvable at the referenced operation and the value is determined using {expression} syntax)
      - name: username
        in: query
        value: $inputs.username
      - name: password
        in: query
        value: $inputs.password
    successCriteria:
      # assertions to determine step was successful
      - condition: $statusCode == 200
    outputs:
      # outputs from this step
      tokenExpires: $response.header.X-Expires-After
      rateLimit: $response.header.X-Rate-Limit
      sessionToken: $response.body
  - stepId: getPetStep
    description: retrieve a pet by status from the GET pets endpoint
    operationPath: $sourceDescriptions.petStoreDescription#/paths/~1pet~1findByStatus
    parameters:
      - name: status
        in: query
        value: 'available'
      - name: Authorization
        in: header
        value: $steps.loginUser.outputs.sessionToken
    successCriteria:
      - condition: $statusCode == 200
    outputs:
      # outputs from this step
      availablePets: $response.body
  outputs:
      available: $steps.getPetStep.availablePets</code></pre>



<p>To lint, all that’s needed is to execute the following command:</p>



<pre class="wp-block-code"><code>spectral lint LoginAndRetrievePets.arazzo.yml</code></pre>



<p><img alt="" class="wp-image-1272" height="76" src="https://blog.stoplight.io/wp-content/uploads/2024/09/arazzo-feedback.png" style="width: 850px;" width="850" /></p>



<p>Thanks to the built-in Arazzo ruleset, we get more feedback than just information about invalid document versions. The value of the ruleset paid off immediately, allowing us to submit fixes to the Arazzo project to improve the repository examples. Tada! <img alt="🎉" class="wp-smiley" src="https://s.w.org/images/core/emoji/17.0.2/72x72/1f389.png" style="height: 1em;" /></p>



<p>The default configuration should be reasonable for most looking to lint and govern their Arazzo Descriptions, but if you want to tweak anything, refer to the <a href="https://meta.stoplight.io/docs/spectral/ZG9jOjI1MTg5-custom-rulesets#modifying-rules">documentation</a> that explains everything.</p>



<p>We’re looking forward to the acceleration in Arazzo usage and giving you the ability to keep your workflows in control!</p>



<h3 class="wp-block-heading">Additional Community Contributions</h3>



<p>Also included in the latest Spectral CLI release are several community-contributed PRs. Here are two of note:</p>



<ul class="wp-block-list">
<li><a href="https://github.com/smsalisbury">Spencer Salisbury</a> added a code-climate output formatter for GitLab.</li>



<li><a href="https://github.com/ouvreboite">Jean-Baptiste Muscat</a> added a markdown output formatter.</li>
</ul>



<p>For a full list of changes in the new Spectral release, check out the <a href="https://github.com/stoplightio/spectral/releases/tag/v6.13.1">release notes</a>.</p>
<p>The post <a href="https://blog.stoplight.io/spectral-governance-for-arazzo-and-api-workflows">Spectral Governance for Arazzo and API Workflows</a> appeared first on <a href="https://blog.stoplight.io">Stoplight</a>.</p>
