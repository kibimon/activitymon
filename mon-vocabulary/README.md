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

This document is part of the [MonStrPub][] set of specifications.
Any process claiming to implement MonStrPub must conform to this specification.

This document builds upon the [Activity Vocabulary][] and is intended for use in an [ActivityStreams 2.0][] context.

###  1.2 Notational Conventions

[Activity Vocabulary][] types are indicated with the prefix `as:`, which represents the `https://www.w3.org/ns/activitystreams#` base URI.
Note that in [ActivityStreams 2.0][] documents, this prefix **MAY** be omitted.

[Roleplaying Vocabulary][] types are indicated with the prefix `rp:`, which represents the `https://www.monstr.pub/ns/roleplaying#` base URI.
Note that in [ActivityStreams 2.0][] documents, this prefix **MUST** be declared in the `@context` of the document to be valid, and a different prefix **MAY** be used instead.

[Mon Vocabulary][] types are indicated with the prefix `mon:`, which represents the `https://www.monstr.pub/ns/monstrpub#` base URI.
Note that in [ActivityStreams 2.0][] documents, this prefix **MUST** be declared in the `@context` of the document to be valid, and a different prefix **MAY** be used instead.

 >  As this is still a draft specification, the above URIs may change at some point in the future.

##  2. Conformance  ##

All sections explicitly marked as non-normative, as well as any diagrams, examples, or notes in this specification, are non-normative.
Everything else in this specification is normative.

The Mon Vocabulary is an extension of the [Roleplaying Vocabulary][], which is in turn an extension of the [Activity Vocabulary][].
Implementations of the Mon Vocabulary **MUST** conform to the requirements set forward in these specifications.

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

##  3. Mon Activity Types  ##

As with the [Activity Vocabulary][], all Mon Activity Types inherit the properties of the base `as:Activity` type.
Many of these are subtypes of a more specific Activity Type; for example, `as:Offer`.

###  3.1 The `mon:Capture` Activity

+ URI: `https://www.monstr.pub/ns/monstrpub#Capture`
+ Extends: `as:Offer`
+ Properties: Inherits all properties from `as:Offer`

A specialization of `as:Offer` in which the `as:actor` is attempting to claim ownership of the `as:object`.
The `as:instrument` property **MAY** be used to indicate mechanisms of capture.

###  3.2 The `mon:Search` Activity

+ URI: `https://www.monstr.pub/ns/monstrpub#Search`
+ Extends: `as:Offer`
+ Properties: Inherits all properties from `as:Offer`

A specialization of `as:Offer` in which the `as:actor` is searching the `as:object`.
The `as:instrument` property **MAY** be used to indicate objects used when searching.
The `as:target` property **MAY** be used to indicate a desired object.

##  4. Mon Actor Types  ##

In the [Activity Vocabulary][], Actors are Objects which are capable of performing an Activity.
Processors **MUST** recognize objects of the following types as Actors:

+ `mon:Mon`
+ `mon:Trainer`

Processors **MAY** automatically assign the type `mon:Trainer` to `as:Person` objects which are not `mon:Mon`.

The Mon Actor Types defined in this section are mutually exclusive; objects **MUST NOT** have more than one of these types.
Actors **MAY**, however, have other types not defined by this specification.
Processors **SHOULD** ignore any unknown types assigned to objects who have types defined in this section.

Processors **MUST** ignore any objects with more than one Mon Actor Type assigned.

###  4.1 The `mon:Mon` Actor

+ URI: `https://www.monstr.pub/ns/monstrpub#Mon`
+ Extends:
    + `rp:Effectual` | `rp:Perishable` | `rp:Performer` | `rp:Qualified` | `rp:Quantified` | `rp:Ranked`
+ Disjoint With:
    + `mon:Route` | `mon:Trainer`
+ Properties:
    + `mon:captureRate`
    + Inherits all properties from `rp:Effectual`, `rp:Perishable`, `rp:Performer`, `rp:Qualified`, `rp:Quantified`, and `rp:Ranked`

Describes a mon.
`mon:Mon` actors **MUST** also be assigned the `as:Person` type.

The `rp:class` property **MAY** be used to specify the species of a `mon:Mon`, which **MUST** be a `mon:Species`.
`mon:Mon` **MUST NOT** have more than one `rp:class`.
Processors **MUST** ignore all values of `rp:class` if there are more than one, or if they do not point to a `mon:Species` object.

To ensure that the `mon:Species` is always able to be dereferenced, `rp:class` links **SHOULD** point to files on the same server as the associated `mon:Mon`.
Processors **MAY** refuse to fetch `rp:class` links which do not share an origin with a given `mon:Mon`.

When processing a `mon:Mon` with an associated `mon:Species`, processors **SHOULD** use values from the associated `mon:Species` if the following properties are not specified on the `mon:Mon`:

+ `as:content`
+ `as:icon`
+ `as:image`
+ `as:name`
+ `as:summary`
+ `mon:captureRate`
+ `rp:growth`

If one or more `rp:Affinity` objects are provided via the `rp:quality` property on an associated `mon:Species`, but *no* `rp:Affinity` is provided via the same property on a given `mon:Mon`, processors **SHOULD** treat the `mon:Mon` as though those objects had been provided on its `rp:quality` property as well.

A `mon:Mon` has an *owner* if there is an `mon:Trainer` object provided, or linked to, via the `as:attributedTo` property.
Each `mon:Mon` **MUST NOT** have more than one owner.
Upon encountering a `mon:Mon` with multiple owners, processors **MUST** ignore all but one.

`mon:Mon` who are owned by a `mon:Trainer` **SHOULD** exist on the same server as that `mon:Trainer`.
The owner of a `mon:Mon` **MUST NOT** change over time.

Information regarding the capture of a `mon:Mon` **MAY** be provided via the `as:generator` property, including:

+ One or more `mon:Item`s used in the capture
+ A `mon:Region` and/or a `mon:Route` where the capture took place
+ A `mon:Trainer` responsible for the capture

These may be provided directly or as links.

The `as:generator` property of a `mon:Mon` **MUST NOT** have, or link to, more than one of each of `mon:Region`s, `mon:Route`s, or `mon:Trainer`s.
Upon encountering a `mon:Mon` with multiple such objects present or linked to as `as:generator` values, processors **MUST** ignore all but one.
The values of the `as:generator` property **MUST NOT** change over time.

The `mon:Region` of a `mon:Mon` **MAY** be provided, or linked to, using the `as:attributedTo` property.
Each `mon:Mon` **MUST NOT** have more than one associated `mon:Region`.
Upon encountering a `mon:Mon` with multiple associated `mon:Region`s, processors **MUST** ignore *all* of them, and treat the `mon:Mon` as though no `mon:Region` was specified.

`mon:ItemSlot`s **MAY** be attached to a `mon:Mon` (*held*) via the `as:attachment` property.

###  4.2 The `mon:Route` Actor

+ URI: `https://www.monstr.pub/ns/monstrpub#Route`
+ Extends: `as:Object`
+ Disjoint With:
    + `mon:Mon` | `mon:Trainer`
+ Properties:
    + `mon:dex` | `mon:routes`
    + Inherits all properties from `as:Object`

Describes a location or other construct, real or imagined, from which mon may be found.
`mon:Route`s which correspond to real-world locations **MUST** also be assigned the `as:Place` type.

An `as:name` **SHOULD** be provided for all `mon:Route`s, naming the route.
A tagline for the `mon:Route` **MAY** be provided via the `as:summary` property.
A longer description of the `mon:Route` **MAY** be provided via the `as:content` property.

The `mon:Region` of a `mon:Route` **MAY** be provided, or linked to, using the `as:attributedTo` property.
Each `mon:Route` **MUST NOT** have more than one associated `mon:Region`.
Upon encountering a `mon:Route` with multiple associated `mon:Region`s, processors **MUST** ignore *all* of them, and treat the `mon:Route` as though no `mon:Region` was specified.

Recognizing `mon:Route`s as Actors is **OPTIONAL** for processors.

###  4.3 The `mon:Trainer` Actor

+ URI: `https://www.monstr.pub/ns/monstrpub#Trainer`
+ Extends: `as:Object`
+ Disjoint With:
    + `mon:Mon` | `mon:Route`
+ Properties:
    + `mon:mon`
    + Inherits all properties from `as:Object`

Describes a potential owner, or *trainer*, of mon.
`mon:Trainer` actors **MUST** also be assigned the `as:Person` type.
Only `mon:Trainer`s are able to own mon.

The `mon:Region` of a `mon:Trainer` **MAY** be provided, or linked to, using the `as:attributedTo` property.
Each `mon:Trainer` **MUST NOT** have more than one associated `mon:Region`.
Upon encountering a `mon:Trainer` with multiple associated `mon:Region`s, processors **MUST** ignore *all* of them, and treat the `mon:Trainer` as though no `mon:Region` was specified.

`mon:ItemSlot`s **MAY** be attached to a `mon:Trainer` (*held*) via the `as:attachment` property.

##  5. Mon Object Types  ##

In addition to the Mon Activity Types and the Mon Actor Types, the Mon Vocabulary includes the following Mon Object Types:

###  5.1 The `mon:Item` Object

+ URI: `https://www.monstr.pub/ns/monstrpub#Item`
+ Extends:
    + `rp:Action` | `rp:Class`
+ Properties: Inherits all properties from `rp:Action` and `rp:Class`

Describes a class of item of some sort.

An `as:name` **SHOULD** be provided for all `mon:Item`s.
A short description or explanation of the `mon:Item` **MAY** be provided via the `as:summary` property.
A longer description of the `mon:Item` **MAY** be provided via the `as:content` property.

###  5.2 The `mon:ItemSlot` Object

+ URI: `https://www.monstr.pub/ns/monstrpub#ItemSlot`
+ Extends:
    + `rp:Action` | `rp:Instance` | `rp:Perishable`
+ Properties: Inherits all properties from `rp:Action`, `rp:Instance`, and `rp:Perishable`

Describes one or more identical instances of a `mon:Item`.

The `rp:class` property **MAY** be used to specify the class of a `mon:ItemSlot`, which **MUST** be a `mon:Item`.
`mon:ItemSlot`s **MUST NOT** have more than one `rp:class`.
Processors **MUST** ignore all values of `rp:class` if there are more than one, or if they do not point to a `mon:Item` object.

To ensure that the `mon:Item` is always able to be dereferenced, `rp:class` links **SHOULD** point to files on the same server as the associated `mon:ItemSlot`.
Processors **MAY** refuse to fetch `rp:class` links which do not share an origin with a given `mon:ItemSlot`.

When processing a `mon:ItemSlot` with an associated `mon:Item`, processors **SHOULD** use values from the associated `mon:ItemSlot` if the following properties are not specified on the `mon:Item`:

+ `as:content`
+ `as:icon`
+ `as:image`
+ `as:name`
+ `as:summary`
+ `rp:likelihood`
+ `rp:maxHealth`
+ `rp:power`

The `rp:health` property describes how many times the items described by the `mon:ItemSlot` are able to be used.
For single-use items, this is the number of items being described.
For multi-use items, this is the number of uses remaining for the item.
Items which may be used more than once **SHOULD** each be given their own `mon:ItemSlot` object.

###  5.3 The `mon:Region` Object

+ URI: `https://www.monstr.pub/ns/monstrpub#Region`
+ Extends: `as:Object`
+ Properties:
    + `mon:dex` | `mon:routes` | `mon:supports`
    + Inherits all properties from `as:Object`

Describes a region.
Regions are objects which **MAY** be used to discover different `mon:Species` or `mon:Routes`.
`mon:Region`s which correspond to real-world locations **MUST** also be assigned the `as:Place` type.

The `mon:supports` property may be used to indicate extensions which are supported by objects associated with the `mon:Region`.
All objects which reference this `mon:Region` in their `as:attributedTo` **SHOULD** support any such extensions.

An `as:name` **SHOULD** be provided for all `Region`s.
A tagline for the `mon:Region` **MAY** be provided via the `as:summary` property.
A longer description of the `mon:Region` **MAY** be provided via the `as:content` property.

The `mon:dex` property, if present, **MUST** contain an `as:OrderedCollection` of `mon:Species` objects.
New `mon:Species` objects **SHOULD** only be added to the *end* of this collection, and `as:Tombstone` objects **SHOULD** be used to mark any `mon:Species` removed from a `mon:dex` collection.
The ordering of existing items in a `mon:dex` collection **SHOULD NOT** change over time.

The `mon:routes` property, if present, **MUST** contain an `as:OrderedCollection` of `mon:Route` objects.
New `mon:Route` objects **SHOULD** only be added to the *end* of this collection, and `as:Tombstone` objects **SHOULD** be used to mark any `mon:Route` removed from a `mon:routes` collection.
The ordering of existing items in a `mon:routes` collection **SHOULD NOT** change over time.

All of the objects in the `as:OrderedCollection`s described above **SHOULD** be located on the same server as the given `mon:Region`.
The `mon:Species` and `mon:Route`s discoverable through these collections **SHOULD** reference this `mon:Region` in their `as:attributedTo`.

###  5.4 The `mon:Species` Object

+ URI: `https://www.monstr.pub/ns/monstrpub#Species`
+ Extends:
    + `rp:Qualified` | `rp:Quantified` | `rp:Role`
+ Properties:
    + `mon:captureRate`
    + Inherits all properties from `rp:Qualified`, `rp:Quantified`, and `rp:Role`

Describes a variety, or *species*, of mon.

An `as:name` **SHOULD** be provided for all `mon:Species`.
A tagline, or short description, for the `mon:Species` **MAY** be provided via the `as:summary` property.
A longer description of the `mon:Species` **MAY** be provided via the `as:content` property.

The `mon:Region` of a `mon:Species` **MAY** be provided, or linked to, using the `as:attributedTo` property.
Each `mon:Species` **MUST NOT** have more than one associated `mon:Region` property.
Upon encountering a `mon:Species` with multiple associated `mon:Region`s, processors **MUST** ignore *all* of them, and treat the `mon:Species` as though no `mon:Region` was specified.

If `mon:Mon` of a particular `mon:Species` are known to exist on a particular `mon:Route`, that `mon:Route` may be provided, or linked to, using the `as:location` property.

Most of the properties on the `mon:Species` object are not directly inherited by associated `mon:Mon`, but **MAY** be used when generating associated `Mon` values, in an implementation-defined manner.

##  6. Properties  ##

###  6.1 The `mon:captureRate` Property

+ URI: `https://www.monstr.pub/ns/monstrpub#captureRate`
+ Domain:
    + `mon:Mon` | `mon:Species`
+ Range: `xsd:float` [>= 0.0f]
+ Functional: True

Provides an estimation of the base likelihood of capturing a `mon:Mon` under ideal conditions, as a float from 0 (impossible) to 1 (guaranteed).
Note that this is merely an estimate, and servers **MAY** take a number of factors into account when determining the success of a capture.

An object **MUST NOT** have more than one value for its `mon:captureRate` property.
Upon encountering an object with more than one `mon:captureRate` value, processors **MUST** ignore all but the smallest one.

###  6.2 The `mon:dex` Property

+ URI: `https://www.monstr.pub/ns/monstrpub#dex`
+ Domain:
    + `mon:Region` | `mon:Route`
+ Range:
    + `as:Collection` | `as:OrderedCollection`
+ Functional: True

Provides an `as:OrderedCollection` or `as:Collection` of `mon:Species` which might be found at a given location.
For `mon:Region`s, this **MUST** be an `as:OrderedCollection`.

An object **MUST NOT** have more than one value for its `mon:dex` property.
Upon encountering an object with more than one `mon:dex` value, processors **MUST** ignore all values; that is, treat the object as if *no* `mon:dex` property had been defined.

###  6.3 The `mon:mon` Property

+ URI: `https://www.monstr.pub/ns/monstrpub#mon`
+ Domain: `mon:Trainer`
+ Range: `as:Link`
+ Functional: True

References an `as:OrderedCollection` or `as:Collection` of the `mon:Mon` for which a `mon:Trainer` is an owner.
All of the `mon:Mon` in the referenced collection **MUST** be owned by that `mon:Trainer`.

The ordering of items within the `as:OrderedCollection` **MAY** change over time.
`as:Tombstone`s **SHOULD NOT** be used to mark removed items.

An object **MUST NOT** have more than one value for its `mon:mon` property.
Upon encountering an object with more than one `mon:mon` value, processors **MUST** ignore all values; that is, treat the object as if *no* `mon:mon` property had been defined.

###  6.4 The `mon:routes` Property

+ URI: `https://www.monstr.pub/ns/monstrpub#routes`
+ Domain:
    + `mon:Region` | `mon:Route`
+ Range:
    + `as:Collection` | `as:OrderedCollection`
+ Functional: True

Provides an `as:OrderedCollection` or `as:Collection` of `mon:Route`s which might be accessed from a given location.
For `mon:Region`s, this **MUST** be an `as:OrderedCollection`.

An object **MUST NOT** have more than one value for its `mon:routes` property.
Upon encountering an object with more than one `mon:routes` value, processors **MUST** ignore all values; that is, treat the object as if *no* `mon:routes` property had been defined.

###  6.5 The `mon:supports` Property

+ URI: `https://www.monstr.pub/ns/monstrpub#routes`
+ Domain: `mon:Region`
+ Range: `xsd:anyURI`

Provides URIs which represent supported extensions to MonStrPub.

##  7. Extension  ##

The types above may be extended by other specifications.
However, any objects whose type extends one of the above types **MUST** also declare the base type, as listed above, to ensure cross-compatibility with other servers.
Similarly, if a server uses types from other vocabularies which overlap with one of the above types, the MonStrPub type **MUST** also be specified.

##  8. Changelog  ##

 >  This section is non-normative.

#####  2018-05-08.

 +  `mon:Region`s are now always assigned via the `as:location` property.

 +  Added the `mon:supports` property to `mon:Region`s for determining supported extensions.

 +  Added a section on indicating support for this specification.

 +  General edits.

#####  2018-05-05.

 +  Added the `mon:captureRate` property to `mon:Species` and `mon:Mon`.

 +  Added `mon:Item` as a `rp:Class` extension, with `rp:Instance`s of type `mon:ItemSlot`.

 +  Clarified that `mon:Trainer`s can also hold items.

 +  Allowed multiple `mon:Item`s to be assigned to the `as:generator` property of `mon:Mon`.

 +  Added a section on extensibility.

#####  2018-05-01.

 +  Renamed ActivityMon to MonStrPub with new URLs.

#####  2018-04-21.

 +  Split off an extensive number of objects and properties to create the [Roleplaying Vocabulary][].
    ☞ [Issue #11](https://github.com/kibimon/activitymon/issues/11)

     +  Removed said types/properties from this specification.

     +  Rewrote remaining sections to make use of this new vocabulary.

 +  Removed image directionality URIs as they belong in a different spec.

 +  Fixed some typos and mistakes in the previous version.

#####  2018-04-20.

 +  Split the [ActivityMon][MonStrPub] specification into two components: [Mon Vocabulary][] and [ActivityMon Core][MonStrPub Core].
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


[Activity Vocabulary]:    <https://www.w3.org/TR/activitystreams-vocabulary/>   "Activity Vocabulary"
[ActivityStreams 2.0]:    <https://www.w3.org/TR/activitystreams-core/>         "Activity Streams 2.0"
[Mon Vocabulary]:         <https://www.monstr.pub/spec/mon-vocabulary/>         "Mon Vocabulary"
[MonStrPub]:              <https://www.monstr.pub/spec/monstrpub-overview/>     "MonStrPub"
[MonStrPub Core]:         <https://www.monstr.pub/spec/monstrpub-core/>         "MonStrPub Core"
[RFC2119]:                <https://tools.ietf.org/html/rfc2119>                 "Key words for use in RFCs to Indicate Requirement Levels"
[Roleplaying Vocabulary]: <https://www.monstr.pub/spec/roleplaying-vocabulary/> "Roleplaying Vocabulary"
