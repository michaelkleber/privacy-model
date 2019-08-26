# A Potential Privacy Model for the Web
## Sharding Web Identity


The identity model of the web has been the implicit result of two interacting browser capabilities:

*   Per-domain state, especially _cookies_, which let one eTLD+1 maintain a consistent notion of a visitor's identity.  This identity extends across top-level sites due to 3p cookies, storage within iframes, etc.

*   _In-browser passing of information_, among the parties co-occurring on a web page (via mechanisms like shared state in DOM or JS, or HTTP redirects, or postMessage).

This combination has led to widely-shared cross-site identities, and so to an ability to perform web-wide tracking of a person's browsing activity.  Global static identifiers (like [device fingerprinting](https://github.com/bslassey/privacy-budget) or user-provided PII) also offer an independent path to global identity.  Limitations on cookies, fingerprinting, and other browser state all aim to reduce this ability to create or access a global identity.

On the one hand, global identity gives rise to the capacity to weave together a record of much of a person's browsing history, a core privacy concern with today's web.  Browsers are well-positioned to take action on this issue, by imposing limits on the underlying capabilities exposed to developers.  On the other hand, global identity also plays a substantial role in today's web advertising ecosystem.  Browsers that impose limitations on these technical capabilities can directly affect [publishers' economic viability](https://www.blog.google/products/ads/next-steps-transparency-choice-control/) and encourage work-arounds, if they haven't provided for the legitimate needs of the ecosystem.

This document describes a way the web could potentially work that would not require cross-site tracking, but would still let publishers support themselves with effective advertising.  See [this Chrome blog post](https://www.blog.google/products/chrome/building-a-more-private-web/) for more.

We need a dialogue within the web platform community — including browsers and the many stakeholders that thrive in and contribute to the ecosystem — so that we can clearly describe a new identity end state that works for everyone.  For browsers, this gives us a framework for evaluating proposed changes, standards, and APIs: what boundaries we need to enforce, and where we should innovate to improve the web.  For developers, a clear long-term vision provides stability and predictability of the changes along the way.

Any discussion of a new identity model must answer two specific questions:



1.  **Across what range of web activity does the browser let websites treat a person as having a single identity?**
1.  **In what ways can information move across identity boundaries without compromising that separation?**

This document offers one possible answer to these questions.  The goal is a balanced way forward, dramatically improving web privacy while allowing enough information flow to carefully support key needs of publishers and advertisers.


# Identity is partitioned by First Party Site



*   The identity "Me while I'm visiting nytimes.com" is distinct from the identity "Me while visiting cnn.com".
*   The central privacy threat is joining these per-site identities across distinct first parties.  Browsers impose limits (on cookies, fingerprinting, and other state) with the goal of preventing the joinability of these per-1p identities.
*   The notion of "First Party" may expand beyond eTLD+1, e.g. as proposed in [First Party Sets](https://mikewest.github.io/first-party-sets/).  It is reasonable for the browser to relax its identity-sharing controls within that expanded notion, provided that the resulting identity scope is [not too large](https://mikewest.github.io/first-party-sets/#how-will-malicious-actors-abuse-this-mechanism) and can be understood by the user.
*   Per-site identities can become joined across first parties, due to user actions like "Pay with PayPal", "Log In with Facebook" or "Use your email address as your account name".  Browsers should help their users understand the implications of allowing that joining, and should be supportive of ways to access sites that do not require that cross-site joining.


# Third Parties can be allowed access to a first-party identity



*   First parties have a way to delegate access to a user identity to specific 3p's, as long as that delegated identity remains sharded by 1p.  (It seems like an open question whether or not this identity should be double-keyed, i.e. sharded by 3p as well: consistency across 3p's would make the identity easier to use, but that must be balanced against the risk to the central threat of joining across per-site identities.)
*   Access to the per-first-party identity can be a 3p privilege, not a right — 1p's should be able to exercise control over which of their 3p's have access to a per-1p identity.  In particular, the browser mechanism for 3p access to identity must not make it promiscuously available merely based on script access to a browsing context.  (It seems like an open question what sort of default, block-list, and allow-list the 1p should have to control per-3p access to a per-1p identity.)
*   This recognizes that composability is central to the Web — for example, it is unreasonable to expect 1p's to implement their own analytics or ad servers.
*   The intent is to let the first party retain control over identity on their site.  A 3p who builds a per-1p user profile should be in the same position as any other company with whom the 1p chooses to share its user data.


# A per-first-party identity can only be associated with small amounts of cross-site information



*   The fuzziness of "small amounts of information" recognizes the balancing act that browsers need to perform between user privacy and web platform usability.  Potential use cases must respect the invariant that it remain hard to join identity across first parties, but subject to that limit, there is room to allow sufficiently useful information to flow in a privacy-respecting way.  Both "sufficiently useful" and "privacy-respecting" must be evaluated on a case-by-case basis.
*   It may be OK to associate a user's per-first-party identity with cross-site-derived information, provided that information is not too personal or too identifying.  For example, it may be reasonable for myfavoritepublisher.com to learn something about its users' rest-of-the-web interests, provided those interests are shared by thousands of different myfavoritepublisher users.  (The [FLoC API explainer](https://github.com/jkarlin/floc) describes an approach that could address this use case.)  This can be very important in picking the right ad to show to a user.
*   It may be OK to associate a user's per-first-party identity with a small amount of information about a cross-site event which is related, important, and rare.  For example, once a user clicks on an ad on a first-party site, it may be OK to associate the fact that the user later converted on the destination site, as long as there is very little additional information beyond the fact that a conversion took place.  (The [Conversion Measurement API](https://github.com/csharrison/conversion-measurement-api) describes an approach that could address this use case.)  This can be very important to advertisers who need to understand how to best spend their money.
*   It may be OK for a site to learn the fact that a user has earned trust on another site — for example, solved a CAPTCHA, become a subscriber, etc.  (The [Trust Tokens API](https://github.com/dvorak42/trust-token-api) based on [Privacy Pass](https://github.com/alxdavids/draft-privacy-pass) describes an approach that could address this use case.)  This can be very important for fraud and abuse prevention.
*   It would _not_ be OK for one site to learn the list of all domains visited by a given user (or even "most domains that include ads" visited by that user).  All APIs which allow small amounts of cross-site information passing must be evaluated for this sort of abuse potential.
*   Any use cases which rely on _more than_ a small amount of cross-site information must not be able to associate that information with _any_ per-first-party identity.  The [Conversion Measurement with Aggregation Explainer](https://github.com/csharrison/conversion-measurement-api/blob/master/AGGREGATE.md) is an exploration of using aggregation to get away from identity entirely.

We think a shared understanding of a privacy model for the web will be important to future standards discussions.  We welcome feedback from the web development community and from members of the broader ecosystem.


# Related Work

The [Tor Browser design's "Privacy Requirements"](https://2019.www.torproject.org/projects/torbrowser/design/#privacy), particularly the clear emphasis on "Unlinkability".

Mozilla has published an anti-tracking policy: https://wiki.mozilla.org/Security/Anti_tracking_policy

WebKit published a related tracking prevention policy: https://webkit.org/tracking-prevention-policy/

Threat models and prevention policies are different parts of the same conversation.
