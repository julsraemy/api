---
title: "Presentation API 3.0 ALPHA DRAFT"
title_override: "IIIF Presentation API 3.0 ALPHA DRAFT"
id: presentation-api
layout: spec
cssversion: 2
tags: [specifications, presentation-api]
major: 3
minor: 0
patch: 0
pre: ALPHA
redirect_from:
  - /api/presentation/index.html
  - /api/presentation/3/index.html
---

## Status of this Document
{:.no_toc}
__This Version:__ {{ page.major }}.{{ page.minor }}.{{ page.patch }}{% if page.pre != 'final' %}-{{ page.pre }}{% endif %}

__Latest Stable Version:__ [{{ site.presentation_api.latest.major }}.{{ site.presentation_api.latest.minor }}.{{ site.presentation_api.latest.patch }}][prezi-api-stable]

__Previous Version:__ [2.1.1][prezi-api-21]

**Editors**

  * **[Michael Appleby](https://orcid.org/0000-0002-1266-298X)** [![ORCID iD]({{ site.url }}{{ site.baseurl }}/img/orcid_16x16.png)](https://orcid.org/0000-0002-1266-298X), [_Yale University_](http://www.yale.edu/)
  * **[Tom Crane](https://orcid.org/0000-0003-1881-243X)** [![ORCID iD]({{ site.url }}{{ site.baseurl }}/img/orcid_16x16.png)](https://orcid.org/0000-0003-1881-243X), [_Digirati_](http://digirati.com/)
  * **[Robert Sanderson](https://orcid.org/0000-0003-4441-6852)** [![ORCID iD]({{ site.url }}{{ site.baseurl }}/img/orcid_16x16.png)](https://orcid.org/0000-0003-4441-6852), [_J. Paul Getty Trust_](http://www.getty.edu/)
  * **[Jon Stroop](https://orcid.org/0000-0002-0367-1243)** [![ORCID iD]({{ site.url }}{{ site.baseurl }}/img/orcid_16x16.png)](https://orcid.org/0000-0002-0367-1243), [_Princeton University Library_](https://library.princeton.edu/)
  * **[Simeon Warner](https://orcid.org/0000-0002-7970-7855)** [![ORCID iD]({{ site.url }}{{ site.baseurl }}/img/orcid_16x16.png)](https://orcid.org/0000-0002-7970-7855), [_Cornell University_](https://www.cornell.edu/)
  {: .names}

{% include copyright.md %}

__Status Warning__
This is a work in progress and may change without any notices. Implementers should be aware that this document is not stable. Implementers are likely to find the specification changing in incompatible ways. Those interested in implementing this document before it reaches beta or release stages should join the [mailing list][iiif-discuss] and take part in the discussions, and follow the [emerging issues][milestone-prezi-3] on Github.
{: .warning}

----

## Table of Contents
{:.no_toc}

* Table of Discontent (will be replaced by macro)
{:toc}

##  1. Introduction

Access to digital representations of structured resources is a fundamental requirement for many research activities, the transmission of cultural knowledge and for the daily pursuits of every web citizen. Digital content is the primary mode of transmission for access to cultural heritage, science and entertainment. Collections of both digitized physical objects and born-digital content benefit from a standardized description of their structure, layout and presentation mode.

This document describes how the structure and layout of composite objects can be made available in a standard manner. Many different styles of viewer can be implemented that consume the information to enable a rich and dynamic experience, presenting content from across collections and institutions.

A composite object may comprise a series of pages, surfaces or other views; for example the single view of a painting, the two sides of a photograph, four cardinal views of a statue, or the many pages of an edition of a newspaper or book. The primary requirements for this specification are to provide an order for these views, the resources needed to display a representation of the view, and the descriptive information needed to allow the user to understand what is being seen.

The principles of [Linked Data][linked-data] and the [Architecture of the Web][web-arch] are adopted in order to provide a distributed and interoperable system. The [Shared Canvas data model][shared-canvas] and [JSON-LD][json-ld] are leveraged to create an easy-to-implement, JSON-based format.

Please send feedback to [iiif-discuss@googlegroups.com][iiif-discuss]

### 1.1. Objectives and Scope

The objective of the IIIF (pronounced "Triple-Eye-Eff") Presentation API is to provide the information necessary to allow a rich, online viewing environment for structured digital objects to be presented to a human user, often in conjunction with the [IIIF Image API][image-api]. This is the sole purpose of the API and therefore the descriptive information is given in a way that is intended for humans to read, but not semantically available to machines. In particular, it explicitly does __not__ aim to provide metadata that would drive a search engine for discovering unknown objects.

Implementations of this specification will be able to:

  * display to the user digitized images, video, audio and other content types associated with a particular physical object, or born-digital compound object ; 
  * allow the user to navigate between multiple views of the object, either sequentially or hierarchically ;
  * and display descriptive information about the object, view or navigation structure to provide context to the user.

The following are __not__ within scope:

  * The discovery or selection of interesting objects is not directly supported; however hooks to reference further resources are available.
  * Search within the object; which is described by the [IIIF Content Search API][search-api].

Note that in the following descriptions, "object" is used to refer to the object that has been digitized or a born-digital compound object, and "resources" refer to the digital resources and content that are the result of that digitization or digital creation process.

###  1.2. Motivating Use Cases

There are many different types of digitized or digital compound objects; ancient scrolls, paintings, letters, books, newspapers, films, operas, albums, field recordings and computer generated animations. Many of them bear the written or spoken word, sometimes difficult to read or hear either due to the decay of the physical object or lack of understanding of the script or language.  These use cases are described in a separate [document][prezi-use-case-doc].

Collectively the use cases require a model in which one can characterize the object (via the _Manifest_ resource), the order(s) in which individual views are presented (the _Sequence_ resource), and the individual views themselves (_Canvas_ resources). Each view may have images, audio, video and other content resources associated with it (_Content_ resources) to allow the view to be rendered to the user appropriately. An object may also have sections; for example, a book may have chapters of several pages, or a play might be divided into acts and scenes (_Range_ resources) and there may be groups of objects (_Collection_ resources).  These resource types, along with their properties, make up the IIIF Presentation API.

### 1.3. Terminology

The key words _MUST_, _MUST NOT_, _REQUIRED_, _SHALL_, _SHALL NOT_, _SHOULD_, _SHOULD NOT_, _RECOMMENDED_, _MAY_, and _OPTIONAL_ in this document are to be interpreted as described in [RFC 2119][rfc-2119].

This specification also uses the following terms:

* __embedded__: When a resource is embedded within another resource, the complete representation of the embedded resource is present within the embedding resource, and dereferencing the URI of the embedded resource will not result in additional important information. Example: A Sequence is embedded in a Manifest.
* __referenced__: When a resource is referenced from another resource, an incomplete representation of the referenced resource is present within the referencing resource, and dereferencing the URI of the referenced resource will result in additional information.  Typically, the `id`, `type`, and `label` properties will be included in the reference. Example:  A Manifest is referenced in a Collection.


##  2. Resource Type Overview

This section provides an overview of the resource types (or classes) that are used in the specification.  They are each presented in more detail in [Section 5][prezi-api-3-resource-structure].

### 2.1. Basic Types

This specification makes use of the following primary resource types:

![Primary Resource Types](img/objects.png){: .h400px}
{: .floatRight}

##### Manifest
{: #overview-manifest}

The overall description of the structure and properties of the digital representation of an object. It carries information needed for the viewer to present the content to the user, such as a title and other descriptive information about the object or the intellectual work that it conveys. Each Manifest describes how to present a single object such as a book, a statue or a music album.

##### Sequence
{: #overview-sequence}

The order of the views of the object. Multiple Sequences are allowed to cover situations when there are multiple equally valid orders through the content, such as when a manuscript's pages are rebound or archival collections are reordered, or there are several possible orderings of musical content.

##### Canvas
{: #overview-canvas}

A virtual container that represents a particular view of the object and has content resources associated with it or with parts of it. The Canvas provides a frame of reference for the layout of the content, both spatially and temporally. The concept of a Canvas is borrowed from standards like PDF and HTML, or applications like Photoshop and PowerPoint, where an initially blank display surface has images, video, text and other content "painted" on to it.

##### Content
{: #overview-content}

Content resources such as images, audio, video or text that are associated with a Canvas.

### 2.2. Additional Types

##### Collection
{: #overview-collection}

An ordered list of Manifests, and/or further Collections.  Collections allow easy navigation among the Manifests in a hierarchical structure, potentially each with its own descriptive information. Collections might be used to model dynamic result sets from a search, fixed sets of related resources, or other groupings of Manifests for presentation.

##### Annotation Page
{: #overview-annotationpage}

An ordered list of Annotations in a single response, typically associated with a single Canvas, and can be part of an Annotation Collection.

##### Annotation
{: #overview-annotation}

All content is linked to a Canvas via Annotations. The same mechanism is used for the visible or audible resources as is used for transcriptions, commentary, tags and other content.  This provides a single, coherent method for aligning information, and provides a standards based framework for distinguishing parts of resources and parts of Canvases.  As Annotations can be added later, it promotes a distributed system in which publishers can align their content with the descriptions created by others.

##### Annotation Collection
{: #overview-annotationcollection}

An ordered list of Annotation Pages.  Annotation Collections allow higher level groupings of Annotations to be recorded. For example, all of the English translation Annotations of a medieval French document could be kept separate from the transcription or an edition in modern French, or the director's commentary on a film can be separated from the script.

##### Range
{: #overview-range}

An ordered list of Canvases, and/or further Ranges.  Ranges allow Canvases, or parts thereof, to be grouped together in some way. This could be for content-based reasons, such as might be described in a table of contents or the set of scenes in a play. Equally, physical features might be important such as quires or gatherings, or when recorded music is split across different physical carriers such as two CDs.

##  3. Resource Properties

This specification defines properties in five distinct areas. Most of the properties may be associated with any of the resource types described above, and may have more than one value.  The property relates to the resource that it is associated with,  so the `label` property on a Manifest is the human readable label of the Manifest, whereas the same `label` property on a Canvas is the human readable label for that particular view.  In the descriptions of the properties, the resource that the property is associated with is called "this resource".

The requirements for which classes have which properties are summarized in [Appendix A][prezi-api-3-appendixa].

Other properties are allowed, either via custom extensions or endorsed by IIIF. If a client discovers properties that it does not understand, then it _MUST_ ignore them.  Other properties _SHOULD_ consist of a prefix and a name in the form "`prefix:name`" to ensure it does not collide with a property defined by IIIF specifications.

###  3.1. Descriptive Properties

##### label
A human readable label, name or title for this resource. The `label` property is intended to be displayed as a short, textual surrogate for the resource if a human needs to make a distinction between it and similar resources, for example between objects, pages, or options for a choice of images to display.

The value of the property _MUST_ be a JSON object, as described in the [languages][prezi-api-3-languages] section.

 * A Collection _MUST_ have the `label` property with at least one entry.<br/>
   Clients _MUST_ render `label` on a Collection.
 * A Manifest _MUST_ have the `label` property with at least one entry.<br/>
   Clients _MUST_ render `label` on a Manifest.
 * A Sequence _MAY_ have the `label` property with at least one entry, and if there are multiple Sequences in a single Manifest then they _MUST_ each have the `label` property with at least one entry.<br/>
   Clients _SHOULD_ support multiple Sequences, and if they do, _MUST_ render `label` on a Sequence. Clients _MAY_ render `label` on a single Sequence.
 * A Canvas _SHOULD_ have the `label` property with at least one entry.<br/>
   Clients _MUST_ render `label` on a Canvas, and _MUST_ generate a `label` for Canvases that do not have them.
 * A content resource _MAY_ have the `label` property with at least one entry. If there is a Choice of content resource for the same Canvas, then they _SHOULD_ each have at least the `label` property with at least one entry.<br/>
   Clients _MAY_ render `label` on content resources, and _MUST_ render them when part of a Choice.
 * A Range _SHOULD_ have the `label` property with at least one entry. <br/>
   Clients _MUST_ render `label` on a Range.
 * An Annotation Collection _MUST_ have the `label` property with at least one entry.<br/>
   Clients _MUST_ render `label` on an Annotation Collection.
 * Other resource types _MAY_ have the `label` property with at least one entry.<br/>
   Clients _MAY_ render `label` on other resource types.

``` json-doc
{ "label": { "en": [ "Example Object Title" ] } }
```

##### metadata
A list of descriptive entries to be displayed to the user when they interact with this resource, given as pairs of human readable `label` and `value` entries. There are no semantics conveyed by this information, only strings to present to the user when interacting with this resource.  A pair might be used to convey information about the creation of the object, a physical description, ownership information, and many other use cases. 

The value of the `metadata` property _MUST_ be an array of JSON objects, where each item in the array has both `label` and `value` properties. The values of both `label` and `value` _MUST_ be JSON objects, as described in the [languages][prezi-api-3-languages] section.

 * A Collection _SHOULD_ have the `metadata` property with at least one item. <br/>
   Clients _MUST_ render `metadata` on a Collection.
 * A Manifest _SHOULD_ have the `metadata` property with at least one item.<br/>
   Clients _MUST_ render `metadata` on a Manifest.
 * A Canvas _MAY_ have the `metadata` property with at least one item.<br/>
   Clients _SHOULD_ render `metadata` on a Canvas.
 * Other resource types _MAY_ have the `metadata` property with at least one item.<br/>
   Clients _MAY_ render `metadata` on other resource types.

Clients _SHOULD_ display the pairs in the order provided. Clients _SHOULD NOT_ use `metadata` for indexing and discovery purposes, as there are intentionally no consistent semantics. Clients _SHOULD_ expect to encounter long texts in the `value` field, and render them appropriately, such as with an expand button, or in a tabbed interface.

``` json-doc
{
  "metadata": [
    {
      "label": { "en": [ "Creator" ] }, 
      "value": { "en": [ "Anne Artist (1776-1824)" ] }
    }
  ]
}
```

##### requiredStatement
Text that must be displayed when this resource is displayed or used. For example, the `requiredStatement` property could be used to present copyright or ownership statements, an acknowledgement of the owning and/or publishing institution, or any other text that the organization deems critical to display to the user.

Given the wide variation of potential client user interfaces, it will not always be possible to display this statement to the user in the client's initial state. If initially hidden, the method of revealing them _MUST_ be obvious, such as a button or scroll bar.

The value of the property _MUST_ be a JSON object, that has the `label` and `value` properties, in the same way as the `metadata` property items. The values of both `label` and `value` _MUST_ be JSON objects, as described in the [languages][prezi-api-3-languages] section.

 * Any resource type _MAY_ have the `requiredStatement` property.<br/>
   Clients _MUST_ render `requiredStatement` on every resource type.

``` json-doc
{
  "requiredStatement": { 
    "label": { "en": [ "Attribution" ] }, 
    "value": { "en": [ "Provided courtesy of Example Institution" ] }
  }
}
```

##### summary
A short textual summary of this resource, intended to be conveyed to the user when the `metadata` pairs for the resource are not being displayed.  This could be used as a snippet for item level search results, for limited screen real-estate environments, or as an alternative user interface when the `metadata` fields are not rendered. 

The value of the property _MUST_ be a JSON object, as described in the [languages][prezi-api-3-languages] section.

 * A Collection _SHOULD_ have the `summary` property with at least one entry.<br/>
   Clients _SHOULD_ render `summary` on a Collection.
 * A Manifest _SHOULD_ have the `summmary` property with at least one entry.<br/>
   Clients _SHOULD_ render `summary` on a Manifest.
 * A Canvas _MAY_ have the `summary` property with at least one entry.<br/>
   Clients _SHOULD_ render `summary` on a Canvas.
 * Other resource types _MAY_ have the `summary` property with at least one entry.<br/>
   Clients _MAY_ render `summary` on other resource types.

``` json-doc
{ "summary": { "en": [ "This is a summary of the object." ] } }
```

##### thumbnail
An external content resource that represents this resource, such as a small image or short audio clip.  It is _RECOMMENDED_ that a [IIIF Image API][image-api] service be available for images to enable manipulations such as resizing. The same resource _MAY_ have multiple thumbnails with the same or different `type` and `format`.

The value _MUST_ be a array of JSON objects, where each item in the array has an `id` property and _SHOULD_ have at least one of the `type` and `format` properties.

 * A Collection _SHOULD_ have the `thumbnail` property with at least one item.<br/>
   Clients _SHOULD_ render `thumbnail` on a Collection.
 * A Manifest _SHOULD_ have the `thumbnail` property with at least one item.<br/>
   Clients _SHOULD_ render `thumbnail` on a Manifest.
 * A Sequence _MAY_ have the `thumbnail` property with at least one item.<br/>
   Clients _SHOULD_ render `thumbnail` on a Sequence.
 * A Canvas _MAY_ have the `thumbnail` property with at least one item. A Canvas _SHOULD_ have the `thumbnail` property if there are multiple resources that make up the representation.<br/>
   Clients _SHOULD_ render `thumbnail` on a Canvas.
 * A content resource _MAY_ have the `thumbnail` property with at least one item. Content resources _SHOULD_ have the `thumbnail` property with at least one item if it is an option in a Choice of resources.<br/>
   Clients _SHOULD_ render `thumbnail` on a content resource.
 * Other resource types _MAY_ have the `thumbnail` property with at least one item.<br/>
   Clients _MAY_ render `thumbnail` on other resource types.

``` json-doc
{ "thumbnail": [ { "id": "https://example.org/img/thumb.jpg", "type": "Image" } ] }
```

##### logo
A small external image resource that represents an individual or organization associated with this resource.  This could be the logo of the owning or hosting institution. The logo _MUST_ be clearly rendered when the resource is displayed or used, without cropping, rotating or otherwise distorting the image. It is _RECOMMENDED_ that a [IIIF Image API][image-api] service be available for this image for other manipulations such as resizing.

The value _MUST_ be an array of JSON objects, each of which _MUST_ have an `id` and _SHOULD_ have at least one of `type` and `format`.

 * Any resource type _MAY_ have the `logo`property with at least one item.<br/>
   Clients _MUST_ render `logo` on every resource type.

``` json-doc
{ "logo": [ { "id": "https://example.org/img/logo.jpg", "type": "Image" } ] }
```

##### posterCanvas
One or more Canvases providing content associated with the object represented by this resource but not part of that object. Examples include an image to show while a duration-only Canvas is playing audio; images, text and sound standing in for video content before the user initiates playback; or a film poster to attract user attention. The content provided by `posterCanvas` differs from a thumbnail: a client might use `thumbnail` to summarise and navigate multiple resources, then show content from `posterCanvas` as part of the presentation of a single resource. A poster Canvas can have different dimensions to those of the Canvas(es) of this resource.

Clients _MAY_ display the content of a linked poster Canvas when presenting the resource, and if more than one is available _MAY_ choose the one most suited to the client user interface. When more than one Canvas is available, for example if `posterCanvas` is provided for the currently selected Range and the current Manifest, and the client is able to show a poster Canvas, the client _SHOULD_ pick the Canvas most specific to the content. Publishers _SHOULD NOT_ assume that `posterCanvas` content will be seen in all clients. Clients _SHOULD_ take care to avoid conflicts between time-based media in the `posterCanvas` and the content of the resource it is associated with.

The value _MUST_ be a JSON object with the `id` and `type` properties, and _MAY_ have other properties of Canvases.

  * A Collection _MAY_ have the `posterCanvas` property with at least one item.<br/>
   Clients _MAY_ render `posterCanvas` on a Collection.
  * A Manifest _MAY_ have the `posterCanvas` property with at least one item.<br/>
   Clients _MAY_ render `posterCanvas` on a Manifest.
  * A Sequence _MAY_ have the `posterCanvas` property with at least one item.<br/>
   Clients _MAY_ render `posterCanvas` on a Sequence.
  * A Canvas _MAY_ have the `posterCanvas` property with at least one item.<br/>
   Clients _MAY_ render `posterCanvas` on a Canvas.
  * A Range _MAY_ have the `posterCanvas` property with at least one item.<br/>
   Clients _MAY_ render `posterCanvas` on a Range.
  * Other resource types _MUST NOT_ have the `posterCanvas` property.<br/>
   Clients _SHOULD_ ignore `posterCanvas` on other resource types.

``` json-doc
{
  "posterCanvas": {
    "id": "https://example.org/iiif/1/canvas/poster", 
    "type": "Canvas",
    "height": 1400,
    "width": 1200
    // ...
  }
}
```

##### navDate
A date that the client can use for navigation purposes when presenting this resource to the user in a time-based user interface, such as a calendar or timeline.  More descriptive date ranges, intended for display directly to the user, _SHOULD_ be included in the `metadata` property for human consumption.

The value _MUST_ be an [`xsd:dateTime` literal][xsd-datetime]. The value _MUST_ have a timezone, and _SHOULD_ be given in UTC with the `Z` timezone indicator but _MAY_ also be given as an offset of the form `+hh:mm`. In the situation where a timezone is not given, clients _SHOULD_ assume it to be UTC. 

 * A Collection, Manifest or Range _MAY_ have the `navDate` property.<br/>
   Clients _MAY_ render `navDate` on Collections, Manifests and Ranges.
 * Other resource types _MUST NOT_ have the `navDate` property.<br/>
   Clients _SHOULD_ ignore `navDate` on other resource types.

``` json-doc
{ "navDate": "2010-01-01T00:00:00Z" }
```

##### rights

A link to an external resource that describes the license or rights statement under which this resource may be used. The rationale for the value being a URI and not a human readable text is that typically there is one license for many resources, and the text is too long to be displayed to the user at the same time as the object. If displaying rights information directly to the user is a requirement, then it is _RECOMMENDED_ to include the information using the `requiredStatement` property.

The value _MUST_ be an array of JSON objects, each of which _MUST_ have an `id` and _SHOULD_ have at least one of `type` and `format`.

 * Any resource type _MAY_ have the `rights` property with at least one item.<br/>
   Clients _MUST_ render `rights` on every resource type.

``` json-doc
{
  "rights": [ 
    {
      "id": "https://example.org/rights/copyright.html", 
      "type": "Text", 
      "format": "text/html"
    }
  ]
}
```

###  3.3. Technical Properties

##### id

The URI that identifies this resource. It is _RECOMMENDED_ that an HTTPS URI be used for all resources. If this resource is only available embedded (see the [terminology section][prezi-api-3-terminology] for an explanation of "embedded") within another resource, such as a Sequence within a Manifest, then the URI _MAY_ be the URI of the encapsulating resource with a unique fragment on the end. This is not true for Canvases, which _MUST_ have their own URI without a fragment.

The value _MUST_ be a string.

 * A Collection _MUST_ have the `id` property, and the value _MUST_ be the HTTP(S) URI at which it is published. <br/>
   Clients _SHOULD_ render `id` on a Collection.
 * A Manifest _MUST_ have the `id` property, and the value _MUST_ be the HTTP(S) URI at which it is published.<br/>
   Clients _SHOULD_ render `id` on a Manifest.
 * A Sequence _MAY_ have the `id` property.<br/>
   Clients _MAY_ render `id` on a Sequence.
 * A Canvas _MUST_ have the `id` property, and the value _MUST_ be an HTTP(S) URI.  The Canvas's JSON representation _MAY_ be published at that URI.<br/>
   Clients _SHOULD_ render `id` on a Canvas.
 * A content resource _MUST_ have the `id` property, and the value _MUST_ be the HTTP(S) URI at which the resource is published.<br/>
   Clients _MAY_ render `id` on content resources.
 * A Range _MUST_ have the `id` property, and the value _MUST_ be an HTTP(S) URI.<br/>
   Clients _MAY_ render `id` on a Range.
 * An Annotation Collection _MUST_ have the `id` property, and the value _MUST_ be an HTTP(S) URI.<br/>
   Clients _MAY_ render `id` on an Annotation Collection.
 * An Annotation Page _MUST_ have the `id` property, and the value _MUST_ be the HTTP(S) URI at which it is published.<br/>
   Clients _MAY_ render `id` on an Annotation Page.
 * An Annotation _MUST_ have the `id` property, and the value _MUST_ be an HTTP(S) URI. The Annotation's representation _SHOULD_ be published at that URI.<br/>
   Clients _MAY_ render `id` on an Annotation.

``` json-doc
{ "id": "https://example.org/iiif/1/manifest" }
```

##### type

The type or class of this resource.  For types defined by this specification, the value of `type` will be described in the sections below describing the individual classes.  For external resources, the type is drawn from other specifications. Recommendations for basic types such as image, text or audio are given in the table below.

The value _MUST_ be a string.

 * All resource types _MUST_ have the `type` property.<br/>
   Clients _MUST_ process, and _MAY_ render, `type` on any resource type.

> | Class         | Description                      |
| ------------- | -------------------------------- |
| `Application` | Software intended to be executed |
| `Dataset`     | Data not intended to be rendered to humans directly |
| `Image`       | Two dimensional visual resources primarily intended to be seen |
| `Sound`       | Auditory resources primarily intended to be heard |
| `Text`        | Resources primarily intended to be read |
| `Video`       | Moving images, with or without accompanying audio |
{: .api-table #table-type}

``` json-doc
{ "type": "Dataset" }
```

##### format
The specific media type (often called a MIME type) for this content resource, for example "image/jpeg". This is important for distinguishing different formats of the same overall type of resource, such as distinguishing text in XML from plain text.

The value _MUST_ be a string.

 * A content resource _SHOULD_ have the `format` propety, and if so, it _SHOULD_ be the value of the `Content-Type` header returned when the resource is dereferenced.<br/>
   Clients _MAY_ render the `format` of any content resource.
 * Other resource types _MUST NOT_ have the `format` property.<br/>
   Clients _SHOULD_ ignore `format` on other resource types.

Note that this is different to the `formats` property in the [Image API][image-api], which gives the extension to use within that API.  It would be inappropriate to use in this case, as `format` can be used with any content resource, not just images.

``` json-doc
{ "type": "Dataset", "format": "application/xml" }
```

##### language
The language or languages used in the content of this external resource. This property is already available from the Web Annotation model for content resources that are the body or target of an Annotation, however it _MAY_ also be used for resources referenced (see the [terminology section][prezi-api-3-terminology] for an explanation of "referenced") from `homepage`, `rendering`, `rights`, and `within`.

The value _MUST_ be an array of strings.

 * An external resource _MAY_ have the `language` property with at least one item, and each item _MUST_ be a valid language code, as described under the [languages section][prezi-api-3-languages].<br/>
   Clients _SHOULD_ process the `language` of external resources to present the most appropriate content to the user.
 * Other resource types _MUST NOT_ have the `language` property.<br/>
   Clients _SHOULD_ ignore `language` on other resource types.

``` json-doc
{
  "rendering": [ 
    { 
      "id": "https://example.org/docs/doc.pdf",
      "type": "Text", 
      "format": "application/pdf", 
      "language": [ "en" ]
    }
  ]
}
```

##### profile

A schema or named set of functionality available from this resource.  The profile can further clarify the `type` and/or `format` of an external resource or service, allowing clients to customize their handling of this resource.

The value _MUST_ be a string, either taken from the table below or a URI.

* Services and resources referenced by `seeAlso` _SHOULD_ have the `profile` property.
  Clients _SHOULD_ process the `profile` of a service or external resource.
* Other resource types _MAY_ have the `profile` property.
  Clients _MAY_ process the `profile` of other resource types.

``` json-doc
{ "profile": "info:srw/schema/1/mods-v3.3" }
```

##### height
The height of this Canvas or external content resource. For content resources, the value is in pixels. For Canvases, the value does not have a unit. In combination with the width, it conveys an aspect ratio for the space in which content resources are located.

The value _MUST_ be a positive integer.

 * A Canvas _MAY_ have the `height` property. If it has a `height`, it _MUST_ also have a `width`.<br/>
   Clients _MUST_ process `height` on a Canvas.
 * Content resources _MAY_ have the `height` property, with the value given in pixels, if appropriate.<br/>
   Clients _SHOULD_ process `height` on content resources.
 * Other resource types _MUST NOT_ have the `height` property.<br/>
   Clients _SHOULD_ ignore `height` on other resource types.

``` json-doc
{ "height": 1800 }
```

##### width
The width of this Canvas or external content resource. For content resources, the value is in pixels. For Canvases, the value does not have a unit. In combination with the height, it conveys an aspect ratio for the space in which content resources are located.

The value _MUST_ be a positive integer.

 * A Canvas _MAY_ have the `width` property. If it has a `width`, it _MUST_ also have a `height`.<br/>
   Clients _MUST_ process `width` on a Canvas.
 * Content resources _MAY_ have the `width` property, with the value given in pixels, if appropriate.<br/>
   Clients _SHOULD_ process `width` on content resources.
 * Other resource types _MUST NOT_ have the `width` property.<br/>
   Clients _SHOULD_ ignore `width` on other resource types.

``` json-doc
{ "width": 1200 }
```

##### duration
The duration of this Canvas or external content resource, given in seconds.  

The value _MUST_ be a positive floating point number.

 * A Canvas _MAY_ have the `duration` property.<br/>
   Clients _MUST_ process `duration` on a Canvas.
 * Content resources _MAY_ have the `duration` property.<br/>
   Clients _SHOULD_ process `duration` on content resources.
 * Other resource types _MUST NOT_ have a `duration`.<br/>
   Clients _SHOULD_ ignore `duration` on other resource types.

``` json-doc
{ "duration": 125.0 } 
```

##### viewingDirection
The direction that a set of Canvases _SHOULD_ be displayed to the user. This specification defines four direction values in the table below. Others may be defined externally and given as a full URI.

The value _MUST_ be a string, taken from the table below or a full URI.

 * A Collection _MAY_ have the `viewingDirection` property, and if so, its value applies to the order in which its members are rendered.<br/>
   Clients _SHOULD_ process `viewingDirection` on a Collection.
 * A Manifest _MAY_ have the `viewingDirection` property, and if so, its value applies to all of its sequences unless the sequence specifies its own value.<br/>
   Clients _SHOULD_ process `viewingDirection` on a Manifest.
 * A Sequence _MAY_ have the `viewingDirection` property.<br/>
   Clients _SHOULD_ process `viewingDirection` on a Sequence.
 * A Range _MAY_ have the `viewingDirection` property.<br/>
   Clients _MAY_ process `viewingDirection` on a Range.
 * Other resource types _MUST NOT_ have the `viewingDirection` property.<br/>
   Clients _SHOULD_ ignore `viewingDirection` on other resource types.

> | Value | Description |
| ----- | ----------- |
| `left-to-right` | The object is displayed from left to right. The default if not specified. |
| `right-to-left` | The object is displayed from right to left. |
| `top-to-bottom` | The object is displayed from the top to the bottom. |
| `bottom-to-top` | The object is displayed from the bottom to the top. |
{: .api-table #table-direction}

``` json-doc
{ "viewingDirection": "left-to-right" }
```

##### behavior
A set of user experience features for the client to use when presenting this resource to the user. This specification defines the values specified in the table below. Others may be defined externally, and would be given as a URI.

The value _MUST_ be an array of strings, taken from the table below or a URI.

 * Any resource type _MAY_ have the `behavior` property with at least one item.<br/>
   Clients _SHOULD_ process `behavior` on any resource where it is valid, unless otherwise stated in the table below (e.g. `non-paged`, `facing-pages`).

> | Value | Description |
| ----- | ----------- |
| `auto-advance` | Valid on Collections, Manifests, Sequences and Canvases, that include or are Canvases with at least the `duration` dimension. When the client reaches the end of a Canvas with a duration dimension that has (or is within a resource that has) this `behavior`, it _SHOULD_ immediately proceed to the next Canvas and render it. If there is no subsequent Canvas in the current context, then this `behavior` should be ignored. When applied to a Collection, the client should treat the first Canvas of the next Manifest as following the last Canvas of the previous Manifest, respecting any `start` property specified. |
| `continuous` | Valid on Manifests, Sequences and Ranges, which include Canvases that have at least `height` and `width` dimensions.  Canvases included in resources with this `behavior` are partial views and an appropriate rendering might display all of the Canvases virtually stitched together, such as a long scroll split into sections. This `behavior` has no implication for audio resources. The `viewingDirection` of the Sequence or Manifest will determine the appropriate arrangement of the Canvases. |
| `facing-pages` | Valid only on Canvases, where the Canvas has at least `height` and `width` dimensions. Canvases with this `behavior`, in a Sequence or Manifest with the "paged" `behavior`, _MUST_ be displayed by themselves, as they depict both parts of the opening.  If all of the Canvases are like this, then page turning is not possible, so simply use "individuals" instead. |
| `individuals` | Valid on Collections, Manifests, Sequences and Ranges. For Collections with this `behavior`, each of the included Manifests are distinct objects. For Manifest, Sequence and Range, the included Canvases are distinct views, and _SHOULD NOT_ be presented in a page-turning interface. This is the default `behavior` if none are specified. |
| `multi-part` | Valid only on Collections. Collections with this `behavior` consist of multiple Manifests that each form part of a logical whole, such as multi-volume books or a set of journal issues. Clients might render the Collection as a table of contents, rather than with thumbnails. |
| `no-nav` | Valid only on Ranges. Ranges with this `behavior` _MUST NOT_ be displayed to the user in a navigation hierarchy. This allows for Ranges to be present that capture unnamed regions with no interesting content, such as the set of blank pages at the beginning of a book, or dead air between parts of a performance, that are still part of the Manifest but do not need to be navigated to directly. |
| `non-paged` | Valid only on Canvases, where the Canvas has at least `height` and `width` dimensions. Canvases with this `behavior` _MUST NOT_ be presented in a page turning interface, and _MUST_ be skipped over when determining the page sequence. This `behavior` _MUST_ be ignored if the current Sequence or Manifest does not have the "paged" `behavior`. |
| `hidden` | Valid on Annotation Collections, Annotation Pages, Annotations, Specific Resources and Choices. If this `behavior` is provided, then the client _SHOULD NOT_ render the resource by default, but allow the user to turn it on and off. |
| `paged` | Valid on Manifests, Sequences and Ranges, which include Canvases that have at least `height` and `width` dimensions. Canvases included in resources with this `behavior` represent pages in a bound volume, and _SHOULD_ be presented in a page-turning interface if one is available.  The first canvas is a single view (the first recto) and thus the second canvas likely represents the back of the object in the first canvas. If this is not the case, see the "non-paged" `behavior`. |
| `repeat` | Valid on Collections, Manifests, and Sequences, that include Canvases with at least the `duration` dimension.  When the client reaches the end of the duration of the final Canvas in the resource, and the "auto-advance" `behavior` is also in effect, then the client _SHOULD_ return to the first Canvas in the resource with the "repeat" `behavior` and start playing again. If the "auto-advance" `behavior` is not in effect, then the client _SHOULD_ render a navigation control for the user to manually return to the first Canvas. |
| `thumbnail-nav` | Valid only on Ranges. Ranges with this `behavior` _MAY_ be used by the client to present an alternative navigation or overview based on thumbnails, such as regular keyframes along a timeline for a video, or sections of a long scroll. Clients _SHOULD NOT_ use them to generate a conventional table of contents. Child Ranges of a Range with this `behavior` _MUST_ have a suitable `thumbnail` property. |
| `together` | Valid only on Collections. A client _SHOULD_ present all of the child Manifests to the user at once in a separate viewing area with its own controls. Clients _SHOULD_ catch attempts to create too many viewing areas. The "together" value _SHOULD NOT_ be interpreted as applying to the members any child resources. |
{: .api-table #table-behavior}

``` json-doc
{ "behavior": [ "auto-advance", "individuals" ] }
```

##### timeMode

A mode associated with an Annotation that is to be applied to the rendering of any time-based media, or otherwise could be considered to have a duration, used as a body resource of that Annotation. Note that the association of `timeMode` with the Annotation means that different resources in the body cannot have different values. This specification defines the values specified in the table below. Others may be defined externally, and would be given as a full URI.

The value _MUST_ be a string, taken from the table below or a full URI.

* An Annotation _MAY_ have the `timeMode` property.<br/>
  Clients _SHOULD_ process `timeMode` on an Annotation.

> | Value | Description |
| ----- | ----------- |
| `trim` | (default, if not supplied) If the content resource has a longer duration than the duration of portion of the Canvas it is associated with, then at the end of the Canvas's duration, the playback of the content resource _MUST_ also end. If the content resource has a shorter duration than the duration of the portion of the Canvas it is associated with, then, for video resources, the last frame _SHOULD_ persist on-screen until the end of the Canvas portion's duration. For example, a video of 120 seconds annotated to a Canvas with a duration of 100 seconds would play only the first 100 seconds and drop the last 20 second. |
| `scale` | Fit the duration of content resource to the duration of the portion of the Canvas it is associated with by scaling. For example, a video of 120 seconds annotated to a Canvas with a duration of 60 seconds would be played at double-speed. |
| `loop` | If the content resource is shorter than the `duration` of the Canvas, it _MUST_ be repeated to fill the entire duration. Resources longer than the `duration` _MUST_ be trimmed as described above. For example, if a 20 second duration audio stream is annotated onto a Canvas with duration 30 seconds, it will be played one and a half times. |
{: .api-table #table-timemode}

``` json-doc
{ "timeMode": "trim" }
```

###  3.4. Linking Properties

#### 3.4.1 External Links

##### homepage
A link to an external web page that primarily describes the real world object that this resource represents. The web page is usually published by the organization responsible for the real world object, and might be generated by a content management system or other cataloging system. The external resource _MUST_ be able to be displayed directly to the user.  External resources that are related, but not home pages, _MUST_ instead be added into the `metadata` property, with an appropriate `label` or `value` to describe the relationship.

The value _MUST_ be an array of JSON objects. Each item _MUST_ have the `id`, `type` and `label` properties, and _SHOULD_ have a `format` property.

 * Any resource type _MAY_ have the `homepage` property with at least one item.<br/>
   Clients _SHOULD_ render `homepage` on a Collection, Manifest or Canvas, and _MAY_ render `homepage` on other resource types.

``` json-doc
{
  "homepage": [
    {
      "id": "https://example.com/info/",
      "type": "Text",
      "label": { "en": [ "Homepage for Example Object" ] },
      "format": "text/html"
    }
  ]
}
```

##### rendering
A link to an external resource that is an alternative, non-IIIF representation of this resource. Such representations typically cannot be painted onto a single Canvas, as they either include too many views, have incompatible dimensions, or are composite resources requiring additional rendering functionality. The external resource _MUST_ be able to be displayed directly to a human user, and _MUST NOT_ have a splash page or other interstitial resource that mediates access to it. If access control is required, then the [IIIF Authentication API][auth-api] is _RECOMMENDED_. Examples include a rendering of a book as a PDF or EPUB, a slide deck with images of a building, or a 3D model of a statue.  External resources that are related to this resource _MUST_ instead be referenced in the `metadata` field, or linked to a particular Canvas segment with an Annotation. Any home page or preferred web view of the resource _MUST_ instead be referenced in the `homepage` field.

The value _MUST_ be an array of JSON objects. Each item _MUST_ have the `id`, `type` and `label` properties, and _SHOULD_ have a `format` property.

 * Any resource type _MAY_ have the `rendering` property with at least one item.<br/>
   Clients _SHOULD_ render `rendering` on a Collection, Manifest or Canvas, and _MAY_ render `rendering` on other resource types.

``` json-doc
{
  "rendering": [
    {
      "id": "https://example.org/1.pdf",
      "type": "Text",
      "label": { "en": [ "PDF Rendering of Book" ] },
      "format": "application/pdf"
    }
  ]
}
```

##### service
A link to an external service that the client might interact with directly and gain additional information or functionality for using this resource, such as from an image to the base URI of an associated [IIIF Image API][image-api] service. The service resource _SHOULD_ have additional information associated with it in order to allow the client to determine how to make appropriate use of it. Please see the [Service Registry][registry-services] document for the details of currently known service types.

The value _MUST_ be an array of JSON objects. Each object will have properties depending on the service's definition, but _MUST_ have either the `id` or `@id` and `type` or `@type` properties. Each object _SHOULD_ have a `profile` property. 

 * Any resource type _MAY_ the `service` property with at least one item.<br/>
   Clients _MAY_ process `service` on any resource type, and _SHOULD_ process the IIIF Image API service.

``` json-doc
{
  "service": [
    {
      "id": "https://example.org/service",
      "type": "Service",
      "profile": "https://example.org/docs/service"
    }
  ]
}
```

For cross-version consistency, this specification defines the following values for the `type` or `@type` field for backwards compatibility with other IIIF APIs. Future versions of these APIs will define their own types.  These `type` values are necessary extensions for compatibility of the older versions.

| Value                | Specification |
| -------------------- | ------------- |
| ImageService1        | [Image API version 1][image-api-1]  |
| ImageService2        | [Image API version 2][image-api-2]  |
| SearchService1       | [Search API version 1][search-api-1] |
| AutoCompleteService1 | [Search API version 1][search-api-1-autocomplete] |
| AuthCookieService1   | [Authentication API version 1][auth-api-1-cookie] |
| AuthTokenService1    | [Authentication API version 1][auth-api-1-token] |
| AuthLogoutService1   | [Authentication API version 1][auth-api-1-logout] |

A reference from a version 3 Presentation API document to both version 2 and version 3 Image API endpoints would thus follow the pattern in the example below.  API versions defined after Presentation API version 3.0 will follow the community best practices of `id` and `type`, but in the interim implementations _MUST_ be prepared to recognize the older property names.  Note that the `@context` key _SHOULD_ not be present within the `service`, but instead included at the beginning of the document.

``` json-doc
{
  "service": [
    {
      "@id": "http://https://example.org/iiif2/image1/identifier",
      "@type": "ImageService2",
      "profile": "http://iiif.io/api/image/2/level2.json"
    },
    {
      "id": "http://https://example.org/iiif3/image1/identifier",
      "type": "ImageService3",
      "profile": "level2"
    }
  ]
}
```


##### seeAlso
A link to an external, machine-readable resource that is related to this resource, such as an XML or RDF description. Properties of the external resource should be given to help the client select between multiple descriptions (if provided), and to make appropriate use of the document. If the relationship between the resource and the document needs to be more specific, then the document should include that relationship rather than the IIIF resource. Other IIIF resources, such as a related Manifest, are valid targets for `seeAlso`. The URI of the document _MUST_ identify a single representation of the data in a particular format. For example, if the same data exists in JSON and XML, then separate resources should be added for each representation, with distinct `id` and `format` properties.

The value _MUST_ be an array of JSON objects. Each item _MUST_ have the `id` and `type` properties, and _SHOULD_ have the `label`, `format` and `profile` properties.

 * Any resource type _MAY_ have the `seeAlso` property with at least one item.<br/>
   Clients _MAY_ process `seeAlso` on any resource type.

``` json-doc
{
  "seeAlso": [ 
    {
      "id": "https://example.org/library/catalog/book1.xml",
      "type": "Dataset",
      "format": "text/xml",
      "profile": "https://example.org/profiles/bibliographic"
    }
  ]
}
```

#### 3.4.2. Internal Links

##### within
A link to another resource that contains this resource, such as a Manifest that is part of a Collection. When encountering the `within` property and the referenced resource is not included in the current representation, clients might retrieve the referenced resource to contribute to the processing of this resource. For example, if a Canvas is encountered as a stand alone resource, it might be `within` a Manifest that includes the Sequence and other contextual information, or a Manifest might be `within` a Collection, which would aid in navigation.

The value _MUST_ be an array of JSON objects.  Each item _MUST_ have the `id` and `type` properties, and _SHOULD_ have the `label` property.

 * Any resource types _MAY_ have the `within` property with at least one item<br/>
   Clients _MAY_ render `within` on other resource types.

``` json-doc
{ "within": [ { "id": "https://example.org/iiif/1", "type": "Manifest" } ] }
```

##### start
A link from this Manifest, Sequence or Range to a Canvas, or part of a Canvas, that is contained within it. The reference to part of a Canvas is handled in the same way that Ranges reference parts of Canvases, by adding a fragment to the end of the Canvas's URI specifing the spatial and/or temporal segment of interest.  When processing this relationship, a client _SHOULD_ advance to the specified Canvas, or specified segment of the Canvas, when beginning navigation through the Sequence or Range.  This allows the client to begin with the first Canvas that contains interesting content rather than requiring the user to manually skip past uninteresting content.  The Canvas _MUST_ be included in the first Sequence embedded within the Manifest.

The value _MUST_ be a JSON object, which _MUST_ have the `id` and `type` properties.

 * A Manifest, Sequence or Range _MAY_ have the `start` property.
   Clients _SHOULD_ process `start` on a Manifest, Sequence or Range.
 * Other resource types _MUST NOT_ have the `start` property.
   Clients _SHOULD_ ignore `start` on other resource types.

``` json-doc
{ "start": { "id": "https://example.org/iiif/1/canvas/1#t=120", "type": "Canvas" } }
```

##### includes
A link from this Range to an Annotation Collection that includes the Annotations of content resources for the Range.  Clients might use this to present content to the user from a different Canvas when interacting with the Range, or to jump to the next part of the Range within the same Canvas.  

The value _MUST_ be a JSON object, which _MUST_ have the `id` and `type` properties, and the `type` _MUST_ be `Annotation Collection`.

 * A Range _MAY_ have the `includes` property.<br/>
   Clients _MAY_ process `includes` on a Range.
 * Other resource types _MUST NOT_ have the `includes` property.<br/>
   Clients _SHOULD_ ignore `includes` on other resource types.

``` json-doc
{ "includes": { "id": "https://example.org/iiif/1/annos/1", "type": "AnnotationCollection" } }
```

### 3.5. Structural Properties

These properties define the structure of the object being represented in IIIF by allowing the inclusion of child resources within parents, such as a Canvas within a Sequence, or a Manifest within a Collection.  The majority of cases use `items`, however there are two special cases for different sorts of structures.

##### items

Much of the functionality of the IIIF Presentation API is simply recording the order in which child resources occur within a parent resource, such as Collections or Manifests within a parent Collection, Sequences within a Manifest, or Canvases within a Sequence.  All of these situations are covered with a single property, `items`.  

The value _MUST_ be an array of JSON objects. The items will be resources of different types, as described below.

* A Collection _MUST_ have the `items` property. Each item _MUST_ be either a Collection or a Manifest.<br/>
  Clients _MUST_ process `items` on a Collection.
* A Manifest _MUST_ the `items` property. Each item _MUST_ be a Sequence.<br/>
  Clients _MUST_ process `items` on a Manifest.
* A Sequence _MUST_ have the `items` property. Each item _MUST_ be a Canvas.<br/>
  Clients _MUST_ process `items` on a Sequence.
* A Canvas _SHOULD_ have the `items` property. Each item _MUST_ be an Annotation Page<br/>
  Clients _MUST_ process `items` on a Canvas.
* An Annotation Page _SHOULD_ have the `items` property. Each item _MUST_ be an Annotation.<br/>
  Clients _MUST_ process `items` on an Annotation Page.
* A Range _MUST_ have the `items` property. Each item _MUST_ be either a Range or a Canvas.<br/>
  Clients _SHOULD_ process `items` on a Range.

```json-doc
{
  "items": [
    { 
      "id": "https://example.org/iiif/manifest1",
      "type": "Manifest"
    },
    {
      "id": "https://example.org/iiif/collection1",
      "type": "Collection"
    },
    // ...
  ]
}
```

##### structures

The structure of an object represented as a Manifest can be described using a hierarchy of Ranges. Ranges can be used to describe the "table of contents" of the object or other structures that the user can interact with beyond a simple linear progression described in the Sequence. The hierarchy is built by nesting the child Range resources in the `items` array of the higher level Range. The top level Ranges of these hierarchies are given in the `structures` property. 

The value _MUST_ be an array of JSON objects. Each item is a Range.

* A Manifest _MAY_ have the `structures` property.<br/>
  Clients _SHOULD_ process `structures` on a Manifest. The first hierarchy _SHOULD_ be presented to the user by default, and further hierarchies _SHOULD_ be able to be selected as alternative structures by the user.

```json-doc
{
  "structures": [
    {
      "id": "https://example.org/iiif/range/1",
      "type": "Range",
      "items": [ { ... } ]
    }
  ]
}
```

##### annotations

A list of Annotation Pages that contain commentary or other Annotations about this resource, separate from the annotations that are used to paint content on to a Canvas. The `motivation` of the Annotations _MUST NOT_ be "painting" or "transcribing", and the target of the Annotations _MUST_ be this resource or part of it.

The value _MUST_ be an array of JSON objects. Each item _MUST_ have at least the `id` and `type` properties.

* A Collection, Manifest, Sequence, Canvas, Range or content resource _MAY_ have the `annotations` property with at least one item.<br/>
  Clients _SHOULD_ process `annotations` on a Collection, Manifest, Sequence, Canvas, Range or content resource.
 * Other resource types _MUST NOT_ have the `annotations` property.
   Clients _SHOULD_ ignore `annotations` on other resource types.

```json-doc
{
  "annotations": [
    {
      "id": "https://example.org/iiif/annotationPage/1",
      "type": "AnnotationPage",
      "items": [ { ... } ]
    }
  ]
}
```

### 3.6. Values

##### Values for motivation

This specification defines two values for the Web Annotation property of `motivation`, or `purpose` when used on a SpecificResource or TextualBody.  Annotations are used to both associate resources that make up the rendering of the Canvas as well as commentary Annotations that are about the Canvas. These motivations allow clients to determine the intent of the Annotation with regards to how it should be rendered to the user, by distinguishing between Annotations that provide the content of the Canvas from ones that are comments about the Canvas. 

Additional motivations may be added to the Annotation to further clarify the intent, drawn from extensions.  Known extensions are listed in the [Motivation registry][registry-motivations].  Clients _MUST_ ignore extension motivation values that they do not understand.  Other motivation values given in the Web Annotation specification _SHOULD_ be used where appropriate, and examples are given in the [Presentation API Cookbook][cookbook].

> | Value | Description |
| ----- | ----------- |
| painting | Resources associated with a Canvas by an Annotation with the "painting" motivation _MUST_ be presented to the user as the representation of the Canvas.  The content can be thought of as being "of" the Canvas. The use of this motivation with target resources other than Canvases is undefined. For example, an Annotation with the "painting" motivation, a body of an Image and the target of the Canvas is an instruction to present that Image as (part of) the visual representation of the Canvas.  Similarly, a textual body is to be presented as (part of) the visual representation of the Canvas and not positioned in some other part of the user interface.|
| transcribing | Resources associated with a Canvas by an Annotation with the "transcribing" motivation _MAY_ be presented to the user as part of the representation of the Canvas, or _MAY_ be presented in a different part of the user interface. The content can be thought of as being "from" the Canvas.  The use of this motivation with target resources other than Canvases is undefined.  For example, an Annotation with the "transcribing" motivation, a body of an Image and the target of part of the Canvas is an instruction to present that Image to the user either in the Canvas's rendering area or somewhere associated with it, and could be used to present an easier to read representation of a diagram. Similar, a textual body is to be presented either in the targeted region of the Canvas or otherwise associated with it, and might be a transcription of handwritten text or captions for what is being said in a Canvas with audio content. |
{: .api-table #table-motivations}

##  4. JSON-LD Considerations

This section describes features applicable to all of the Presentation API content.  For the most part, these are features of the JSON-LD specification that have particular uses within the API and recommendations about URIs to use.

### 4.1. HTTPS URI Scheme

It is strongly _RECOMMENDED_ that all URIs use the HTTPS scheme, and be available via that protocol.  All URIs _MUST_ be either HTTPS or HTTP, abbreviated to "HTTP(S)" in this specification.

### 4.2. URI Representation

Resource descriptions _SHOULD_ be embedded within higher-level descriptions, and _MAY_ also be available via separate requests from HTTP(S) URIs linked in the responses. These URIs are in the `id` property for the resource. Links to resources _MUST_ be given as a JSON object with the `id` property and at least one other property, typically either `type`, `format` or `profile` to give a hint as to what sort of resource is being referred to. Other URI schemes _MAY_ be used if the resource is not able to be retrieved via HTTP(S).

``` json-doc
{
  "thumbnail": [
    { "id": "https://example.org/images/thumb1.jpg", "type": "Image" }
  ]
}
```

### 4.3. Repeatable Properties

Any of the properties in the API that can have multiple values _MUST_ always be given as an array of values, even if there is only a single item in that array.

``` json-doc
{
  "seeAlso": [
    { "id": "https://example.org/images/thumb1.jpg", "type": "Image" },
    { "id": "https://example.org/videos/thumb1.pmg", "type": "Video" }   
  ]
}
```

### 4.4. Language of Property Values

Language _MAY_ be associated with strings that are intended to be displayed to the user for the `label` and `summary` fields, plus the `label` and `value` fields of the `metadata` and `requiredStatement` objects.

The values of these fields _MUST_ be JSON objects, with the keys being the [RFC 5646][rfc5646] language code for the language, or if the language is either not known or the string does not have a language, then the key must be `"@none"`. The associated values _MUST_ be arrays of strings, where each string is the content in the given language.

``` json-doc
{
  "label": {
    "en": [
      "Whistler's Mother",
      "Arrangement in Grey and Black No. 1: The Artist's Mother"
    ],
    "fr": [
      "Arrangement en gris et noir no 1",
      "Portrait de la mère de l'artiste",
      "La Mère de Whistler"
    ],
    "@none": [ "Whistler (1871)" ]
  }
}
```

Note that [RFC 5646][rfc5646] allows the script of the text to be included after a hyphen, such as `ar-latn`, and clients should be aware of this possibility. This allows for full internationalization of the user interface components described in the response, as the labels as well as values may be translated in this manner; examples are given below.

In the case where multiple values are supplied, clients _MUST_ use the following algorithm to determine which values to display to the user.  

* If all of the values are in the `@none` list, treated as not having a language associated with them, the client _MUST_ display all of those values.
* Else, the client should try to determine the user's language preferences, or failing that use some default language preferences. Then:
  * If any of the values have a language associated with them, the client _MUST_ display all of the values associated with the language that best matches the language preference.
  * If all of the values have a language associated with them, and none match the language preference, the client _MUST_ select a language and display all of the values associated with that language.
  * If some of the values have a language associated with them, but none match the language preference, the client _MUST_ display all of the values that do not have a language associated with them.

Note that this does not apply to embedded textual bodies in Annotations, which use the Web Annotation pattern of `value` and `langauge` as separate properties.

### 4.5. HTML Markup in Property Values

Minimal HTML markup _MAY_ be included in the `summary` property and the `value` property in the `metadata` and `requiredStatement` objects.  It _MUST NOT_ be used in `label` or other properties. This is included to allow Manifest creators to add links and simple formatting instructions to blocks of text. The content _MUST_ be well-formed XML and therefore must be wrapped in an element such as `p` or `span`.  There _MUST NOT_ be whitespace on either side of the HTML string, and thus the first character in the string _MUST_ be a '<' character and the last character _MUST_ be '>', allowing a consuming application to test whether the value is HTML or plain text using these.  To avoid a non-HTML string matching this, it is _RECOMMENDED_ that an additional whitespace character be added to the end of the value in situations where plain text happens to start and end this way.

In order to avoid HTML or script injection attacks, clients _MUST_ remove:

  * Tags such as `script`, `style`, `object`, `form`, `input` and similar.
  * All attributes other than `href` on the `a` tag, `src` and `alt` on the `img` tag.
  * CData sections.
  * XML Comments.
  * Processing instructions.

Clients _SHOULD_ allow only `a`, `b`, `br`, `i`, `img`, `p`, `small`, `span`, `sub` and `sup` tags. Clients _MAY_ choose to remove any and all tags, therefore it _SHOULD NOT_ be assumed that the formatting will always be rendered.

``` json-doc
{ "summary": { "en-latn": [ "<p>Short <b>summary</b> of the resource.</p>" ] } }
```

### 4.6. Linked Data Context and Extensions

The top level resource in the response _MUST_ have the `@context` property, and it _SHOULD_ appear as the very first key/value pair of the JSON representation. This tells Linked Data processors how to interpret the information. The IIIF Presentation API context, below, _MUST_ occur once per response, and be omitted from any embedded resources. For example, when embedding a sequence without any extensions within a Manifest, the sequence _MUST NOT_ have the `@context` field.

The value of the `@context` property _MUST_ be a list, and the __last__ two values _MUST_ be the Web Annotation context and the Presentation API context, in that order.  And further contexts _MUST_ be added at the beginning of the list.

``` json-doc
{
  "@context": [
    "http://www.w3.org/ns/anno.jsonld",
    "http://iiif.io/api/presentation/{{ page.major }}/context.json"
  ]
}
```

Any additional fields beyond those defined in this specification or the Web Annotation Data Model _SHOULD_ be mapped to RDF predicates using further context documents.   These extensions _SHOULD_ be added to the top level `@context` field, and _MUST_ be added before the above contexts.  The JSON-LD 1.1 functionality of type and predicate specific context definitions, known as [scoped contexts]][json-ld-scoped-contexts], _MUST_ be used to minimize cross-extension collisions.

The JSON representation _MUST NOT_ include the `@graph` key at the top level. This key might be created when serializing directly from RDF data using the JSON-LD 1.0 compaction algorithm. Instead, JSON-LD framing and/or custom code should be used to ensure the structure of the document is as described by this specification.

Embedded JSON-LD data that uses a JSON-LD version 1.0 context definition, such as references to older external services or extensions, _MUST_ be retrofitted to use a scoped context based either on a property or type, as per the examples in the definition of the `service` property. Care should be taken to use the mappings defined by those contexts, especially with regard to `id` versus `@id`, and `type` versus `@type`, to ensure that clients receive the keys that they are expecting to process. 

##  5. Resource Structure

This section provides detailed description of the resource types used in this specification. [Section 2][prezi-api-3-type-overview] provides an overview of the resource types and figures illustrating allowed relationships between them, and [Appendix A][prezi-api-3-appendixa] provides summary tables of the property requirements.

###  5.1. Manifest

The Manifest resource typically represents a single object and any intellectual work or works embodied within that object. In particular it includes the descriptive, rights and linking information for the object. It then embeds the Sequence(s) of Canvases that should be rendered to the user. The Manifest response contains sufficient information for the client to initialize itself and begin to display something quickly to the user.

The identifier in `id` _MUST_ be able to be dereferenced to retrieve the JSON description of the Manifest, and thus _MUST_ use the HTTP(S) URI scheme.

Along with the descriptive information, there is an `items` section, which is a list of JSON-LD objects. Each object describes a [Sequence][prezi-api-3-sequence], discussed in the next section, that represents the order of the parts of the work, each represented by a [Canvas][prezi-api-3-canvas].  There _MUST_ be at least one Sequence, and the first Sequence _MUST_ be included within the Manifest as well as optionally being available from its own URI. Subsequent Sequences _MAY_ be embedded within the Manifest, or referenced with their identifier (`id`), class (`type`) and label (`label`).

There _MAY_ also be a `structures` section listing one or more [Ranges][prezi-api-3-range] which describe additional structure of the content, such as might be rendered as a table of contents.

Finally, the Manifest _MAY_ have an `annotations` list, which includes Annotation Page resources where the Annotations are have the Manifest as their `target`.  These will typically be comment style annotations, and _MUST NOT_ have `painting` as their `motivation`. The `annotations` property may also be found on any other resource type with these same restrictions.

The example below includes only the Manifest-level information, however actual implementations _MUST_ embed at least the first Sequence, Canvas and content information.

``` json-doc
{
  // Metadata about this manifest file
  "@context": [
    "http://www.w3.org/ns/anno.jsonld",
    "http://iiif.io/api/presentation/{{ page.major }}/context.json"
  ],
  "id": "https://example.org/iiif/book1/manifest",
  "type": "Manifest",

  // Descriptive metadata about the object/work
  "label": { "en": [ "Book 1" ] },
  "metadata": [
    {
      "label": { "en": [ "Author" ] },
      "value": { "@none": [ "Anne Author" ] } 
    },
    {
      "label": { "en": [ "Published" ] },
      "value": {
        "en": [ "Paris, circa 1400" ],
        "fr": [ "Paris, environ 1400" ]
      }
    },
    { 
      "label": { "en": [ "Notes" ] },
      "value": {
        "en": [ 
          "Text of note 1", 
          "Text of note 2"
        ]
      }
    },
    {
      "label": { "en": [ "Source" ] },
      "value": { "@none": [ "<span>From: <a href=\"https://example.org/db/1.html\">Some Collection</a></span>" ] }
    }
  ],
  "summary": { "en": [ "Book 1, written be Anne Author, published in Paris around 1400." ] },

  "thumbnail": [ 
    {
      "id": "https://example.org/images/book1-page1/full/80,100/0/default.jpg",
      "type": "Image",
      "service": {
        "id": "https://example.org/images/book1-page1",
        "type": "ImageService3",
        "profile": "level1"
      }
    }
  ],

  // Presentation Information
  "viewingDirection": "right-to-left",
  "behavior": [ "paged" ],
  "navDate": "1856-01-01T00:00:00Z",

  // Rights Information
  "rights": [ 
    {
      "id":"https://example.org/license.html",
      "type": "Text",
      "language": "en",
      "format": "text/html"
    } 
  ],
  "requiredStatement": {
    "label": { "en": [ "Attribution" ] }, 
    "value": { "en": [ "Provided by Example Organization" ] }
  },
  "logo": {
    "id": "https://example.org/logos/institution1.jpg",
    "service": {
      "id": "https://example.org/service/inst1",
      "type": "ImageService3",
      "profile": "level2"
    }
  },

  // Links
  "homepage": [
    {
      "id": "https://example.org/info/book1/",
      "type": "Text",
      "label": { "en": [ "Home page for Book 1" ] },
      "format": "text/html"
    }
  ],
  "service": [
    {
      "id": "https://example.org/service/example",
      "type": "Service",
      "profile": "https://example.org/docs/example-service.html"
    }
  ],
  "seeAlso": [
    {
      "id": "https://example.org/library/catalog/book1.xml",
      "type": "Dataset",
      "format": "text/xml",
      "profile": "https://example.org/profiles/bibliographic"
    }
  ],
  "rendering": [
    {
      "id": "https://example.org/iiif/book1.pdf",
      "type": "Text",
      "label": { "en": [ "Download as PDF" ] },
      "format": "application/pdf"
    }
  ],
  "within": [
    {
      "id": "https://example.org/collections/books/",
      "type": "Collection"
    }
  ],
  "start": {
    "id": "https://example.org/iiif/book1/canvas/p2",
    "type": "Canvas"
  },

  // List of Sequences
  "items": [
    {
      "id": "https://example.org/iiif/book1/sequence/normal",
      "type": "Sequence",
      "label": { "en": [ "Current Page Order" ] }
      // Sequence's page order should be included here
    }
    // Any additional Sequences can be included or linked here
  ],

  // structure of the resource, described with Ranges
  "structures": [
    {
      "id": "https://example.org/iiif/book1/range/top",
      "type": "Range"
      // Ranges members should be included here
    }
    // Any additional top level Ranges can be included here
  ],

  // Commentary Annotations on the Manifest
  "annotations": [
    {
      "id": "https://example.org/iiif/book1/annotations/p1",
      "type": "AnnotationPage",
      "items": [
        // Annotations about the Manifest are included here
      ]
    }
  ]
}
```

###  5.2. Sequence

The Sequence conveys the ordering of the views of the object. The default Sequence (and typically the only Sequence) _MUST_ be embedded within the Manifest as the first object in the `items` property, and _MAY_ also be available from its own URI.  This Sequence _SHOULD_ have a URI to identify it. Any additional Sequences _MAY_ be included, or referenced externally from the Manifest.  References to external Sequences _MUST_ include an `id` and `type`, _SHOULD_ have a `label`, and _MUST NOT_ have an `items` property. The description of the Sequence _MUST_ be available by dereferencing the HTTP(S) URI in `id`.

Sequences _MAY_ have their own descriptive, rights and linking metadata using the same fields as for Manifests. The `label` property _MAY_ be given for Sequences and _MUST_ be given if there is more than one referenced from a Manifest. After the metadata, the set of views of the object, represented by Canvas resources, _MUST_ be listed in order in the `items` property.  There _MUST_ be at least one Canvas given.

``` json-doc
{
  // Metadata about this sequence
  "id": "https://example.org/iiif/book1/sequence/normal",
  "type": "Sequence",
  "label": { "en": [ "Current Page Order" ] },

  "viewingDirection": "left-to-right",
  "behavior": [ "paged" ],
  "start": "https://example.org/iiif/book1/canvas/p2",

  // The order of the canvases
  "items": [
    {
      "id": "https://example.org/iiif/book1/canvas/p1",
      "type": "Canvas",
      "label": { "@none": [ "p. 1" ] }
      // ...
    },
    {
      "id": "https://example.org/iiif/book1/canvas/p2",
      "type": "Canvas",
      "label": { "@none": [ "p. 2" ] }
      // ...
    },
    {
      "id": "https://example.org/iiif/book1/canvas/p3",
      "type": "Canvas",
      "label": { "@none": [ "p. 3" ] }
      // ...
    }
  ]
}
```

###  5.3. Canvas

The Canvas represents an individual page or view and acts as a central point for assembling the different content resources that make up the display. Canvases _MUST_ be identified by a URI and it _MUST_ be an HTTP(S) URI. The URI of the canvas _MUST NOT_ contain a fragment (a `#` followed by further characters), as this would make it impossible to refer to a segment of the Canvas's area using the [media fragment syntax][media-frags] of `#xywh=` for spatial regions, and/or `#t=` for temporal segments. Canvases _MAY_ be able to be dereferenced separately from the Manifest via their URIs as well as being embedded within the Sequence.

Every Canvas _SHOULD_ have a `label` to display. If one is not provided, the client _SHOULD_ automatically generate one for use based on the Canvas's position within the current Sequence.

Content resources are associated with the Canvas via Web Annotations.  Content that is to be rendered as part of the Canvas _MUST_ be associated by an Annotation with the "painting" `motivation`. Content that is derived from the Canvas, such as a transcription of text in an image or the words spoken in an audio representation, _MUST_ be associated by an Annotation with the "transcribing" `motivation`. These Annotations are recorded in the `items` of one or more Annotation Pages, referred to in the `items` array of the Canvas. If, according to the organization providing the Manifest, clients _SHOULD_ render the Annotation quickly then it _SHOULD_ be embedded within the Manifest directly. Clients _SHOULD_ process the Annotation Pages and their items in the order given in the Canvas.  Other Annotation Pages can be referenced with just their `id`, `type` and optionally a `label`, and clients _SHOULD_ dereference these pages to discover further content.  Content in this case includes media assets such as images, video and audio, textual transcriptions or editions of the Canvas.  These different uses _MAY_ be split up across different Annotation Pages. Annotations that have neither the "painting" nor "transcribing" `motivation` MUST NOT be in pages referenced in `items`, but instead in the `annotations` property.

A Canvas _MUST_ have a rectangular aspect ratio (described with the `height` and `width` properties) and/or a `duration` to provide an extent in time. These dimensions allow resources to be associated with specific regions of the Canvas, within the space and/or time extents provided. Content _MUST NOT_ be associated with space or time outside of the Canvas's dimensions, such as at coordinates below 0,0, greater than the height or width, before 0 seconds, or after the duration. Content resources that have dimensions which are not defined for the Canvas _MUST NOT_ be associated with that Canvas. For example, it is valid to use a "painting" Annotation to associate an Image (which has only height and width) with a Canvas that has all three dimensions, but it is an error to associate a Video resource (which has height, width and duration) with a Canvas that does not have all three dimensions. Such a resource _SHOULD_ instead be referenced with the `rendering` property, or by Annotations with a `motivation` other than "painting" or "transcribing" in the `annotations` property.

Parts of Canvases are still Canvases and have a `type` of "Canvas". Parts of Canvases can be referenced from Ranges, Annotations or the `start` property. Spatial parts of Canvases, when referenced from outside an Annotation, _MUST_ be rectangular and are described by appending an `xywh=` fragment to the end of the Canvas's URI. Similarly, temporal parts of Canvases _MUST_ be described by appending a `t=` fragment to the end of the Canvas's URI. Spatial and temporal fragments _MAY_ be combined, using an `&` character between them, and the temporal dimension _SHOULD_ come first.  It is an error to select a region using a dimension that is not defined by the Canvas, such as a temporal region of a Canvas that only has height and width dimensions.

Canvases may be treated as content resources for the purposes of annotating on to other Canvases. For example, a Canvas (Canvas A) with a video resource and Annotations representing subtitles or captions may be annotated on to another Canvas (Canvas B). This pattern maintains the correct spatial and temporal alignment of Canvas A's content relative to Canvas B's dimensions.

Renderers _MUST_ scale content into the space represented by the Canvas, and _SHOULD_ follow any `timeMode` value provided for time-based media.  If the Canvas represents a view of a physical object, the spatial dimensions of the Canvas _SHOULD_ be the same scale as that physical object, and images _SHOULD_ depict only the object.


``` json-doc
{
  // Metadata about this canvas
  "id": "https://example.org/iiif/book1/canvas/p1",
  "type": "Canvas",
  "label": { "@none": [ "p. 1" ] },
  "height": 1000,
  "width": 750,
  "duration": 180.0,

  "items": [
    {
      "id": "https://example.org/iiif/book1/page/p1/1",
      "type": "AnnotationPage",
      "items": [
        // Content Annotations on the Canvas are included here
      ]
    }
  ]
}
```

###  5.4. Annotation Pages

Association of images and other content with their respective Canvases is done via Annotations. Traditionally Annotations are used for associating commentary with the resource the Annotation's text or body is about, the [Web Annotation][webanno] model allows any resource to be associated with any other resource, or parts thereof, and it is reused for both commentary and painting resources on the Canvas. Other resources beyond images might include the full text of the object, musical notations, musical performances, diagram transcriptions, commentary annotations, tags, video, data and more.

These Annotations are collected together in Annotation Page resources, which are included in the `items` list from the Canvas.  Each Annotation Page can be embedded in its entirety, if the Annotations should be processed as soon as possible when the user navigates to that Canvas, or a reference to an external page. This reference _MUST_ include `id` and `type`, _MUST NOT_ include `items` and _MAY_ include other properties, such as `behavior`. All of the Annotations in the Annotation Page _SHOULD_ have the Canvas as their `target`.  Embedded Annotation Pages _SHOULD_ be processed by the client first, before externally referenced pages.

The Annotation Page _MUST_ have an HTTP(S) URI given in `id`, and the JSON representation _MUST_ be returned when that URI is dereferenced.  They _MAY_ have any of the other fields defined in this specification, or the Web Annotation specification.  The Annotations are listed in an `items` list of the Annotation Page.

``` json-doc
{
  "@context": [
    "http://www.w3.org/ns/anno.jsonld",
    "http://iiif.io/api/presentation/{{ page.major }}/context.json"
  ],
  "id": "https://example.org/iiif/book1/annopage/p1",
  "type": "AnnotationPage",

  "items": [
    {
      "id": "https://example.org/iiif/book1/annopage/p1/a1",
      "type": "Annotation"
      // ...
    },
    {
      "id": "https://example.org/iiif/book1/annopage/p1/a2",
      "type": "Annotation"
      // ...
    }
  ]
}
```

### 5.5. Annotations

Annotations follow the [Web Annotation][webanno] data model.  The description provided here is a summary plus any IIIF specific requirements. It must be noted that the W3C standard is the official documentation.

Annotations _MUST_ have their own HTTP(S) URIs, conveyed in the `id` property. The JSON-LD description of the Annotation _SHOULD_ be returned if the URI is dereferenced, according to the [Web Annotation Protocol][webannoprotocol].

Annotations that associate content that is part of the representation of the view _MUST_ have the `motivation` field and the value _MUST_ be "painting". This is in order to distinguish it from commentary style Annotations. Text may be thus either be associated with the Canvas via a "painting" annotation, meaning the content is part of the representation, or with another `motivation`, meaning that it is somehow about the view.

The content resource is linked in the `body` of the Annotation. The content resource _MUST_ have an `id` field, with the value being the URI at which it can be obtained.

The type of the content resource _MUST_ be included, and _SHOULD_ be taken from the table listed under the definition of `type`. The format of the resource _SHOULD_ be included and, if so, _SHOULD_ be the media type that is returned when the resource is dereferenced. Content resources _MAY_ also have any of the other fields defined in this specification, including commonly `label`, `summary`, `metadata`, `rights` and `requiredStatement`.

If the content resource is an Image, and a IIIF Image service is available for it, then the URI _MAY_ be a complete URI to any particular representation made available, such as `https://example.org/image1/full/1000,/0/default.jpg`, but _MUST NOT_ be just the URI of the IIIF Image service. It _MUST_ have a `type` of "Image". Its media type _MAY_ be listed in `format`, and its height and width _MAY_ be given as integer values for `height` and `width` respectively. The image then _SHOULD_ have the service referenced from it.

The URI of the Canvas _MUST_ be repeated in the `target` field of the Annotation. This is to ensure consistency with Annotations that target only part of the resource, described in more detail below, and to remain faithful to the Web Annotation specification, where `target` is mandatory.

Additional features of the [Web Annotation][webanno] data model _MAY_ also be used, such as selecting a segment of the Canvas or content resource, or embedding the comment or transcription within the Annotation. The use of these advanced features sometimes results in situations where the `target` is not a content resource, but instead a `SpecificResource`, a `Choice`, or other non-content object. Implementations should check the `type` of the resource and not assume that it is always content to be rendered.


``` json-doc
{
  "@context": [
    "http://www.w3.org/ns/anno.jsonld",
    "http://iiif.io/api/presentation/{{ page.major }}/context.json"
  ],
  "id": "https://example.org/iiif/book1/annotation/p0001-image",
  "type": "Annotation",
  "motivation": "painting",
  "body": {
    "id": "https://example.org/iiif/book1/res/page1.jpg",
    "type": "Image",
    "format": "image/jpeg",
    "service": {
      "id": "https://example.org/images/book1-page1",
      "type": "ImageService3",
      "profile": "level2"
    },
    "height": 2000,
    "width": 1500
  },
  "target": "https://example.org/iiif/book1/canvas/p1"
}
```

###  5.6. Range

Ranges are used to represent structure within an object beyond the overall Sequence, such as newspaper sections or articles, chapters within a book, or movements within a piece of music. Ranges can include Canvases, parts of Canvases, or other Ranges, creating a tree structure like a table of contents.

The intent of adding a Range to the Manifest is to allow the client to display a hierarchical navigation interface to enable the user to quickly move through the object's content. Clients _SHOULD_ present only Ranges with the `label` property and without the "no-nav" `behavior` to the user. Clients _SHOULD NOT_ render Canvas labels as part of the navigation, and a Range that wraps the Canvas _MUST_ be created if this is the desired presentation.

Ranges _MUST_ have URIs and they _SHOULD_ be HTTP(S) URIs.  Top level Ranges are embedded or externally referenced within the Manifest in a `structures` property. These top level Ranges then embed other Ranges, Canvases or parts of Canvases in the `items` property.  Each entry in the `items` field _MUST_ be a JSON object, and it _MUST_ have the `id` and `type` properties.  If a top level Range needs to be dereferenced by the client, then it _MUST NOT_ have the `items` property, such that clients are able to recognize that it should be retrieved in order to be processed.

All of the Canvases or parts that should be considered as being part of a Range _MUST_ be included within the Range's `items` list, or a descendant Range's `items`. 

The Canvases and parts of Canvases need not be contiguous or in the same order as in any Sequence. Examples include newspaper articles that are continued in different sections, a chapter that starts half way through a page, or time segments of a single canvas that represent different sections of a piece of music. 

Ranges _MAY_ link to an Annotation Collection that has the content of the Range using the `includes` property. The referenced Annotation Collection will contain Annotations that target areas of Canvases within the Range and link content resources to those Canvases.


``` json-doc
{
  "@context": [
    "http://www.w3.org/ns/anno.jsonld",
    "http://iiif.io/api/presentation/{{ page.major }}/context.json"
  ],
  "id": "https://example.org/iiif/book1/manifest",
  "type": "Manifest",
  // Metadata here ...

  "items": [
    // Sequences here ...
  ],

  "structures": [
    {
      "id": "https://example.org/iiif/book1/range/r0",
      "type": "Range",
      "label": { "en": [ "Table of Contents" ] },
      "items": [
        {
          "id": "https://example.org/iiif/book1/canvas/cover",
          "type": "Canvas"
        },
        {
          "id": "https://example.org/iiif/book1/range/r1",
          "type": "Range",
          "label": { "en": [ "Introduction" ] },
          "includes": "https://example.org/iiif/book1/annocoll/introTexts",
          "items": [
            {
              "id": "https://example.org/iiif/book1/canvas/p1",
              "type": "Canvas"
            },
            {
              "id": "https://example.org/iiif/book1/canvas/p2",
              "type": "Canvas"
            },
            {
              "id": "https://example.org/iiif/book1/canvas/p3#xywh=0,0,750,300",
              "type": "Canvas"
            }  
          ]
        },
        {
          "id": "https://example.org/iiif/book1/canvas/backCover",
          "type": "Canvas"
        }
      ]
    }
  ]
}
```

### 5.7. Annotation Collection

Annotation Collections represent groupings of Annotation Pages that should be managed as a single whole, regardless of which Canvas or resource they target. This allows, for example, all of the Annotations that make up a particular translation of the text of a book to be collected together. A client might then present a user interface that allows all of the Annotations in an Annotation Collection to be displayed or hidden according to the user's preference.

Annotation Collections _MUST_ have a URI, and it _SHOULD_ be an HTTP URI.  They _MUST_ have a `label` and _MAY_ have any of the other descriptive, linking or rights properties.

``` json-doc
{
  "@context": [
    "http://www.w3.org/ns/anno.jsonld",
    "http://iiif.io/api/presentation/{{ page.major }}/context.json"
  ],
  "id": "https://example.org/iiif/book1/annocoll/transcription",
  "type": "AnnotationCollection",
  "label": { "en": [ "Diplomatic Transcription" ] },

  "first": { "id": "https://example.org/iiif/book1/annopage/l1", "type": "AnnotationPage" },
  "last": { "id": "https://example.org/iiif/book1/annopage/l120", "type": "AnnotationPage" }
}
```

### 5.8. Collection

Collections are used to list the Manifests available for viewing, and to describe the structures, hierarchies or sets that the resources are part of.  Collections _MAY_ include both other Collections and Manifests, in order to form a tree-structured hierarchy.  Collections might be used to model dynamic result sets from a search, fixed sets of related resources, or other groupings of Manifests for presentation to the user, typically for navigation amongst the member items.

Collection objects _MAY_ be embedded inline within other Collection objects, such as when the Collection is used primarily to subdivide a larger one into more manageable pieces, however Manifests _MUST NOT_ be embedded within Collections. An embedded Collection _SHOULD_ also have its own URI from which the JSON description is available.

Manifests or Collections _MAY_ appear within more than one Collection. For example, an institution might define four Collections: one for modern works, one for historical works, one for newspapers and one for books.  The Manifest for a modern newspaper would then appear in both the modern Collection and the newspaper Collection.  Alternatively, the institution may choose to have two separate newspaper Collections, and reference each as a sub-Collection of modern and historical.

The intended usage of collections is to allow clients to:

  * Load a pre-defined set of Manifests at initialization time.
  * Receive a set of Manifests, such as search results, for rendering.
  * Visualize lists or hierarchies of related Manifests.
  * Provide navigation through a list or hierarchy of available Manifests.

An empty Collection, with no member resources, is allowed but discouraged.

An example Collection document:

``` json-doc
{
  "@context": [
    "http://www.w3.org/ns/anno.jsonld",
    "http://iiif.io/api/presentation/{{ page.major }}/context.json"
  ],
  "id": "https://example.org/iiif/collection/top",
  "type": "Collection",
  "label": { "en": [ "Top Level Collection for Example Organization" ] },
  "summary": { "en": [ "Short summary of the Collection" ] },
  "behavior": [ "top" ],
  "requiredStatement": {
    "label": { "en": [ "Attribution" ] }, 
    "value": { "en": [ "Provided by Example Organization" ] }
  },

  "items": [
    {
      "id": "https://example.org/iiif/1/manifest", 
      "type": "Manifest",
      "label": { "en": "Example Manifest 1" }
    }
  ]
}
```

Note that while the Collection _MAY_ include Collections or Manifests from previous versions of the API, the information included in this document _MUST_ follow the current version requirements, not the requirements of the target document.  This is in contrast to the requirements of `service`, as there is no way to distinguish a version 2 Manifest from a version 3 Manifest by its `type`.

## 6. HTTP Requests and Responses

This section describes the _RECOMMENDED_ request and response interactions for the API. The REST and simple HATEOAS approach is followed where an interaction will retrieve a description of the resource, and additional calls may be made by following links obtained from within the description. All of the requests use the HTTP GET method; creation and update of resources is not covered by this specification.

### 6.1 URI Recommendations

While any URI is technically acceptable for any of the resources in the API, there are several best practices for designing the URIs for the resources.

* The URI _SHOULD_ use the HTTPS scheme, not HTTP.
* The URI _SHOULD NOT_ include query parameters or fragments.
* Once published, they _SHOULD_ be as persistent and unchanging as possible.
* Special characters _MUST_ be encoded.

###  6.2. Requests

Clients _MUST NOT_ attempt to construct resource URIs by themselves, instead they _MUST_ follow links from within retrieved descriptions or elsewhere.

###  6.3. Responses

The format for all responses is JSON, as described above.  The different requirements for which resources _MUST_ provide a response is summarized in [Appendix A][prezi-api-3-appendixa]. While some resources do not require their URI to provide the description, it is good practice if possible.

The HTTP `Content-Type` header of the response _SHOULD_ have the value "application/ld+json" (JSON-LD) with the `profile` parameter given as the context document: `http://iiif.io/api/presentation/3/context.json`.

``` none
Content-Type: application/ld+json;profile="http://iiif.io/api/presentation/3/context.json"
```
{: .urltemplate}

If this cannot be generated due to server configuration details, then the content-type _MUST_ instead be `application/json` (regular JSON), without a `profile` parameter.

``` none
Content-Type: application/json
```
{: .urltemplate}

The HTTP server _MUST_ follow the [CORS requirements][w3c-cors] to enable browser-based clients to retrieve the descriptions. In particular, the response _MUST_ include the `Access-Control-Allow-Origin` header, and the value _SHOULD_ be `*`.

``` none
Access-Control-Allow-Origin: *
```
{: .urltemplate}

Responses _SHOULD_ be compressed by the server as there are significant performance gains to be made for very repetitive data structures.

Recipes for enabling CORS, conditional Content-Type headers and other technical details are provided in the [Apache HTTP Server Implementation Notes][apache-notes].


## 7. Authentication

It may be necessary to restrict access to the descriptions made available via the Presentation API.  As the primary means of interaction with the descriptions is by web browsers using XmlHttpRequests across domains, there are some considerations regarding the most appropriate methods for authenticating users and authorizing their access.  The approach taken is described in the [Authentication][auth-api] specification, and requires requesting a token to add to the requests to identify the user.  This token might also be used for other requests defined by other APIs.

It is possible to include Image API service descriptions within the Manifest, and within those it is also possible to include links to the Authentication API's services that are needed to interact with the image content. The first time an Authentication API service is included within a Manifest, it _MUST_ be the complete description. Subsequent references _SHOULD_ be just the URI of the service, and clients are expected to look up the details from the full description by matching the URI.  Clients _MUST_ anticipate situations where the Authentication service description in the Manifest is out of date: the source of truth is the Image Information document, or other system that references the Authentication API services.

## Appendices

### A. Summary of Metadata Requirements

| Field                      | Meaning     |
| -------------------------- | ----------- |
| ![required][icon-req]      | Required    |
| ![recommended][icon-recc]  | Recommended |
| ![optional][icon-opt]      | Optional    |
| ![not allowed][icon-na]    | Not Allowed |
{: .api-table #table-reqs-icons} 

__Descriptive and Rights Properties__

|                      | label                  | metadata                     | summary                     | thumbnail                   | posterCanvas                | requiredStatement            | license                 | logo                     |
| -------------------- | ---------------------- | ---------------------------- | --------------------------- | ----------------------------| ----------------------------| ---------------------- | ----------------------- | ------------------------ |
| Collection           | ![required][icon-req]  | ![recommended][icon-recc]    | ![recommended][icon-recc]   | ![recommended][icon-recc]   | ![optional][icon-opt]       | ![optional][icon-opt]  | ![optional][icon-opt]   | ![optional][icon-opt]    |
| Manifest             | ![required][icon-req]  | ![recommended][icon-recc]    | ![recommended][icon-recc]   | ![recommended][icon-recc]   | ![optional][icon-opt]       | ![optional][icon-opt]  | ![optional][icon-opt]   | ![optional][icon-opt]    |
| Sequence             | ![optional][icon-opt]  | ![optional][icon-opt]        | ![optional][icon-opt]       | ![optional][icon-opt]       | ![optional][icon-opt]       | ![optional][icon-opt]  | ![optional][icon-opt]   | ![optional][icon-opt]    |
| Canvas               | ![required][icon-req]  | ![optional][icon-opt]        | ![optional][icon-opt]       | ![recommended][icon-recc]   | ![optional][icon-opt]       | ![optional][icon-opt]  | ![optional][icon-opt]   | ![optional][icon-opt]    |
| Annotation           | ![optional][icon-opt]  | ![optional][icon-opt]        | ![optional][icon-opt]       | ![optional][icon-opt]       | ![not allowed][icon-na]     | ![optional][icon-opt]  | ![optional][icon-opt]   | ![optional][icon-opt]    |
| AnnotationPage       | ![optional][icon-opt]  | ![optional][icon-opt]        | ![optional][icon-opt]       | ![optional][icon-opt]       | ![not allowed][icon-na]     | ![optional][icon-opt]  | ![optional][icon-opt]   | ![optional][icon-opt]    |
| Range                | ![required][icon-req]  | ![optional][icon-opt]        | ![optional][icon-opt]       | ![optional][icon-opt]       | ![optional][icon-opt]       | ![optional][icon-opt]  | ![optional][icon-opt]   | ![optional][icon-opt]    |
| AnnotationCollection | ![required][icon-req]  | ![optional][icon-opt]        | ![optional][icon-opt]       | ![optional][icon-opt]       | ![not allowed][icon-na]     | ![optional][icon-opt]  | ![optional][icon-opt]   | ![optional][icon-opt]    |
| Image Content        | ![optional][icon-opt]  | ![optional][icon-opt]        | ![optional][icon-opt]       | ![optional][icon-opt]       | ![not allowed][icon-na]     | ![optional][icon-opt]  | ![optional][icon-opt]   | ![optional][icon-opt]    |
| Other Content        | ![optional][icon-opt]  | ![optional][icon-opt]        | ![optional][icon-opt]       | ![optional][icon-opt]       | ![not allowed][icon-na]     | ![optional][icon-opt]  | ![optional][icon-opt]   | ![optional][icon-opt]    |
{: .api-table #table-reqs-1}

__Technical Properties__

|                      | id                        | type                  | format                  | height                    | width                     | viewingDirection        | behavior               | navDate                  |
| -------------------- | ------------------------- | --------------------- | ----------------------- | ------------------------- | ------------------------- | ----------------------- | ---------------------- | ------------------------ |
| Collection           | ![required][icon-req]     | ![required][icon-req] | ![not allowed][icon-na] | ![not allowed][icon-na]   | ![not allowed][icon-na]   | ![not allowed][icon-na] | ![optional][icon-opt]  | ![optional][icon-opt]    |
| Manifest             | ![required][icon-req]     | ![required][icon-req] | ![not allowed][icon-na] | ![not allowed][icon-na]   | ![not allowed][icon-na]   | ![optional][icon-opt]   | ![optional][icon-opt]  | ![optional][icon-opt]    |
| Sequence             | ![optional][icon-opt]     | ![required][icon-req] | ![not allowed][icon-na] | ![not allowed][icon-na]   | ![not allowed][icon-na]   | ![optional][icon-opt]   | ![optional][icon-opt]  | ![not allowed][icon-na]  |
| Canvas               | ![required][icon-req]     | ![required][icon-req] | ![not allowed][icon-na] | ![required][icon-req]     | ![required][icon-req]     | ![not allowed][icon-na] | ![optional][icon-opt]  | ![not allowed][icon-na]  |
| Annotation           | ![recommended][icon-recc] | ![required][icon-req] | ![not allowed][icon-na] | ![not allowed][icon-na]   | ![not allowed][icon-na]   | ![not allowed][icon-na] | ![optional][icon-opt]  | ![not allowed][icon-na]  |
| Annotation Page       | ![required][icon-req]     | ![required][icon-req] | ![not allowed][icon-na] | ![not allowed][icon-na]   | ![not allowed][icon-na]   | ![not allowed][icon-na] | ![optional][icon-opt]  | ![not allowed][icon-na]  |
| Range                | ![required][icon-req]     | ![required][icon-req] | ![not allowed][icon-na] | ![not allowed][icon-na]   | ![not allowed][icon-na]   | ![optional][icon-opt]   | ![optional][icon-opt]  | ![not allowed][icon-na]  |
| Annotation Collection | ![required][icon-req]     | ![required][icon-req] | ![not allowed][icon-na] | ![not allowed][icon-na]   | ![not allowed][icon-na]   | ![optional][icon-opt]   | ![optional][icon-opt]  | ![not allowed][icon-na]  |
| Image Content        | ![required][icon-req]     | ![required][icon-req] | ![optional][icon-opt]   | ![recommended][icon-opt]  | ![recommended][icon-opt]  | ![not allowed][icon-na] | ![optional][icon-opt]  | ![not allowed][icon-na]  |
| Other Content        | ![required][icon-req]     | ![required][icon-req] | ![optional][icon-opt]   | ![optional][icon-opt]     | ![optional][icon-opt]     | ![not allowed][icon-na] | ![optional][icon-opt]  | ![not allowed][icon-na]  |
{: .api-table #table-reqs-2}

__Linking Properties__

|                      | seeAlso                | service                | homepage                | rendering              | within                 | start                  |
| -------------------- | ---------------------- | ---------------------- | ---------------------- | ---------------------- | ---------------------- | ----------------------- |
| Collection           | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![not allowed][icon-na] |
| Manifest             | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![not allowed][icon-na] |
| Sequence             | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]   |
| Canvas               | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![not allowed][icon-na] |
| Annotation           | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![not allowed][icon-na] |
| Annotation Page       | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![not allowed][icon-na] |
| Range                | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]   |
| Annotation Collection | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![not allowed][icon-na] |
| Image Content        | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![not allowed][icon-na] |
| Other Content        | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![optional][icon-opt]  | ![not allowed][icon-na] |
{: .api-table #table-reqs-3}

__Structural Properties__

|                      | collections             | manifests               | members                 | sequences               | structures              | canvases                | resources               | otherContent            | images                  | ranges                  |
| -------------------- | ----------------------- | ----------------------- | ----------------------- | ----------------------- | ----------------------- | ----------------------- | ----------------------- | ----------------------- | ----------------------- | ----------------------- |
| Collection           | ![optional][icon-opt]   | ![optional][icon-opt]   | ![optional][icon-opt]   | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] |
| Manifest             | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![required][icon-req]   | ![optional][icon-opt]   | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] |
| Sequence             | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![required][icon-req]   | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] |
| Canvas               | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![optional][icon-opt]   | ![optional][icon-opt]   | ![not allowed][icon-na] |
| Annotation           | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] |
| Annotation Page       | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![optional][icon-opt]   | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] |
| Range                | ![not allowed][icon-na] | ![not allowed][icon-na] | ![optional][icon-opt]   | ![not allowed][icon-na] | ![not allowed][icon-na] | ![optional][icon-opt]   | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![optional][icon-opt]   |
| Annotation Collection | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![optional][icon-opt]   | ![not allowed][icon-na] | ![not allowed][icon-na] |
| Image Content        | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] |
| Other Content        | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] | ![not allowed][icon-na] |
{: .api-table #table-reqs-5}


__Protocol Behavior__

|                      | id is dereferenceable     |
| -------------------- | ------------------------- |
| Collection           | ![required][icon-req]     |
| Manifest             | ![required][icon-req]     |
| Sequence             | ![optional][icon-opt]     |
| Canvas               | ![recommended][icon-recc] |
| Annotation           | ![recommended][icon-recc] |
| Annotation Page       | ![required][icon-req]     |
| Range                | ![optional][icon-opt]     |
| Annotation Collection | ![optional][icon-opt]     |
| Image Content        | ![required][icon-req]     |
| Other Content        | ![required][icon-req]     |
{: .api-table #table-reqs-deref}

### B. Example Manifest Response

Rebuild the example
{: .warning}


### C. Versioning

Starting with version 2.0, this specification follows [Semantic Versioning][semver]. See the note [Versioning of APIs][versioning] for details regarding how this is implemented.

### D. Acknowledgements

Many thanks to the members of the [IIIF][iiif-community] for their continuous engagement, innovative ideas and feedback.

### E. Change Log

| Date       | Description           |
| ---------- | --------------------- |
| XXXX-YY-ZZ | Version 3.0 |
| 2017-06-09 | Version 2.1.1 [View change log][prezi-change-log-30] |
| 2016-05-12 | Version 2.1 (Hinty McHintface) [View change log][prezi-change-log-21] |
| 2014-09-11 | Version 2.0 (Triumphant Giraffe) [View change log][prezi-change-log-20] |
| 2013-08-26 | Version 1.0 (unnamed) |
| 2013-06-14 | Version 0.9 (unnamed) |

{% include acronyms.md %}
{% include links.md %}