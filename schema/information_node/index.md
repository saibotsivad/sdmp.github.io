---
layout: schema
title: information_node
subtitle: Published information about a node, such as name and IP address.
---


Nodes are an application-level identity, authorized by a user to host or
publish resources on behalf of the user. A node might be an application
running on a server, or running on a phone. This resource contains the
information necessary to connect two nodes together.

---

## Schema: `information_node` ([JSON Schema][schema])

Information about the node is not published in the [identity](/schema/identity)
resource, it is published in this object. Primarily, this object holds the
IP and port used to connect to the node.

This object contains the following properties:

###### `information` *(object)*

Object containing information about the node.

###### `information.type` *(string)*

Must be the exact string: `node`

###### `information.name` *(string)*

A human-readable description of the node.

###### `information.ips` *(array of strings, required)*

Each array element is a string representation of the IP address *and* port at which the
node is reachable.

This address may be IPv4 or IPv6, and the port is **required**.


[schema]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/information_node.json