# Content Classification Framework (CCF)

### A privacy-preserving alternative to identity-based age verification

**Status: Draft for discussion | Version 1.1, April 2026**

The Content Classification Framework (CCF) proposes a different approach to protecting children from harmful online content.

Instead of requiring users to prove their age or identity to websites, **websites classify the content they serve and the user's device decides locally what may be displayed.**

> **Don't ask the user who they are. Ask the content what it is.**

## The problem

Current approaches to age-restricted online content generally put the verification decision at the point of consumption.

A user may be required to provide an age credential, identity information, biometric verification, or other evidence before accessing a website.

This creates a fundamental tension:

* Strong privacy requires the verifier to know as little as possible about the user.
* Effective verification requires confidence that the person currently accessing the content is the person whose age was verified.
* Privacy-preserving credentials can create relay and credential-sharing problems.
* Escalating verification can lead to increasingly invasive forms of verification.

CCF moves the protection mechanism to a different architectural layer: **the device.**

## The CCF approach

The information flow is reversed.

### Identity-based age verification

```text
USER
  |
  |  "Prove who you are / how old you are"
  v
WEBSITE / VERIFIER
  |
  |  Identity / age decision
  v
ALLOW OR DENY
```

### Content Classification Framework

```text
WEBSITE
  |
  |  "This is what the content contains"
  v
DEVICE
  |
  |  Local parental / age-appropriate policy
  v
SHOW / HIDE / BLUR / MUTE / SKIP
```

The website does not need to know whether the visitor is a child or an adult.

The device does not need to send the user's identity or age to the website.

The decision happens locally.

## What this enables

CCF is designed to provide **granular content protection rather than binary website access control**.

A page can remain available while individual content is filtered.

For example:

* A news article remains accessible.
* A graphic photograph within the article is blurred.
* Educational sexual-health information can remain accessible.
* Explicit sexual material can be blocked.
* A film can continue playing while a restricted scene is skipped or muted.
* An embedded third-party resource can be evaluated independently.

The framework therefore does not require the choice to be simply **"block the website" or "allow everything."**

## Classification layers

CCF proposes four complementary mechanisms.

### 1. Domain-level classification

A site can publish:

```text
/.well-known/content-rating.json
```

This provides a default classification for a domain, with optional path-specific classifications.

### 2. Page-level classification

An HTTP response can include a machine-readable classification:

```http
Content-Rating: adult/sexual
Content-Rating-Source: platform-classified
```

The page-level classification overrides the domain default.

### 3. Element-level classification

Individual HTML elements can carry a classification:

```html
<div data-content-rating="mature/violence">
    <img src="conflict-photo.jpg" alt="Conflict zone">
</div>
```

This allows a device to filter a particular image or element while leaving the surrounding page available.

### 4. Segment-level classification

Streaming content delivered through HLS or DASH can carry classification metadata at the segment level.

This allows a restricted scene within an otherwise appropriate programme to be filtered without blocking the entire programme.

## Content taxonomy

The proposed taxonomy includes:

| Level              | Category                                       |
| ------------------ | ---------------------------------------------- |
| `safe`             | Suitable for all audiences                     |
| `mature/language`  | Profanity and strong language                  |
| `mature/violence`  | Violence and conflict imagery                  |
| `mature/political` | Politically sensitive or partisan content      |
| `mature/health`    | Medical, sexual-health or drug-related content |
| `adult/sexual`     | Sexually explicit content                      |
| `adult/gambling`   | Gambling, betting or casino content            |
| `adult/substances` | Alcohol, tobacco or drug-use content           |
| `adult/general`    | Other adult content                            |

The taxonomy is intentionally extensible. Devices can map classifications to their locally configured policies.

## Local enforcement

Classification is consumed and enforced on the device.

Possible implementations include:

* Device manufacturer integration
* Operating-system parental controls
* Browser-native enforcement
* Browser extensions
* Media-framework integration
* Third-party parental-control software

The initial proof-of-concept deployment can use a browser extension and community classification lists, while native browser and operating-system integration can provide stronger enforcement later.

## Unclassified content

CCF does not assume that every website will immediately participate.

Devices can choose how to handle unclassified content:

* **Allow by default**
* **Warn and allow**
* **Block by default**

This makes the absence of classification a configurable device policy rather than an architectural failure.

## Trust and misclassification

A self-classification system needs mechanisms to deal with inaccurate or deliberately misleading classifications.

CCF therefore proposes:

* Community-maintained classification lists
* Anonymous reporting of classification discrepancies
* Reputation and trust signals
* Detection of coordinated reporting abuse
* On-device ML classification as a fallback

Multiple classification lists can coexist, allowing parents and organisations to choose which sources they trust.

## Privacy

The central privacy property is architectural.

The website does not need to receive:

* Name
* Date of birth
* Passport details
* Biometric information
* Age credentials
* Proof of identity

For content enforcement, the information flows **from the website to the device, not from the user to the website**.

The website does not need to know whether its content was displayed, filtered or blocked.

## Adults are not required to participate

CCF is designed so that adults do not need to interact with an age-verification system.

There is no requirement to:

* Upload identification
* Scan a passport
* Submit biometrics
* Create an age-verification account
* Present an age credential to every website

The unrestricted experience remains the default for devices without parental restrictions.

## Anti-censorship safeguards

A content classification mechanism could potentially be abused for censorship. CCF therefore proposes explicit safeguards:

1. Classification lists are optional.
2. Lists must be transparent and auditable.
3. Conflicts between classification sources are surfaced rather than silently resolved.
4. Lists classify content rather than permanently blocking domains.
5. Parents retain the ability to approve content.
6. Multiple competing classification lists can coexist.
7. List reputation and reporting provide accountability.

CCF explicitly acknowledges that no content-classification system can be made completely censorship-proof. The objective is to make the system **censorship-resistant through transparency, optionality and competition**.

## Limitations

CCF does not claim to solve every child-safety problem.

It cannot, by itself:

* Protect children using completely unmanaged devices with no parental controls or manufacturer safeguards.
* Force websites to classify their content honestly without enforcement.
* Prevent a technically sophisticated user from circumventing controls on a device they fully control.

These limitations are explicit parts of the proposal rather than reasons to introduce progressively more invasive user surveillance.

## Why this is different

The fundamental difference is the direction of information flow.

|                               | Identity-based age verification | CCF                        |
| ----------------------------- | ------------------------------- | -------------------------- |
| User identity required        | Yes                             | No                         |
| User age disclosed to website | Yes / potentially               | No                         |
| Device-side decision          | No                              | Yes                        |
| Adult interaction required    | Yes                             | No                         |
| Granularity                   | Access / deny                   | Page / element / segment   |
| User credentials              | Required                        | None                       |
| Local enforcement             | Limited                         | Core architecture          |
| Privacy model                 | Minimise collected data         | Avoid collecting user data |

## Status

This repository contains a **first draft for discussion**.

It is intended to stimulate technical, policy, regulatory, privacy and child-safety discussion. It is not presented as a finished web standard or complete security specification.

The next stages proposed by the framework include:

1. Developing a formal standards proposal.
2. Defining the classification-list format.
3. Building community classification infrastructure.
4. Engaging device and operating-system manufacturers.
5. Engaging browser vendors.
6. Testing the framework with real platforms.
7. Developing a crowd-sourced trust layer.
8. Exploring streaming integration.

## The proposition

The central idea is simple:

> **We don't need to know who a user is to know what content they should see.**

Classify the content.

Let the device enforce the policy.

Protect children.

Keep safe material accessible.

And preserve the privacy of everyone else.

---

**Content Classification Framework v1.1**
Draft for Discussion | April 2026
