#  ActivityMon Levels
Draft Specification

 - - -

##  Abstract  ##

This specification describes a set of basic requirements for dealing with levels and experience for mon in [ActivityPub][].

 - - -

##  1. Introduction  ##

The [Mon Vocabulary][] defines `mon:Mon` as `rs:Ranked` objects, which may have levels and gain experience.
However, no processing requirements for `rs:level` or `rs:experience` are included in the [ActivityMon Core][] specification.
This specification defines a basic set of requirements for assigning and handling mon levels on servers as part of the larger [ActivityMon][] protocol.

###  1.1 Relationship to Other Specifications

This document is an **OPTIONAL** part of the [ActivityMon][] set of specifications.
Conform to this specification is **RECOMMENDED** for ActivityMon implementations which intend to set or handle the `rp:level` or `rp:experience` properties on `mon:Mon`.

This document uses the vocabulary defined in the [Mon Vocabulary][] specification.
This vocabulary itself depends on that defined in the [Roleplaying Vocabulary][] specification.

This document assumes conformance with the [ActivityMon Core][].

This document defines an [ActivityPub][] extension, and depends upon both the [Activity Vocabulary][] and [ActivityStreams 2.0][].

###  1.2 Notational Conventions

[Activity Vocabulary][] types are indicated with the prefix `as:`, which represents the `https://www.w3.org/ns/activitystreams#` base URI.
Note that in [ActivityStreams 2.0][] documents, this prefix **MAY** be omitted.

Roleplaying Vocabulary types are indicated with the prefix `rp:`, which represents the `tag:marrus.xyz,2018:roleplaying::` base URI.
Note that in [ActivityStreams 2.0][] documents, this prefix **MUST** be declared in the `@context` of the document to be valid, and a different prefix **MAY** be used instead.

Mon Vocabulary types are indicated with no prefix `mon:`, which represents the `tag:marrus.xyz,2018:activitymon::` base URI.
Note that in [ActivityStreams 2.0][] documents, this prefix **MUST** be declared in the `@context` of the document to be valid, and a different prefix **MAY** be used instead.

##  2. Conformance  ##

All sections explicitly marked as non-normative, as well as any diagrams, exmaples, or notes in this specification, are non-normative.
Everything else in this specification is normative.

ActivityMon is an extension of [ActivityPub][] and inherits its conformance requirements, including its conformance classes.

[ActivityMon Levels][] is an extension to [ActivityMon Core][], and every conforming ActivityMon Levels implementation **MUST** also conform to ActivityMon Core.

Implementations **MAY** deviate from the exact steps of algorithms defined in this specification, so long as the implemented algorithm produces the same result.

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **NOT RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in [RFC2119][].

##  3. Levels and Experience  ##

The level of a `mon:Mon` **MAY** be given using the `rp:level` property.
Servers **SHOULD** implement a maximum level for `mon:Mon`, and the number `100` is **RECOMMENDED** as this maximum value.
When following the steps for cloning mon, servers **MAY** replace any value for the `rp:level` property which exceeds the maximum with the maximum value.

Servers **SHOULD** treat `mon:Mon` whose `rp:level` property is absent or unspecified as though the value `0` had been specified instead.

The `rp:experience` property gives the amount of experience a `mon:Mon` has, *above and beyond* the current level.
Servers **MUST NOT** interpret this property as the *total* experience (ie, over all levels) instead.

Servers **SHOULD** treat `mon:Mon` whose `rp:level` property is absent or unspecified as though the value `0` had been specified instead.

###  3.1 Growth

The `rp:growth` property provides an optionally-piecewise polynomial function for determining the amount of experience required to increase the level of a `mon:Mon`, given the current level.
The exact semantics of this property are specified by the [Roleplaying Vocabulary][].

If the `rp:growth` property is absent or invalid, or does not specify a polynomial function for the current level of a `mon:Mon`, the function `3x^2` **SHOULD** be used instead.

No requirements are made regarding the shape of a `rp:growth` polynomial; however, it is **RECOMMENDED** that polynomials remain reasonably similar in shape to the default of `3x^2`, and moreover that the integral of the function over the range \[0, 100] be close to the `3x^2` result of one million.

###  3.2 Levelling Up

Whenever the `rp:experience` property changes on a `mon:Mon` which is controlled by the server, servers **SHOULD** run the following steps:

1.  Calculate the amount of experience required for an increase in level, as specified by the `rp:growth` property.

2.  If the value of `rp:experience` is greater than this calculated value, increment the `rp:level` of the `mon:Mon` and set the `rp:experience` to the positive difference between these two values.

3.  Repeat these steps as necessary until `rp:experience` is no longer greater than the amount calculated using `rp:growth`, or the maximum level is reached.

Servers **SHOULD NOT** run the preceding steps for `mon:Mon` whose `rp:level` is already at the maximum value.
Servers **MAY** run these steps at other times.

###  3.3 Interactions with Other Servers

Servers implementing this specification **SHOULD** be able to gracefully interact with servers which do not.
When cloning `mon:Mon` from another server which does not specify a `mon:level`, `mon:experience`, or `mon:growth` property, servers **MAY** use predefined defaults.

##  4. Changelog  ##

 >  This section is non-normative.

#####  2018-04-21.

 +  Initial specification.
    Some of the requirements in this specification are taken from earlier drafts of the [Mon Vocabulary][].


[Activity Vocabulary]:    <https://www.w3.org/TR/activitystreams-vocabulary/> "Activity Vocabulary"
[ActivityMon]:            <https://kibimon.github.io/activitymon/>            "ActivityMon"
[ActivityMon Core]:       <https://kibimon.github.io/activitymon/core/>       "ActivityMon Core"
[ActivityPub]:            <https://www.w3.org/TR/activitypub/>                "ActivityPub"
[ActivityStreams 2.0]:    <https://www.w3.org/TR/activitystreams-core/>       "Activity Streams 2.0"
[Mon Vocabulary]:         <https://kibimon.github.io/activitymon/vocabulary/> "Mon Vocabulary"
[RFC2119]:                <https://tools.ietf.org/html/rfc2119>               "Key words for use in RFCs to Indicate Requirement Levels"
[Roleplaying Vocabulary]: <https://kibimon.github.io/roleplaying/vocabulary/> "Roleplaying Vocabulary"