# Graph Report - .  (2026-06-26)

## Corpus Check
- Corpus is ~44,603 words - fits in a single context window. You may not need a graph.

## Summary
- 10 nodes · 11 edges · 3 communities (2 shown, 1 thin omitted)
- Extraction: 91% EXTRACTED · 9% INFERRED · 0% AMBIGUOUS · INFERRED: 1 edges (avg confidence: 0.85)
- Token cost: 0 input · 0 output

## Community Hubs (Navigation)
- [[_COMMUNITY_Brand Identity Design|Brand Identity Design]]
- [[_COMMUNITY_Project Assets|Project Assets]]
- [[_COMMUNITY_Visual Style|Visual Style]]

## God Nodes (most connected - your core abstractions)
1. `AGENTIC OS Brand Identity` - 7 edges
2. `AgenticOS Banner Image` - 3 edges
3. `Circular A Logo Symbol` - 3 edges
4. `High-Contrast Futuristic Color Palette` - 2 edges
5. `Neon-Blue Glow Effects` - 2 edges
6. `AgenticOS README` - 1 edges
7. `Horizontal Logo-Text Layout` - 1 edges
8. `Sans-Serif Bold Typography` - 1 edges
9. `Dark Background Negative Space` - 1 edges
10. `Tech-Innovation Positioning Strategy` - 1 edges

## Surprising Connections (you probably didn't know these)
- `AgenticOS README` --references--> `AgenticOS Banner Image`  [EXTRACTED]
  README.md → banner-agentic-os.png

## Import Cycles
- None detected.

## Hyperedges (group relationships)
- **AGENTIC OS Visual Identity System** — agentic_os_circular_logo, agentic_os_color_palette, agentic_os_sans_serif_typography, agentic_os_neon_glow_effect, agentic_os_horizontal_layout [EXTRACTED 1.00]

## Communities (3 total, 1 thin omitted)

### Community 0 - "Brand Identity Design"
Cohesion: 0.40
Nodes (5): AGENTIC OS Brand Identity, Horizontal Logo-Text Layout, Dark Background Negative Space, Tech-Innovation Positioning Strategy, Sans-Serif Bold Typography

### Community 1 - "Project Assets"
Cohesion: 0.67
Nodes (3): AgenticOS Banner Image, Circular A Logo Symbol, AgenticOS README

## Knowledge Gaps
- **1 isolated node(s):** `AgenticOS README`
  These have ≤1 connection - possible missing edges or undocumented components.
- **1 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `AGENTIC OS Brand Identity` connect `Brand Identity Design` to `Project Assets`, `Visual Style`?**
  _High betweenness centrality (0.792) - this node is a cross-community bridge._
- **Why does `AgenticOS Banner Image` connect `Project Assets` to `Brand Identity Design`?**
  _High betweenness centrality (0.222) - this node is a cross-community bridge._
- **Why does `Circular A Logo Symbol` connect `Project Assets` to `Brand Identity Design`, `Visual Style`?**
  _High betweenness centrality (0.125) - this node is a cross-community bridge._
- **What connects `AgenticOS README`, `Horizontal Logo-Text Layout`, `Sans-Serif Bold Typography` to the rest of the system?**
  _5 weakly-connected nodes found - possible documentation gaps or missing edges._