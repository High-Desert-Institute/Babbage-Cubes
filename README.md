# Babbage Cubes — Fully 3D-Printed, Reconfigurable Mechanical Logic

**Snap-together, magnet-latched cubes that implement clocked mechanical logic (NAND, NOT, latches, splitters, crossovers) using only 3D-printed parts.**
No metal springs, no bearings. Optional conductive-filament pads let you light indicators or buzzers at logic-level currents.

---

## Why

Digital logic doesn’t have to be silicon. Babbage Cubes are **physical logic gates** that you can **see, touch, and reconfigure** like interlocking building bricks. Each cube is a self-contained mechanism that accepts **position-encoded inputs** (pushrods) and produces **position-encoded outputs**, all synchronized by a shared cam clock that couples through the stack.

The Steampunk genre asks the question of how the world would be different today if Babbage's (very real) mechanical computers had taken off when they ere invented (two-hundred years ago) rather than being abandoned. Steampunk invites us to ask ourselves to imagine a world where the computer revolution happened during the industrial revolution. Today, we take silicon and electricity for granted, but actually we don't need either to build complete, working computers. We van even build robots that print and assemble and reconfigure out mechanical computers. Just imagine the possibilities!

---

## Repository Layout

```
/cad/                 # STEP/STL source for each cube
  nand/
  not/
  splitter/
  crossover/
  wire/
  register/
  clock/
  indicator/
  test-jigs/
/print-profiles/      # Slicer profiles for common FDM printers (0.4 mm nozzle)
/assembly/            # Exploded views, photos, and short build videos
/docs/                # Specs, face pinout, timing, FAQ, pedagogy notes
/examples/            # Ring oscillator, 1-bit adder, SR latch, 2-bit counter
/firmware/            # (Optional) servo clock driver sketches
LICENSE
README.md
```

---

## Cube Form Factor & Face Standard

**Cube size:** 40 mm (v0.1 reference).
**Corner magnets:** 8 × Ø5 × 3 mm disks per cube, polarity keyed for orientation.

**Face port grid (3×3):**

```
[ C ] [ D ] [ G ]   C = Clock boss pilot (top/bottom carry clock)
[ I1] [   ] [ O ]   I1 = Input lane 1,  O = Output lane
[ I2] [ R ] [ P ]   I2 = Input lane 2,  R = Return spring vent, P = spare
```

* **Left face:** expects input **A** on I1.
* **Right face:** expects input **B** on I2.
* **Front face:** outputs **Y** on O.
* **Top/Bottom:** pass the **cam clock** via a shallow hex boss.
* **Back face:** typically pass-through or auxiliary I/O.

**Signal encoding:**

* `0` = rod flush/retracted; `1` = rod extended 1.5–2.0 mm.
* Internal return springs default inputs to `0`.

---

## Core Mechanism (How a NAND Cube Works)

* **AND as kinematics:** an actuator rail advances on **Φ1 (evaluate)** but is **blocked** by two serial pawls (A, B). Only when **A = 1** *and* **B = 1** do both pawls retract, allowing the rail to pass.
* **NOT as latch:** the rail trips a **normally-closed over-center rocker** holding output **Y**. If it trips, **Y opens (0)**; otherwise **Y stays closed (1)** → **NAND**.
* **Φ2 (restore):** cams reset the actuator while the over-center latch preserves **Y**, unless you use the non-latching variant.

---

## Bill of Materials (per cube, v0.1)

* **Filament (insulator):** PLA or PETG, ~25–40 g.
* **(Optional) Filament (conductive):** for low-current output pads on indicator cubes.
* **Magnets:** 8 × Ø5 × 3 mm.
* **Fasteners:** none required (snap-fit lids).
* **Adhesive:** cyanoacrylate for magnets.

> No metal springs, bearings, or wire required for logic. Optional coin cell/LEDs only in I/O cubes.

---

## Print Guide

* **Nozzle:** 0.4 mm (0.2 mm layer height).
* **Material:** PLA (easy) or PETG (springier).
* **Clearances:**

  * Rod in bore: Ø3.0 mm rod → Ø3.2–3.3 mm bore
  * Slides/guides: 0.25–0.35 mm
  * Cam rise: 2.0 mm; evaluate/restore dwell ≈ 50/50
* **Orientation:** print springs and buckled beams **flat** (in-plane) for longevity.
* **Tuning:** if latches are too soft/firm, use included **shim plates** (0.1/0.2 mm) under latch anchors.

---

## Assembly (Quick)

1. **Insert magnets** per polarity map (embossed arrows on shells). Verify face attraction matches the standard.
2. **Snap in sub-assemblies** (actuator, pawls, rocker, return springs).
3. **Close the lid**; stroke test: actuator should travel 3–4 mm with a distinct latch “click.”
4. **Rod check:** insert 3 mm printed rods through face ports; ensure smooth travel and positive return.
5. **Clock coupling:** stack cubes; rotate a Clock cube to confirm Φ1/Φ2 motion.

See `/assembly/` for photo steps.

---

## Getting Started (Your First Logic)

### A. 3-Cube Ring Oscillator (mechanical blinker)

* 3 × **NOT** cubes in a loop, plus 1 × **Clock** cube.
* Offset clock cams by 120° (hub has index marks).
* Add an **Indicator** cube on any node. Turn the clock; watch flags step around.

### B. 1-Bit Half-Adder

* **Sum = A ⊕ B** (built from **4 × NAND** using two **Splitter** cubes).
* **Carry = A · B** (NAND + NOT).
* Latch outputs with a **Register** cube (cross-coupled NANDs).
* Ready-made layouts are in `/examples/`.

---

## Module Catalog (MVP)

* **NAND** — 2-input gate with latching or momentary output variants.
* **NOT** — inverter/restorer; also used for buffering long runs.
* **Splitter (1→2)** — balanced yoke; fan-out up to 1→3 with stronger return.
* **Crossover** — orthogonal pass-through with minimal friction.
* **Wire** — straight pass; also “corner” and “U-turn” variants.
* **Register / SR Latch** — exposes S, R, Q, /Q; D-latch configuration included.
* **Clock** — hand-crank or small-servo cam pair (Φ1/Φ2).
* **Indicator / I/O** — mechanical flag and optional conductive pads for a low-current LED or buzzer.

---

## Design Notes & Tuning

* **Noise margins (mechanical):** blocker teeth at 60–70° self-lock; actuator has 0.5 mm over-travel to ensure trip even with under-stroke inputs.
* **Friction control:** crowned rod tips + wiping blocker faces clear dust; a small amount of dry PTFE is acceptable.
* **Fan-out:** each driver reliably moves 2–3 loads. Use a **NOT** as a buffer if you see under-travel.
* **Phasing:** multi-stage designs prefer Φ1 evaluate → Φ2 restore. For edge-triggered behavior, auto-relatch at the start of Φ1.

---

## Robot Assembly & Physical FPGA Workflow

Turn Babbage Cubes into a robot-assembled, reconfigurable **physical FPGA**. This section specifies standardized grasp features, visual fiducials, and a simple plan/execute flow so a vision-guided arm can pick, place, rewire, and iterate designs.

### Corner Grasp & Separation Standard (v0.1)

* **Corner sockets (all 8 corners):** conical insets for kinematic grasp and magnet separation.

  * Mouth Ø4.0 mm → tip Ø2.0 mm, depth 3.5 mm, 60° included angle.
  * Socket center offset ~1.2 mm from the true vertex to allow a gentle **twist-lift** that breaks magnetic joints cleanly.
  * Minimum wall to shell: ≥1.0 mm; internal fillet: 0.6 mm.
* **Face approach chamfers:** 0.5 mm × 45° around each face; 0.2 mm lead-ins on all rod ports to avoid catching.
* **Reference notches:** tiny relief at two consistent face corners for human tool access and coarse orientation.

### Machine-Readable Markings & Fiducials

* **Embossed orientation marks:** `A` (left), `B` (right), `Y` (front), and **clock direction** arrows on top/bottom. Minimum stroke 0.5 mm, height 0.6 mm.
* **Gate glyphs:** standardized icons (“NAND”, “NOT”, etc.) centered on the **front** face.
* **Binary fiducials (per face):** two square tags for detection under a single overhead or wrist camera:

  * **Pose tag:** 12 mm square at top-left of the face frame (unique ID per cube instance during assembly).
  * **Type tag:** 10 mm square at bottom-right encoding cube class/version (e.g., `NAND.v1`).
  * Print as raised geometry for shadow contrast; dual-material prints may use the conductive filament for higher contrast.
* **Face frame datum:** 1 mm raised perimeter with a small tick at the canonical +X edge for robust edge/pose estimation.

### Robot Ops (Atomic Actions)

* `pick(cube_id, pose, grasp=corner_2pin)` – engage two opposite corner sockets with conical pins.
* `separate(target_pose, twist_deg=10–15)` – slight torsion to overcome magnet force before lift.
* `place(target_pose, approach=planar, force_limit≤10 N)` – align via fiducials, press to contact, release.
* `verify(face)` – confirm type/orientation via fiducials and embossed marks.
* `nudge_rod(face, port, δ)` – tiny axial push to ensure rod seating if required.
* `clock(steps)` – actuate a Clock cube (motorized or hand shaft) for test cycles.

### Planner I/O (Design → Plan)

**Cube library (example, JSON):**

```json
{
  "cubes": [
    {
      "id": "NAND.v1",
      "ports": {"A": "left.I1", "B": "right.I2", "Y": "front.O"},
      "size_mm": 40,
      "fiducials": {"pose": 101, "type": 201},
      "clock": {"top_boss": true, "bottom_boss": true},
      "grasp": "corner_2pin"
    }
  ]
}
```

**Netlist/placement request (example):**

```json
{
  "instances": [
    {"name": "U1", "type": "NAND.v1"},
    {"name": "U2", "type": "NOT.v1"}
  ],
  "nets": [
    {"from": "U1.Y", "to": "U2.A"},
    {"from": "U2.Y", "to": "U1.A"}
  ],
  "constraints": {"grid": "40mm", "rows": 3, "cols": 5}
}
```

**Planner output (pick-and-place plan excerpt):**

```json
{
  "operations": [
    {"op": "pick", "ref": "U1", "from": "tray_1"},
    {"op": "place", "ref": "U1", "to": {"row": 1, "col": 1, "yaw_deg": 0}},
    {"op": "pick", "ref": "U2", "from": "tray_1"},
    {"op": "place", "ref": "U2", "to": {"row": 1, "col": 2, "yaw_deg": 270}},
    {"op": "place", "ref": "WIRE_1", "to": {"row": 1, "col": 1.5}},
    {"op": "verify", "ref": "row1col1.front"},
    {"op": "clock", "steps": 5}
  ]
}
```

### Assembly Pipeline (Physical FPGA Flow)

1. **Inventory:** vision scans staging tray; reads **type tags**; builds available stock list.
2. **Place/route:** translate netlist to a Manhattan grid; auto-insert **Wire/Crossover/Splitter** cubes; maintain continuous **clock rails**.
3. **Build:** back-row to front-row placement; verify each placement by pose/ID; check rod freedom with a gentle tap.
4. **Self-test:** run test vectors (Input cubes or manual rods), clock a few cycles, read **Indicator** cube flags with the camera.
5. **Iterate:** router emits **diff**; robot performs surgical edits (pull/replace/move); re-test.

### End-Effector (Minimal Design)

* **Parallel jaws** with **two conical pins** (matching the corner sockets) on one jaw and **flat elastomer pads** on the other for planar placements.
* **Adjustable pin span** to support 30/40/50 mm families.
* **Wrist-mounted camera** (or calibrated overhead) aligned to jaw frame for accurate pose.

### Tolerances & Targets

* **Pose detection:** ≤ ±0.3 mm, ≤ ±2°.
* **Placement gap:** ≤ 0.15 mm after magnet snap.
* **Rod insertion force:** ≤ 1.5 N peak (use specified chamfers).
* **Separation:** 10–15° twist typically overcomes corner magnet force for Ø5×3 mm magnets.

### Safety & Robustness

* **Over-travel guards** on internal latches to prevent accidental trips during placement.
* **Anti-glare/anti-wear** lips on fiducials; matte surface finish preferred.
* **Polarity sanity:** reference notches allow the vision system to reject mis-built cubes before placement.

> See `/docs/robotics/` for fiducial sheets (SVG/PDF), end-effector STEP, JSON schemas, and a sample assembly plan for a 3-NOT ring oscillator.

---

## Roadmap

* v0.2: 30 mm and 50 mm cube families; improved magnet keying; parametric generators.
* v0.3: Synchronous **D flip-flop**, **binary counter**, and **mechanical 7-segment** driver.
* v0.4: Curriculum packs, lab worksheets, and challenge builds.
* Stretch: Hybrid **electro-mechanical I/O cubes** (reed/Hall sensors), **servo clock** board.

---

## Contributing

* License for the whole repository: **GPL-3.0-only**.
* Include **STEP + STL** for mechanical parts, and an **exploded view** in `/assembly/`.
* Keep to the **face pinout** and **clock boss** standard.
* Target the stated **clearances** and add a brief printability note.
* Provide a minimal **test jig** (e.g., latch-force coupon) if you change springs.

Open an issue for new cube proposals—attach a kinematics sketch or short video.

---

## License

This project is licensed under the **GNU General Public License v3.0 only (GPL-3.0-only)**.
See `LICENSE` for the full text. By contributing, you agree that your contributions are licensed under GPL-3.0-only.

---

## FAQ

**Do I need conductive filament?**
Only for optional indicator pads on I/O cubes. Logic is entirely mechanical.

**How fast can it run?**
Manual clock: about 1–2 Hz. Small-servo clock: about 2–5 Hz (geometry-dependent).

**Can I scale cubes up/down?**
Yes—springs and clearances must be re-tuned. Parametric scripts will land in a future release.

**How do I “wire” between non-adjacent cubes?**
Use **Wire** cubes and **Crossovers** to route; for long chains, drop in a **NOT** to restore level.
