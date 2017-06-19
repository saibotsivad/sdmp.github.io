---
version: 0.13
layout: spec
title: Network Traffic
subtitle: Communicating over a network using established session keys.
---

Since the protocol is network agnostic, the specifications for
network traffic describe particular limitations that must be
honored for any implementation.

---

## Traffic is Encrypted

After the [shared key](../network-session) is established, all
future network traffic *must* be encrypted using that key.

Specifically, the [key fingerprint](../cryptography#key-fingerprint)
or any other information or metadata identifying a node must not be
exposed as metadata on the network traffic.

Examples:

* In a TCP implementation, network datagrams must not contain the key
	fingerprint of the node.
* In an HTTP implementation, HTTP headers describing the node software
	version are not allowed.

---

## Network Communication

An *encrypted network object* is a [container](../../core/container)
object where the **only** specified schema is the
[encrypted](../../core/encrypted) schema, and **no**
other metadata is allowed.

Specifically, the [key fingerprint](../../core/cryptography#key-fingerprint)
or any other information about the node initiating the connection
must **not** be exposed in this object.

The [UTF-8][w_utf8] encoded string representation of the JSON object
is sent as the data stream for all network traffic after the session
key is established.

E.g., the data stream sent is the output of the function:

	UTF8_ENCODE(JSON.stringify(Encrypted Container))

Specific network implementations may describe additional encoding
or transforms, such as requiring gzip on the data.

---

## No Additional Data

Placing *any* additional unencrypted information or metadata 
in the containers beyond what is specified by the specific schemas
is considered an error.

If this state is detected at any point, the network connection *must*
be rejected.

---

## Network traffic

After both nodes have completed the [handshake](../handshake), they
have established a secure shared session key. At this point, all future
connection network traffic *must* be encrypted using this established key.
