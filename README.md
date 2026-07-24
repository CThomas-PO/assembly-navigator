# Assembly Navigator

An interactive prototype that pairs a **3D product viewer** with a **hierarchical assembly navigator**, built to demonstrate three new Navigator capabilities: **Filtering**, **Column Configuration**, and **Scroll to Item**.

Select a part in the 3D model and its row highlights in the tree; filter the tree and the matching geometry stays in focus; right-click a component in the viewer and jump straight to it in the list. The whole thing is a single self-contained HTML file — no build step, no install.

> **Note:** This is a demonstration prototype. It uses a bicycle assembly as a stand-in for the kind of complex product structure engineers navigate in professional CAD/PLM tooling. The goal is to communicate feature intent and interaction design, not to ship production code.

<!-- Recommended: drop a screenshot or short GIF here — it's the fastest way to convey what the prototype does. -->
<!-- ![Assembly Navigator](docs/preview.gif) -->

---

## Why this exists

Product structure trees in engineering software can run to thousands of components. Finding the right part, tailoring the columns you care about, and staying oriented between a 3D model and its bill of materials are everyday friction points. This prototype explores three focused improvements to that experience and, importantly, makes them *tangible* — something a stakeholder can click through in a browser rather than imagine from a spec.

It was built as a working prototype to pressure-test the interaction design of each feature before committing engineering effort.

---

## The three features

### 1. Filtering

A single search box that understands both plain terms and field-scoped filters, combined with AND logic.

| Syntax | What it does |
| --- | --- |
| `carbon` | Match the term against any field (name, ID, material, geometry, specification, description) |
| `"brake pads"` | Match an exact phrase |
| `spoke*` | Wildcard match (`*` matches any characters) |
| `:material aluminum` | Match by material only |
| `:geometry <type>` | Match by geometry type only |
| `:visible` | Show only visible (not hidden) parts |
| `:hidden` | Show only parts that have been hidden |
| `:material steel spoke*` | Combine clauses — every clause must match |

A filter-type dropdown next to the search box inserts the scoped prefixes for you. Results can be shown as a **flat list** or as a **tree** that keeps matches in the context of their parent assemblies (ancestors auto-expand). A clearable "Filtered" badge and a live count keep you oriented.

### 2. Column Configuration

The navigator ships showing just **Name** and **Qty** so it fits a narrow panel, and lets you tailor the rest:

- **Show / hide columns** via a Configure Columns dialog — choose from ID, Material, Geometry, Specification, and Description (Name stays pinned).
- **Reorder columns** by dragging the headers.
- **Resize columns** by dragging the edge, or **double-click** the edge to auto-fit the column to its content.
- Your column choices and order **persist** between sessions.

### 3. Scroll to Item

Right-click any part in the 3D viewer to bring up a context menu with **Scroll to item**. The navigator then selects that part, expands whatever assemblies contain it, reopens the panel if it was collapsed, and smoothly scrolls the row into view — closing the loop from "I can see it in the model" to "I've found it in the structure."

---

## Bonus interactions

Beyond the three headline features, the prototype wires up the details that make it feel real:

- **Two-way selection & hover** — selecting or hovering a part highlights it in both the 3D scene (selected = amber, hovered = blue, everything else dimmed) and the table.
- **Show / hide parts** — the eye toggle on each row hides that component and its children from the 3D view; hidden state persists.
- **Multi-select** — Ctrl/Cmd-click to add, Shift-click for ranges.
- **Resizable, collapsible panel** — drag the panel edge to resize, collapse it to give the viewer full width, and reopen from the edge tab.

---

## Running it

No dependencies to install and no build step. Two options:

**Open it directly** — double-click `assembly-navigator.html` (or drag it into a browser). An internet connection is required, since the 3D library and model load from CDNs.

**Or serve it locally** (most reliable across browsers):

```bash
# from the repo folder
python3 -m http.server 8000
# then open http://localhost:8000/assembly-navigator.html
```

Use a modern desktop browser (Chrome, Edge, Firefox, or Safari). Orbit the model by dragging, zoom with the scroll wheel.

---

## How it's built

| Aspect | Detail |
| --- | --- |
| **Structure** | One self-contained HTML file — markup, styles, and logic together |
| **3D rendering** | [three.js](https://threejs.org/) r160 with `OrbitControls`, `GLTFLoader`, `DRACOLoader`, and a `RoomEnvironment` for image-based lighting |
| **Model** | A Draco-compressed `.glb` bicycle model loaded at runtime |
| **Navigator** | Vanilla JavaScript — no framework — rendering the assembly tree from a JSON data structure |
| **Sync layer** | The 3D viewer and the navigator are decoupled and communicate through DOM `CustomEvent`s (`asm-select`, `nav-hover`, `3d-select`, `3d-contextmenu`, and friends), so neither side needs to know the other's internals |
| **Persistence** | Column configuration and hidden-part state are saved to `localStorage` |

### Data model

The assembly is defined as a nested JSON object at the top of the script. Each component carries an `id`, `name`, `quantity`, optional `material` / `specification`, a `mesh` name that maps the row to its geometry in the 3D model, and an array of `sub_components`. Swapping in a different product is a matter of replacing that object and matching the `mesh` names to the model.

---

## Known limitations

This is a prototype, so a few things are deliberately scoped down:

- The assembly data and 3D model are fixed demo content, not connected to a live data source.
- Filtering, columns, and persistence run entirely client-side in the browser.
- It's designed for modern desktop browsers; small-screen layouts aren't a focus.

---

## Credits

3D bicycle model from [Kirilbt/bike-demo-threejs](https://github.com/Kirilbt/bike-demo-threejs). Rendering by [three.js](https://threejs.org/).
