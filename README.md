# Warfarin App

```
██╗    ██╗ █████╗ ██████╗ ███████╗ █████╗ ██████╗ ██╗███╗   ██╗
██║    ██║██╔══██╗██╔══██╗██╔════╝██╔══██╗██╔══██╗██║████╗  ██║
██║ █╗ ██║███████║██████╔╝█████╗  ███████║██████╔╝██║██╔██╗ ██║
██║███╗██║██╔══██║██╔══██╗██╔══╝  ██╔══██║██╔══██╗██║██║╚██╗██║
╚███╔███╔╝██║  ██║██║  ██║██║     ██║  ██║██║  ██║██║██║ ╚████║
 ╚══╝╚══╝ ╚═╝  ╚═╝╚═╝  ╚═╝╚═╝╚═╝  ╚═╝╚═╝  ╚═╝╚═╝╚═╝  ╚═══╝
```

---

## ◆ PULSE

Warfarin is the dose that cannot be guessed twice. This web app
generates weekly dosing regimens from target weekly dose, available
pill strengths, and the patient's special patterns - uniform or
non-uniform, stop days included - and ranks the options by complexity:
fewer half-pills first, fewer strengths first. A percentage-change
adjustment recalculates the week in one input, and appointment-based
pill counting says exactly how many pills the box needs. The math
runs in a Rust WebAssembly core; the interface is Vue; the browser is
the only server.

| WASM core ▣ | Prioritized ▣ | Adjust ▣ | Pill count ▣ |
|---|---|---|---|

*The dosing loop - generate, rank, adjust, count - is sealed.*

> Built with Vue 3 + Rust compiled to WASM, deployed to Vercel, with
> zero backend and zero excuses for a wrong calculation.
>
> **suradet-ps**, artifact keeper

---

## ◆ IGNITION

Three tools, four commands.

```
⟫ rustup update stable
⟫ cargo install wasm-pack
⟫ git clone https://github.com/suradet-ps/warfarin-app.git
⟫ cd warfarin-app
⟫ bun install
⟫ wasm-pack build ./warfarin_logic --target web
⟫ bun run dev
```

Open [http://localhost:5173](http://localhost:5173).

<details>
<summary>Notes</summary>

- Node.js LTS and Rust via `rustup` are the prerequisites.
- Re-run `wasm-pack build` only after changing Rust (`.rs`) files.
- On Vercel: Framework Preset `Vite`, install command `bun install`,
  build command `bun run build`, output `dist` - the install command
  must build the WASM module before Vite builds the frontend.

</details>

---

## ◆ ANATOMY

Two layers, one rule: the interface asks, the Rust core answers.

- **Computes** - `warfarin_logic` is the Rust crate behind the UI:
  every dose, every split, every regimen is computed in WASM with
  Rust's safety guarantees - the math that matters runs where the
  compiler can watch it.
- **Generates** - weekly regimens in both flavors: uniform weeks and
  non-uniform weeks with special dose days and stop days - the plan
  follows the pattern, not a formula.
- **Ranks** - options are prioritized by complexity: minimize
  half-pills, minimize distinct pill strengths - the easiest schedule
  to keep is listed first.
- **Adjusts** - a percentage change from the previous weekly dose
  produces the new week in one input - titration without arithmetic.
- **Counts** - the pill count to the next appointment is computed
  from the regimen and the calendar - the box is filled correctly
  before the patient leaves.
- **Configures** - pill strengths (1, 2, 3, 5 mg) selectable and
  half-pills toggleable - the calculator respects the pharmacy's
  actual shelf.

---

## ◆ RITUALS

**The core ceremony** - the weekly regimen:

1. Enter the target weekly dose and the available pill strengths.
2. Choose the pattern - uniform, or special days and stop days.
3. Read the ranked options: the most keepable schedule - fewest
   half-pills sits at the top.
4. Adjust when the INR moves: enter the percentage change, and the
   new week answers. Count the pills to the next appointment, and
   the box is filled.

**The ceremony of the split** - half-pills are a cost, and the
ranking knows it: the option with the fewest splits leads, and the
week is as light to keep as the math allows.

**The ceremony of the local calculation** - nothing leaves the
browser: the WASM core computes on the machine in front of the user,
and the page works on a static host with no backend to betray it.

---

## ◆ ECHOES

**Where this artifact is heading**

```
compute ▸ WASM dosing engine, safety-checked math ──────────────────── ▸ sealed
generate ▸ uniform + non-uniform regimens ──────────────────────────── ▸ sealed
rank     ▸ complexity-ordered options ──────────────────────────────── ▸ sealed
adjust   ▸ percentage-change titration ─────────────────────────────── ▸ sealed
count    ▸ appointment-based pill count ────────────────────────────── ▸ sealed
```

**Raising the artifact** - the logic lives in `warfarin_logic/src/lib.rs`;
the interface in `src/components/`. Open an issue first to discuss a
change.

**Status** - dependencies are maintained through Renovate; deployment
is a Vercel connect away.

> This application is for educational and demonstration purposes
> only. It must NOT be used for actual medical decision-making.
> Warfarin dosage adjustments always belong to a qualified healthcare
> professional.

---

```
  ─────────────────────────────────────────
   The INR does not negotiate.
   The regimen should not have to guess.
  ─────────────────────────────────────────
```

Distributed under the [MIT License](LICENSE).