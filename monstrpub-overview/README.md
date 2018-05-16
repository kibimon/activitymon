#  MonStrPub
Draft Specification

 - - -

##  Abstract  {#abstract}

MonStrPub (<i>Mon Streams Publishing</i>) is an extension to [ActivityPub] for federated interactions between trainers and mon.
This document describes the various specifications which make up MonStrPub.

<nav id="toc" markdown="block">
##  Contents

+ Table of Contents
{:toc id="toc.contents"}
</nav>

 - - -

##  1. Introduction  {#introduction}

<dfn>Mon</dfn> are imaginary creatures which can be collected and interacted with by <dfn>trainers</dfn>.
MonStrPub is a specification for federated interactions between trainers and mon.

This document outlines the different components of the specification, and describes which components are **REQUIRED**.

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **NOT RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in [RFC2119].

##  2. Required Specifications  {#required}

Conforming MonStrPub implementations **MUST** meet the appropriate conformance requirements as outlined in the following specifications:

[RPPub]

 :  The [Roleplaying Vocabulary] extends the [Activity Vocabulary] with new types and properties for describing various roleplaying ideas.
    Other components of this specification **MAY** be implemented to provide additional functionality.

[Mon Vocabulary]

 :  Extends the [Roleplaying Vocabulary] with actions and types for finding, capturing, and interacting with mon.
    Defines the core objects of MonStrPub and their basic relationships.

[MonStrPub Core]

 :  Extends [ActivityPub] to support the [Mon Vocabulary] by defining special processing rules for certain object types.
    Outlines basic server treatment and handling of trainers and mon.

##  3. Optional Specifications  {#optional}

The following specifications are **OPTIONAL** for conforming MonStrPub support:

[MonStrPub Capsule Captures]

 :  Describes conformance requirements for handling [`mon:Capture`] actions on a probabilistic basis, making use of a new item type, [`mon:Capsule`].

[MonStrPub Levels]

 :  Describes conformance requirements for representing mon that can gain experience and change levels.

[MonStrPub Stats]

 :  Describes conformance requirements for representing mon that have stats.
    Defines a common initial set of mon attributes and attribute natures.

##  4. Changelog  {#changelog}

<div role="note" markdown="block">
This section is non-normative.
</div>

#####  2018-05-16.
{: id="changelog.2018-05-16"}

 +  Updated layout and formatting.

^

#####  2018-05-05.
{: id="changelog.2018-05-05"}

 +  Added [MonStrPub Capsule Captures] as an optional specification.

^

#####  2018-05-01.
{: id="changelog.2018-05-01"}

 +  Renamed ActivityMon to MonStrPub with new URLs.

 +  Added [MonStrPub Stats] as an optional specification.

 +  Changed the [Roleplaying Vocabulary] requirement to just include all of [RPPub].

#####  2018-04-21.
{: id="changelog.2018-04-21"}

 +  Added the [Roleplaying Vocabulary] as a required specification.

    <aside markdown="block">
    [Issue #11](https://github.com/kibimon/activitymon/issues/11)
    : <cite>Roleplaying Vocabulary</cite>
    </aside>

 +  Added [ActivityMon Levels][MonStrPub Levels] as an optional specification.

#####  2018-04-20.
{: id="changelog.2018-04-20"}

 +  Split the [ActivityMon][MonStrPub] specification into two components: [Mon Vocabulary] and [ActivityMon Core][MonStrPub Core].

    <aside markdown="block">
    [Issue #7](https://github.com/kibimon/activitymon/issues/7)
    : <cite>Split vocabulary from specification and reorganize</cite>
    </aside>

^


{% include references.md %}
