---
title: "Down the API Authentication Rabbit Hole with Dan Moore from FusionAuth"
url: "https://blog.stoplight.io/api-authentication-with-dan-moore"
date: "Tue, 19 Dec 2023 14:00:00 +0000"
author: "Jason Harmon CTO"
feed_url: "https://blog.stoplight.io/feed"
---
<p>Over my career, I’ve helped a wide variety of companies build their platform strategy, and one thing always seems too true: the “long pole in the tent” has always been auth and identity systems.</p>



<p>Scaling these systems in a secure way can strain the growth of a platform, which presents us with layers of tricky design problems. Furthermore, the last few years in the security space have been marked by API-based attack vectors, and the pace of exploitation is picking up.</p>



<p>As API practitioners, we have a lot of room to improve on API authentication, and we were fortunate to have Dan Moore to help walk us through it all. In this episode, we take a deep dive into OAuth, tokens, scopes, and how to avoid creating security risks that could lead to highly damaging data breaches.</p>



<h2 class="wp-block-heading" id="h-broken-authorization-is-the-norm-owasp-api-top-10">Broken Authorization is the Norm: OWASP API Top 10</h2>



<p>In 2023, the OWASP project (Open Worldwide Application Security Project) updated its <a href="https://owasp.org/API-Security/editions/2023/en/0x11-t10/">Top 10 API Security Risks</a> guidance. This list attempts to capture the most common root cause for exploits – which is also to say the aspects developers get wrong most often. OWASP has been sharing this information on general application development for over 20 years, but since 2019 they have shared an API-centric list.</p>



<p>In both the general “Top 10” and the API Top 10, the top risk for many years has been BOLA (Broken Object Level Authorization). As this has been the most common exploit for many years, we went deep with Dan on breaking down how to approach your platform development to minimize BOLA risks.</p>



<h2 class="wp-block-heading" id="h-what-problem-are-you-solving-oauth-solves-for-nearly-all-of-them">What Problem Are You Solving? OAuth Solves for Nearly All of Them</h2>



<p>We can’t talk about API authorization without first addressing OAuth. This framework was created in 2006, and the 2.0 version was published as <a href="https://datatracker.ietf.org/doc/html/rfc6749">RFC6749</a> over ten years ago. Version 2.1 is in draft, and covers mobile and browser-based apps, as well as a wide variety of security best practices, especially around tokens.</p>



<p>It’s safe to say this is the de facto standard for access delegation, very mature, which is critical to exposing data from your platform. Over the course of my conversation with Dan, we agreed vehemently that OAuth is the way forward, and those who choose to do it on their own without OAuth are set up to fail.</p>



<p>As OAuth is a sprawling topic, we just scratch the surface in our conversation, but Dan helped us walk through some of the more common considerations.</p>



<p>The first question you should ask is very customer-centric in nature: what problem are you solving? Dan’s team at FusionAuth has shared their breakdown of <a href="https://fusionauth.io/docs/lifecycle/authenticate-users/oauth/modes">OAuth “modes”</a>, 8 different implementation patterns that you should be aware of, and intentional with choosing. This is an area that you do not want to “boil the ocean”, i.e. implement the modes your customers and partners need, not everything possible.</p>



<p>While this can seem intimidating at first, rest assured your use case is contained in these 8 modes, and OAuth has been battle-tested for many years in all modes.</p>



<h2 class="wp-block-heading" id="h-authorization-is-shared-business-logic">Authorization is Shared Business Logic</h2>



<p>Beyond OAuth, when we think about how and where to implement authorization, Dan reminds us that AuthZ/authorization is fundamentally business logic. As with any other implementation that includes business logic, we should be mindful of how to implement this in the lowest complexity that is appropriate.</p>



<p>In larger more mature platforms, RBAC/ABAC systems are common practice, but often not where we get started. Much in the same way that most platforms start as monoliths and migrate to microservices to achieve scale, authorization code most commonly lives in the API’s business logic and migrates to distributed authorization systems to achieve scale.</p>



<p>Dan suggests that extracting access control logic into a library shared across your codebase is a suitable place to start. This is a fitting example of where code maintainability can mitigate real-world security risks: if you have access control logic sprinkled around your API implementations, it can become unwieldy to understand it.</p>



<p>Additionally, if we accept that as things scale out, you will want a centralized authorization infrastructure, abstracting this logic into a library will set you up for success when that time comes.</p>



<h2 class="wp-block-heading" id="h-scopes-and-claims-are-a-design-challenge">Scopes and Claims are a Design Challenge</h2>



<p>Regardless of the implementation approach, you should be extremely cautious with how you define your access controls, as this is where BOLA risks present themselves. In the OAuth framework, access control claims are expressed as scopes (<a href="https://oauth.net/2/scope/">https://oauth.net/2/scope/</a>), especially relevant in third-party authorization flows.</p>



<p>To help explain the concept of third-party flows, Dan offers an example: Zendesk stores tickets for your company, and a third-party platform might need access to analyze these tickets. In that scenario, the “first party”, i.e. the user who already has access, is prompted to grant access to this third party. The list of data the third party wants access to will be presented to the user so they have clarity as to which data they have granted. That list is based on OAuth scopes.</p>



<p>The first big design problem with OAuth scopes is granularity. This is a classic <a href="https://en.wikipedia.org/wiki/Goldilocks_principle">“Goldilocks principle”</a> problem that we often see in API design: too coarse-grained or too fine-grained definitions can present issues on either end of the spectrum and finding that balance is a bit more art than science.</p>



<p>If we provide super fine-grained scopes for every piece of data we want to share, the end-user could be presented with a massive list of scopes, which could be intimidating and confusing, and will hurt adoption. On the contrary, if we define one huge scope of access, users might feel a lack of trust in overexposing their information to third parties.</p>



<p>Dan suggests that scopes should be constrained to be smaller (this certainly fulfills the “rule of least privilege” principle), but the big-picture design should be kept in mind to avoid overwhelming users with too many scopes.</p>



<h2 class="wp-block-heading" id="h-edge-vs-service-access-control-the-answer-is-probably-both">Edge vs. Service Access Control? The Answer is (Probably) Both.</h2>



<p>Upon discovering scopes, it might be tempting to evaluate scopes at the edge (i.e. hosted in CDN/ADN platforms) for all types of access control on every request, offloading the work from service implementations and centralizing logic. However, in the real world, we often need finer-grained business logic that can only be served from the application itself.</p>



<p>In terms of platform evolution, we reiterated that most of the time this logic starts in the service and migrates to the edge. However, it is important to recognize that not all access control logic can be abstracted to scopes and hosted at the edge.</p>



<p>Do not let the mix of approaches become a heated debate on picking one; in any mature platform, both approaches are likely to be in play in concert.</p>



<h2 class="wp-block-heading" id="h-json-web-tokens-as-an-access-control-envelope">JSON Web Tokens as an Access Control Envelope</h2>



<p>In most modern OAuth implementations, we see JWT (JSON Web Token) utilized to contain relevant information needed on each request to provide adequate high-level access control. The data contained in these JWTs is what can be evaluated as business logic at the edge and within your API application code.</p>



<p>In the course of evaluating token contents, trust is the name of the game. You should not blindly trust the contents of a token on each call. Dan gave us several things to watch for, as well as some typical approaches:</p>



<ul class="wp-block-list">
<li><strong>Validation:</strong> signed JWT can be validated without calling a central authorization server, which can offer much greater scale potential.</li>



<li><strong>Introspection: </strong>a call can be made to the token issuer to “rehydrate” a token with more detailed information, implicitly verifying trust with the issuer. </li>



<li><strong>Claim inspection:</strong> your application should Inspect the claim values (represented as scopes) in the token (or the response from the issuer if using introspection). </li>



<li><strong>Account-level constraints:</strong> especially in SaaS (Software as a Service) APIs, premium features are often restricted based on the user’s account tier/level. </li>



<li><strong>Subclaim size:</strong> be mindful of the data included in subclaims in the JWT; it can be tempting to stuff everything you might ever need to evaluate a request in the JWT. </li>



<li><strong>Cookies for browser-based apps:</strong> for browser-based applications, data stored in cookies can provide many advantages; however, this will not help in server-to-server style integrations. </li>



<li><strong>Don’t leave your keys lying around:</strong> Dan suggests tokens are like a car key; if you leave them lying around your car can easily be stolen.</li>



<li><strong>Symmetrical vs. public/private cryptography:</strong> public/private cryptography is expensive and is often used in externally provided tokens.</li>
</ul>



<h2 class="wp-block-heading" id="h-key-takeaways-and-getting-started">Key Takeaways and Getting Started</h2>



<p>API authorization can be an overwhelming topic, and the risks have never been greater. Dan gave us some pointers to boil this down to some simple concepts to get you started:</p>



<ul class="wp-block-list">
<li><strong>Keep it simple:</strong> if simple API keys get the job done, go with it. Distributed keys and access control management can get pretty complicated and are often not the best place to start. Distributed logic can evolve over time as you need to solve those sorts of problems.</li>



<li><strong>Start centralizing with code libraries:</strong> if you’re starting from scratch, abstract your authorization logic into a library, and perhaps as you scale up, you can shift this logic into an authorization server.</li>



<li><strong>Application vs. edge authorization:</strong> not everything can be at the edge, but with token-based claims, the best scale and future-proofed approach is to manage the majority of authorization at the edge.</li>
</ul>



<h2 class="wp-block-heading" id="h-the-most-important-recap-item-is-simple-do-not-build-it-yourself">The Most Important Recap Item is Simple: Do Not Build It Yourself</h2>



<p>There are many commercial and open-source options available to implement OAuth/JWT/etc, and it’s a mistake to scratch build anything auth-related. Many of the big data breaches have been caused by the “not built here” syndrome; developers who insist on building their own solutions rather than using something that already exists.</p>



<p>Obviously, Fusionauth is a great option, but Dan mentions other options out there like Keycloak and Auth0, as well as many open-source options available in all coding languages.</p>



<p>Building your own auth solutions is harder and more expensive than you think, and the risks are almost never worth it.</p>



<p>With all that in mind, go get started, and keep your customers’ security and privacy in a good place!</p>



<figure class="wp-block-embed is-type-video is-provider-youtube wp-block-embed-youtube wp-embed-aspect-16-9 wp-has-aspect-ratio"><div class="wp-block-embed__wrapper">

</div></figure>
<p>The post <a href="https://blog.stoplight.io/api-authentication-with-dan-moore">Down the API Authentication Rabbit Hole with Dan Moore from FusionAuth</a> appeared first on <a href="https://blog.stoplight.io">Stoplight</a>.</p>
