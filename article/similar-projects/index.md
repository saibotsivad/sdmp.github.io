---
layout: article
title: Similar Projects
subtitle: Compare this project to similar projects.
---


This article seeks to answer the question: how does
the SDMP compare to other similar protocols or projects.

---

The SDMP is:

* a language and network agnostic protocol,
* used to distribute encrypted messages between users,
* without requiring always-on servers (e.g. IMAP),
* and simple enough that humans can understand it.

Other projects have started since this one began, sometime back
in 2012, and some are working on a similar problem, so this is
an attempt to catalog the differences between similar projects.

> *Note:*
>
> If you know of another project not mentioned in here that you
> think is working towards similar goals, please [file an issue][issue]
> and it'll probably get added to this list!

---

## Not a Protocol

Most projects in cryptography seem to start as whitepapers, then
software development. Note that the missing point is the development
of a language agnostic protocol.

For example, a popular bit of software in the JS world is
[socket.io](http://socket.io/), which is a really easy way
to communicate using [websockets](https://developer.mozilla.org/en-US/docs/Glossary/WebSockets).
However, `socket.io` started as a JS library, so that when
developers wanted to implement it in Java or C# or Swift,
they essentially had to reverse-engineer the protocol used.

This isn't meant to excoriate socket.io--there are *many*
valid reasons that developers start with the software and
hammer out the protocol specifics later.

However, one of the main goals of the SDMP is to create
a language agnostic protocol, such that developers can
feel safe creating implementations in whatever language
they are using.

Most projects similar in goals to the SDMP were rejected
because they start with software, not with specifications.

---

## IMAP

As far as existing technology goes, IMAP is undoubtedly
the most well known messaging protocol.

However, IMAP messaging is very much unsecure: users
typically send messages in unencrypted plaintext, and
even if the message itself is encrypted, metadata is
attached to the sent data, potentially exposing private
information from the sender.

Although many attempts have been made to modify the
protocol to be more secure, it still relies on "central"
servers.

The SDMP makes peer-to-peer the default, and always-on
servers unnecessary.

---

## [Keybase](https://keybase.io/)

Keybase has similar goals of making it easier to find
and user another users public key. Their stated goal is:

> Get a public key, safely, starting just with someone's
> social media username(s).

For example, given a Twitter or Github username, you can
find their [account on Keybase](https://keybase.io/saibotsivad),
and have a reasonable assurance that the public key obtained
there is secure.

Keybase is really cool, and I highly recommend checking
it out, but it is not a secure messaging protocol.

---

## [Secure Scuttlebutt](https://ssbc.github.io/)

Scuttlebutt shares many similar goals: it uses p2p to
distribute data, it has concepts of users vs application,
and it uses solid cryptography underneath.

Unfortunately, it appears that Scuttlebutt has also
started with a whitepaper-to-software approach, and
does not have clear specifications for implementation
in other languages.

This means that implementing Scuttlebutt in some other
language, e.g. C#, will require reverse engineering.

I'll be reading up on this project more, but for now
it does not meet the requirements of the SDMP.
