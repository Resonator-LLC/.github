# Resonator

Peer-to-peer network where the wire language is RDF.

```
    app / rsntr                          app / rsntr
         |                                    |
   +-------------+                      +-------------+
   |   SPARQL    |                      |   SPARQL    |
   |   SQLite    |                      |   SQLite    |
   +-------------+                      +-------------+
         |                                    |
        iroh -------[ RDF envelope ]-------- iroh
```

There is no server and no central feed. A node's identity is an ed25519
keypair; two nodes that have each other's public key can talk.

## The node `Rust`

**SQLite, peer-to-peer, and SPARQL over both**

Storage is SQLite. Transport is [iroh](https://iroh.computer) over QUIC, which
proves both identities and opens the channel. SPARQL executes over that SQLite,
so the same data is reachable as rows or as triples.

It ships as Rust crates and is meant to be embedded — which is how the iOS and
Android clients are built. There is a Python package (pyo3/maturin) for
notebook use, and a mod system for extending a node.

## rsntr `CLI`

**The console tool**

Multi-platform, exposing every function of a node. Built to sit inside shell
pipelines and to be comfortable for LLM agents, and it serves the web
interface.

## The envelope `protocol`

**Engine-neutral wire, engine-specific payload**

Every message on the network is an RDF object serialised as Turtle. A query is
also an RDF object, carrying its engine-specific text as a literal:

```turtle
[] a rsntr:Query ;
   rsntr:mod    "sql-sqlite" ;
   rsntr:signal "SELECT name, seen FROM _peers ORDER BY seen DESC" .
```

That split is the point. The wire language is shared, so every peer must speak
it; the query payload stays engine-specific, so a Postgres or DuckDB node can
join, answer through its own authenticator, and ask questions of its own.
Peers speak the payload families they understand and render what they can.

Transport sits behind a trait. iroh is the current base; Bluetooth and radio
are the reason the seam exists.

## Repositories

| | |
|---|---|
| [rsntr](https://github.com/Resonator-LLC/rsntr) | The v3 workspace: core crates, the console tool, the Python package, mobile FFI. |

`carrier` and `antenna` were the v2 architecture — a C transport over Tox and a
JavaScript stream processor. They are archived and no longer developed. The
[history page](https://resonator.network/docs/history.html) covers how v2
became v3.

## Licensing

Dual licensed under **Apache-2.0 or MIT**, at your option. That covers the whole
workspace: the node, `rsntr`, the Python package, the mod PDK and the example
mods. Nothing here requires you to open source what you build on it, run it as a
service, or embed it in a closed product. No CLA.

The permissive choice is deliberate: a network is worth what its reach is worth,
so third-party nodes, clients, mods and embedders are the point.

## Links

- [resonator.network](https://resonator.network) — protocol and documentation
- [resonator.am](https://resonator.am) — the company, and what else we build
