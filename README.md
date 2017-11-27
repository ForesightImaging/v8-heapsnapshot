# v8-heapsnapshot
A npm module to parse and work with v8's heapsnapshots.

For more information about heapsnapshots see [here](https://developers.google.com/web/tools/chrome-devtools/memory-problems/heap-snapshots)

# Install

Just add the package `v8-heapsnapshot` to your dependencies.

~~~~~
npm install --save v8-heapsnapshot
~~~~~

# How to use

JavaScript:

~~~~~{.js}
var v8hs = require("v8-heapsnapshot")

v8hs.parseSnapshotFromFile()
    .then(function(snapshot) {
        // all nodes
        snapshot.nodes

        // all edges
        snapshot.edges

        // the global object
        snapshot.global

        // a list of loaded modules
        snapshot.modules

        // find node by id
        snapshot.findNodeById(id)
    })
~~~~~

TypeScript:


~~~~~{.ts}
import {parseSnapshotFromFile} from 'v8-heapsnapshot'

// ...

const snapshot = await v8hs.parseSnapshotFromFile()

// all nodes
snapshot.nodes

// all edges
snapshot.edges

// the global object
snapshot.global

// a list of loaded modules
snapshot.modules

// find node by id
snapshot.findNodeById(id)
~~~~~

# API

## Parse a Snapshot

Parse the Snapshot with the data already in memory:

~~~~
async function parseSnapshot(stream: fs.ReadStream): Promise<Snapshot>
async function parseSnapshot(json: string): Promise<Snapshot>
async function parseSnapshot(obj: Object): Promise<Snapshot>
~~~~

Read a file and parse the Snapshot:

~~~~
async function parseSnapshotFromFile(filename: fs.PathLike, options?: /* same option type that fs.createReadStream takes*/): Promise<Snapshot>
~~~~

## Work with a Snapshot

A snapshot consists of nodes and edges.
The node of the global object (name: `global / `) and the list of modules (name: `Module`) are already found.
Use `findNodeById` to find a node by its id.

~~~~
interface Snapshot {
    readonly nodes: Node[]
    readonly edges: Edge[]

    readonly global: Node
    readonly modules: Node[]

    findNodeById(id: number): Node | undefined
}
~~~~

You can then go through the snapshot graph:

~~~~
export interface Node {
    readonly type: NodeType
    readonly name: string
    readonly id: number
    readonly self_size: number
    readonly edge_count: number
    readonly trace_node_id: number

    readonly out_edges: Edge[]
    readonly in_edges: Edge[]

    toLongString(): string

    // print the node and `deep` reffered nodes to console
    print(deep?: number): void
}

export interface Edge {
    readonly type: EdgeType
    readonly name: string | number
    readonly from: Node
    readonly to: Node

    toLongString(): string
}
~~~~

