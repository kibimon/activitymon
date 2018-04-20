#  Mon Vocabulary
Draft Specification

 - - -

##  Abstract  ##

This specification describes the Mon Vocabulary.
The Mon Vocabulary is an extension of the [Activity Vocabulary][] vocabulary inteded for use in the context of the [ActivityStreams 2.0][] format and provides a foundational vocabulary for representing relationships between trainers, routes, and mon.

 - - -

##  1. Introduction  ##

A *mon* is an imaginary creature or object which may be collected, traded, and interacted with.
Mon frequently have individual properties, known as *stats*, and are capable of particular actions, known as *techniques*.
The collectors, or *owners*, of mon are known as *trainers*.
This specification defines a common vocabulary for describing mon and trainers, intended for use in the context of [ActivityStreams 2.0][] documents.

###  1.1 Relationship to Other Specifications

This document is part of the [ActivityMon][] set of specifications.
Any process claiming to implement ActivityMon must conform to this specification.

This document builds upon the [Activity Vocabulary][] and is intended for use in an [ActivityStreams 2.0][] context.

###  1.2 Notational Conventions

[Activity Vocabulary][] types are indicated with the prefix `as:`, which represents the `https://www.w3.org/ns/activitystreams#` base URI.
Note that in [ActivityStreams 2.0][] documents, this prefix **MAY** be omitted.

Mon Vocabulary types are indicated with no prefix `mon:`, which represents the `tag:marrus.xyz,2018:activitymon::` base URI.
Note that in [ActivityStreams 2.0][] documents, this prefix **MUST** be declared in the `@context` of the document to be valid, and a different prefix **MAY** be used instead.

##  2. Conformance  ##

All sections explicitly marked as non-normative, as well as any diagrams, exmaples, or notes in this specification, are non-normative.
Everything else in this specification is normative.

The Mon Vocabulary is an extension to the [Activity Vocabulary][].
Uses of the Mon Vocabulary **MUST** conform to the requirements set forward in the Activity Vocabulary specification.

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

##  3. Important URIs  ##

The base URI for types in this specification is `tag:marrus.xyz,2018:activitymon::`.

This specification additionally specifies the URIs `tag:marrus.xyz,2018:activitymon::FORWARD`, `tag:marrus.xyz,2018:activitymon::BACKWARD`, `tag:marrus.xyz,2018:activitymon::LEFT`, and `tag:marrus.xyz,2018:activitymon::RIGHT` for denoting directions.
In particular, these URIs **MAY** be used in the `as:context` of an `as:Image` to represent the direction faced by the subject of the image.
Note that an image of a forward-facing subject is an image of its backside.

 >  As this is still a draft specification, the above URIs may change at some point in the future.

##  4. Mon Activity Types  ##

As with the [Activity Vocabulary], all Mon Activity Types inherit the properties of the base `as:Activity` type.
Many of these are subtypes of a more specific Activity Type; for example, `as:Offer`.

###  4.1 The `mon:Capture` Activity

+ URI: `tag:marrus.xyz,2018:activitymon::Capture`
+ Extends: `as:Offer`
+ Properties: Inherits all properties from `as:Offer`

A specialization of `as:Offer` in which the `as:actor` is attempting to claim ownership of the `as:object`.
The `as:instrument` property **MAY** be used to indicate mechanisms of capture.

###  4.2 The `mon:Search` Activity

+ URI: `tag:marrus.xyz,2018:activitymon::Search`
+ Extends: `as:Offer`
+ Properties: Inherits all properties from `as:Offer`

A specialization of `as:Offer` in which the `as:actor` is searching the `as:object`.
The `as:instrument` property **MAY** be used to indicate objects used when searching.
The `as:target` property **MAY** be used to indicate a desired object.

###  4.3 The `mon:Use` Activity

+ URI: `tag:marrus.xyz,2018:activitymon::Use`
+ Extends: `as:Offer`
+ Properties: Inherits all properties from `as:Offer`

A specialization of `as:Offer` in which the `as:actor` is attempting to use the `as:object` on the `as:target`.

##  5. Mon Actor Types  ##

In the [Activity Vocabulary][], Actors are Objects which are capable of performing an Activity.
Processors **MUST** recognize objects of the following types as Actors:

+ `mon:Mon`
+ `mon:Trainer`

Processors **MAY** automatically assign the type `mon:Trainer` to `as:Person` objects which are not `mon:Mon`.

The Mon Actor Types defined in this section are mutually exclusive; objects **MUST NOT** have more than one of these types.
Objects **MAY**, however, have other types not defined by this specification.
Processors **SHOULD** ignore any unknown types assigned to objects who have types defined in this section.

Processors **MUST** ignore any objects with more than one Mon Actor Type assigned.

###  5.1 The `mon:Mon` Actor

+ URI: `tag:marrus.xyz,2018:activitymon::Mon`
+ Extends: `mon:Species`
+ Disjoint With:
    + `mon:Route` | `mon:Trainer`
+ Properties:
    + `mon:effect` | `mon:exp` | `mon:health` | `mon:learned` | `mon:level` | `mon:statExp` | `mon:statInd`
    + Inherits all properties from `mon:Species`

Describes a mon.
`mon:Mon` actors **MUST** also be assigned the `as:Person` type.

The `mon:Species` of a `mon:Mon` **MAY** be provided, or linked to, using the `as:context` property.
Each `mon:Mon` **MUST NOT** have more than one associated `mon:Species`.
Upon encountering a `mon:Mon` with multiple associated `mon:Species`s, processors **MUST** ignore *all* of them, and treat the `mon:Mon` as though no `mon:Species` was specified.

To ensure that the `mon:Species` is always able to be dereferenced, `mon:Species` links **SHOULD** point to files on the same server as the associated `mon:Mon`.
Processors **MAY** refuse to fetch `as:context` links which do not share an origin with a given `mon:Mon`.

When processing a `mon:Mon` with an associated `mon:Species`, processors **SHOULD** use the values of the associated `mon:Species` if the following properties are not specified on the `mon:Mon`:

+ `as:content`
+ `as:icon`
+ `as:image`
+ `as:name`
+ `as:summary`
+ `mon:affinity`
+ `mon:growth`

When processing a `mon:Mon` with an associated `mon:Species`, processors **SHOULD** treat any values for the `mon:learnset` property on the `mon:Species` as though they had also been specified on the `mon:Mon`, *in addition to* any values specified on the `mon:Mon` directly.

A `mon:Mon` has an *owner* if there is an `mon:Trainer` object provided, or linked to, via the `as:attributedTo` property.
Each `mon:Mon` **MUST NOT** have more than one owner.
Upon encountering a `mon:Mon` with multiple owners, processors **MUST** ignore all but one.

`mon:Mon` who are owned by a `mon:Trainer` **SHOULD** exist on the same server as that `mon:Trainer`.
The owner of a `mon:Mon` **MUST NOT** change over time.

Information regarding the capture of a `mon:Mon` **MAY** be provided via the `as:generator` property, including:

+ A `mon:Item` used in the capture
+ A `mon:Region` and/or `mon:Route` where the capture took place
+ A `mon:Trainer` responsible for the capture

These may be provided directly or as links.
The `as:generator` property of a `mon:Mon` **MUST NOT** have, or link to, more than one of the above objects which share the same type.
Upon encountering a `mon:Mon` with multiple such objects present or linked to as `as:generator` values, processors **MUST** ignore all but one.
The values of the `as:generator` property **MUST NOT** change over time.

`mon:Item`s **MAY** be attached to a `mon:Mon` (*held*) via the `as:attachment` property.

###  5.2 The `mon:Route` Actor

+ URI: `tag:marrus.xyz,2018:activitymon::Route`
+ Extends: `as:Object`
+ Disjoint With:
    + `mon:Mon` | `mon:Trainer`
+ Properties:
    + `mon:dex` | `mon:routes`
    + Inherits all properties from `as:Object`

Describes a location, real or imagined, at which mon may be found.
`mon:Route`s which correspond to real-world locations **MUST** also be assigned the `as:Place` type.

An `as:name` **SHOULD** be provided for all `mon:Route`s, naming the route.
A tagline for the `mon:Route` **MAY** be provided via the `as:summary` property.
A longer description of the `mon:Route` **MAY** be provided via the `as:content` property.

The `mon:Region` of a `mon:Route` **MAY** be provided, or linked to, using the `as:assignedTo` property.
Each `mon:Route` **MUST NOT** have more than one associated `mon:Region`.
Upon encountering a `mon:Route` with multiple associated `mon:Region`s, processors **MUST** ignore *all* of them, and treat the `mon:Route` as though no `mon:Region` was specified.

Processors are **NOT REQUIRED** to recognize `mon:Route`s as Actors.

###  5.3 The `mon:Trainer` Actor

+ URI: `tag:marrus.xyz,2018:activitymon::Trainer`
+ Extends: `as:Object`
+ Disjoint With:
    + `mon:Mon` | `mon:Route`
+ Properties:
    + `mon:mon`
    + Inherits all properties from `as:Object`

Describes a potential owner, or *trainer*, of mon.
`mon:Trainer` actors **MUST** also be assigned the `as:Person` type.
Only `mon:Trainer`s are able to own mon.

##  6. Mon Object Types  ##

In addition to the Mon Activity Types and the Mon Actor Types, the Mon Vocabulary includes the following Mon Object Types:

###  6.1 The `mon:GrowthFunc` Object

+ URI: `tag:marrus.xyz,2018:activitymon::GrowthFunc`
+ Extends: `as:Object`
+ Properties:
    + `mon:maxLevel` | `mon:minLevel` | `mon:n0` | `mon:n1` | `mon:n2` | `mon:n3`
    + Inherits all properties from `as:Object`

Describes a cubic function, which calculates how many `mon:exp` a `mon:Mon` needs before reaching its next `mon:level`.

###  6.2 The `mon:Item` Object

+ URI: `tag:marrus.xyz,2018:activitymon::Item`
+ Extends: `as:Object`
+ Properties:
    + `mon:health` | `mon:maxHealth` | `mon:probability`
    + Inherits all properties from `as:Object`

Describes an item of some sort.

An `as:name` **SHOULD** be provided for all `mon:Item`s.
A short description or explanation of the `mon:Item` **MAY** be provided via the `as:summary` property.
A longer description of the `mon:Item` **MAY** be provided via the `as:content` property.

The types or effects of `mon:Item`s are not specified by this specification.

###  6.3 The `mon:Learned` Object

+ URI: `tag:marrus.xyz,2018:activitymon::Learned`
+ Extends: `as:Object`
+ Properties:
    + `mon:health` | `mon:level` | `mon:technique`
    + Inherits all properties from `as:Object`

Describes a learned technique, which is a type of action performable by a mon.

The `mon:Technique` of a `mon:Learned` object **MUST** be linked to using the `mon:technique` property.
Each `mon:Learned` object **MUST NOT** have more than one associated `mon:Technique`.
Processors **MUST** ignore any `mon:Learned` object with multiple `mon:Technique`s associated in this manner.

Processors **MUST** ignore all `mon:Learned` objects without an associated `mon:Technique`.
To ensure that the `mon:Technique` is always able to be dereferenced, a linked `mon:Technique` **SHOULD** be stored on the same server as the associated `mon:Learned` object.

Processors **MUST** ignore the `mon:health` property for all `mon:Learned` objects which are included in a `mon:learnset`.
Processors **MUST** ignore the `mon:level` property for all `mon:Learned` objects which are included in the `as:items` of an `as:Collection` or which are included as an `as:object` of an `as:Activity`.

###  6.4 The `mon:Region` Object

+ URI: `tag:marrus.xyz,2018:activitymon::Region`
+ Extends: `as:Object`
+ Properties:
    + `mon:dex` | `mon:routes`
    + Inherits all properties from `as:Object`

Describes a region.
Regions are objects which **MAY** be used to discover different `mon:Species` or `mon:Routes`.
`mon:Region`s which correspond to real-world locations **MUST** also be assigned the `as:Place` type.

An `as:name` **SHOULD** be provided for all `Region`s.
A tagline for the `mon:Region` **MAY** be provided via the `as:summary` property.
A longer description of the `mon:Region` **MAY** be provided via the `as:content` property.

The `mon:dex` property, if present, **MUST** contain a link to an `as:OrderedCollection` of `mon:Species` objects.
The ordering of `mon:Species` objects in this collection **SHOULD NOT** change over time (although new objects **MAY** be added); `as:Tombstone` objects **SHOULD** be used to mark any `mon:Species` removed from a `mon:dex` collection.

The `mon:routes` property, if present, **MUST** contain a link to an `as:OrderedCollection` of `mon:Route` objects.
The ordering of `mon:Route` objects in this collection **SHOULD NOT** change over time (although new objects **MAY** be added); `as:Tombstone` objects **SHOULD** be used to mark any `Route`s removed from a `mon:routes` collection.

Both of the `as:OrderedCollection`s described above **SHOULD** be located on the same server as the given `mon:Region`, and so **SHOULD** all of the objects they contain.
The `mon:Species` and `mon:Route`s discoverable through these collections **SHOULD** reference this `mon:Region` in their `as:assignedTo`.

###  6.5 The `mon:Species` Object

+ URI: `tag:marrus.xyz,2018:activitymon::Species`
+ Extends: `as:Object`
+ Properties:
    + `mon:ability` | `mon:affinity` | `mon:forme` | `mon:growth` | `mon:learnset` | `mon:maxHealth` | `mon:nature` | `mon:stats`
    + Inherits all properties from `as:Object`

Describes a variety, or *species*, of mon.

An `as:name` **SHOULD** be provided for all `mon:Species`.
A tagline, or short description, for the `mon:Species` **MAY** be provided via the `as:summary` property.
A longer description of the `mon:Species` **MAY** be provided via the `as:content` property.

The `mon:Region` of a `mon:Species` **MAY** be provided, or linked to, using the `as:assignedTo` property.
Each `mon:Species` **MUST NOT** have more than one associated `mon:Region` property.
Upon encountering a `mon:Species` with multiple associated `mon:Region`s, processors **MUST** ignore *all* of them, and treat the `mon:Species` as though no `mon:Region` was specified.

If `mon:Mon` of a particular `mon:Species` are known to exist on a particular `mon:Route`, that `mon:Route` may be provided, or linked to, using the `as:location` property.

The `mon:ability`, `mon:forme`, and `mon:nature` properties give the anticipated *possible* values of those properties for an associated `mon:Mon`.
However, no guarantees are made regarding this, and associated `mon:Mon` **MAY** have values not specified on their associated `mon:Species`.

Most of the properties on the `mon:Species` object are not directly inherited by associated `mon:Mon`, but **MAY** be used when generating associated `Mon` values, in an implementation-defined manner.

###  6.6 The `mon:Stat` Object

+ URI: `tag:marrus.xyz,2018:activitymon::Stat`
+ Extends: `as:Object`
+ Properties:
    + `mon:level`
    + Inherits all properties from `as:Object`

Describes a stat.

An `as:name` **SHOULD** be provided for all `mon:Stat`s.
A short description of the `mon:Stat` **MAY** be provided via the `as:summary` property.
A longer description of the `mon:Stat` **MAY** be provided via the `as:content` property.

The types or effects of `mon:Stat`s are not specified by this specification.

The `mon:level` property **MUST** be ignored for all `mon:Stat`s which are values of a `mon:usesStat`.
Otherwise, the following (inclusive) ranges of values for `mon:level` are **RECOMMENDED**:

+ For `mon:Stat`s which are included in a `mon:statInd`: 0–15
+ For `mon:Stat`s which are included in a `mon:statExp`: 0–255
+ For all other `mon:Stat`s: 0–1023

###  6.7 The `mon:Technique` Object

+ URI: `tag:marrus.xyz,2018:activitymon::Technique`
+ Extends: `as:Object`
+ Properties:
    + `mon:affinity` | `mon:effect` | `mon:maxHealth` | `mon:probability` | `mon:power` | `mon:usesStat`
    + Inherits all properties from `as:Object`.

Describes a variety of technique.

An `as:name` **SHOULD** be provided for all `Technique`s.
A tagline, or short description, for the `mon:Technique` **MAY** be provided via the `as:summary` property.
A longer description of the `mon:Technique` **MAY** be provided via the `as:content` property.

##  7. Properties  ##

###  7.1 The `mon:ability` Property

+ URI: `tag:marrus.xyz,2018:activitymon::ability`
+ Domain: `mon:Species`
+ Range: `xsd:anyURI`

Indicates a "passive" ability for a `mon:Mon`.
No abilities are defined by this specification.

For `mon:Species`, this property gives the anticipated *possible* values of the `mon:ability` property for `mon:Mon` of that species type.

###  7.2 The `mon:affinity` Property

+ URI: `tag:marrus.xyz,2018:activitymon::affinity`
+ Domain: `mon:Species` | `mon:Technique`
+ Range: `xsd:anyURI`

Indicates an affinity (or "type") of a `mon:Mon` or `mon:Technique`.
No affinities are defined by this specification.

###  7.3 The `mon:dex` Property

+ URI: `tag:marrus.xyz,2018:activitymon::dex`
+ Domain: `mon:Region` | `mon:Route`
+ Range: `as:Link`
+ Functional: True

References an `as:OrderedCollection` or `as:Collection` of `mon:Species` which might be found at a given location.
For `mon:Region`s, this **MUST** be an `as:OrderedCollection`.

An object **MUST NOT** have more than one value for its `mon:dex` property.
Upon encountering an object with more than one `mon:dex` value, processors **MUST** ignore all values; that is, treat the object as if *no* `mon:dex` property had been defined.

###  7.4 The `mon:effect` Property

+ URI: `tag:marrus.xyz,2018:activitymon::effect`
+ Domain: `mon:Mon` | `mon:Technique`
+ Range: `xsd:anyURI`

Indicates a status effect, either active on a `mon:Mon`, or which will result from a `mon:Technique`.
Status effects may be ephemeral or persistent.
No effects are defined by this specification.

###  7.5 The `mon:exp` Property

+ URI: `tag:marrus.xyz,2018:activitymon::exp`
+ Domain: `mon:Mon`
+ Range: `xsd:nonNegativeInteger`
+ Functional: True

Indicates some integral amount of experience, above and beyond the current `mon:level` of a `mon:Mon`, if specified.

An object **MUST NOT** have more than one value for its `mon:exp` property.
Upon encountering an object with more than one `mon:exp` value, processors **MUST** ignore all but the smallest value.

###  7.6 The `mon:forme` Property

+ URI: `tag:marrus.xyz,2018:activitymon::forme`
+ Domain: `mon:Species`
+ Range: `xsd:anyURI`

Indicates a "forme" of a `mon:Mon`, which is a broad category used to describe variations in gender, appearance, or properties.
The schema.org values `https://schema.org/Male` and `https://schema.org/Female` **SHOULD** be used for the male and female formes, where applicable.
No other formes are defined by this specification.

For `mon:Species`, this property gives the anticipated *possible* values of the `mon:forme` property for `mon:Mon` of that species type.

###  7.7 The `mon:growth` Property

+ URI: `tag:marrus.xyz,2018:activitymon::growth`
+ Domain: `mon:Species`
+ Range: `mon:GrowthFunc`

Indicates a growth or levelling rate for a `mon:Mon`—that is, the amount of `mon:exp` required for a change in `mon:level`—as a (optionally piecewise) polynomial, specified via `mon:GrowthFunc`s.

The `mon:GrowthFunc` functions provided by the `mon:growth` property **MUST NOT** overlap; on encountering an overlap, processors **SHOULD** take the average result of all applicable functions.
If no applicable `mon:GrowthFunc` object is provided for the current `mon:level` of a `mon:Mon`, the function `3x^2` **SHOULD** be used by processors as a default.

No requirements are made regarding the shape of a `mon:growth` polynomial; however, it is **RECOMMENDED** that polynomials remain reasonably similar in shape to the default of `3x^2`, and moreover that the integral over the range \[0, 100] be close to the `3x^2` result of one million.

The `mon:growth` polynomial **MUST** map to a nonnegative number for every integer in the range \[1, 100].
When calculating the result of a `mon:growth` polynomial for a given number, processors **MUST** clamp the result to \[0, ∞).

When processing the result of a `mon:growth` polynomial, processors **MUST** round the value up to the nearest whole integer.

###  7.8 The `mon:health` Property

+ URI: `tag:marrus.xyz,2018:activitymon::health`
+ Domain: `mon:Item` | `mon:Learned` | `mon:Mon`
+ Range: `xsd:nonNegativeInteger`
+ Functional: True

For `mon:Mon`, indicates current vitality.
For `mon:Item`s or `mon:Learned` objects, indicates the amount of times the object can be used.
This property **MUST NOT** be defined for `mon:Item`s or `mon:Learned`s which may be used an infinite number of times.

An object **MUST NOT** have more than one value for its `mon:health` property.
Upon encountering an object with more than one `mon:health` value, processors **MUST** ignore all but the smallest value.

###  7.9 The `mon:learned` Property

+ URI: `tag:marrus.xyz,2018:activitymon::learned`
+ Domain: `mon:Mon`
+ Range: `as:OrderedCollection`
+ Functional: True

Indicates a `as:OrderedCollection` of `mon:Learned` objects which a `mon:Mon` is able to perform.
Processors **MAY** set limits on the number of `mon:Learned` objects which will be associated with a given `mon:Mon`.

The ordering of items within the `as:OrderedCollection` **MAY** change over time.
`as:Tombstone`s **SHOULD NOT** be used to mark removed items.

An object **MUST NOT** have more than one value for its `mon:learned` property.
Upon encountering an object with more than one `mon:learned` value, processors **MAY**, at their discretion, ignore some or all values, or somehow attempt to combine the `as:OrderedCollection`s.

###  7.10 The `mon:learnset` Property

+ URI: `tag:marrus.xyz,2018:activitymon::learnset`
+ Domain: `mon:Species`
+ Range: `mon:Learned`

Indicates the potential `mon:Learned` objects for a given `mon:Mon`.
For `mon:Mon`, this property is *additive*:
Processors **SHOULD** consider both the `mon:Learned` objects specified on a `mon:Mon`s own `mon:learnset` property and those declared in that of its associated `mon:Species`, where applicable.

###  7.11 The `mon:level` Property

+ URI: `tag:marrus.xyz,2018:activitymon::level`
+ Domain: `mon:Learned` | `mon:Mon` | `mon:Stat`
+ Range: `xsd:nonNegativeInteger`
+ Functional: True

For `mon:Mon` or `mon:Stat`s, indicates a current numeric rank of proficiency.
For `mon:Learned` objects, indicates the required numeric rank to be added to the `mon:learned` techniques of a `mon:Mon`.
A `mon:Learned` object which cannot be learned through levelling **MUST NOT** have a `mon:level` property.

The `mon:level` of `mon:Mon` and `mon:Learned` objects **SHOULD** be in the range \[1, 100].
The `mon:level` of `mon:Stat`s **MAY** be higher.

An object **MUST NOT** have more than one value for its `mon:level` property.
Upon encountering an object with more than one `mon:level` value, processors **MUST** ignore all but the smallest one.

###  7.12 The `mon:maxHealth` Property

+ URI: `tag:marrus.xyz,2018:activitymon::maxHealth`
+ Domain: `mon:Item` | `mon:Species` | `mon:Technique`
+ Range: `xsd:nonNegativeInteger`
+ Functional: True

Indicates a maximum value for the `mon:health` property.
Although this property can have any non-negative integer value, it is **RECOMMENDED** that it be in the range \[0, 1023].

An object **MUST NOT** have more than one value for its `mon:learned` property.
Upon encountering an object with more than one `mon:learned` value, processors **MUST** ignore all but the smallest one.

###  7.13 The `mon:maxLevel` Property

+ URI: `tag:marrus.xyz,2018:activitymon::maxLevel`
+ Domain: `mon:GrowthFunc`
+ Range: `xsd:nonNegativeInteger`
+ Functional: True

Indicates the maximum value of `mon:level` for which the given `mon:GrowthFunc` applies.

An object **MUST NOT** have more than one value for its `mon:learned` property.
Upon encountering an object with more than one `mon:learned` value, processors **MUST** ignore all but the smallest one.

###  7.14 The `mon:minLevel` Property

+ URI: `tag:marrus.xyz,2018:activitymon::minLevel`
+ Domain: `mon:GrowthFunc`
+ Range: `xsd:nonNegativeInteger`
+ Functional: True

Indicates the minimum value of `mon:level` for which the given `mon:GrowthFunc` applies.

An object **MUST NOT** have more than one value for its `mon:learned` property.
Upon encountering an object with more than one `mon:learned` value, processors **MUST** ignore all but the largest one.

###  7.15 The `mon:mon` Property

+ URI: `tag:marrus.xyz,2018:activitymon::party`
+ Domain: `mon:Trainer`
+ Range: `as:Link`
+ Functional: True

References an `as:OrderedCollection` or `as:Collection` of the `mon:Mon` for which a `mon:Trainer` is an owner.
All of the `mon:Mon` in the referenced collection **MUST** be owned by that `mon:Trainer`.

The ordering of items within the `as:OrderedCollection` **MAY** change over time.
`as:Tombstone`s **SHOULD NOT** be used to mark removed items.

An object **MUST NOT** have more than one value for its `mon:mon` property.
Upon encountering an object with more than one `mon:mon` value, processors **MUST** ignore all values; that is, treat the object as if *no* `mon:mon` property had been defined.

###  7.16 The `mon:n0` Property

+ URI: `tag:marrus.xyz,2018:activitymon::n0`
+ Domain: `mon:GrowthFunc`
+ Range: `xsd:float`
+ Functional: True

The zeroth-degree coefficient of a `mon:GrowthFunc` polynomial.

An object **MUST NOT** have more than one value for its `mon:n0` property.
Upon encountering an object with more than one `mon:n0` value, processors **SHOULD** use the average of all of the values given.

###  7.17 The `mon:n1` Property

+ URI: `tag:marrus.xyz,2018:activitymon::n1`
+ Domain: `mon:GrowthFunc`
+ Range: `xsd:float`
+ Functional: True

The first-degree coefficient of a `mon:GrowthFunc` polynomial.

An object **MUST NOT** have more than one value for its `mon:n1` property.
Upon encountering an object with more than one `mon:n1` value, processors **SHOULD** use the average of all of the values given.

###  7.18 The `mon:n2` Property

+ URI: `tag:marrus.xyz,2018:activitymon::n2`
+ Domain: `mon:GrowthFunc`
+ Range: `xsd:float`
+ Functional: True

The second-degree coefficient of a `mon:GrowthFunc` polynomial.

An object **MUST NOT** have more than one value for its `mon:n2` property.
Upon encountering an object with more than one `mon:n2` value, processors **SHOULD** use the average of all of the values given.

###  7.19 The `mon:n3` Property

+ URI: `tag:marrus.xyz,2018:activitymon::n3`
+ Domain: `mon:GrowthFunc`
+ Range: `xsd:float`
+ Functional: True

The third-degree coefficient of a `mon:GrowthFunc` polynomial.

An object **MUST NOT** have more than one value for its `mon:n3` property.
Upon encountering an object with more than one `mon:n3` value, processors **SHOULD** use the average of all of the values given.

###  7.20 The `mon:nature` Property

+ URI: `tag:marrus.xyz,2018:activitymon::nature`
+ Domain: `mon:Species`
+ Range: `xsd:anyURI`

Indicates individual temperament of a `mon:Mon`.
No natures are defined by this specification.

For `mon:Species`, this property gives the anticipated *possible* values of the `mon:nature` property for `mon:Mon` of that species type.

###  7.21 The `mon:power` Property

+ URI: `tag:marrus.xyz,2018:activitymon::power`
+ Domain: `mon:Technique`
+ Range: `xsd:nonNegativeInteger`
+ Functional: True

Indicates the power of a `mon:Technique`; eg, its effectiveness.
The range \[0, 100] **SHOULD** be used as a guideline for "normal" levels of effectiveness.

An object **MUST NOT** have more than one value for its `mon:power` property.
Upon encountering an object with more than one `mon:power` value, processors **MUST** ignore all but the smallest one.

###  7.22 The `mon:probability` Property

+ URI: `tag:marrus.xyz,2018:activitymon::probability`
+ Domain: `mon:Item` | `mon:Technique`
+ Range: `xsd:float` [>= 0.0f, <=1.0f]
+ Functional: True

Indicates the probability that a `mon:Item` or `mon:Move` will have an effect.

An object **MUST NOT** have more than one value for its `mon:probability` property.
Upon encountering an object with more than one `mon:probability` value, processors **MUST** ignore all but the smallest one.

###  7.23 The `mon:routes` Property

+ URI: `tag:marrus.xyz,2018:activitymon::routes`
+ Domain: `mon:Region` | `mon:Route`
+ Range: `as:Link`
+ Functional: True

References an `as:OrderedCollection` or `as:Collection` of `mon:Route`s which might be accessed from a given location.
For `mon:Region`s, this **MUST** be an `as:OrderedCollection`.

An object **MUST NOT** have more than one value for its `mon:routes` property.
Upon encountering an object with more than one `mon:routes` value, processors **MUST** ignore all values; that is, treat the object as if *no* `mon:routes` property had been defined.

###  7.24 The `mon:statExp` Property

+ URI: `tag:marrus.xyz,2018:activitymon::statExp`
+ Domain: `mon:Mon`
+ Range: `mon:Stat`

Indicates the experience of an individual `mon:Mon` in various stats.
The `mon:level`s of the `mon:Stat` values of this property **SHOULD** be in the range \[0, 255], and indicate the amount of experience.
These values **MAY** be used by authors when generating the `mon:stats` of a `mon:Mon`, in an implementation-defined manner.

###  7.25 The `mon:statInd` Property

+ URI: `tag:marrus.xyz,2018:activitymon::statInd`
+ Domain: `mon:Mon`
+ Range: `mon:Stat`

Indicates individual strengths and weaknesses of a `mon:Mon` in various stats.
The `mon:level`s of the `mon:Stat` values of this property **SHOULD** be in the range \[0, 16], and indicate the innate strength of a `mon:Mon` in that stat.
These values **MAY** be used by authors when generating the `mon:stats` of a `mon:Mon`, in an implementation-defined manner.

###  7.26 The `mon:stats` Property

+ URI: `tag:marrus.xyz,2018:activitymon::stats`
+ Domain: `mon:Species`
+ Range: `mon:Stat`

Indicates the `mon:level`s of a `mon:Mon` in various stats.
For `mon:Species`, these are the base `mon:level`s, which **MAY** be used by processors when computing the stats of a `mon:Mon`, in an implementation-defined manner.

The `mon:level`s of the `mon:Stat` values of this property **SHOULD** be in the range \[0, 1023].

###  7.27 The `mon:technique` Property

+ URI: `tag:marrus.xyz,2018:activitymon::technique`
+ Domain: `mon:Learned`
+ Range: `as:Link`
+ Functional: True

Indicates a link to a `mon:Technique` that has been `mon:Learned`.

###  7.28 The `mon:usesStat` Property

+ URI: `tag:marrus.xyz,2018:activitymon::usesStat`
+ Domain: `mon:Technique`
+ Range: `mon:Stat`

Indicates that a `mon:Technique` depends upon a given `mon:Stat`.

##  8. Changelog  ##

#####  2018-04-20.

 +  Split the [ActivityMon][] specification into two components: [Mon Vocabulary][] and [ActivityMon Core][].
    ☞ [Issue #7](https://github.com/kibimon/activitymon/issues/7)

 +  Significant rewriting of sections for clarity and precision; fixed typos.

     +  Clarified the relationship between this and other specifications.

     +  Clarified notational conventions regarding type URIs.

 +  Added the `mon:Trainer` Mon Actor Type and mandated `as:Person` as an additional type for `mon:Mon`.
    ☞ [Issue #3](https://github.com/kibimon/activitymon/issues/3)

 +  Renamed `mon:Move` and `mon:MoveKind` to `mon:Learned` and `mon:Technique` to avoid confusion with `as:Move`.

     +  Also renamed `mon:moveset` to `mon:learnset` and `mon:moves` to `mon:learned`.

 +  Better clarified the expected behaviour for processors when encountering an object with more than one associated objects when one or fewer is expected.
    ☞ [Issue #1](https://github.com/kibimon/activitymon/issues/1)

     +  Also clarified this for functional properties.

 +  Made `mon:Route` an Actor type.
    ☞ [Issue #4](https://github.com/kibimon/activitymon/issues/4)

     +  Also added `mon:dex` and `mon:routes` properties to `mon:Route` for better mon/route discovery.

 +  The `mon:learned` `mon:Learned` objects of a `mon:Mon` are now ordered.

     +  As a result, the `mon:learned` property is now functional.

 +  Added a `mon:Search` Activity, intended for searching `mon:Route`s.
    ☞ [Issue #5](https://github.com/kibimon/activitymon/issues/5)

 +  `mon:Species` are now associated with `mon:Region`s using `as:assignedTo`, not `as:context`, to match `mon:Route`s.

 +  The default `mon:growth` function has changed from `x^3` to the much more sensible `3x^2` (the old function measured *total* experience).

     +  Additionally, guidelines for creating and processing `mon:growth` functions have been added.

 +  `mon:power` was changed to allow values of 0.

 +  Renamed `mon:GrowthPart` to `mon:GrowthFunc` for clarity.

 +  The `mon:Technique` associated with a `mon:Learned` object is now specified via the `mon:technique` property, rather than `as:context`, to indicate the close semantic relationship.

 +  `mon:Mon` are no longer required to have `mon:Species`.

 +  `mon:Item`s, `mon:Region`s, and `mon:Route`s are now explicitly listed as valid values for the `as:generator` property.

 +  The meaning of `as:summary` and `as:content` is now defined for many types.

#####  2018-04-18.

 +  Initial specification.


[Activity Vocabulary]: <https://www.w3.org/TR/activitystreams-vocabulary/> "Activity Vocabulary"
[ActivityMon]:         <https://kibimon.github.io/activitymon/>            "ActivityMon"
[ActivityMon Core]:    <https://kibimon.github.io/activitymon/core/>       "ActivityMon Core"
[ActivityStreams 2.0]: <https://www.w3.org/TR/activitystreams-core/>       "Activity Streams 2.0"
[Mon Vocabulary]:      <https://kibimon.github.io/activitymon/vocabulary/> "Mon Vocabulary"
[RFC2119]:             <https://tools.ietf.org/html/rfc2119>               "Key words for use in RFCs to Indicate Requirement Levels"
