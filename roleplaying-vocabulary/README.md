#  Roleplaying Vocabulary
Draft Specification

 - - -

##  Abstract  ##

This specification describes the Roleplaying Vocabulary.
The Roleplaying Vocabulary is an extension of the [Activity Vocabulary][] vocabulary inteded for use in the context of the [ActivityStreams 2.0][] format and provides a foundational vocabulary for representing basic roleplaying constructs like classes, levels, and stats.

 - - -

##  1. Introduction  ##

Many roleplaying games, and roleplaying-like scenarios, feature familiar constructs, like classes or levels.
This specification defines a common vocabulary for describing such ideas, intended for use in the context of [ActivityStreams 2.0][] documents.

###  1.1 Relationship to Other Specifications

This document is part of the [RPPub][] set of specifications.

This document builds upon the [Activity Vocabulary][] and is intended for use in an [ActivityStreams 2.0][] context.

###  1.2 Notational Conventions

[Activity Vocabulary][] types are indicated with the prefix `as:`, which represents the `https://www.w3.org/ns/activitystreams#` base URI.
Note that in [ActivityStreams 2.0][] documents, this prefix **MAY** be omitted.

Roleplaying Vocabulary types are indicated with the prefix `rp:`, which represents the `https://www.monstr.pub/ns/roleplaying#` base URI.
Note that in [ActivityStreams 2.0][] documents, this prefix **MUST** be declared in the `@context` of the document to be valid, and a different prefix **MAY** be used instead.

 >  As this is still a draft specification, the above URIs may change at some point in the future.

###  1.3 Structure of this Specification

 >  This section is non-normative.

Section 1 introduces this specification, and section 2 gives the conformance requirements.
The remaining sections define the Roleplaying Vocabulary itself.

Section 3 gives the various Activities defined by the Roleplaying Vocabulary.
To aid in the writing of documents or processes which only need to implement a subset of the vocabulary, the remainder of the vocabulary is organized by topic, rather than by type.
The topics are ordered alphabetically, and are as follows:

- Actions (Section 4)
- Classes (Section 5)
- Effects (Section 6)
- Levels (Section 7)
- Perishables (Section 8)
- Qualities (Section 9)
- Scopes (Section 10)
- Stats (Section 11)
- Techniques (Section 12)

A changelog is available at the end of the specification.

##  2. Conformance  ##

All sections explicitly marked as non-normative, as well as any diagrams, examples, or notes in this specification, are non-normative.
Everything else in this specification is normative.

The Roleplaying Vocabulary is an extension of the [Activity Vocabulary][].
Uses of the Roleplaying Vocabulary **MUST** conform to the requirements set forward in the Activity Vocabulary specification.

This specification defines three conformance classes:

 +  *Conforming documents* are documents which make use of the vocabulary defined in this specification, and conform to the requirements for documents.
    [ActivityStreams 2.0][] documents **MUST** use this vocabulary in the manner specified by that specification.
    The mechanisms by which this vocabulary might be used in another document type are not specified here.

 +  *Conforming authors* are processes which produce conforming documents.
    No additional requirements for authors are made.

 +  *Conforming processors* are processes which process conforming documents.
    A processor is conforming if it conforms to the requirements for processors defined in this specification.

Except where explicitly stated, conformance requirements are for documents, not processors.

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **NOT RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in [RFC2119][].

##  3. Activities  ##

As with the [Activity Vocabulary], all Roleplaying Activity Types inherit the properties of the base `as:Activity` type.
Many of these are subtypes of a more specific Activity Type; for example, `as:Offer`.

###  3.1 Useful Types from the Activity Vocabulary

 >  This section is non-normative.

The following types already exist in the [Activity Vocabulary][], so there is no need to define them here:

 +  **`as:Arrive`**:
    Indicates that the `as:actor` has arrived at the `as:location`.

 +  **`as:Listen`**:
    Indicates that the `as:actor` has heard the `as:object`.
    In the context of roleplaying, this could signal that an entity has been alerted to another's presence through sound.

 +  **`as:Travel`**:
    Indicates that the `as:actor` is travelling from `as:origin` to `as:target`.

 +  **`as:View`**:
    Indicates that the `as:actor` has seen the `as:object`.
    In the context of roleplaying, this could signal that an entity has been alerted to another's presence through sight.

###  3.2 The `rp:Affect` Activity

+ URI: `https://www.monstr.pub/ns/roleplaying#Affect`
+ Extends: `as:Activity`
+ Properties: Inherits all properties from `as:Activity`.

Indicates that the `as:actor` is affecting the `as:object`.
The `as:instrument` property **MAY** be used to indicate the manner of affect.

###  3.3 The `rp:Evolve` Activity

+ URI: `https://www.monstr.pub/ns/roleplaying#Evolve`
+ Extends:
    + `as:IntransitiveActivity` | `rp:Instance`
+ Properties: Inherits all properties from `as:IntransitiveActivity` and `rp:Instance`.

An `as:IntransitiveActivity` that indicates that the `as:actor` has changed its class.
The new class **MAY** be specified via the `rp:class` property.

###  3.4 The `rp:Learn` Activity

+ URI: `https://www.monstr.pub/ns/roleplaying#Learn`
+ Extends: `as:Activity`
+ Properties: Inherits all properties from `as:Activity`

Indicates that the `as:actor` has learned how to use the `as:object`.

###  3.5 The `rp:Teach` Activity

+ URI: `https://www.monstr.pub/ns/roleplaying#Teach`
+ Extends: `as:Offer`
+ Properties: Inherits all properties from `as:Offer`

A specialization of `as:Offer` in which the `as:actor` is attempting to teach the `as:target` how to use the `as:object`.

###  3.6 The `rp:Use` Activity

+ URI: `https://www.monstr.pub/ns/roleplaying#Use`
+ Extends: `as:Offer`
+ Properties: Inherits all properties from `as:Offer`

A specialization of `as:Offer` in which the `as:actor` is attempting to use the `as:object` on the `as:target`.

##  4. Actions  ##

An object which produces an effect is an *action*.
In the Roleplaying Vocabulary, actions have type `rp:Action`.

###  4.1 The `rp:Action` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Action`
+ Extends: `rp:Effectual`
+ Properties:
    + `rp:likelihood` | `rp:power`
    + Inherits all properties from `rp:Effectual`

Describes an action of any kind.

The `as:name` property **SHOULD** be used to provide a name for the `rp:Action`.
A short description of the `rp:Action` **MAY** be provided via the `as:summary` property.
A longer description of the `rp:Action` **MAY** be provided via the `as:content` property.

###  4.2 The `rp:likelihood` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#likelihood`
+ Domain: `rp:Action`
+ Range: `xsd:float` [>= 0.0f]
+ Functional: True

Indicates the probability that an `rp:Action` will produce a result.
A value of 0 indicates an impossible action, and a value of 1 indicates a guaranteed action.
Values larger than 1 **MAY** be used as a multiplier if the success of the action depends on a number of factors.

An object **MUST NOT** have more than one value for its `rp:likelihood` property.
Upon encountering an object with more than one `rp:likelihood` value, processors **MUST** ignore all but the smallest one.

###  4.3 The `rp:power` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#power`
+ Domain: `rp:Action`
+ Range: `xsd:nonNegativeInteger`
+ Functional: True

Indicates the strength of a `rp:Action`; eg, its effectiveness.

An object **MUST NOT** have more than one value for its `rp:power` property.
Upon encountering an object with more than one `rp:power` value, processors **MUST** ignore all but the smallest one.

##  5. Classes  ##

*Classes* are used to classify objects.
Objects which belong to a class are known as *instances*.
In the Roleplaying Vocabulary, instances have type `rp:Instance`, classes have type `rp:Class`, and the `rp:class` property is used to signify the relationship between the two.

Classes **SHOULD NOT** be used to distinguish between objects of a different `@type`—different `@types` **SHOULD** be used instead.
Classes **SHOULD** be used to group together objects of the *same* `@type`—with the same kinds of properties and behaviours—based on common features.
In many roleplaying contexts, not every possible combination of property values are always viable for a given object.
Classes **MAY** be used to group together viable combinations so that they can be easily associated with objects.

###  5.1 The `rp:Class` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Class`
+ Extends: `as:Object`
+ Properties:
    + `rp:becomes` | `rp:maxHealth`
    + Inherits all properties from `as:Object`

Represents a class.

The `rp:becomes` property **MAY** be used to mark potential `rp:ClassChange`s from this class.

The `rp:maxHealth` property **MAY** be used to indicate the maximum health of instances.

The `as:name` property **SHOULD** be used to provide a name for the `rp:Class`.
A short description of the `rp:Class` **MAY** be provided via the `as:summary` property.
A longer description of the `rp:Class` **MAY** be provided via the `as:content` property.

###  5.2 The `rp:ClassChange` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#ClassChange`
+ Extends: `rp:Scoped`
+ Properties
    + `rp:class`
    + Inherits all proerties from `rp:Scoped`

Represents an option for changing class.
`rp:ClassChange` objects inherit from `rp:Scoped`, which provides `rp:maxLevel` and `rp:minLevel` attributes for specifying class change requirements.
If neither `rp:maxLevel` nor `rp:minLevel` are provided, the `rp:ClassChange` has no level requirements.

###  5.3 The `rp:Instance` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Instance`
+ Extends: `as:Object`
+ Properties:
    + `rp:class`
    + Inherits all properties from `as:Object`

Represents an instance of a class.

The `rp:class` property **SHOULD** be used to denote the `rp:Class` that this `rp:Instance` belongs to, if one exists.

###  5.4 The `rp:becomes` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#becomes`
+ Domain: `rp:Class`
+ Range: `rp:ClassChange`

Represents a class change which **MAY** be available for this `rp:Class`.
The `rp:becomes` property is **NOT REQUIRED** to be exhaustive.

###  5.5 The `rp:class` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#class`
+ Domain:
    + `rp:ClassChange` | `rp:Instance`
+ Range: `as:Link`

Represents a link to a `rp:Class`.

The `rp:class` property **MUST** point to a `rp:Class`.
Processors **MUST** ignore all values of `rp:class` which do not point to a `rp:Class` object.

##  6. Effects  ##

Roleplaying actions often have *effects*, and frequently objects have passive *status effects* as well.
In the Roleplaying Vocabulary, both active and passive effects are represented using `rp:Effect` objects.
These can be assigned to `rp:Effectual` objects with the `rp:effect` property.

###  6.1 The `rp:Effect` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Effect`
+ Extends: `as:Object`
+ Properties: Inherits all properties from `as:Object`

Describes an effect of any kind.

The `as:name` property **SHOULD** be used to provide a name for the `rp:Effect`.
A short description of the `rp:Effect` **MAY** be provided via the `as:summary` property.
A longer description of the `rp:Effect` **MAY** be provided via the `as:content` property.

###  6.2 The `rp:Effectual` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Effectual`
+ Extends: `as:Object`
+ Properties:
    + `rp:effect`
    + Inherits all properties from `as:Object`

Describes an object which has or produces an effect.

###  6.3 The `rp:effect` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#effect`
+ Domain: `rp:Effectual`
+ Range: `as:Link`

Represents a link to a `rp:Effect`.

The `rp:effect` property **MUST** point to an `rp:Effect`.
Processors **MUST** ignore all values of `rp:effect` which do not point to an `rp:Effect` object.

##  7. Levels  ##

Many roleplaying objects have associated ranks or *levels*, which are typically gained through *experience*.
In the Roleplaying Vocabulary, objects which can gain levels and/or experience are given the type `rp:Ranked`.

`rp:Ranked` objects are **NOT REQUIRED** to have either levels (denoted via `rp:level`) or experience (denoted via `rp:exp`), and the relationship between the two is left to individual implementations.
However, `rp:GrowthFunc`s **MAY** be used to define polynomial functions for relating experience gain to changes in level.

###  7.1 The `rp:GrowthFunc` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#GrowthFunc`
+ Extends: `rp:Scoped`
+ Properties:
    + `rp:n0` | `rp:n1` | `rp:n2` | `rp:n3`
    + Inherits all properties from `rp:Scoped`

Describes a cubic function, which takes a level as input and calculates how much experience is required before reaching the next one.
`rp:GrowthFunc`s are `rp:Scoped`, and only apply to the levels in-between `rp:maxLevel` and `rp:minLevel`.

###  7.2 The `rp:Ranked` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Ranked`
+ Extends: `rp:Object`
+ Properties:
    + `rp:exp` | `rp:growth` | `rp:level`
    + Inherits all properties from `rp:Object`

Describes an object which may gain `rp:level`s or `rp:exp`.
The `rp:growth` property **MAY** be used to denote the relationship between these two properties.

###  7.3 The `rp:exp` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#exp`
+ Domain: `rp:Ranked`
+ Range: `xsd:nonNegativeInteger`
+ Functional: True

Indicates some integral amount of experience, above and beyond the current `rp:level` of a `rp:Ranked` object, if specified.

An object **MUST NOT** have more than one value for its `rp:exp` property.
Upon encountering an object with more than one `rp:exp` value, processors **MUST** ignore all but the smallest value.

###  7.4 The `rp:growth` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#growth`
+ Domain: `rp:Ranked`
+ Range: `rp:GrowthFunc`

Indicates a growth or levelling rate for a `rp:Scoped` object—that is, the amount of `rp:exp` required for a change in `rp:level`—as a (optionally piecewise) polynomial, specified via `rp:GrowthFunc`s.

The `rp:GrowthFunc` functions provided by the `rp:growth` property **MUST NOT** overlap; on encountering an overlap, processors **SHOULD** take the average result of all applicable functions.
If no applicable `rp:GrowthFunc` object is provided for the current `rp:level` of an `rp:Ranked` object, a default function **SHOULD** be used.
The shape of this default function is left to implementations.

The `rp:growth` polynomial **SHOULD** map to a nonnegative number for every integer in its expected domain.
When calculating the result of a `rp:growth` polynomial for a given number, processors **MUST** clamp the result to \[0, ∞).

When processing the result of a `rp:growth` polynomial, processors **MUST** round the value up to the nearest whole integer.

###  7.5 The `rp:level` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#level`
+ Domain: `rp:Ranked`
+ Range: `xsd:nonNegativeInteger`
+ Functional: True

Indicates a current numeric rank of proficiency.

An object **MUST NOT** have more than one value for its `rp:level` property.
Upon encountering an object with more than one `rp:level` value, processors **MUST** ignore all but the smallest one.

###  7.6 The `rp:n0` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#n0`
+ Domain: `rp:GrowthFunc`
+ Range: `xsd:float`
+ Functional: True

The zeroth-degree coefficient of a `rp:GrowthFunc` polynomial.

An object **MUST NOT** have more than one value for its `rp:n0` property.
Upon encountering an object with more than one `rp:n0` value, processors **SHOULD** use the average of all of the values given.

###  7.7 The `rp:n1` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#n1`
+ Domain: `rp:GrowthFunc`
+ Range: `xsd:float`
+ Functional: True

The first-degree coefficient of a `rp:GrowthFunc` polynomial.

An object **MUST NOT** have more than one value for its `rp:n1` property.
Upon encountering an object with more than one `rp:n1` value, processors **SHOULD** use the average of all of the values given.

###  7.8 The `rp:n2` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#n2`
+ Domain: `rp:GrowthFunc`
+ Range: `xsd:float`
+ Functional: True

The second-degree coefficient of a `rp:GrowthFunc` polynomial.

An object **MUST NOT** have more than one value for its `rp:n2` property.
Upon encountering an object with more than one `rp:n2` value, processors **SHOULD** use the average of all of the values given.

###  7.9 The `rp:n3` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#n3`
+ Domain: `rp:GrowthFunc`
+ Range: `xsd:float`
+ Functional: True

The third-degree coefficient of a `rp:GrowthFunc` polynomial.

An object **MUST NOT** have more than one value for its `rp:n3` property.
Upon encountering an object with more than one `rp:n3` value, processors **SHOULD** use the average of all of the values given.

##  8. Perishables  ##

Roleplaying contexts frequently feature objects which have a set amount of health, or which can only be used a certain number of times.
Collectively, these are known as *perishables*.
In the Roleplaying Vocabulary, perishables have type `rp:Perishable`, and their remaining health/uses are provided via the `rp:health` property.

###  8.1 The `rp:Perishable` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Perishable`
+ Extends: `rp:Object`
+ Properties:
    + `rp:health` | `rp:maxHealth`
    + Inherits all properties from `rp:Object`

The health of a `rp:Perishable` object **SHOULD** under normal circumstances be less than or equal to its `rp:maxHealth`.
It **MAY** be greater than `rp:maxHealth` in exceptional circumstances, as defined by the implementation.

###  8.2 The `rp:Health` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#health`
+ Domain: `rp:Perishable`
+ Range: `xsd:nonNegativeInteger`
+ Functional: True

Indicates current vitality, or the number of remaining uses for an object.
If this property is not defined, the `rp:Perishable` has infinite health or uses.

An object **MUST NOT** have more than one value for its `rp:health` property.
Upon encountering an object with more than one `rp:health` value, processors **MUST** ignore all but the smallest value.

###  8.3 The `rp:maxHealth` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#maxHealth`
+ Domain:
    + `rp:Class` | `rp:Perishable`
+ Range: `xsd:nonNegativeInteger`
+ Functional: True

Indicates a maximum value for the `rp:health` property under normal circumstances.
When assigned to a `rp:Class` which is not also `rp:Perishable`, it indicates the maximum value for the class's instances.

An object **MUST NOT** have more than one value for its `rp:maxHealth` property.
Upon encountering an object with more than one `rp:maxHealth` value, processors **MUST** ignore all but the smallest one.

##  9. Qualities  ##

Roleplaying objects may have a number of qualitative properties known as *qualities*.
In the Roleplaying Vocabulary, objects which have qualities have type `rp:Qualified`, and have qualities of type `rp:Quality`, which are assigned via the `rp:quality` property.

The Roleplaying Vocabulary also defines a number of subclasses of `rp:Quality` for defining specific types of qualities.

###  9.1 The `rp:Ability` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Ability`
+ Extends: `rp:Quality`
+ Properties: Inherits all properties from `rp:Quality`

Describes a passive ability of an object.

###  9.2 The `rp:Affinity` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Affinity`
+ Extends: `rp:Quality`
+ Properties:
    + `rp:matchup`
    + Inherits all properties from `rp:Quality`

Describes an affinity, or "type", of an object.
`as:Affinity`s can *conduct* or *resist* other `as:Affinity`s; this is recorded on the `rp:matchup` property.

###  9.3 The `rp:Conducted` Link

+ URI: `https://www.monstr.pub/ns/roleplaying#Conducted`
+ Extends: `as:Link`
+ Properties: Inherits all properties from `as:Link`

A specialized `as:Link` that represents an `rp:Affinity` whose behaviours are strengthened.

The `as:href` property **MUST** be present and point to an `rp:Affinity`.
Processors **MUST** ignore any `rp:Conducted` link whose `as:href` is not defined, or does not point to an `rp:Affinity`.

###  9.4 The `rp:Forme` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Forme`
+ Extends: `rp:Quality`
+ Properties: Inherits all properties from `rp:Quality`

Indicates a "forme" of an object, which is a broad category used to describe variations in gender, appearance, or properties.
The schema.org types `https://schema.org/Male` and `https://schema.org/Female` **SHOULD** be used with male and female formes, where applicable.

###  9.5 The `rp:Nature` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Nature`
+ Extends: `rp:Quality`
+ Properties: Inherits all properties from `rp:Quality`

Describes the individual manner, nature, or temperament an object.

###  9.6 The `rp:Qualified` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Qualified`
+ Extends: `rp:Object`
+ Properties:
    + `rp:quality`
    + Inherits all properties from `rp:Object`

Describes an object which has `rp:Quality`s.

###  9.7 The `rp:Quality` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Quality`
+ Extends: `as:Object`
+ Properties: Inherits all properties from `as:Object`

Describes a quality.

The `as:name` property **SHOULD** be used to provide a name for the `rp:Quality`.
A short description of the `rp:Quality` **MAY** be provided via the `as:summary` property.
A longer description of the `rp:Quality` **MAY** be provided via the `as:content` property.

###  9.8 The `rp:Resisted` Link

+ URI: `https://www.monstr.pub/ns/roleplaying#Resisted`
+ Extends: `as:Link`
+ Properties: Inherits all properties from `as:Link`

A specialized `as:Link` that represents an `rp:Affinity` whose behaviours are weakened.

The `as:href` property **MUST** be present and point to an `rp:Affinity`.
Processors **MUST** ignore any `rp:Resisted` link whose `as:href` is not defined, or does not point to an `rp:Affinity`.

###  9.9 The `rp:Unaffected` Link

+ URI: `https://www.monstr.pub/ns/roleplaying#Unaffected`
+ Extends: `as:Link`
+ Properties: Inherits all properties from `as:Link`

A specialized `as:Link` that represents an `rp:Affinity` whose behaviours are neither strengthened nor weakened.

The `as:href` property **MUST** be present and point to an `rp:Affinity`.
Processors **MUST** ignore any `rp:Unaffected` link whose `as:href` is not defined, or does not point to an `rp:Affinity`.

###  9.10 The `rp:matchup` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#matchup`
+ Domain: `rp:Affinity`
+ Range:
    + `rp:Conducted` | `rp:Resisted` | `rp:Unaffected`

Links to `rp:Affinity`s which are conducted, resisted, or unaffected by this `rp:Affinity`.
These relationships are *passive*:
They are the result of the *other* `rp:Affinity` being applied on this one.

###  9.11 The `rp:quality` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#quality`
+ Domain: `rp:Qualified`
+ Range: `rp:Quality`

Indicates the `rp:Quality`s of an `rp:Qualified` object.

##  10. Scopes  ##

Many objects in roleplaying contexts require a certain level or rank to use; such objects are *scoped*.
In the Roleplaying Vocabulary, scoped objects are represented by `rp:Scoped`, and the permitted levels are indicated by `rp:maxLevel` and `rp:minLevel`.

###  10.1 The `rp:Scoped` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Scoped`
+ Extends: `as:Object`
+ Properties:
    + `rp:maxLevel` | `rp:minLevel`
    + Inherits all properties from `as:Object`

Describes a scoped object.

A `rp:Scoped` object with no `rp:maxLevel` or `rp:minLevel` has no level scoping.
A `rp:Scoped` object whose `rp:maxLevel` is less than `rp:minLevel` cannot be used by any level.

###  10.2 The `rp:maxLevel` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#maxLevel`
+ Domain: `rp:Scoped`
+ Range: `xsd:nonNegativeInteger`
+ Functional: True

Indicates the maximum level for which the given `rp:Scoped` object applies.

An object **MUST NOT** have more than one value for its `rp:maxLevel` property.
Upon encountering an object with more than one `rp:maxLevel` value, processors **MUST** ignore all but the smallest one.

###  10.3 The `rp:minLevel` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#minLevel`
+ Domain: `rp:Scoped`
+ Range: `xsd:nonNegativeInteger`
+ Functional: True

Indicates the minimum level for which the given `rp:Scoped` object applies.

An object **MUST NOT** have more than one value for its `rp:minLevel` property.
Upon encountering an object with more than one `rp:minLevel` value, processors **MUST** ignore all but the largest one.

##  11. Stats  ##

In addition to or instead of levels, roleplaying objects often have a set of *stats*, which define the object's unique quantifiable attributes, properties, or proficiencies.
In the Roleplaying Vocabulary, objects with stats are of type `rp:Quantified` and have their stats assigned via `rp:Stat` objects to the `rp:stat` property.

`rp:Stat` objects are themselves `rp:Ranked` entities, and consequently can gain levels and experience.

###  11.1 The `rp:Attribute` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Attribute`
+ Extends: `rp:Class`
+ Properties: Inherits all properties from `rp:Class`

Describes a type of stat.
`rp:Attribute`s **SHOULD NOT** have an `rp:becomes` property, as stats cannot change class.

The `as:name` property **SHOULD** be used to provide a name for the `rp:Attribute`.
A short description of the `rp:Attribute` **MAY** be provided via the `as:summary` property.
A longer description of the `rp:Attribute` **MAY** be provided via the `as:content` property.

###  11.2 The `rp:Quantified` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Quantified`
+ Extends: `as:Object`
+ Properties:
    + `rp:stat`
    + Inherits all properties from `as:Object`

Describes an object with stats.

The stats of an `rp:Quantified` object **SHOULD** be given via the `rp:stat` property.

###  11.3 The `rp:Stat` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Stat`
+ Extends:
    + `rp:Instance` | `rp:Ranked`
+ Properties: Inherits all properties from `rp:Instance` and `rp:Ranked`

Describes a stat.
The type of the stat **SHOULD** be given via the `rp:class` property, which **MUST** point to an `rp:Attribute`.

`rp:Stat`s **MUST NOT** have more than one `rp:class`.
Processors **MUST** ignore all values of `rp:class` if there are more than one, or if they do not point to an `rp:Attribute` object.

The value of the `rp:class` property of a stat **SHOULD NOT** change over time.

###  11.4 The `rp:stat` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#stat`
+ Domain: `rp:Quantified`
+ Range: `rp:Stat`

Indicates the `rp:Stat`s of an `rp:Quantified` object.

##  12. Techniques  ##

Roleplaying typically depends on objects being able to perform actions, which may be broadly categorized into *techniques*.
Often, not every technique is able to be used by every object; rather, techniques must first be *learned*.
In the Roleplaying Vocabulary, objects which can learn techniques are represented with `rp:Performer`s, who may be instances of `rp:Role`s.
Learned techniques are represented with `rp:Learned`, and technique classes are represented with `rp:Technique`.

###  12.1 The `rp:Learnable` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Learnable`
+ Extends:
    + `rp:Instance` | `rp:Scoped`
+ Properties: Inherits all properties from `rp:Instance` and `rp:Scoped`

Describes a technique which may be learned by an object.

The type of technique **SHOULD** be given via the `rp:class` property, which **MUST** point to a `rp:Technique`.

`rp:Learnable` objects **MUST NOT** have more than one `rp:class`.
Processors **MUST** ignore all values of `rp:class` if there are more than one, or if they do not point to a `rp:Technique` object.

`rp:Learnable` objects are `rp:Scoped`.
The `rp:maxLevel` and `rp:minLevel` properties **MAY** be used to indicate the range of levels during which a techique can be learned.
If these properties are absent, the `rp:Learnable` object is learnable at any level.

###  12.2 The `rp:Learned` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Learned`
+ Extends:
    + `rp:Instance` | `rp:Perishable`
+ Properties: Inherits all properties from `rp:Instance` and `rp:Perishable`

Describes a learned technique, which is a type of action performable by an object.

The type of technique **SHOULD** be given via the `rp:class` property, which **MUST** point to a `rp:Technique`.

`rp:Learned` objects **MUST NOT** have more than one `rp:class`.
Processors **MUST** ignore all values of `rp:class` if there are more than one, or if they do not point to a `rp:Technique` object.

`rp:Learned` objects are `rp:Perishable`.
The `rp:health` property **MAY** be used to indicate how many times the learned technique may be used.
`rp:Learned` objects are **NOT REQUIRED** to make use of the `rp:health` propeprty.

###  12.3 The `rp:Performer` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Performer`
+ Extends: `rp:Instance`
+ Properties:
    + `rp:learned` | `rp:learnset`
    + Inherits all properties from `rp:Instance`.

Describes an object which can learn techniques.

The type of technique **SHOULD** be given via the `rp:class` property, which **MUST** point to a `rp:Role`.
Processors **MUST** ignore all values of `rp:class` which do not point to a `rp:Role` object.

When processing a `rp:Performer` with one or more `rp:Role`s associated via the `rp:class` property, processors **SHOULD** treat any values for the `rp:learnset` property on the `rp:Role`(s) as though they had also been specified on the `rp:Performer`, *in addition to* any values specified on the `rp:Performer` directly.

###  12.4 The `rp:Role` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Role`
+ Extends: `rp:Class`
+ Properties:
    + `rp:learnset`
    + Inherits all properties from `rp:Class`.

Describes a role, which is a `rp:Class` which has a `rp:learnset`.

The `as:name` property **SHOULD** be used to provide a name for the `rp:Role`.
A short description of the `rp:Role` **MAY** be provided via the `as:summary` property.
A longer description of the `rp:Role` **MAY** be provided via the `as:content` property.

###  12.5 The `rp:Technique` Object

+ URI: `https://www.monstr.pub/ns/roleplaying#Technique`
+ Extends:
    + `rp:Action` | `rp:Class` | `rp:Qualified`
+ Properties: Inherits all properties from `rp:Action`, `rp:Class`, and `rp:Qualified`.

Describes a variety of technique.

The `as:name` property **SHOULD** be used to provide a name for the `rp:Technique`.
A short description of the `rp:Technique` **MAY** be provided via the `as:summary` property.
A longer description of the `rp:Technique` **MAY** be provided via the `as:content` property.

###  12.6 The `rp:learned` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#learned`
+ Domain: `rp:Performer`
+ Range:
    + `as:Collection` | `as:OrderedCollection`
+ Functional: True

Provides a `as:OrderedCollection` or `as:Collection` of `rp:Learned` objects which a `rp:Performer` is able to perform.
Processors **MAY** set limits on the number of `rp:Learned` objects which will be associated with a given `rp:Performer`.

The existence and ordering of items in the provided collection **MAY** change over time.
`as:Tombstone`s **SHOULD NOT** be used to mark removed items.

An object **MUST NOT** have more than one value for its `rp:learned` property.
Upon encountering an object with more than one `rp:learned` value, processors **MAY**, at their discretion, ignore some or all values, or somehow attempt to combine the collections into one.

###  12.7 The `rp:learnset` Property

+ URI: `https://www.monstr.pub/ns/roleplaying#learnset`
+ Domain:
    + `rp:Performer` | `rp:Role`
+ Range: `rp:Learnable`

Indicates the potentially `rp:Learnable` techniques for a given `rp:Performer` or `rp:Instance` of an `rp:Role`.
For `rp:Performer`s, this property is *additive*:
Processors **SHOULD** consider both the `rp:Learnable` objects specified on a `rp:Performer`'s own `rp:learnset` property and those declared in that of its associated `rp:Role`(s), where applicable.

##  13. Extension  ##

The types above are intended to be extended by other specifications.
No requirements are made regarding this extension.

##  14. Changelog  ##

 >  This section is non-normative.

#####  2018-05-05.

 +  Allowed `rp:likelihood` to be used as a multiplier in addition to an isolated value.

 +  Added a section on extensibility.

#####  2018-05-01.

 +  New <https://www.monstr.pub/> URLs.

 +  Typo fixes and corrections from the previous version.

#####  2018-04-21.

 +  Allowed the `rp:maxHealth` property on `rp:Class` objects.

#####  2018-04-20 (2).

 +  Added the missing `rp:Performer` and `rp:Role` objects and the `rp:learnset` and `rp:learned` properties.

#####  2018-04-20.

 +  Initial specification; forked from an early [Mon Vocabulary][] draft.


[Activity Vocabulary]: <https://www.w3.org/TR/activitystreams-vocabulary/> "Activity Vocabulary"
[ActivityStreams 2.0]: <https://www.w3.org/TR/activitystreams-core/>       "Activity Streams 2.0"
[Mon Vocabulary]:      <https://www.monstr.pub/spec/mon-vocabulary/>       "Mon Vocabulary"
[RFC2119]:             <https://tools.ietf.org/html/rfc2119>               "Key words for use in RFCs to Indicate Requirement Levels"
[RPPub]:               <https://www.monstr.pub/spec/rppub-overview/>       "RPPub"
