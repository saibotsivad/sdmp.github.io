---
layout: docs
title: Container
subtitle: Containers used to publish resources and communicate between nodes.
---


When communicating between nodes and when publishing resources, the data is placed inside
a [JSON Web Signature (JWS)](http://self-issued.info/docs/draft-ietf-jose-json-web-signature.html)
object. This JSON object is the SDMP *container*, and must be used for all communication
and for all published content in the SDMP.

---

## Container Payload Object

The [JWS payload](http://self-issued.info/docs/draft-ietf-jose-json-web-signature.html#rfc.section.2)
is a string. This string is a JSON object which has been
[stringified](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify),
and whose octets are [unpadded base64url](https://tools.ietf.org/html/rfc4648#section-5) encoded.

This JSON object is referred to as the *container payload object*, or simply *payload object*.

---

## Payload Schema

The payload object is defined using [JSON Schema](http://json-schema.org/), and the different
schema definitions are available [here](https://github.com/sdmp/sdmp-schema). This document
discusses the meaning of the different fields in a human readable format.

*All* payload objects are JSON objects which must conform to the
[payload schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/payload.json),
which has the following properties:

###### `sdmp` *(object, required)*

The root property of the object, which describes the payload object.

###### `sdmp.version` *(string, required)*

The version of the SDMP specifications used to generate and validate the payload
object. The property must be a valid [semver number](http://semver.org/).

###### `sdmp.publisher` *(string, required)*

The [key fingerprint](../cryptography/#key-fingerprint) of the user or node that generated
and signed the container.

###### `sdmp.created` *(string, required)*

Timestamp the container was created. The time must be the local time of the publisher,
translated to UTC. The timestamp is [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)
formatted, with granularity to the millisecond.

E.g., `2015-07-26T15:48:37.703Z`.

###### `sdmp.schemas` *(array of strings, required)*

An ordered array of strings. Each array element of this property must be *either* a
valid [URI](https://tools.ietf.org/html/rfc3986), which should resolve to a valid
[JSON schema file](http://json-schema.org/), *or* one of the following exact
reserved strings:

* `connection`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/connection.json),
  [specs](../session/#connection))
* `encrypted`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/encrypted.json),
  [specs](../resource/#encrypted))
* `identity`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/identity.json),
  [specs](../resource/#identity))
* `journal_request`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/journal_request.json),
  [specs](../synchronization/#journal-request))
* `journal_update`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/journal_update.json),
  [specs](../synchronization/#journal-update))
* `message`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/message.json),
  [specs](../resource/#message))
* `message_receipt`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/message_receipt.json),
  [specs](../resource/#message-receipt))
* `node_information`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/node_information.json),
  [specs](../resource/#node-information))
* `post`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/post.json),
  [specs](../resource/#post))
* `relationship`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/relationship.json),
  [specs](../resource/#relationship))
* `resource`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/resource.json),
  [specs](../resource/#resource))
* `resource_request`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/resource_request.json),
  [specs](../synchronization/#resource-request))
* `resource_response`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/resource_response.json),
  [specs](../synchronization/#resource-response))
* `user_information`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/user_information.json),
  [specs](../resource/#user-information))

---

## Validating Payloads

The payload object is first validated using the
[payload schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/payload.json),
after which all schemas referenced in the `sdmp.schemas` object are used to
validate the message object, in the order they are found in the array.