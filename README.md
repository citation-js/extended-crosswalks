# extended-crosswalks
(Attempt at) a generic JSON crosswalk format with support for value transformations and linked data. First of all, some definitions when it comes to describing mappings. Then, some requirements needed for Citation.js. Finally, a comparison of existing methods for maintaining crosswalks.

Feedback or ideas are welcome in the [Issue Tracker](https://github.com/citation-js/extended-crosswalks/issues).

## Requirements

  - **Reversible.**
  - **Flattening and nesting linked data.** For example, CSL-JSON is completely flat apart from date and name values; instead of having an `original` property linking to a resource, it has `original-title`, `original-author`, `original-author`, etc. The crosswalk format should support not only generic property transformations but flattening and nesting resources.
  - **One-to-many mappings.** For example, BibTeX has `year`, `month`, `day` while CSL-JSON has `issued`. It should support combining them all one way, and splitting them apart the other way.
  - **All-to-one mappings.** More extensive formats like the MARC family may have multiple title properties. It should support mapping all to one one way, and mapping one to the most generic in the other direction.
  - **Conditional mappings.** Generally, the CSL-JSON `author` property corresponds to the BibJSON `author` property. However, if the resource is a review, the CSL-JSON `author` property corresponds to the BibJSON `reviewer` property, while the BibJSON `author` property corresponds to the CSL-JSON `reviewed-author` property.
  - **Value mappings.** Resource type mappings, and other value mappings if so needed, should be supported.
  - **Value transformations.** The BibTeX `author` property is a ` and `-delimited list of plain-text names, while the Citation.js `author` property is an array of objects with `given`, `family` and other properties.

### Wishlist

  - **(Mostly) system-agnostic.** Value transformations (and other parts) should be mostly JSON (or JSON-LD), possibly with Regex parts. For common value transformations like name and date parsing, built-in converters could be set up which should be provided by the runtime.
  - **Provenance.** Record the quality of mappings, both in terms of the field mapping and the value conversion, and output an object with provenance information.
  - **Schema support.** For better integration with other systems, include some basic schema URI prefixes for types and fields.

## Prior art

### CodeMeta

[CodeMeta](https://codemeta.github.io) "provides an explicit map between the metadata fields used by a broad range of software repositories, registries and archives." They use a common set of properties and types, mostly from schema.org but with a few custom ones. The way one format can be converted to the other, is by first *expanding* it with the CodeMeta `@context` mapped to properties in the source format, followed by *compressing* it with a `@context` mapped to the target format.

This only solves a few of the requirements, since it only allows one-to-one mappings and does not account for complex value transformations and possibly not for flattening and nesting linked data either.

### XSLT

[XSLT](https://www.w3.org/TR/xslt) is a common way of creating crosswalks between XML formats. However, it is XML and not JSON. Creating a JSON representation for XSLT would be interesting (and would have the added benefit of there being a host of mappings readily available), but for new mappings it might be a bit overkill.

### `Translator` experiment

As an experiment, some peripheral Citation.js plugins have been developed with a [`Translator`](https://github.com/citation-js/plugin-software-formats/blob/v0.3.1/src/translator.js) class, which transforms an annotated list of property mappings to a two-way conversion function. Additionally, the core RIS plugin has been developed with a limited, one-way prototype.

Disregarding the first prototype, [the annotated list of property mappings](https://github.com/citation-js/plugin-isbn/blob/v0.1.0/src/google-books.js#L8-L92) allows for quite some flexibility in transforming, and while some requirements do not have first-class support, they are still possible. However, that comes at the cost of being only partly declarative (JSON), and a big part imperative (JS, and so not system-agnostic). While the annotated mappings are detailed, for fully covering the range of requirements some improvements could be made.
