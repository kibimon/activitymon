#  MonStrPub Capsule Captures
Draft Specification

 - - -

##  Abstract  ##

This specification describes a set of basic requirements for probabilistic captures of mon in [ActivityPub][].
It defines a new item type, `mon:Capsule`, for use in captures, and describes server interactions.

 - - -

##  1. Introduction  ##

The [MonStrPub Core][] specification describes proper server behaviours for generating `mon:Capture` actions, and for generating and handling `as:Accept` and `as:Reject` actions in response.
However, it does not specify under which circumstances a `mon:Capture` should be `as:Accept`ed or `as:Reject`ed.
This specification provides a set of requirements for servers which wish to implement captures on a probabilistic basis, and defines a new item type, `mon:Capsule`, for use in capture scenarios.

###  1.1 Relationship to Other Specifications

This document is an **OPTIONAL** part of the [MonStrPub][] set of specifications.
Conformance to this specification is **RECOMMENDED** for MonStrPub implementations which intend to generate or handle `mon:Capture` activities which succeed or fail on a probabilistic basis.

This document uses the vocabulary defined in the [Mon Vocabulary][] specification.
This vocabulary itself depends on that defined in the [Roleplaying Vocabulary][] specification.

This document assumes conformance with the [MonStrPub Core][].

This document defines an [ActivityPub][] extension, and depends upon both the [Activity Vocabulary][] and [ActivityStreams 2.0][].

###  1.2 Notational Conventions

[Activity Vocabulary][] types are indicated with the prefix `as:`, which represents the `https://www.w3.org/ns/activitystreams#` base URI.
Note that in [ActivityStreams 2.0][] documents, this prefix **MAY** be omitted.

[Roleplaying Vocabulary][] types are indicated with the prefix `rp:`, which represents the `https://www.monstr.pub/ns/roleplaying#` base URI.
Note that in [ActivityStreams 2.0][] documents, this prefix **MUST** be declared in the `@context` of the document to be valid, and a different prefix **MAY** be used instead.

[Mon Vocabulary][] types are indicated with the prefix `mon:`, which represents the `https://www.monstr.pub/ns/monstrpub#` base URI.
Note that in [ActivityStreams 2.0][] documents, this prefix **MUST** be declared in the `@context` of the document to be valid, and a different prefix **MAY** be used instead.

 >  As this is still a draft specification, the above URIs may change at some point in the future.

###  1.3 Indicating Support

Regional support for this extension may be indicated by including the following URI in the `mon:supports` property of a `mon:Region`:

    https://www.monstr.pub/spec/monstrpub-capsules

##  2. Conformance  ##

All sections explicitly marked as non-normative, as well as any diagrams, examples, or notes in this specification, are non-normative.
Everything else in this specification is normative.

MonStrPub is an extension of [ActivityPub][] and inherits its conformance requirements, including its conformance classes.

MonStrPub Captures is an extension to [MonStrPub Core][], and every conforming MonStrPub Stats implementation **MUST** also conform to MonStrPub Core.

Implementations **MAY** deviate from the exact steps of algorithms defined in this specification, so long as the implemented algorithm produces the same result.

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **NOT RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in [RFC2119][].

##  3. Using Capsules  ##

###  3.1 Overview of Capsule Captures

 >  This section is non-normative.

A *capsule capture* is an attempt to `mon:Capture` a `mon:Mon` by use of a special `mon:Item`, of type `mon:Capsule`.
`mon:Capsule`s are not guaranteed to be successful—rather, they have an associated likelihood of success, which is taken into account in combination with that of the `mon:Mon` being captured.

###  3.2 Creating a Capsule Capture

A capsule capture is represented by a `mon:Capture` object whose `as:instrument` contains an `as:Link` to a `mon:Capsule` object.
The means of assigning `mon:Capsule`s to `mon:Capture`s is outside the scope of this specification.

Servers **SHOULD NOT** generate `mon:Capture` objects with more than one `mon:Capsule` as `as:instrument`.
Upon encountering such an object, servers **SHOULD** ignore all `mon:Capsule`s but one.

###  3.3 Accepted Capsule Captures

Upon receiving an `as:Accept` referencing a `mon:Capture` as the `as:object`, for which the `as:actor` is a `mon:Trainer` controlled by the server and the `as:object` is a `mon:Mon`, and which has an `as:instrument` which points to a `mon:Capsule`, the server **SHOULD** perform the following steps:

1.  Generate a cloned `mon:Mon` as described in [ActivityMon Core][].

2.  Assign a link to the `mon:Capsule` used in the `mon:Capture` to the `as:generator` property of the cloned `mon:Mon`.

##  4. The `mon:Capsule` Item  ##

+ URI: `https://www.monstr.pub/ns/monstrpub#Capsule`
+ Extends: `mon:Item`
+ Properties: Inherits all properties from `mon:Item`

Describes a capsule for use in a capsule capture.
The `rp:likelihood` property indicates the likelihood of success provided by the `mon:Capsule`, as a multiplier on normal conditions.

The `mon:Item` type **MUST** also be (explicitly) specified on `mon:Capsule`s, to ensure cross-compatibility with servers which do not implement this spec.

##  5. Determining Capsule Success  ##

Servers are free to use whatever algorithm they prefer to determine the success of any `mon:Capture` which makes use of a `mon:Capsule`.
However, an algorithm which produces similar results to the one below is **RECOMMENDED**.
In particular, the impact of `mon:captureRate` and `rp:likelihood` on captures should be roughly equivalent to that of the algorithm below.
This will help ensure captures have predictable results in a federated context.

###  5.1 Recommended Capture Algorithm

Given a `mon:Capture` for which the `as:actor` is a `mon:Trainer` and the `as:object` is a `mon:Mon`, and which has a valid `mon:Capsule` as an `as:instrument`:

1.  Ensure that the `mon:Mon` is able to be captured:

    1.  If the `mon:Mon` is not controlled by this server, then the capture **MUST** fail.
        It is the responsibility of the server which controls the `mon:Mon` to determine if a capture is a success.

    2.  If the `mon:Mon` has a `mon:Trainer` provided, or linked to, via the `as:attributedTo` property, then the capture **MUST** fail.
        Owned `mon:Mon` **MUST NOT** be captured.

2.  Let `S` be the `mon:Species` pointed to by the `rp:class` attribute of the `mon:Mon`, if valid and present.

3.  Define the variables for our algorithm:

    1.  Let `c` be the `rp:likelihood` of the `mon:Capsule`, if defined and valid.
        Otherwise, let `c` be 1.

    2.  Let `m` equal the product of any capture rate adjustments defined by external specifications, or 1 if no such adjustments apply.

    3.  Let `r` be the `mon:captureRate` of the `mon:Mon`, if defined and valid.
        Otherwise, let `r` be the `mon:captureRate` of `S`, if defined and valid.
        Otherwise, let `r` be 1.

4.  Compute the final values:

    1.  Let `n` equal `r * c * m`, clamped to the range \[0, 1].
        This is the adjusted capture rate.

    2.  Let `a` be a random floating-point number in the range \[0, 1).

5.  If `a` is less than `n`, then the capture is successful.
    Otherwise, the capture fails.

##  6. Changelog  ##

 >  This section is non-normative.

#####  2018-05-08.

 +  Added a section on indicating support for this specification.

 +  Minor edits.

#####  2018-05-05.

 +  Initial specification.


[Activity Vocabulary]:    <https://www.w3.org/TR/activitystreams-vocabulary/>   "Activity Vocabulary"
[ActivityPub]:            <https://www.w3.org/TR/activitypub/>                  "ActivityPub"
[ActivityStreams 2.0]:    <https://www.w3.org/TR/activitystreams-core/>         "Activity Streams 2.0"
[Mon Vocabulary]:         <https://www.monstr.pub/spec/mon-vocabulary/>         "Mon Vocabulary"
[MonStrPub]:              <https://www.monstr.pub/spec/monstrpub-overview/>     "MonStrPub"
[MonStrPub Core]:         <https://www.monstr.pub/spec/monstrpub-core/>         "MonStrPub Core"
[RFC2119]:                <https://tools.ietf.org/html/rfc2119>                 "Key words for use in RFCs to Indicate Requirement Levels"
[Roleplaying Vocabulary]: <https://www.monstr.pub/spec/roleplaying-vocabulary/> "Roleplaying Vocabulary"
