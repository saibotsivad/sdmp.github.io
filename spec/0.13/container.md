---
version: 0.13
layout: spec
title: Container
subtitle: Extendable, defined JSON object.
---


Within the SDMP, the container is a [JSON object](http://json.org/)
which contains metadata describing itself programmatically. The
container and container metadata is defined using the
[JSON Schema](http://json-schema.org/) syntax.

Applications extend the base container object by specifying additional
JSON schemas within the container.

---

## Reserved Property

The protocol reserves the root property `sdmp` of the container. This
property describes the overall JSON object. Applications extending the
protocol should not add properties to this property.

---

## Container Extension

A container object is "extended" by specifying a valid
[JSON Schema](http://json-schema.org/) with the `sdmp.schemas`
property.

The `sdmp.schemas` property is a map, where the map key is
the property name at the container object root used to store
the data, and the map value is a schema reference.

---

## Property Name

Property names may be any valid UTF-8 string, but should be
constrained to short values, and may not be the reserved
value `sdmp`.

A container extension may not specify a map key: the key is
named by the application generating the container.

A pseudo example shows multiple container extensions being used:

```txt
{
	"sdmp": {
		"schemas": {
			"anyname": "sdmp://GlvAreTo...",
			"0": "message"
		}
	},
	"anyname": [ "some", "data" ],
	"0": {
		"message": "text content"
	}
}
```

---

## Schema Reference

The schema reference in the container `sdmp.schemas` property must
be either a complete [resource URI](../resource#resource-uri) or
the exact name of any of the [core schema names](../schema).

---

# Undefined Properties

A container is considered invalid if there is a property at the
root of the container that is not named in the `sdmp.schemas` map,
other than the `sdmp` property.

A pseudo example of an invalid container is (the `anyname` property
is not listed in `sdmp.schemas`):

```txt
{
	"sdmp": {
		"schemas": {
			"0": "message"
		}
	},
	"anyname": [ "some", "data" ],
	"0": {
		"message": "text content"
	}
}
```

---

## Validation

The container may be validated using the
[JSON Schema method](http://json-schema.org/latest/json-schema-validation.html)
of object validation.

Validation follows these steps:

* The container JSON object is first validated against the
	[container schema](../schema/container).
* For each property name of the container root, excluding
	the `sdmp` property, the schema referenced is located
	and the property is validated with that schema.

If any of the following are true, the container object must be considered invalid:

* The container itself is considered invalid according to
	the [container schema](../schema/container).
* Any value in the `sdmp.schemas` map is an invalid
	[SDMP resource URI](../resource#resource-uri), and is
	also not a [core schema name](../schema).
* Any schema specified within the container schema is not available
	to the application validating the container.
* The container object does not have a property with the same name
	as the map key.
* For any schema listed, the corresponding container property is
	considered invalid according to that schema.
