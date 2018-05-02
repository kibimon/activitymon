#  RPPub
Draft Specification

 - - -

##  Abstract  ##

RPPub (RolePlaying Publishing) is an extension to [ActivityPub][] for federated roleplaying.
This document describes the various specifications which make up RPPub.

 - - -

##  1. Introduction  ##

RPPub is a specification for federated roleplaying, and conveying roleplaying information in a federated manner.

This document outlines the different components of the specification, and describes which components are **REQUIRED**.

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **NOT RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in [RFC2119][].

##  2. Required Specifications  ##

Conforming RPPub implementations **MUST** meet the appropriate conformance requirements as outlined in the following specifications:

 +  **[Roleplaying Vocabulary][]**:
    Extends the [Activity Vocabulary][] with new types and properties for describing various roleplaying ideas.
    This is the only required specification for RPPub implementations.

##  3. Optional Specifications  ##

The following specifications are **OPTIONAL** for conforming RPPub support:

 +  *To come.*

##  4. Changelog  ##

 >  This section is non-normative.

#####  2018-05-01.

 +  Initial RPPub definition.


[Activity Vocabulary]:    <https://www.w3.org/TR/activitystreams-vocabulary/>   "Activity Vocabulary"
[ActivityPub]:            <https://www.w3.org/TR/activitypub/>                  "ActivityPub"
[RFC2119]:                <https://tools.ietf.org/html/rfc2119>                 "Key words for use in RFCs to Indicate Requirement Levels"
[Roleplaying Vocabulary]: <https://www.monstr.pub/spec/roleplaying-vocabulary/> "Roleplaying Vocabulary"
