libdns - Universal DNS provider APIs for Go <a href="https://pkg.go.dev/github.com/libdns/libdns"><img src="https://img.shields.io/badge/godoc-reference-blue.svg"></a>
===========================================

`libdns` is a collection of free-range DNS provider client implementations written in Go! With libdns packages, your Go program can manage DNS records across any supported providers.

**⚠️ Work-in-progress. Exported APIs are subject to change. More documentation is coming soon.**

This repository defines the core interfaces that providers should implement. They are small and idiomatic Go interfaces with well-defined semantics.

The interfaces include:

- `RecordGetter` to list records.
- `RecordAppender` to append new records.
- `RecordSetter` to set (create or change existing) records.
- `RecordDeleter` to delete records.

## Related projects

**[OctoDNS](https://github.com/github/octodns)** is a suite of tools written in Python for managing DNS. However, its approach is a bit heavy-handed when all you need are small, incremental changes to a zone:

> WARNING: OctoDNS assumes ownership of any domain you point it to. When you tell it to act it will do whatever is necessary to try and match up states including deleting any unexpected records. Be careful when playing around with OctoDNS. 

This is incredibly useful when you are maintaining your own zone file, but risky when you just need incremental changes.

**[StackExchange/dnscontrol](https://github.com/StackExchange/dnscontrol)** is written in Go, but is similar to OctoDNS in that it tends to obliterate your entire zone and replace it with your input. Again, this is very useful if you are maintaining your own master list of records, but doesn't do well for simply adding or removing records.

**[go-acme/lego](https://github.com/go-acme/lego)** has support for a huge number of DNS providers (75+!), but their APIs are only capable of setting and deleting TXT records for ACME challenges.

**`libdns`** takes inspiration from the above projects but aims for a more generally-useful set of APIs that homogenize pretty well across providers. In contrast to the above projects, libdns can add, set, delete, and get arbitrary records from a zone without obliterating it (although syncing up an entire zone is also possible!). Its APIs also include context so long-running calls can be cancelled early, for example to accommodate on-line config changes downstream. libdns interfaces are also smaller and more composable. Additionally, libdns can grow to support a nearly infinite number of DNS providers without added bloat, because each provider implementation is a separate Go module, which keeps your builds lean and fast.

In summary, the goal is that libdns providers can do what the above libraries/tools can do, but with more flexibility: they can create and delete TXT records for ACME challenges, they can replace entire zones, but they can also do incremental changes or simply read records.


## Record abstraction

How records are represented across providers varies widely, and each kind of record has different fields and semantics. In time, our goal is for the `libdns.Record` type to be able to represent most of them as concisely and simply as possible, with the interface methods able to deliver on most of the possible zone operations.

Realistically, libdns should enable most common record manipulations, but may not be able to fit absolutely 100% of all possibilities with DNS in a provider-agnostic way. That is probably OK; and given the wide varieties in DNS record types and provider APIs, it would be unreasonable to expect otherwise. We are not aiming for 100% fulfillment of 100% of users' requirements; more like 100% fulfillment of ~90% of users' requirements.
