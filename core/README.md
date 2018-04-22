#  ActivityMon Core
Draft Specification

 - - -

##  Abstract  ##

This specification describes the core requirements for the [ActivityMon][] extension to [ActivityPub][].
In particular, it deliniates the basic processing requirements for [Mon Vocabulary][] objects sent across the [ActivityPub][] protocol.

 - - -

##  1. Introduction  ##

The [Mon Vocabulary][] specifies a vocabulary for describing *mon*—fictitious, collectable entities that can perform actions, and outline basic processing requirements in an [ActivityStreams 2.0][] context.
This specification extends that work into the [ActivityPub][] protocol, helping to make federated mon collection into a reality.

###  1.1 Relationship to Other Specifications

This document is part of the [ActivityMon][] set of specifications.
Any process claiming to implement ActivityMon must conform to this specification.

This document uses the vocabulary defined in the [Mon Vocabulary][] specification.
This vocabulary itself depends on that defined in the [Roleplaying Vocabulary][] specification.

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
Every conforming ActivityMon implementation **MUST** also be a conforming ActivityPub implementation.

Implementations **MAY** deviate from the exact steps of algorithms defined in this specification, so long as the implemented algorithm produces the same result.

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **NOT RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in [RFC2119][].

##  3. Actors and Objects  ##

ActivityMon servers are **REQUIRED** to recognize objects of type `mon:Mon` and `mon:Trainer` as actors.
ActivityMon servers **MAY** treat `as:Person` objects as though a type of `mon:Trainer` were specified if no other Mon Actor Type is present.
ActivityMon servers **SHOULD** ignore any unrecognized types on objects for whom a Mon Actor Type is specified.

`mon:Trainer` Actor objects **SHOULD** have a `mon:mon` property.
This is link to a list of all of the `mon:Mon` which an actor has successfully `mon:Capture`d, added as a side effect.
The `mon:mon` collection **MUST** be either an `mon:OrderedCollection` or a `mon:Collection` and **MAY** be filtered on privileges of an authenticated user or as appropriate when no authentication is given.

 >  **Example:**
 >  A server with a concept of a "party" might hide all non-party `mon:Mon` from a `mon:mon` collection for non-authenticated users.

ActivityMon servers **MUST NOT** include `mon:Mon` that they do not control in the collection referenced by the `mon:mon` property of a `mon:Trainer` that they do.

###  3.1 Cloning Objects

This section sets forth processing rules for cloning certain types of ActivityMon object.
When cloning objects, implementations **MAY** assign or copy over additional properties or values, so long as the resulting object still conforms to the [Mon Vocabulary][] specification.

####  3.1.1 Cloning ordinary objects

To clone an ordinary object, a server **SHOULD** take the following steps:

1.  Create a new object whose `@type` contains all recognized, valid types on the original object.
    Servers **MAY** additionally copy over types which they do not recognize.

2.  Generate a new `@id` and assign it to the new object, if necessary and at the server's discretion.
    Note that [ActivityPub][] requires all objects to have non-null `@id`s if they are not anonymous or transient.

3.  For every recognized, valid value of a property on the original object other than `@type` and `@id`, complete the following steps:

    1.  If the property is `rp:class`, attempt to resolve the link.
        If the link resolves to a valid object of a recognized type which is, or extends, `rp:Class`, run the steps for cloning a `rp:Class` on the object as described below, and assign a link to the resulting object to the same property on the new object.
        Otherwise, if the server has previously cloned a `rp:Class` for this link, use the previous result, if valid and possible.
        Otherwise, do nothing.

    2.  Otherwise, if the value is an object, run the steps for cloning an ordinary object and assign the result to the same property on the new object.

    3.  Otherwise, assign the value to the same property on the new object.

    Servers **MAY** copy over additional properties which they do not recognize.

####  3.1.2 Cloning `rp:Class`es

To clone a `rp:Class`, a server **SHOULD** take the following steps:

1.  If the original object has a `as:source` property whose value has a valid `@id`, attempt to resolve the `@id`.
    If the `@id` resolves to a valid object of a recognized type which is, or extends, `rp:Class`, abort and re-run these steps using this resolved object instead.

2.  Run the steps for cloning an ordinary object on the original object, with the following caviats:

    1.  If the server has cloned a class with this `@id` before, then the new clone **SHOULD** replace the old one and share the same `@id`.

    2.  The resulting object **MUST** have a valid, non-null `@id`.

3.  Assign the original object to the `as:source` property of the resulting object.

 >  **Note:**
    The following types all extend `rp:Class`:

    + `mon:Species`
    + `rp:Attribute`
    + `rp:Role`
    + `rp:Technique`

####  3.1.3  Cloning `mon:Mon` objects

The steps for cloning a `mon:Mon` object are the same as the steps for cloning an ordinary object, with the caviat that all Actor-specific properties or collections, or links thereof, **MUST** be generated anew (and **MUST NOT** merely be copied over), and the resulting object **MUST** have a valid, non-null `@id`.

##  4. Client to Server Interactions  ##

The credentials used to authenticate a request to a `mon:Trainer` **SHOULD** also authenticate requests to any `mon:Mon` in the `mon:mon` collection of that `mon:Trainer`.
(In other words, a `mon:Trainer` **SHOULD** be able to make requests on behalf of their `mon:Mon`).

However, servers **MAY** restrict `mon:Mon` authentication to only allow certain activities.

>  **Example:** A server may allow users to generate `rp:Use` activities on behalf of their mon, but disallow them from `as:Create`ing posts.

###  4.1 The `as:Arrive` Activity

The `as:Arrive` activity **MAY** be used to signal arrival at a `mon:Route` via the `as:location` property.

Upon receiving an `as:Arrive` activity in an **inbox** which includes or points to a `mon:Route` in its `as:location` property, servers **SHOULD** include a reference to this `mon:Route` in the `as:cc` or `as:bcc` property of the `as:Arrive`, if one is not already present, prior to delivery.

###  4.2 The `as:Delete` Activity

Servers **SHOULD** replace deleted `rp:Attribute`s, `rp:Class`es, `mon:Region`s, `mon:Route`s, `mon:Species`, and `rp:Technique`s with `as:Tombstone`s.
Servers **SHOULD NOT** replace deleted `mon:Item`s with `as:Tombstone`s.

###  4.3 The `as:Follow` Activity

The `as:Follow` activity, in addition to its semantics under [ActivityPub][], is used to travel to a `mon:Route`.
`mon:Route`s **MUST NOT** be the `as:actor` of an `as:Follow` whose `as:object` is also a `mon:Route`, and servers **MUST NOT** dispatch any such activities.

If the `as:object` of this activity is a `mon:Route`, then after receiving this in an **outbox**, the server **SHOULD** do the following:

1.  Remove any `as:Route`s, or links thereof, from the `as:location` property of the `as:object`.
2.  If there are any `as:Route`s in the `as:following` collection of the `as:actor`, generate and dispatch `as:Undo` activities to undo these `as:Follow`s, handling them exactly as if they had been delivered to the **outbox** by a client.

The above steps **MUST NOT** be undone in the case of an `as:Undo`.
Servers **MAY** delay these steps until after an `as:Accept` activity is subsequently received with this `as:Follow` activity as its `as:object`.

###  4.4 The `as:Undo` Activity

The `mon:Capture`, `mon:Search`, and `rp:Use` activities cannot be undone after an `as:Accept` activity has been received, and servers **MUST NOT** allow `as:Undo` activities for such objects.
However, `as:Undo`s **MAY** be generated for these activities while they are still pending, to cancel the attempt.

###  4.5 The `mon:Capture` Activity

The `mon:Capture` activity makes an attempt to capture a mon.

Upon receiving a `mon:Capture` activity in an **outbox**, servers **SHOULD**, at minimum, decrement the value of the `rp:health` property on any `mon:Item` or `mon:Learned` objects in the `as:instrument`, if present, valid, and nonzero.
Servers **MAY** wait to apply these effects until after an `as:Accept` activity is subsequently received with this `mon:Capture` activity as its `as:object`.

###  4.6 The `mon:Search` Activity

The `mon:Search` activity makes an attempt to capture a mon.

Upon receiving a `mon:Search` activity in an **outbox**, servers **SHOULD**, at minimum, decrement the value of the `rp:health` property on any `mon:Item` or `mon:Learned` objects in the `as:instrument`, if present, valid, and nonzero.
Servers **MAY** wait to apply these effects until after an `as:Accept` activity is subsequently received with this `mon:Search` activity as its `as:object`.

##  4. Server to Server Interactions  ##

As with Client to Server interactions, ActivityMon both specifies additional Activities with special behaviour under client to server interactions, and specifies additional restrictions and processing behaviours for those defined by [ActivityPub][].

###  4.1 The `as:Arrive` Activity

The `as:Arrive` activity is used to announce the arrival of its `as:actor` at an `as:location`.
In ActivityMon, this **MAY** be used to signal successful arrival at a `mon:Route`.

Upon receiving an `as:Arrive` activity in an **inbox**, servers **SHOULD** update its copy of the object with the same `@id` as specified by the activity's `as:actor`, to ensure that the location change is recorded.

If the `mon:Route` specified by the `as:location` property is controlled by the server and included or linked in the `as:Arrive` by the `as:to` or `as:cc` properties, servers **MAY** generate an `as:Announce` activity with the specified `mon:Route` as its `as:actor` and the `as:Arrive` activity as its `as:object`.
Servers **SHOULD NOT** generate `as:Announce` activities for `as:Arrive`s which do not specify the `mon:Route` in the (public) `as:to` or `as:cc` properties.

###  4.2 The `as:Follow` Activity

Upon receiving an `as:Follow` activity in an **inbox**, servers **MUST** generate and deliver an `as:Reject` if both the `as:actor` and `as:object` are of type `mon:Route`.

 >  **Note:** Like with any follow, servers can generate an `as:Reject` for `as:Follow` activities whose `as:object` are of type `mon:Route` for other reasons as well.
 >  For example, a server might only `as:Accept` attempts to access a `mon:Route` if the `as:actor` is a `mon:Trainer` with a certain number of gym badges.

In the case of receiving an `as:Accept` referencing this `as:Follow` activity as its object, the server **SHOULD**, as a side effect, add the `as:object` to the `as:location` property of the `as:actor` if it is a `mon:Route` or a link thereof.

###  4.3 The `as:Undo` Activity

After receiving an `as:Undo` activity in an **inbox**, servers **SHOULD NOT** generate an `as:Accept` or `as:Reject` for the `as:object`.

###  4.4 The `mon:Capture` Activity

A `mon:Capture` activity attempts to capture a mon.

Upon receiving a `mon:Capture` activity in an **inbox**, a server should generate either an `as:Accept` or `as:Reject` activity with the `mon:Capture` as the `as:object` and deliver it to the `as:actor` of the `as:Capture`.
A server **MUST** send an `as:Reject` for any `mon:Capture` whose `as:object` is a `mon:Mon` that is already associated with an owner.

In the case of receiving an `as:Accept` referencing this `mon:Capture` as the `as:object`, for which the `as:actor` is a `mon:Trainer` and the `as:object` is a `mon:Mon`,  the server **SHOULD** perform the following steps:

1.  Clone the `as:object` of the `as:actor` according to the steps for cloning `mon:Mon` outlined above.

2.  Add the `as:actor` of the `mon:Capture` to the `as:attributedTo` and `as:generator` properties of the resulting `mon:Mon`.
    Servers **MAY** add additional values to the `as:generator` property as well.

3.  Add the resulting `mon:Mon` to the `mon:mon` collection of the `as:actor` of the `mon:Capture`.

###  4.4 The `mon:Search` Activity

The `mon:Search` activity makes an attempt to search a `mon:Route`.

Upon receiving a `mon:Search` activity in an **inbox**, a server **SHOULD** generate either an `as:Accept` or `as:Reject` activity with the `mon:Search` as the `as:object` and deliver it to the `as:actor` of the `as:Search`.
An `as:Accept` **SHOULD** include an `as:result` property, whose value is the result of the search.

###  4.5 The `rp:Use` Activity

The `rp:Use` activity makes an attempt to use an object.

If a server "understands" the intended effects of a `rp:Use` activity received in an **inbox**, it **SHOULD** apply those effects and then generate an `as:Accept` activity with the `rp:Use` as the `as:object` and deliver it to the `as:actor` of the `rp:Use`.
Otherwise, the server **SHOULD** generate a `as:Reject` activity with the `rp:Use` as the `as:object` and deliver it to the `as:actor` of the `rp:Use`.

>  **Note:** A server might fail to understand the intended effects of a `rp:Use` activity if the `rp:Use` references an `mon:Item` that the server does not understand, or a `rp:Technique` has an unknown `rp:effect`.

In the case of receiving an `as:Accept` referencing this `rp:Use` as the `as:object`, the server **SHOULD** apply any effects resulting from successful use.
In particular, the server **SHOULD**, at minimum, decrement the value of the `rp:health` property on any `mon:Item` or `rp:Learned` objects in the `as:object`, if present, valid, and nonzero.
In the case of an `as:Reject`, the server **SHOULD NOT** apply such effects.

##  5. Changelog  ##

 >  This section is non-normative.

#####  2018-04-21.

 +  Updated to utilize the new [Roleplaying Vocabulary][].

 +  Greatly simplified the steps for cloning objects and recommended against needless object proliferation.
    ☞ [Issue #9](https://github.com/kibimon/activitymon/issues/9)

 +  Specified behaviours for signalling and announcing arrivals.
    ☞ [Issue #6](https://github.com/kibimon/activitymon/issues/6)

 +  Fixed typos and mistakes in the previous version.

#####  2018-04-20.

 +  Split the [ActivityMon][] specification into two components: [Mon Vocabulary][] and [ActivityMon Core][].
    ☞ [Issue #7](https://github.com/kibimon/activitymon/issues/7)

 +  Significant rewriting of sections for clarity and precision; fixed typos.

     +  Clarified the relationship between this and other specifications.

     +  Clarified notational conventions regarding type URIs.

 +  The `mon:Trainer` Mon Actor Type is now used instead of just `as:Person` for trainers.
    ☞ [Issue #3](https://github.com/kibimon/activitymon/issues/3)

 +  Made `mon:Route` an Actor type.
    ☞ [Issue #4](https://github.com/kibimon/activitymon/issues/4)

     +  Defined special behaviours for travelling to `mon:Route`s via `as:Follow`ing.

 +  Added a `mon:Search` Activity, intended for searching `mon:Route`s.
    ☞ [Issue #5](https://github.com/kibimon/activitymon/issues/5)

     +  Defined processing behaviours for `mon:Search`es with respect to `mon:Route`s.

 +  Explicitly recommended that the health of items and moves be decremented when involved in various activities.

 +  Specified behaviour for `as:Arrive`.
    ☞ [Issue #6](https://github.com/kibimon/activitymon/issues/6)

 +  Created formal steps for cloning ActivityMon objects.
    ☞ [Issue #2](https://github.com/kibimon/activitymon/issues/2)

 +  Clarified `as:Tombstone` behaviour.

#####  2018-04-18.

 +  Initial specification.


[Activity Vocabulary]:    <https://www.w3.org/TR/activitystreams-vocabulary/> "Activity Vocabulary"
[ActivityMon]:            <https://kibimon.github.io/activitymon/>            "ActivityMon"
[ActivityMon Core]:       <https://kibimon.github.io/activitymon/core/>       "ActivityMon Core"
[ActivityPub]:            <https://www.w3.org/TR/activitypub/>                "ActivityPub"
[ActivityStreams 2.0]:    <https://www.w3.org/TR/activitystreams-core/>       "Activity Streams 2.0"
[Mon Vocabulary]:         <https://kibimon.github.io/activitymon/vocabulary/> "Mon Vocabulary"
[RFC2119]:                <https://tools.ietf.org/html/rfc2119>               "Key words for use in RFCs to Indicate Requirement Levels"
[Roleplaying Vocabulary]: <https://kibimon.github.io/roleplaying/vocabulary/> "Roleplaying Vocabulary"
