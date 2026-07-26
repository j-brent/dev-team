# Graph Visualization Learnings

## [2026-07-26] Lesson: Validate the edge set on a real corpus before designing the view that renders it
**ID**: c7e4a1b9
**Category**: domain
**Context**: When building any relationship-first visualization -- code maps, dependency graphs, entity diagrams -- where the edges come from an extraction step rather than from data that already exists as a graph.
**Learning**: Design the *encoding* only after measuring the edge set on a corpus the size of the real target. A small hand-built fixture will show a healthy-looking graph because the fixture author connected everything they put in it; a real codebase will not. Measure two numbers before committing to a relationship-first default view: edge count relative to node count, and the fraction of nodes touching any edge. If most nodes are isolated, a relationship view is the wrong default no matter how good the layout is, and filtering it down is treating a symptom -- it makes the wrong graph smaller, not more meaningful. Also check what the edges *mean*: an extractor that string-matches type names inside signature text produces edges that look real in aggregate and cannot answer any specific question.
**Evidence**: CodeAtlas C++ plan-01 Part C. Five redesign steps (relationship-first default, topology layout, encoding, importance sizing, labels) shipped against a 17-node hand-built fixture where everything looked right. First render against FTXUI (1,901 symbols, real clangd extraction): 446 nodes and 79 edges in one scene, with only 12% of nodes touching an edge, fitting the viewport at 24% zoom. The `uses-iface`/`uses-impl` edges turned out to be type names matched inside `detail` strings -- no call graph, no data flow. The plan had named FTXUI as a test corpus from day one; nobody rendered it until after the redesign was built. The engine/producer seam built in the same phase is what makes the model rewrite affordable, so the phase was not wasted -- but the two steps that depended on edge quality were built on an untested premise.
**Confidence**: high
**Validations**: 1
**Projects**: codeatlas-cpp
