---
version: 0.13
layout: spec
title: container
subtitle: Base JSON object for the protocol. Extendable, defined JSON object.
---


## JSON Properties

The JSON object of the container has the following required properties:

###### `sdmp` *(object, required)*

This root property of the object holds the metadata which describes the
rest of the object.

###### `sdmp.version` *(string, required)*

The version of the SDMP specifications used to define and validate the
properties of the container object.

This property must be a valid [semver](http://semver.org/) number
containing only the major and minor version.

Example: `0.13`

###### `sdmp.schemas` *(array, required)*

Each array element of this property must be a [UTF-8](http://www.utf-8.com/)
encoded string which must be either:

1. a valid [SDMP resource URI](../resource#resource-uri), which must
	resolve to a valid [JSON schema](http://json-schema.org/), or
2. one of the string values of the [core schema names](#core-schema-names).

---

## Example

Consider this example SDMP [resource URI](../resource/#resource-uri)
(shortened with ellipsis for readability):

	sdmp://GlvA...1A8Q/h6FW...usYA

If this example URI yields a valid [JSON schema](http://json-schema.org/):

	{
		"type": "object",
		"properties": {
			"hello": {
				"type": "string",
				"pattern": "^[a-z]+$"
			}
		}
	}

Then an example valid container might look like this (URI and property
name shortened with ellipsis for readability):

	{
		"sdmp": {
			"version": "0.10.6",
			"schemas": [
				"sdmp://GlvA...1A8Q/h6FW...usYA"
			]
		},
		"sdmp://GlvA...1A8Q/h6FW...usYA": {
			"hello": "world"
		}
	}

An example **invalid** container might look like this:

	{
		"sdmp": {
			"version": "0.10.6",
			"schemas": [
				"sdmp://GlvA...1A8Q/h6FW...usYA"
			]
		},
		"sdmp://GlvA...1A8Q/h6FW...usYA": {
			"hello": 123
		}
	}

> Note: This is invalid because the schema requires the value of
> the property `hello` to be a *string*.

Another example **invalid** container might look like this:

	{
		"sdmp": {
			"version": "0.10.6",
			"schemas": [
				"sdmp://GlvA...1A8Q/h6FW...usYA"
			]
		}
	}

> Note: This is invalid because the container object must contain
> a property for the specified schema.

And another example **invalid** container might look like this:

	{
		"sdmp": {
			"version": "0.10.6",
			"schemas": []
		},
		"sdmp://GlvA...1A8Q/h6FW...usYA": {
			"hello": "world"
		}
	}

> Note: This is invalid because all container properties other
> than `sdmp` must be listed in the `sdmp.schemas` list.

---

## JSON Schema

The exact [JSON Schema](http://json-schema.org/) for the container object is:

	{
		"$schema": "http://json-schema.org/draft-04/schema#",
		"type": "object",
		"properties": {
			"sdmp": {
				"type": "object",
				"properties": {
					"version": {
						"type": "string",
						"pattern": "^\\d+\\.\\d+$"
					},
					"schemas": {
						"type": "array",
						"items": {
							"type": "string"
						},
						"uniqueItems": true
					}
				},
				"required": [ "version", "schemas" ]
			}
		},
		"required": [ "sdmp" ]
	}
