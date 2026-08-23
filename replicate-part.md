# Replicating a Physical Part as a Printable Model

**How to use this file.** Point Claude at it — or paste it — at the start of any
conversation where you want a broken or existing physical object rebuilt as a
parametric, printable 3D model. It describes the *process*, not any particular
part, and is meant to be reused on unrelated objects.

Toolchain assumed: **OpenSCAD**, driven headlessly. Output: a parametric `.scad`
source, a verified `.stl`, and a dimensioned measurement record. Scope ends when
the part is ready to slice.

---

## Operating principles

These are the expensive lessons. They outrank convenience at every step.

**Measured, inferred, and assumed are three different things — label which.**
Every dimension in the model carries a comment saying where it came from. A
guessed value that looks authoritative is worse than an obvious blank, because
nothing downstream will flag it.

**Files bundled with a downloaded model describe someone else's part.** If the
user found something "close to mine" online, its README, photos, and geometry are
a *donor* — reference at most. Never reason from a donor's documentation about the
user's object, installation, or problem. Establish in the first exchange which
features differ from theirs.

**Photographs carry shape, not size.** No scale reference, perspective distortion,
unknown camera angle. Read form and topology from a photo; never a dimension.

**Verify by parsing, not by looking.** A render can look perfect while the export
is the wrong size or the wrong variant. Check the actual file.

**Ask what failed before strengthening anything.** The instinct to thicken the
weak-looking section is usually wrong. Parts fail from print settings, material
degradation, or installation load far more often than from an undersized section.
Thickening a hollow part just makes a bigger hollow part.

**Never adjust a measured value to make something fit.** If the model disagrees
with reality, re-measure. Silently nudging a number leaves the source disagreeing
with the record, and the next person cannot tell which is true.

**Say plainly when new information invalidates earlier work.** Corrections are
cheap early and expensive late. Name what changed and what it means, then move on.

---

## Phase 0 — Intake

Ask for everything at once, before modelling or measuring:

- **Photos** — at least two angles. A shot with a ruler or caliper alongside is
  worth more than four without.
- **What it does** — what it attaches to, what moves, what carries load.
- **If broken: what broke, and how.** Snapped? Crumbled? Worn through? Ask
  directly; do not deduce it from the photo.
- **Existing files** — STL/STEP/OBJ they already have, and where they came from.
- **Printer and material intentions**, and whether the printer is enclosed.
- **Do they have calipers**, or only a rule.

Do not begin modelling. Do not begin guessing dimensions.

---

## Phase 1 — Establish the form before the size

Shape first, numbers second. Getting this order wrong wastes whole rounds, because
a dimension attached to the wrong feature is worthless.

1. **Inspect whatever files exist.** Read the geometry directly — bounding box,
   cross-sections, feature positions — rather than trusting filenames or
   descriptions. A bundled `.obj` may be an empty stub; a README may be a stranger's.
2. **Describe the form back to the user in words**, feature by feature, and ask
   them to confirm or correct. "A round end with a solid cylindrical boss, an arm,
   and a block straddling a hole" is checkable. A render is better.
3. **Draw or render your understanding and show it.** Iterate here until they say
   it matches. Every correction at this stage costs one message; the same
   correction after modelling costs a rebuild.
4. **Watch for structural corrections**, not just cosmetic ones — "it's one piece,
   not two", "there's no hole there", "that feature sits on top of this one". These
   change what must be measured, not just how it looks.

Record each confirmed fact. These become the model's structural assumptions and
belong in the source comments.

---

## Phase 2 — Build the measurement sheet

**Always produce this**, before asking for a single number. A numbered drawing is
the difference between a user who can measure confidently and one who guesses.

### What it contains

- **A section view** through the part's main axis — reveals thicknesses, bores,
  recesses.
- **A plan view** — footprint, hole positions, feature spacing.
- **Detail sections** for any feature too small to dimension on the plan.
- **Option diagrams** for every either/or you cannot resolve yourself: which way
  something points, open versus closed, which face mounts. Draw both, let the user
  point at one. These are answers, not measurements.
- **A fill-in table**: number, what to measure, where to measure it, priority,
  units, blank value column.
- **Priorities.** Mark the handful that are genuinely load-bearing as critical, so
  a user with ten minutes knows what to do first.

### Drawing conventions that make it usable

- **Draw to scale and pick an explicit ratio** — e.g. 4 px/mm. Audit it: compute
  px ÷ mm for every feature and confirm they all match. A stretched drawing quietly
  misleads about proportion, and users notice.
- **Enlarge small features as a labelled detail view** (`SCALE 2:1`) rather than
  distorting one view internally. Proportions stay true; legibility is preserved.
- **Numbered balloons** on leader lines, breaking the dimension line at its midpoint.
- **Extension lines** starting with a small gap from the object.
- **Hatch section cuts** with a diagonal pattern so cut material reads as solid.
- **Mark the fracture** on a broken part with a zig-zag break line. Where it broke
  is information.
- **Say what is schematic.** If an outline is approximate, the caption must say so;
  what matters is which span each arrow points at.

### Technical requirements

- One **self-contained HTML file**: inline `<style>` and `<script>`, no CDN, no
  external fonts or images. It must open by double-clicking, forever, offline.
- **Theme-aware.** Define colours as CSS custom properties on `:root`, redefine
  under `@media (prefers-color-scheme: dark)` and under `:root[data-theme="dark"]`
  / `[data-theme="light"]`. Add a small JS theme toggle — a standalone file has no
  host to supply one.
- **Print stylesheet**, so it can be taken to the workbench on paper.
- **SVG element ids are document-global.** Give every `<marker>` and `<pattern>` a
  unique id per drawing (`arrP`, `arrT`, `arrB`) or later diagrams will silently
  borrow the first one's definitions.
- **Also export each drawing as a standalone `.svg`**, with its own inline
  `<style>` block carrying the colour tokens plus a background `<rect>`, so it
  stands alone anywhere.
- **Generate the standalone SVGs from the HTML in one scripted pass**, so the
  embedded and extracted copies cannot drift apart.

### Verify before sending

Parse every SVG as XML to catch syntax errors, then render each to PNG and *look
at it*. Hand-authored coordinates collide in ways that only show visually: labels
over centrelines, leaders crossing dimension lines, balloons on top of arrows.

```bash
qlmanage -t -s 1000 -o . drawing.svg     # macOS, renders SVG to PNG
```

---

## Phase 3 — Collect measurements and resolve contradictions

When the numbers come back, **check them against each other before modelling**:

- Do the sub-lengths sum to the overall length?
- Does a feature fit inside the face it sits on?
- Do wall thicknesses plus a gap equal the stated overall width?

**Contradictions are signal, not noise.** If a user answers "solid" but also gives
a gap width, one of the two is wrong — and the numbers usually reveal which. Say
what you found, show both interpretations if it is cheap to, and ask. Do not
silently pick one.

Where a derived value disagrees slightly with a measured one, prefer the derived
value for internal consistency and **record the discrepancy** in both the source
and the measurement record.

Dimensions that **cross a fracture** cannot be measured directly. Tell the user to
press the halves together against a flat edge, or to measure the mounting points on
the assembly instead — and to say which they did.

---

## Phase 4 — Build the parametric model

Rebuild from scratch. Do not edit the donor mesh — meshes cannot be reshaped, only
deformed, and any real change means hand-patching triangles.

Structure the `.scad` file as:

1. **Header comment**: revision, date, and the structural facts established in
   Phase 1 — the things a fresh reader would otherwise get wrong.
2. **Parameter blocks**, grouped by feature, each value tagged with its measurement
   number and its provenance (`// (7) measured`, `// NOT MEASURED - assumed`).
3. **Derived values** computed from parameters, never duplicated by hand, so
   related dimensions cannot drift apart. If two features must stay aligned,
   express one in terms of the other (`block_x = screw_x;`) rather than trusting
   two numbers to match.
4. **One module per feature**, named for the feature.
5. **A single `difference()`** at the end: union the solids, subtract the holes.
   Holes cut last so they pass through everything added.
6. **Print notes** at the bottom, so the settings travel with the file.

Keep alternates as parameters (`block_solid = false`) rather than deleting the
branch you are not using. The user may switch back, and rendering both is how you
let them choose.

**Deliberate improvements over the original are fine — flag them.** A fillet at a
sharp internal corner costs nothing to print and removes a stress raiser. Say in
the comment that it is an addition, and why.

---

## Phase 5 — Verify the export

Never ship an STL you have only looked at.

```bash
python3 -c "
import struct
d=open('part.stl','rb').read(); n=struct.unpack('<I',d[80:84])[0]
v=[struct.unpack('<3f',d[84+50*i+12+12*k:84+50*i+24+12*k]) for i in range(n) for k in range(3)]
print('%d tris  %.2f x %.2f x %.2f'%((n,)+tuple(max(a[j] for a in v)-min(a[j] for a in v) for j in range(3))))"
```

Check the bounding box against the measured envelope and state the comparison
explicitly. Carry that envelope forward as a checksum: any later change that moves
it unintentionally is a bug.

Render from several angles and look. Choose at least one angle that proves the
feature you are least sure about — an interior gap, a hole's position, a step. The
default three-quarter view hides exactly the things worth checking.

---

## Phase 6 — Deliver

Send the `.stl`, the `.scad`, and the measurement record together. Say what was
verified and how. Name anything still open, rather than letting a delivered file
imply it is final.

---

## Phase 7 — Material and print settings

The model is only half the job. **Say so explicitly**, because a solid model plus
default slicer settings reproduces a hollow part — which is a very common cause of
the original failure.

Walk through, in this order:

1. **Was hollowness the original failure?** If so, the fix is infill and
   perimeters, not geometry. State that no dimension needs to change.
2. **Perimeters do most of the work** on small parts. Six perimeters at a 0.4 mm
   nozzle put 4.8 mm of solid shell across an 18 mm-wide section before any infill
   is placed — nearly solid already. Reach for walls before infill.
3. **Name the tradeoff.** Higher infill resists breaking but increases thermal
   contraction, which warps parts off the bed in ABS/ASA. These pull in opposite
   directions; give a compromise rather than one number, and say why.
4. **Orientation versus load.** Layer lines are the weak axis. Identify how the
   part is loaded in service and say which direction the layers will run. A fillet
   at a loaded root spreads that load over several layers instead of one.
5. **Environment before geometry** for adhesion problems: enclosure, cooling fan
   off, bed temperature, bed surface prep, draughts. A brim is a removable *fix*;
   supports are for overhangs and will not help a part peeling off the plate.
6. **Question the material.** Users often pick ABS out of habit for "strong". If
   the part does not need heat resistance, PETG has better layer adhesion, needs no
   enclosure, and will solve an adhesion fight outright. Say so.
7. **Shrinkage.** ABS/ASA shrink 0.5–0.7%. If a feature comes out undersized,
   change that feature's parameter — never scale the whole part, which shifts every
   hole spacing.

---

## OpenSCAD reference

```bash
brew install --cask openscad          # macOS

openscad -o part.stl --export-format binstl part.scad
openscad -o view.png --imgsize=760,540 \
         --camera=16,0,10,60,0,200,140 --colorscheme=Monotone part.scad
openscad -o variant.stl -D 'flag=true' -D 'width=8' part.scad
```

**Gotchas that cost time:**

- `--render` **takes an argument.** Passing it bare prints the usage text and
  silently produces nothing. Omit it — preview mode exports PNG fine.
- `-D 'var=val'` **overrides top-level assignments.** This is how you render
  variants; never edit the source to produce an alternative.
- `--camera` is `tx,ty,tz,rx,ry,rz,dist`. `rx≈60, rz≈200` is a serviceable
  three-quarter view; `rx=180` looks at the underside; `rx≈35` with `rz≈270` sights
  along a long axis, which is often the only way to see inside a gap.
- `$fn = 96` at the top gives smooth cylinders without absurd triangle counts.
- Overlapping unioned solids are fine; make subtracted solids overshoot by `0.1`
  at each end so they cut cleanly and leave no zero-thickness faces.

---

## Checklist

- [ ] Photos, function, failure mode, existing files, printer, material collected
- [ ] Donor files identified as donor; nothing reasoned from their documentation
- [ ] Form confirmed by the user in words or a render, before any measuring
- [ ] Measurement sheet produced, drawn to scale, scale ratio audited
- [ ] Option diagrams drawn for every unresolved either/or
- [ ] SVGs parsed and visually rendered before sending
- [ ] Measurements cross-checked for internal consistency; contradictions raised
- [ ] Model parametric, every value tagged measured / derived / assumed
- [ ] Related dimensions expressed in terms of each other, not duplicated
- [ ] STL bounding box parsed and compared to the measured envelope
- [ ] Rendered from an angle that proves the least-certain feature
- [ ] Print settings given, with the infill-versus-warping tradeoff named
- [ ] Anything still unresolved stated explicitly at handover
