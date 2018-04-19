#  ActivityMon  #
Draft Specification

 - - -

##  Abstract  ##

This specification describes the ActivityMon vocabulary, alongside recommendations for its usage.
It is intended to be used in the context of the ActivityPub protocol to create federated servers of Persons and Mons.

 - - -

##  1. Introduction  ##

>  This section is non-normative.

A "Mon" is an imaginary creature or object which may be collected, traded, and interacted with. Mons frequently have individual properties, known as "stats", and are capable of particular actions, known as "moves". The goal of this specification is to provide a mechanism for describing and interacting with mon in a decentralized manner, by building on top of the existing ActivityPub protocol for federated social interactions.

>  **Note:** Although this specification details an extensive model for representing mon and their activities in a federated manner, it leaves a number of things *unspecified*. The following are some examples of things which this specification does *not* describe:
>
>  - The circumstances under which unowned mon are created or announce themselves to users
>  - The applicable context of moves; ie, when a move should be accepted or rejected by a server
>  - When a server should accept or reject the capturing of a mon
>  - The specific abilities, formes, types, moves, or stats of mon
>  - The mon themselves

###  1.1 Structure of This Specification

>  This section is non-normative.

Section 2 puts forward the conformance requirements for this specification.

Section 3 informatively lists a subset of the URIs defined by this specification, for easy reference.

Sections 4–7 define the ActivityMon vocabulary, including activities, actors, objects, and properties.

Section 8 gives the requirements for interacting with this vocabulary using the ActivityPub protocol.

##  2. Conformance  ##

All sections explicitly marked as non-normative, as well as any diagrams, exmaples, or notes in this specification, are non-normative.
Everything else in this specification is normative.

ActivityMon is an extension of [ActivityPub](https://www.w3.org/TR/activitypub/) and inherits its conformance requirements, including its conformance classes. Every conforming ActivityMon implementation *MUST* also be a conforming ActivityPub implementation.

The key words *MUST*, *MUST NOT*, *REQUIRED*, *SHOULD*, *SHOULD NOT*, *RECOMMENDED*, and *MAY* are to be interpreted as described in [RFC2119](https://tools.ietf.org/html/rfc2119).

##  3. Important URIs  ##

>  This section is non-normative.

The following URIs are given special significance in ActivityMon:

- `tag:marrus.xyz,2018:activitymon::`: The base URI for the ActivityMon vocabulary
- `tag:marrus.xyz,2018:activitymon::FORWARD`
- `tag:marrus.xyz,2018:activitymon::BACKWARD`
- `tag:marrus.xyz,2018:activitymon::LEFT`
- `tag:marrus.xyz,2018:activitymon::RIGHT`: The four cardinal directions; used for specifying directional images

>  As this is still a draft specification, the above URIs may change at some point in the future.

##  4. Activities  ##

In addition to the activities recognized by ActivityPub, ActivityMon implementations are *REQUIRED* to recognize the following:

###  4.1 The `as:Announce` Activity

- URI: `https://www.w3.org/ns/activitystreams#Announce`
- Extends: `as:Activity`
- Properties: Inherits all properties from `as:Activity`

Used by ActivityPub to share objects with others.

In ActivityMon, the `as:Announce` activity has another, specialized usage, which is to make an `as:Person` aware of an unowned `Mon`. (This `Mon` can be said to be "annoucing itself".)

For this usage, both the `as:actor` and `as:object` properties *MUST* be the `Mon` in question, which *MUST NOT* have an owner, and the `as:target` *MUST* be an `as:Person`.

###  4.2 The `Use` Activity

- URI: `tag:marrus.xyz,2018:activitymon::Use`
- Extends: `as:Offer`
- Properties: Inherits all properties from `as:Offer`

A specialization of `as:Offer` in which the `as:actor` is attempting to use the `as:object` on the `as:target`.

###  4.3 The `Capture` Activity

- URI: `tag:marrus.xyz,2018:activitymon::Capture`
- Extends: `as:Offer`
- Properties: Inherits all properties from `as:Offer`

A specialization of `as:Offer` in which the `as:actor`, which *MUST* be an `as:Person`, is attempting to own the `as:object`, which *MUST* be a `Mon`.

An `Item` *MAY* be associated with the `Capture` via the `as:instrument` property.

##  5. Actors  ##

ActivityMon implementations are *REQUIRED* to recognize two different types of actor: `as:Person` and `Mon`.

###  5.1 The `as:Person` Actor

- URI: `https://www.w3.org/ns/activitystreams#Person`
- Extends: `as:Object`
- Properties:
    - `mon`
    - Inherits all properties from `as:Object`

Describes an owner, or "trainer", of mon. Only `as:Person`s are able to own mon.

###  5.2 The `Mon` Actor

- URI: `tag:marrus.xyz,2018:activitymon::Mon`
- Extends: `Species`
- Properties:
    - `effect` | `exp` | `health` | `level` | `moves` | `statExp` | `statInd`
    - Inherits all properties from `Species`

Describes a mon.

The `Species` of a `Mon` *MUST* be provided, or linked to, using the `as:context` property. Each `Mon` *MUST* have no more than one associated `Species` property; if multiple are provided, implementations *MUST* ignore all but the first.

Implementations *MUST* ignore all `Mon` without an associated `Species`. To ensure that the `Species` is always able to be dereferenced, a linked `Species` *SHOULD* be stored on the same server as the associated `Mon`.

The values of the following properties, if not specified on the `Mon`, *SHOULD* be inherited from the associated `Species`, if present: `as:icon`, `as:image`, `as:name`, `affinity`, `growth`.

The value of the `moveset` property *SHOULD* be combined with that of the associated `Species` to produce the computed moveset of the `Mon`.

A `Mon` has an owner if there is an `as:Person` object provided, or linked to, via the `as:attributedTo` property. The first `as:Person` object associated with a `Mon` in this manner *MUST* be interpreted as its owner, and all other `as:Person` objects assigned to this property *MUST* be ignored.

Likewise, the owner of the first owned version of a `Mon` *SHOULD* be provided, or linked to, using the `as:generator` property. The first `as:Person` object associated with a `Mon` in this manner *MUST* be interpreted as its original owner, and all other `as:Person` objects assigned to this property *MUST* be ignored.

`Mon` who are owned by an `as:Person` *MUST* exist on the same server as that `as:Person`. The owner of a `Mon` *MUST NOT* change.

Other objects *MAY* be associated with a `Mon` via the `as:attributedTo` or `as:generator` properties; for example, the current or originary location of the `Mon` *MAY* be provided via a `Route`.

`Item`s *MAY* be associated with a `Mon` (ie, "held") via the `as:attachment` property.

##  6. Objects  ##

In addition to the objects required by ActivityPub, an ActivityMon implementation must recognize the following:

###  6.1 The `GrowthPart` Object

- URI: `tag:marrus.xyz,2018:activitymon::Species`
- Extends: `as:Object`
- Properties:
    - `maxLevel` | `minLevel` | `n0` | `n1` | `n2` | `n3`
    - Inherits all properties from `as:Object`

Describes a cubic function, which calculates how many `exp` a `Mon` needs before reaching its next `level`. The `GrowthPart` functions provided by the `growth` property *MUST NOT* overlap; on encountering an overlap, implementations *SHOULD* take the average result of all applicable functions.

If no applicable `GrowthPart` object is provided for the current `level` of a `Mon`, a simple cubic function (with coefficient 1) should be used instead.

###  6.2 The `Item` Object

- URI: `tag:marrus.xyz,2018:activitymon::Item`
- Extends: `as:Object`
- Properties:
    - `health` | `maxHealth`
    - Inherits all properties from `as:Object`

Describes an item of some sort.

An `as:name` *SHOULD* be provided for all `Item`s.

The types or effects of `Item`s are not specified by this specification. However, it is *RECOMMENDED* that `as:context` be used as the means of identifying items.

###  6.3 The `Move` Object

- URI: `tag:marrus.xyz,2018:activitymon::Move`
- Extends: `as:Object`
- Properties:
    - `health` | `level`
    - Inherits all properties from `as:Object`

>  __Note:__ This object bears no relation to `as:Move`, which is an activity.

Describes a move, which is a type of action performable by a mon.

The `MoveKind` of a `Move` *MUST* be provided, or linked to, using the `as:context` property. Each `Move` *MUST* have no more than one associated `MoveKind` property; if multiple are provided, implementations *MUST* ignore all but the first.

Implementations *MUST* ignore all `Move`s without an associated `MoveKind`. To ensure that the `MoveKind` is always able to be dereferenced, a linked `MoveKind` *SHOULD* be stored on the same server as the associated `Move`.

The `health` property *MUST* be ignored for all `Move`s which are included in a `moveset`. The `level` property *MUST* be ignored for `Move`s which are included in a `moves` or which are included in an `as:object` of an `as:Activity`.

###  6.4 The `MoveKind` Object

- URI: `tag:marrus.xyz,2018:activitymon::MoveKind`
- Extends: `as:Object`
- Properties:
    - `affinity` | `effect` | `maxHealth` | `probability` | `power` | `usesStat`
    - Inherits all properties from `as:Object`.

Describes a variety of move.

An `as:name` *SHOULD* be provided for all `MoveKind`s.

###  6.5 The `Region` Object

- URI: `tag:marrus.xyz,2018:activitymon::Region`
- Extends: `as:Object`
- Properties:
    - `dex` | `routes`
    - Inherits all properties from `as:Object`

Describes a region. Regions are objects which *MAY* be used to discover different `Species` or `Routes` for `Mon`.

An `as:name` *SHOULD* be provided for all `Region`s.

The `dex` property *MUST* contain a link to an `as:OrderedCollection` of `Species` objects. The ordering of `Species` objects in this collection *SHOULD NOT* change over time (although new objects *MAY* be added); `as:Tombstone` objects *SHOULD* be used to mark any `Species` removed from a `dex` collection.

The `routes` property *MUST* contain a link to an `as:OrderedCollection` of `Route` objects. The ordering of `Route` objects in this collection *SHOULD NOT* change over time (although new objects *MAY* be added); `as:Tombstone` objects *SHOULD* be used to mark any `Route`s removed from a `route` collection.

Both of the `as:OrderedCollection`s described above *SHOULD* be located on the same server as the given `Region`, and so *SHOULD* all of the objects they contain.

###  6.6 The `Route` Object

- URI: `tag:marrus.xyz,2018:activitymon::Route`
- Extends: `as:Object`
- Properties:
    - Inherits all properties from `as:Object`

Describes a subregion, or "route".

An `as:name` *SHOULD* be provided for all `Route`s.

The `Region` of a `Route` *SHOULD* be provided, or linked to, using the `as:assignedTo` property. Each `Route` *MUST* have no more than one associated `Region` property; if multiple are provided, implementations *MUST* ignore all but the first.

###  6.7 The `Species` Object

- URI: `tag:marrus.xyz,2018:activitymon::Species`
- Extends: `as:Object`
- Properties:
    - `ability` | `affinity` | `forme` | `growth` | `maxHealth` | `moveset` | `nature` | `stats`
    - Inherits all properties from `as:Object`

Describes a variety of mon.

An `as:name` *SHOULD* be provided for all `Species`.

The `Region` of a `Species` *MAY* be provided, or linked to, using the `as:context` property. Each `Species` *MUST* have no more than one associated `Region` property; if multiple are provided, implementations *MUST* ignore all but the first.

The `ability`, `forme`, and `nature` properties give the anticipated *possible* values of those properties for an associated `Mon`. However, no guarantees are made regarding this, and associated `Mon` *MAY* have values not specified on their associated `Species`.

Most of the properties on the `Species` object are not directly inherited by associated `Mon`, but *MAY* be used when generating associated `Mon` values.

###  6.8 The `Stat` Object

- URI: `tag:marrus.xyz,2018:activitymon::Stat`
- Extends: `as:Object`
- Properties:
    - `level`
    - Inherits all properties from `as:Object`

Describes a stat.

An `as:name` *SHOULD* be provided for all `Stat`s.

The types or effects of `Stat`s are not specified by this specification. However, it is *RECOMMENDED* that `as:context` be used as the means of identifying stats.

The `level` property *MUST* be ignored for all `Stat`s which are included in a `usesStat`. Otherwise, the following (inclusive) ranges of values for `level` are *RECOMMENDED*:

- For `Stat`s which are included in a `statInd`: 0–15
- For `Stat`s which are included in a `statExp`: 0–255
- For all other `Stat`s: 0–1023

##  7. Properties  ##

###  7.1 The `ability` Property

- URI: `tag:marrus.xyz,2018:activitymon::ability`
- Domain: `Species`
- Range: `xsd:anyURI`

Indicates a "passive" ability for a `Mon`. No abilities are defined by this specification.

For `Species`, this property gives the anticipated *possible* values of the `ability` property for `Mon` of that species type.

###  7.2 The `affinity` Property

- URI: `tag:marrus.xyz,2018:activitymon::affinity`
- Domain: `Species` | `MoveKind`
- Range: `xsd:anyURI`

Indicates an affinity (or "type") of a `Mon` or `Move`. No affinities are defined by this specification.

###  7.3 The `dex` Property

- URI: `tag:marrus.xyz,2018:activitymon::dex`
- Domain: `Region`
- Range: `as:Link`
- Functional: True

References an `as:OrderedCollection` of `Species` which might be found in a given `Region`.

###  7.4 The `effect` Property

- URI: `tag:marrus.xyz,2018:activitymon::effect`
- Domain: `Mon` | `MoveKind`
- Range: `xsd:anyURI`

Indicates a status effect, either active on a `Mon`, or which will result from a `MoveKind`. No effects are defined by this specification.

###  7.5 The `exp` Property

- URI: `tag:marrus.xyz,2018:activitymon::exp`
- Domain: `Mon`
- Range: `xsd:nonNegativeInteger`
- Functional: True

Indicates some integral amount of experience, above and beyond the current `level` of a `Mon`, if specified.

###  7.6 The `forme` Property

- URI: `tag:marrus.xyz,2018:activitymon::forme`
- Domain: `Species`
- Range: `xsd:anyURI`

Indicates a "forme" of a `Mon`, which is a broad category used to describe variations in gender, appearance, or properties. The schema.org values `https://schema.org/Male` and `https://schema.org/Female` *SHOULD* be used for the male and female formes, where applicable. No other formes are defined by this specification.

For `Species`, this property gives the anticipated *possible* values of the `forme` property for `Mon` of that species type.

###  7.7 The `growth` Property

- URI: `tag:marrus.xyz,2018:activitymon::growth`
- Domain: `Species`
- Range: `GrowthPart`

Indicates a growth or levelling rate for a `Mon`—that is, the amount of `exp` required for a change in `level`—as a (optionally piecewise) polynomial, specified through `GrowthPart`s.

###  7.8 The `health` Property

- URI: `tag:marrus.xyz,2018:activitymon::health`
- Domain: `Item` | `Mon` | `Move`
- Range: `xsd:nonNegativeInteger`
- Functional: True

For `Mon`, indicates current vitality. For `Item`s or `Move`s, indicates the amount of times the item can be used. This property *MUST NOT* be defined for `Item`s or `Move`s which may be used an infinite number of times.

###  7.9 The `level` Property

- URI: `tag:marrus.xyz,2018:activitymon::level`
- Domain: `Mon` | `Move` | `Stat`
- Range: `xsd:nonNegativeInteger`
- Functional: True

For `Mon` or `Stat`s, indicates the current numeric rank or proficiency. For `Move`s, indicates the required numeric rank for a `Move` to be added to the `moveset` of a `Mon`. A `Move` which cannot be learned through levelling *MUST NOT* have a `level` property.

The `level` of `Mon` and `Move`s *SHOULD* be in the range 1 to 100. The `level` of `Stat`s *MAY* be higher, but *SHOULD NOT* exceed the hundreds.

###  7.10 The `maxHealth` Property

- URI: `tag:marrus.xyz,2018:activitymon::maxHealth`
- Domain: `Item` | `MoveKind` | `Species`
- Range: `xsd:nonNegativeInteger`
- Functional: True

Indicates a maximum value for the `health` property. Although this property can have any non-negative integer value, it *SHOULD NOT* exceed the hundreds.

###  7.11 The `maxLevel` Property

- URI: `tag:marrus.xyz,2018:activitymon::maxLevel`
- Domain: `GrowthPart`
- Range: `xsd:nonNegativeInteger` [<= 100]
- Functional: True

Indicates the maximum value of `level` for which the given `GrowthPart` applies.

###  7.12 The `minLevel` Property

- URI: `tag:marrus.xyz,2018:activitymon::minLevel`
- Domain: `GrowthPart`
- Range: `xsd:nonNegativeInteger` [<= 100]
- Functional: True

Indicates the minimum value of `level` for which the given `GrowthPart` applies.

###  7.13 The `mon` Property

- URI: `tag:marrus.xyz,2018:activitymon::party`
- Domain: `as:Person`
- Range: `as:Link`
- Functional: True

References an `as:OrderedCollection` or `as:Collection` of the `Mon` for which an `as:Person` is an owner.

###  7.14 The `moves` Property

- URI: `tag:marrus.xyz,2018:activitymon::moves`
- Domain: `Mon`
- Range: `Move`

Indicates the `Move`s which a `Mon` is able to perform. Implementations *MAY* set limits on the number of `Move`s which may be associated with a given `Mon`.

###  7.15 The `moveset` Property

- URI: `tag:marrus.xyz,2018:activitymon::moveset`
- Domain: `Species`
- Range: `Move`

Indicates the `Move`s which it is possible for a `Mon` to learn. For `Mon`, this property is *additive*: the computed moveset for a `Mon` *SHOULD* include both the `Move`s declared in the `Mon`s own `moveset` property and those declared in that of its associated `Species`, where applicable.

###  7.16 The `n0` Property

- URI: `tag:marrus.xyz,2018:activitymon::n0`
- Domain: `GrowthPart`
- Range: `xsd:float`
- Functional: True

The zeroth-degree coefficient of a `GrowthPart` polynomial.

###  7.17 The `n1` Property

- URI: `tag:marrus.xyz,2018:activitymon::n1`
- Domain: `GrowthPart`
- Range: `xsd:float`
- Functional: True

The first-degree coefficient of a `GrowthPart` polynomial.

###  7.18 The `n2` Property

- URI: `tag:marrus.xyz,2018:activitymon::n2`
- Domain: `GrowthPart`
- Range: `xsd:float`
- Functional: True

The second-degree coefficient of a `GrowthPart` polynomial.

###  7.19 The `n3` Property

- URI: `tag:marrus.xyz,2018:activitymon::n3`
- Domain: `GrowthPart`
- Range: `xsd:float`
- Functional: True

The third-degree coefficient of a `GrowthPart` polynomial.

###  7.20 The `nature` Property

- URI: `tag:marrus.xyz,2018:activitymon::nature`
- Domain: `Species`
- Range: `xsd:anyURI`

Indicates individual temperament of a `Mon`. No natures are defined by this specification.

For `Species`, this property gives the anticipated *possible* values of the `nature` property for `Mon` of that species type.

###  7.21 The `power` Property

- URI: `tag:marrus.xyz,2018:activitymon::power`
- Domain: `MoveKind`
- Range: `xsd:positiveInteger`
- Functional: True

Indicates the power of a `Move`; eg, its effectiveness. The range from 0 to 100 *SHOULD* be used as a guideline for "normal" levels of effectiveness.

###  7.22 The `probability` Property

- URI: `tag:marrus.xyz,2018:activitymon::probability`
- Domain: `Item` | `MoveKind`
- Range: `xsd:float` [>= 0.0f, <=1.0f]
- Functional: True

Indicates the probability that an `Item` or `Move` will have an effect.

###  7.23 The `routes` Property

- URI: `tag:marrus.xyz,2018:activitymon::routes`
- Domain: `Region`
- Range: `as:Link`
- Functional: True

References an `as:OrderedCollection` of `Route`s which might be found in a given `Region`.

###  7.24 The `stats` Property

- URI: `tag:marrus.xyz,2018:activitymon::statExp`
- Domain: `Species`
- Range: `Stat`

Indicates the levels of various stats. For `Species`, these are the base levels; for `Mon`, these are the individual levels.

###  7.25 The `statExp` Property

- URI: `tag:marrus.xyz,2018:activitymon::statExp`
- Domain: `Mon`
- Range: `Stat`

Indicates experience gained for specific stats.

###  7.26 The `statInd` Property

- URI: `tag:marrus.xyz,2018:activitymon::statInd`
- Domain: `Mon`
- Range: `Stat`

Indicates individual strengths and weaknesses for specific stats.

###  7.27 The `usesStat` Property

- URI: `tag:marrus.xyz,2018:activitymon::usesStat`
- Domain: `MoveKind`
- Range: `Stat`

Indicates that a `Move` depends upon a given `Stat`.

##  8. ActivityMon in ActivityPub  ##

###  8.1 Mon Collection

An `as:Person` actor *MAY* have a `mon` collection.
This is a list of all of the `Mon` which an actor has successfully `Capture`d, added as a side effect.
The `mon` collection *MUST* be either an `OrderedCollection` or a `Collection` and *MAY* be filtered on privileges of an authenticated user or as appropriate when no authentication is given.

>  **Example:** A server with a concept of a "party" might hide all non-party `Mon` from a `mon` collection for non-authenticated users.

###  8.2 Capture Activity

Upon receiving a `Capture` activity, a server should generate either an `as:Accept` or `as:Reject` activity with the `Capture` as the `as:object` and deliver it to the `as:actor` of the `Capture`.
A server *MUST* send a `Reject` for any `Capture` whose `as:object` either is not a `Mon`, or is a `Mon` that is already associated with an owner.
A server *MUST* also send a `Reject` for any `Capture` whose `as:actor` is not an `as:Person`.

In the case of receiving an `as:Accept` referencing this `Capture` as the `as:object`, the server *SHOULD* perform the following steps:

1.  Create a **new** `Mon` actor whose resulting graph, when dereferenced to some extent, matches that of the `as:object` of the `Capture`, discounting any ActivityPub collections.

2.  Add the `as:actor` of the `Capture` to the `as:attributedTo` and `as:generator` properties of the resulting `Mon`.

3.  Add the resulting `Mon` to the `mon` collection of the `as:actor` of the `Capture`.

###  8.3 Use Activity

If a server "understands" the intended effects of a `Use` activity received in an **inbox**, it *SHOULD* apply those effects and then generate an `as:Accept` activity with the `Use` as the `as:object` and deliver it to the `as:actor` of the `Use`.
Otherwise, the server *SHOULD* generate a `as:Reject` activity with the `Use` as the `as:object` and deliver it to the `as:actor` of the `Use`.

>  **Note:** A server might fail to understand the intended effects of a `Use` activity if the `Use` references an `Item` that the server does not understand, or a `MoveKind` has an unknown `effect`.

In the case of receiving an `as:Accept` referencing this `Use` as the `as:object`, the server *SHOULD* apply any effects resulting from successful use.
In the case of an `as:Reject`, the server *SHOULD NOT* apply such effects.

###  8.4 Authentication Considerations

The credentials used to authenticate a request to an `as:Person` *SHOULD* also authenticate requests to any `Mon` in the `mon` collection of that `as:Person`. (In other words, a user *SHOULD* be able to make requests on behalf of their `Mon`).

However, servers *MAY* restrict `Mon` authentication to only allow certain activities.

>  **Example:** A server may allow users to generate `Use` activities on behalf of their mon, but disallow them from `Create`ing posts.

###  8.5 Contextual Images

The URIs `tag:marrus.xyz,2018:activitymon::FORWARD`, `tag:marrus.xyz,2018:activitymon::BACKWARD`, `tag:marrus.xyz,2018:activitymon::LEFT`, and `tag:marrus.xyz,2018:activitymon::RIGHT`, when they appear in the `as:context` for an `as:Image`, denote forward-, backward-, leftward- and rightward-facing images, respectively. Note that a forward-facing image of an object is an image of its backside.

>  **Note:** An implementation might recognize other `as:context`s for images as well; for example, a `MoveKind` context might be used to provide the image of a mon using a move.
