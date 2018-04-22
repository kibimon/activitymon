#  ActivityMon
Draft Specification

 - - -

##  Abstract  ##

ActivityMon is an extension to [ActivityPub][] for federated interactions between trainers and mon.
This document describes the various specifications which make up ActivityMon.

 - - -

##  1. Introduction  ##

*Mon* are imaginary creatures which can be collected and interacted with by *trainers*.
ActivityMon is a specification for federated interactions between trainers and mon.

This document outlines the different components of the specification, and describes which components are **REQUIRED**.

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **NOT RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in [RFC2119][].

##  2. Required Specifications  ##

Conforming ActivityMon implementations **MUST** meet the appropriate conformance requirements as outlined in the following specifications:

 +  **[Roleplaying Vocabulary][]**:
    Extends the [Activity Vocabulary][] with new types and properties for describing various roleplaying ideas.
    Defines most of the base object types and properties used by ActivityMon.

 +  **[Mon Vocabulary][]**:
    Extends the [Roleplaying Vocabulary][] with actions and types for finding, capturing, and interacting with mon.
    Defines the core objects of ActivityMon and their basic relationships.

 +  **[ActivityMon Core][]**:
    Extends [ActivityPub][] to support the [Mon Vocabulary][] by defining special processing rules for certain object types.
    Outlines basic server treatment and handling of trainers and mon.

##  3. Optional Specifications  ##

The following specifications are **OPTIONAL** for conforming ActivityMon support:

 +  **[ActivityMon Levels][]**:
    Describes conformance requirements for representing mon that can gain experience and change levels.

##  4. Changelog  ##

 >  This section is non-normative.

#####  2018-04-21.

 +  Added the [Roleplaying Vocabulary][] as a required specification.
    ☞ [Issue #11](https://github.com/kibimon/activitymon/issues/11)

 +  Added [ActivityMon Levels][] as an optional specification.

#####  2018-04-20.

 +  Split the [ActivityMon][] specification into two components: [Mon Vocabulary][] and [ActivityMon Core][].
    ☞ [Issue #7](https://github.com/kibimon/activitymon/issues/7)


[Activity Vocabulary]:    <https://www.w3.org/TR/activitystreams-vocabulary/> "Activity Vocabulary"
[ActivityMon]:            <https://kibimon.github.io/activitymon/>            "ActivityMon"
[ActivityMon Core]:       <https://kibimon.github.io/activitymon/core/>       "ActivityMon Core"
[ActivityMon Levels]:     <https://kibimon.github.io/activitymon/levels/>     "ActivityMon Levels"
[ActivityPub]:            <https://www.w3.org/TR/activitypub/>                "ActivityPub"
[Mon Vocabulary]:         <https://kibimon.github.io/activitymon/vocabulary/> "Mon Vocabulary"
[RFC2119]:                <https://tools.ietf.org/html/rfc2119>               "Key words for use in RFCs to Indicate Requirement Levels"
[Roleplaying Vocabulary]: <https://kibimon.github.io/roleplaying/vocabulary/> "Roleplaying Vocabulary"
