#  MonStrPub Stats
Draft Specification

 - - -

##  Abstract  ##

This specification describes a set of basic requirements for dealing with attributes and stats for mon in [ActivityPub][].

 - - -

##  1. Introduction  ##

A stat is a quantified value for a particular attribute.
The [Mon Vocabulary][] defines `mon:Mon` as `rs:Quantified` objects, which may have stats.
However, no processing requirements for `rp:stat` are included in the [MonStrPub Core][] specification.
This specification defines a basic set of requirements for assigning and handling mon stats on servers as part of the larger [MonStrPub][] protocol.

###  1.1 Relationship to Other Specifications

This document is an **OPTIONAL** part of the [MonStrPub][] set of specifications.
Conformance to this specification is **RECOMMENDED** for MonStrPub implementations which intend to set or handle the `rp:stat` properties on `mon:Mon`, or interact with `rp:Stat` or `rp:Attribute` objects.

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

    https://www.monstr.pub/spec/monstrpub-stats

##  2. Conformance  ##

All sections explicitly marked as non-normative, as well as any diagrams, examples, or notes in this specification, are non-normative.
Everything else in this specification is normative.

MonStrPub is an extension of [ActivityPub][] and inherits its conformance requirements, including its conformance classes.

MonStrPub Stats is an extension to [MonStrPub Core][], and every conforming MonStrPub Stats implementation **MUST** also conform to MonStrPub Core.

Implementations **MAY** deviate from the exact steps of algorithms defined in this specification, so long as the implemented algorithm produces the same result.

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **NOT RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in [RFC2119][].

##  3. Attribute Processing  ##

The `@id` of an `rp:Attribute` is used for attribute identification.
However, it is **RECOMMENDED** that `rp:Attribute`s also be given human-readable `as:name`s, and that MonStrPub servers support these in an appropriate manner.

###  3.1 Mon Attribute Types

The `@type` of an `rp:Attribute` can be used to provide additional semantics for attributes which play a role in their processing or use.
This specification defines six Mon Attribute Types:

- `mon:Constitution`
- `mon:Defense`
- `mon:Resistance`
- `mon:Special`
- `mon:Speed`
- `mon:Strength`

A single `rp:Attribute` **MAY** have more than one type.

 >  **Example:**
 >  An `rp:Attribute` might have the types `mon:Resistance` and `mon:Special` if its `rp:level` is intended to affect both offensive and defensive special behaviours.

For cross-compatibility with other systems, all conforming MonStrPub servers **MUST** explicitly declare an additional `@type` of `rp:Attribute` on all objects which have one of the Mon Attribute Types listed above.

##  4. Stat Processing  ##

###  4.1 Stats Overview

 >  This section is non-normative.

A `rp:Stat` is an assigned value of an `rp:Attribute` on an object.
For `mon:Mon`, as many as four different values are given significance by this specification:

 -  A `rp:BaseStat` assigned to the associated `mon:Species` gives the *base value* for the `rp:Attribute`.
 -  An `mon:IndStat` assigned to the `mon:Mon` gives the *individual value* for the `rp:Attribute`.
 -  An `mon:ExpStat` assigned to the `mon:Mon` gives the *experience value* for the `rp:Attribute`.
 -  A `mon:CmpStat` assigned to the `mon:Mon` gives the *computed value* for the `rp:Attribute`.

These values are the inputs and result of the stat computation algorithm given later in this specification.

###  4.2 Types of Stats

The `@type` of an `rp:Stat` can be used to provide additional semantics for stats which play a role in their processing or use.
This specification defines four Mon Stat Types:

- `mon:BaseStat`
- `mon:CmpStat`
- `mon:ExpStat`
- `mon:IndStat`

The Mon Stat Types above are disjoint:
An `rp:Stat` object **MUST NOT** have more than one of these types.
Implementations **MUST** ignore any objects which have multiple Mon Stat Types.

For cross-compatibility with other systems, all conforming MonStrPub servers **MUST** explicitly declare an additional `@type` of `rp:Stat` on all objects which have one of the Mon Stat Types listed above.

###  4.3 Stat Attributes

Every `rp:Stat` **MUST** have an `rp:class` property, which **MUST** point to an `rp:Attribute`.
Servers **MUST** ignore any `rp:Stat` objects which do not have an `rp:class` property, or whose `rp:class` property does not point to an `rp:Attribute`.

To ensure that the `rp:Attribute` is always able to be dereferenced, `rp:class` links **SHOULD** point to files on the same server as the associated `rp:Stat`.
Processors **MAY** refuse to fetch `rp:class` links which do not share an origin with a given `rp:Stat`.

Multiple stats may share the same `rp:class`.
However, two `rp:Stat`s assigned to a `mon:Mon` or `mon:Species` **MUST NOT** share the same `rp:class` if they are both of the same Mon Stat Type.
Upon encountering any such conflicting `rp:Stat`s, servers **MUST** ignore all but the one with the lowest value for `rp:level`.

###  4.4 Stat Levels

The `rp:level` of an `rp:Stat` gives the current value of the stat.
For `mon:CmpStat` objects, `rp:level` **SHOULD** be in the range \[1, 720].
For all other `rp:Stat` objects, `rp:level` **SHOULD** be in the range \[0, 255].

If no `rp:Stat` of an appropriate type is associated with a `mon:Mon` for a given `rp:Attribute`, servers **SHOULD** use 1 as the default value for `mon:CmpStat` objects, and 0 as the default value otherwise.

Servers **MAY** clamp the value of `rp:level` to a range of their choosing for any `rp:Stat` type during processing.

###  4.4 Assigning Stats to Species

`mon:BaseStat`s **MAY** be assigned to `mon:Species` using the `rp:stat` property.

`mon:CmpStat`, `mon:ExpStat`, and `mon:IndStat` objects have no defined meaning under this specification when assigned to a `mon:Species` object, and servers **SHOULD** ignore any such objects rather than using them in processing.

###  4.5 Assigning Stats to Mon

`mon:CmpStat`, `mon:ExpStat`, and `mon:IndStat` objects **MAY** be assigned to `mon:Mon` using the `rp:stat` property.

`mon:BaseStat` objects have no defined meaning under this specification when assigned to a `mon:Mon` object, and servers **SHOULD** ignore any such objects rather than using them in processing.

###  4.6 Inherited Properties of Stats

When processing an `rp:Stat` with an `rp:Attribute` associated via the `rp:class` property, processors **SHOULD** use values from the associated `rp:Attribute` if the following properties are not specified on the `rp:Stat`:

+ `as:content`
+ `as:icon`
+ `as:image`
+ `as:name`
+ `as:summary`

##  5. Definition of Mon Attribute Types  ##

The following Mon Attribute Types are provided for use with the `rp:class` property on `rp:Stat`s or extensions thereof.
For all of the types listed in this section, the `rp:Attribute` type **MUST** also be (explicitly) specified on the object, to ensure cross-compatibility with servers which do not implement this spec.

###  5.1 The `mon:Constitution` Attribute

+ URI: `https://www.monstr.pub/ns/monstrpub#Constitution`
+ Extends: `rp:Attribute`
+ Properties: Inherits all properties from `rp:Attribute`

Indicates vitality.

###  5.2 The `mon:Defense` Attribute

+ URI: `https://www.monstr.pub/ns/monstrpub#Defense`
+ Extends: `rp:Attribute`
+ Properties: Inherits all properties from `rp:Attribute`

Indicates proficiency with protecting against physical actions.

###  5.3 The `mon:Resistance` Attribute

+ URI: `https://www.monstr.pub/ns/monstrpub#Resistance`
+ Extends: `rp:Attribute`
+ Properties: Inherits all properties from `rp:Attribute`

Indicates proficiency with protecting against nonphysical actions.

###  5.4 The `mon:Special` Attribute

+ URI: `https://www.monstr.pub/ns/monstrpub#Special`
+ Extends: `rp:Attribute`
+ Properties: Inherits all properties from `rp:Attribute`

Indicates proficiency with nonphysical actions.

###  5.5 The `mon:Speed` Attribute

+ URI: `https://www.monstr.pub/ns/monstrpub#Speed`
+ Extends: `rp:Attribute`
+ Properties: Inherits all properties from `rp:Attribute`

Indicates speed or quickness of actions.

###  5.6 The `mon:Strength` Attribute

+ URI: `https://www.monstr.pub/ns/monstrpub#Strength`
+ Extends: `rp:Attribute`
+ Properties: Inherits all properties from `rp:Attribute`

Indicates proficiency with physical actions.

##  6. Definition of Mon Stat Types  ##

The following Mon Stat Types are provided for use with the `rp:stat` property on `mon:Mon`.
For all of the types listed in this section, the `rp:Stat` type **MUST** also be (explicitly) specified on the object, to ensure cross-compatibility with servers which do not implement this spec.

The Mon Stat Types defined in this section are mutually exclusive; objects **MUST NOT** have more than one of these types.

###  6.1 The `mon:BaseStat` Stat

+ URI: `https://www.monstr.pub/ns/monstrpub#BaseStat`
+ Extends: `rp:Stat`
+ Disjoint With:
    + `mon:CmpStat` | `mon:ExpStat` | `mon:IndStat`
+ Properties: Inherits all properties from `rp:Stat`

Indicates the base proficiency of all `mon:Mon` of a given `rp:Species` with the `rp:Attribute` specified via `rp:class`.
The `rp:level` of this object **SHOULD NOT** change over time.

###  6.1 The `mon:CmpStat` Stat

+ URI: `https://www.monstr.pub/ns/monstrpub#CmpStat`
+ Extends: `rp:Stat`
+ Disjoint With:
    + `mon:BaseStat` | `mon:ExpStat` | `mon:IndStat`
+ Properties: Inherits all properties from `rp:Stat`

Indicates the computed proficiency of an individual `mon:Mon` with the `rp:Attribute` specified via `rp:class`.
The `rp:level` of this object **SHOULD** take into account a variety of relevant factors and provide the single, overall skill level of the `mon:Mon` in the given `rp:Attribute`.

###  6.2 The `mon:ExpStat` Stat

+ URI: `https://www.monstr.pub/ns/monstrpub#ExpStat`
+ Extends: `rp:Stat`
+ Disjoint With:
    + `mon:BaseStat` | `mon:CmpStat` | `mon:IndStat`
+ Properties: Inherits all properties from `rp:Stat`

Indicates how experienced an individual `mon:Mon` is with the `rp:Attribute` specified via `rp:class`.
The `rp:level` of this object **SHOULD** change over time as the `mon:Mon` gains or loses experience with the `rp:Attribute`.

###  6.3 The `mon:IndStat` Stat

+ URI: `https://www.monstr.pub/ns/monstrpub#IndStat`
+ Extends: `rp:Stat`
+ Disjoint With:
    + `mon:BaseStat` | `mon:CmpStat` | `mon:ExpStat`
+ Properties: Inherits all properties from `rp:Stat`

Indicates the innate ability of an individual `mon:Mon` with the `rp:Attribute` specified via `rp:class`.
The `rp:level` of this object **SHOULD NOT** change over time.

##  7. Computed Stats  ##

The `mon:CmpStat` stats of a `mon:Mon` controlled by a MonStrPub server **SHOULD** be recomputed whenever the `mon:level` of the `mon:Mon` changes, and **MAY** be computed at other times.

Servers are free to use whatever algorithm they prefer to compute stats for `mon:Mon`.
However, an algorithm which produces similar results to the one below is **RECOMMENDED**.
In particular, the algorithm used by a server **SHOULD** operate on the same inputs and **SHOULD** return a similar result.
For servers which recognize `rp:level` properties on `mon:Mon`, the relationship between stats and levels **SHOULD** be roughly linear.

###  7.1 Recommended Stat Computation Algorithm

Given a `mon:Mon` and an `rp:Attribute`:

1.  Let `S` be the `mon:Species` pointed to by the `rp:class` attribute of the `mon:Mon`, if present.

2.  Define the starting variables for our equation:

    1.  If `S` is defined, let `b` equal the `rp:level` of the `mon:BaseStat` assigned to `S` via the `rp:stat` property, whose `rp:class` points to the `rp:Attribute`, if present and valid.
        If `S` is not defined, or no such `rp:Stat` object exists, set `b` equal to 0.

    2.  Let `e` equal the `rp:level` of the `mon:ExpStat` of the `rp:Mon` whose `rp:class` points to the `rp:Attribute`, if present and valid, or 0 otherwise.

    3.  Let `i` equal the `rp:level` of the `mon:IndStat` of the `rp:Mon` whose `rp:class` points to the `rp:Attribute`, if present and valid, or 0 otherwise.

    4.  Let `l` equal the `rp:level` of the `mon:Mon` itself, if present and valid, or 1 otherwise.

    5.  Let `n` equal the product of any stat adjustments defined by external specifications, or 1 if no such adjustments apply.

3.  Calculate the value of our stat:

    1.  Let `u` equal `2*b + e/4 + i/16`, rounded *down* to the nearest integer.
        This creates a 32:4:1 ratio between `b`, `e`, and `i`, and provides the level-independent value of the stat.
        Servers **MAY** implement their own ratios, but it is **RECOMMENDED** that the final expected range of `u` match that of the above equation; namely, \[0, 589].

    2.  Let `v` equal `u * l/100`, rounded *down* to the nearest integer.
        This is the level-dependent value of the stat.
        On a server which does not recognize `rp:level` properties on `mon:Mon`, `u` and `v` **SHOULD** be equal.

    3.  Let `w` equal `v + 3`.
        This shifts the initial value of the stat upwards slightly so that it is not insignificant on `mon:Mon` with low `rp:level`s.

    4.  Let `x` equal `w + 8 + l` if the `rp:Attribute` has or extends type `mon:Health`, or `w` otherwise.
        This shifts the initial value of the stat upwards even further and ensures that it will always keep growing, regardless of other properties.

    5.  Let `y` equal `x * n`, rounded *up* to the nearest integer.
        We round this number upwards to ensure that it is always positive.
        This is the adjusted value of the stat.

    6.  Let `z` equal the smaller of `y` and 720.
        This is the final computed value of the stat.

<blockquote>
	<p>
		<strong>Note:</strong>
		This algorithm can be represented using the following formula (requires MathML):
	</p>
	<math display="block">
		<mo fence="true" stretchy="true">[</mo>
		<mrow>
			<mo fence="true" stretchy="true">⌈</mo>
			<mrow>
				<mo fence="true" stretchy="true">(</mo>
				<mrow>
					<mo fence="true" stretchy="true">⌊</mo>
					<mfrac>
						<mrow>
							<mrow>
								<mo fence="true" stretchy="true">⌊</mo>
								<mrow>
									<mn>2</mn>
									<mo>×</mo>
									<mi>b</mi>
								</mrow>
								<mo>+</mo>
								<mfrac>
									<mi>e</mi>
									<mn>4</mn>
								</mfrac>
								<mo>+</mo>
								<mfrac>
									<mi>i</mi>
									<mn>16</mn>
								</mfrac>
								<mo fence="true" stretchy="true">⌋</mo>
							</mrow>
							<mo>×</mo>
							<mi>l</mi>
						</mrow>
						<mn>100</mn>
					</mfrac>
					<mo fence="true" stretchy="true">⌋</mo>
				</mrow>
				<mo>+</mo>
				<mrow>
					<mo fence="true" stretchy="true">{</mo>
					<mtable columnalign="left">
						<mtr>
							<mtd>
								<mrow>
									<mn>11</mn>
									<mo>+</mo>
									<mi>l</mi>
								</mrow>
								<mo>,</mo>
							</mtd>
							<mtd>
								<mo lspace="0em">if</mo>
								<mrow>
									<mi>rp:class</mi>
									<mo>∈</mo>
									<mi>mon:Health</mi>
								</mrow>
							</mtd>
						</mtr>
						<mtr>
							<mtd>
								<mn>3</mn>
								<mo>,</mo>
							</mtd>
							<mtd>
								<mo>otherwise</mo>
							</mtd>
						</mtr>
					</mtable>
					<mo fence="true" stretchy="true">}</mo>
				</mrow>
				<mo fence="true" stretchy="true">)</mo>
			</mrow>
			<mo>×</mo>
			<mi>n</mi>
			<mo fence="true" stretchy="true">⌉</mo>
		</mrow>
		<msubsup>
			<mo fence="true" stretchy="true">]</mo>
			<mn>1</mn>
			<mn>720</mn>
		</msubsup>
	</math>
</blockquote>

##  8. Interactions with Other Servers

Servers implementing this specification **SHOULD** be able to gracefully interact with servers which do not.
When cloning `mon:Mon` or `mon:Species` from another server which does not specify `mon:Stat`s of the expected types or with the expected associated `mon:Attribute`s, servers **MAY** use predefined defaults.

##  9. Changelog  ##

 >  This section is non-normative.

#####  2018-05-08.

 +  Added a section on indicating support for this specification.

#####  2018-05-05.

 +  Changed the requirements regarding the processing of conflicting stats.
    The old recommendation was to average, not take the lowest-levelled one.

 +  Fixed typos.

#####  2018-05-01 (2).

 +  Fixed typos.

#####  2018-05-01.

 +  Initial specification.
    Some of the requirements in this specification are taken from earlier drafts of the [Mon Vocabulary][].


[Activity Vocabulary]:    <https://www.w3.org/TR/activitystreams-vocabulary/>   "Activity Vocabulary"
[ActivityPub]:            <https://www.w3.org/TR/activitypub/>                  "ActivityPub"
[ActivityStreams 2.0]:    <https://www.w3.org/TR/activitystreams-core/>         "Activity Streams 2.0"
[Mon Vocabulary]:         <https://www.monstr.pub/spec/mon-vocabulary/>         "Mon Vocabulary"
[MonStrPub]:              <https://www.monstr.pub/spec/monstrpub-overview/>     "MonStrPub"
[MonStrPub Core]:         <https://www.monstr.pub/spec/monstrpub-core/>         "MonStrPub Core"
[RFC2119]:                <https://tools.ietf.org/html/rfc2119>                 "Key words for use in RFCs to Indicate Requirement Levels"
[Roleplaying Vocabulary]: <https://www.monstr.pub/spec/roleplaying-vocabulary/> "Roleplaying Vocabulary"
