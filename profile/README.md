# Resonator

P2P protocol for discovery, communication and distributed computing

```
  Station (UI)          Station (UI)
      |                     |
   Antenna               Antenna
      |                     |
   Carrier ---[ Tox ]--- Carrier
```

## Layers

### Carrier `C`

**Encrypted P2P transport**

A 30 KB static C library and streaming CLI built on the
[Tox protocol](https://en.wikipedia.org/wiki/Tox_(protocol)).
Encrypted peer-to-peer tunnels with automatic NAT traversal and
peer discovery. The wire protocol is RDF Turtle -- every message
is a set of triples. Extra triples pass through untouched, so the
protocol is extensible without versioning. Supports text, files,
audio/video calls, groups, and raw binary pipe mode. Embeddable
in any language via C FFI.

### Antenna `Rust`

**RDF stream processor and triplestore**

An embedded RDF triplestore (Oxigraph) with a reactive compute layer.
Turtle in, Turtle out. Data flows through a DAG of JavaScript
scripts (QuickJS) connected by named channels with clock signals --
no polling. Queries and mutations use the SPIN vocabulary, so they
are themselves RDF (`sp:Select`, `sp:InsertData`,
etc.). The pipeline definition is also live RDF -- queryable and
hot-reloadable at runtime via the same SPARQL interface it serves.

### Station `Flutter`

**Fractal zoom UI**

Cross-platform UI (iOS, macOS, web) with a continuous fractal zoom
viewport. Objects appear as icons, cards, or full views depending
on zoom scale. Portals map remote spaces into local coordinates --
no teleport, just zoom through. Only renders what is visible, so
rendering cost stays constant regardless of universe size.

## Concepts

### Storage

**Everything is RDF triples**

All data is stored as RDF triples: messages, scripts, pipeline
definitions, UI state. Any peer can add statements about any
resource without coordination (open-world semantics). Graphs from
multiple sources merge naturally. Vocabularies are RDF too, and
can be extended at runtime. No schema migrations -- new predicates
just appear.

### Querying

**SPARQL via the SPIN vocabulary**

Queries are not a separate API. They are RDF documents sent on
the same channel as data. A `sp:Select` triple carries
a SPARQL query; Antenna evaluates it and emits result triples.
`sp:Ask`, `sp:Construct`,
`sp:InsertData`, `sp:DeleteData` and
`sp:Modify` cover the full CRUD surface. Because
queries are data, scripts can generate and chain them.

### Exchange

**Channels with clock signals**

Data moves through named channels that carry Turtle RDF strings.
Each channel has a clock file descriptor that wakes subscribers
when new data arrives -- no polling. Scripts wire into the DAG
via `antenna:in` / `antenna:out` connections,
forming fan-in/fan-out topologies. Any process that can read and
write Turtle can participate: sensors, audio, mesh radios, other
Antennas, or Carrier peers across the network.
