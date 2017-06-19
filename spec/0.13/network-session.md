---
version: 0.13
layout: spec
title: Network Session
subtitle: Establishing and using a session key for network communications.
---


The handshake described below establishes a session-unique key in
a *network-agnostic* manner. This handshake procedure should be
followed for all implementations.

---

## Identity Knowledge Required

The handshake assumes that both parties have, using some
other mechanism, obtained and verified the public key used
by the other. Therefore, the handshake algorithm does not
concern itself with the normal key/certificate chain that
occurs in TLS/SSL connections.

---

## Using Diffie-Hellman

Although each party has the other parties public key, in order
to establish [forward secrecy][w_forward], the SDMP requires use
of the [Diffie-Hellman][w_dh] key exchange as the way to establish
a session key for a connection.

---

## Handshake

The Diffie-Hellman exchange required by the protocol is:

### 1: Send Session Creation Object

1. The node initiating the connection sends the receiving node a
  [session creation object](#session-creation-object).
2. On receiving,
  - *A.* If the receiving node accepts the session key, it will
    respond with its own session creation object and proceed
    to the next step of the handshake.
  - *B.* If the receiving node does not accept the session key, it
    will respond with an appropriate [response object](#session-rejection)
    and then terminate the handshake.

### 2: Verify Signatures

At this point, both nodes have each others session creation
object, therefore each node has enough information to complete
the Diffie-Hellman shared key calculation.

All following network traffic, including the finalization of this
handshake, must be sent as [encrypted objects](../network-traffic),
encrypted using this calculated key.

1. The initiating node sends the [signed session](#session-validation-object)
  to the receiving node, proving that the initiating node is who
  it claims to be.
2. On receiving,
  - *A.* If the receiving node can verify trust in the initiating node, it
    sends a session signature object to the initiating node, proving that
    the receiving node is who it claims to be.
  - *B.* If the receiving node cannot verify trust, or otherwise rejects
    the connection, it responds to the initiating node with an appropriate
    [response object](#session-rejection).

At this point each node has established the identity of the other node,
and has proof that the calculated session key was established by the
identified node.

---

## Session Creation Object

The *session creation object* is a [container](../container)
object where the *only* specified schema is the
[shared key](../schema/shared-key) schema, and no
other metadata is allowed.

Specifically, the [key fingerprint](../cryptography#key-fingerprint)
or any other information potentially identifying the node initiating
the connection must *not* be exposed in this object. This is done
to preserve privacy while using unsecured networks.

The [UTF-8][w_utf8] encoded string representation of the JSON object
is sent as the data stream for session creation.

E.g., the data stream sent is the output of the function:

	UTF8_ENCODE(JSON.stringify(Session Creation Object))

---

## Session Rejection

If the initiating or receiving node rejects a session creation
object, or does not recognize the initiating node as a trusted
node, it *should* transmit a rejection response prior to dropping
the connection.

The rejection response must be a [container object](../container)
where the only specified schema is the [response](../schema/response)
schema, and *no* other metadata is allowed in the container.

Specifically, the [key fingerprint](../cryptography#key-fingerprint)
or any other information potentially identifying the node must *not*
be exposed in this object.

---

## Session Validation Object

The *session validation object* is a [container](../container) object where
the *only* specified schema is the [signature](../schema/signature) schema,
and no other metadata is allowed in the container.

The payload of the signature object is the previously exchanged session
connection object.

When the node sends a signature object with the payload being that nodes
session connection object, it demonstrates the the calculated session key
was actually initiated by the actual node, and not a man-in-the-middle
attacker.

---

## No Additional Data

Placing *any* additional information or metadata in the
[session creation object](#session-creation-object) beyond what
is specified in the object schema is considered an error.

If this state is detected at any point, the network connection *must*
be rejected.

---

## Connection Validation

When a node receives a [session creation object](#session-creation-object), if
the following circumstances are detected the connection *must* be rejected:

* If the node cannot verify the signature in the object for any reason,
	including but not limited to:
	- malformed data,
	- an improperly generated signature,
	- the node does not have the public key of the node which
		generated the object.
* If the node does not accept any values of the [shared-key](../schema/shared-key)
  for any reason. (Such as a non-unique key being used.)

[w_dh]: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
[w_forward]: https://en.wikipedia.org/wiki/Forward_secrecy
[w_utf8]: https://en.wikipedia.org/wiki/UTF-8
