# Circuit Design Assistant v0.4.0: a tour of the features

Circuit Design Assistant turns block diagrams into SystemVerilog. You draw a module as
blocks and wires, and **To SV** writes the RTL. It can also draw existing RTL as
diagrams, turn pictures of architecture diagrams into working RTL with Claude, and Claude
can draw and check designs alongside you. This guide walks through what the application
does, in the order you will usually meet it. Features added in this version are marked
**New in v0.4.0**.

> New to it? Open one of the example projects first. The installer puts 35 of them in
> **Documents\Circuit Design Assistant Examples**: half adders, ALUs, state machines, a
> UART and a tiny CPU. Each has its diagrams, the generated SystemVerilog and a README.

---

## 1. The window at a glance

| Area | What it is for |
|---|---|
| **Block window** (left) | The shape libraries. Drag a block onto the canvas. |
| **Canvas** (centre) | One tab per diagram. In an RTL design, **each tab is one SystemVerilog module**. |
| **Properties** (right) | The selected item's text, style and RTL options. |
| **Bottom panel** | **Terminal** (PowerShell), **Compile** (the To SV log), **Warning** (problems found), **Claude** (Claude Code). |
| **Toolbar** | Common actions: To SV, Add Shape, Image, Import RTL, Report an Issue, and others. |
| **Menu bar** | File, Edit, View, Insert, Arrange and Help. The settings gear and the light/dark theme switch are in its right corner. |

Projects are saved as `.cida` files and libraries as `.cidalib` files. Double-clicking
either file in Explorer opens it in the application.

### Settings
**New in v0.4.0:** the gear in the menu bar's corner (or **Edit > Settings...**, Ctrl+,)
opens **Settings**:

- **General**: the application's font, family and size. Diagram text keeps the system
  font, so a diagram looks the same whatever font the window uses.
- **Theme**: light or dark mode, and a colour palette for each mode (rose, violet, ocean,
  teal, forest, amber, graphite).

Changes apply as you make them. The look is saved with the project and restored when you
open it.

## 2. Your first module: a half adder

1. **File > New Project** (Ctrl+N). Rename the tab after the module, e.g. `half_adder`
   (**View > Tabs...** renames, hides and reorders tabs).
2. From **RTL I/O**, drag two **Input Ports** and two **Output Ports**. Name them by the
   coding style's convention: `i_a`, `i_b`, `o_sum`, `o_carry`.
3. From **RTL Combinational**, drag an **XOR** and an **AND**.
4. Wire them: drag from an output pin to an input pin. When one output feeds several
   inputs, the junction dot is drawn for you. Wires that only cross are drawn with a
   small hop, so a crossing is never mistaken for a connection.
5. Press **To SV** (Ctrl+G). The **Compile** tab shows each step, and the generated
   module opens in the SystemVerilog viewer. **View > Show SV** shows it again later.

Tips:
- **Arrange > Auto Layout** (Ctrl+Shift+L) tidies a tab: inputs on the left, outputs on
  the right, pins lined up so wires run straight. **New in v0.4.0:** it also turns every
  block to face left to right, puts each block just before the block it feeds (a Bit
  Select just before its mux, the gate that makes an enable just before its flop), keeps
  closely connected blocks together, and runs a feedback wire right beside its loop
  instead of around the whole diagram. It then searches nearby arrangements for fewer
  crossings and bends, and gives the same result every time.
- **View > Fit to Window** (Ctrl+F) zooms to the whole diagram.
- Name a wire (a net) on the line leaving its driver. The name becomes the signal's name
  in the code.

## 3. The block libraries

| Library | Blocks |
|---|---|
| **RTL Combinational** | AND, OR, NAND, NOR, XOR, XNOR, NOT, Adder, Subtractor, Multiplier, Divider, Mux, Decoder, Encoder, Constant, Comparator, Shifter, Extend, SV Expression, Logic Block |
| **RTL Flops and Counter** | Flop, Set FF, Counter, 2-Stage Synchronizer, FSM |
| **RTL I/O** | Input Port, Output Port, Go To, From, Bit Select, Merge Data, Module Instance |
| **General Blocks** | Rectangles, circles, diamonds, text, lines and arrows for sketches and flow charts (these generate no code) |
| **Analog** | Resistors, capacitors, inductors, diodes, MOSFETs, BJTs, sources, ground, supply rails, switches, op-amps |
| **Mixed Signal** | Mixer, summing node, PFD, VCO, divider, low-pass filter, ADC, DAC |

**View > Libraries...** chooses which libraries the block window shows.

### Block options

Double-click a block to set its options: widths, reset style (async/sync, active level),
enables, counter direction, FSM states, and so on. The options decide what code is
generated, and the result is plain, readable SystemVerilog. A block's logic is written
inline in the module by default. Some blocks can be written as a separate module instead.

The **Flop** and **Counter** have a **Signal name** field. It names the register on
their output (for example `logic [31:0] program_counter`), so the code reads the way you
would write it by hand. The block on the canvas shows its Signal name instead of its
default label, so you can tell two flops apart at a glance.

**New in v0.4.0** in RTL Flops and Counter:

- **Clear on the Flop and the Counter.** Tick **Clear** for a synchronous clear pin
  (`clr`, or `clr_n` when active low) and set the value it loads. It is part of the
  logic, not a reset: drive it from any control signal. It comes after the resets and
  wins over the enable. On a Flop with several stages, it clears every stage.
- **Set FF**: a one-bit flag with separate **set 1** and **set 0** pins, made for state
  flags and for drawings that say "set to 1" / "set to 0". **Priority** decides which
  wins when both are high. It has the usual resets and a Signal name.
- **2-Stage Synchronizer**: brings a signal from another clock domain (or an asynchronous
  input) in through two flops, against metastability. When your coding style defines a
  synchroniser module, the code is always that module, one instance per bit; the default
  style's cell is used this way.

**New in v0.4.0:** a **Constant** accepts SystemVerilog literals: sized and based numbers
(`9'd500`, `3'b111`, `8'hFF`), concatenations and replications (`{2'b10, 3'b111}`,
`{4{1'b1}}`). The code and the block's label keep them as you wrote them, and values that
don't fit are reported.

### Styling shapes
Select a shape and use the **Style** section of the Properties panel: fill and border,
line style, font and alignment. **New in v0.4.0:** a gradient fill (four directions or
radial, to a colour you choose or a deeper shade of the fill), fill opacity, and a drop
shadow. draw.io export and import keep them.

## 4. Bigger designs

### Hierarchy: Module Instance
Build the submodule on its own tab. On another tab, place a **Module Instance** and set
`MODULE` to that tab's name: its pins appear at once. **Edit > Open Module Tab** (Ctrl+E,
or Ctrl+double-click the block) jumps to the submodule.

### Parameters
**Edit > Parameters...** holds the project's global parameters, such as `N` or `WIDTH`.
Use them for widths and loop counts, so that changing one value resizes the whole design.

### Parallel hardware: Replicate
For N copies of the same logic, use **one block replicated N times**, not N drawn blocks.
Double-click the block and tick **Replicate**: a **Replicate** tab opens beside the
block's options (unticking removes it). The block is drawn stacked with an "×N" badge
and becomes a single `generate` loop. For each pin, choose how the copies share it:

- **Sliced**: copy *i* takes slice *i* of a wider bus (the default).
- **Shared**: every copy gets the same signal (the default for clocks and resets).
- **Chained**: copy *i*'s output feeds copy *i+1*, like a carry.
- **Select**: copy *i* takes `data[i]`, `[row][col]`, `[i*8 +: 8]` and so on. This is
  the best choice for arrays.

Loops can be nested, and you name each loop variable yourself (`row`, `col`). To repeat
several blocks as plain code rather than as a module instance, draw them on their own
tab, place it with a Module Instance, tick **Write the tab's logic here (flatten one
level)** and replicate it.

### Buses and arrays
- **Merge Data** bundles signals into a bus, or into an unpacked array (set `OUTPUT` to
  `array`). The bus remembers its fields by the names of the nets merged.
- **Bit Select** takes part of a bus out, either by bit range or by naming a field
  (`i_data_2[1][7:0]`).

### Go To / From: wires you don't draw
A net that would cross the whole tab can travel invisibly. Wire it into a **Go To** with
a variable name (`TAG`) and take it out of any number of **From** blocks with the same
`TAG`. Use them sparingly: a diagram full of hidden wires is hard to follow.

## 5. Generating SystemVerilog (To SV)

Pressing **To SV** on a tab runs these steps:

1. Module instances are updated to match the tabs they use.
2. The netlist is built and checked. Errors stop generation and warnings don't. Both
   are listed in the **Warning** tab.
3. If the coding style offers module substitutes (such as pipeline flop modules), you
   choose which blocks use them. Your choice is remembered. Substitutes the style
   requires, such as its synchroniser cell, are always used and not asked about.
4. If Claude is set up, Claude reviews the netlist, and any fixes you approve are applied
   to the diagram.
5. One file is written per module.
6. The modules are syntax-checked together.
7. If Claude is set up, Claude reviews the code against your coding style guide. You
   accept or reject each revision.

**View > Netlist in SV Viewer** also shows the netlist next to the code.

**New in v0.4.0: code that reads like hand-written RTL.**
- A one-bit condition used in one place is written straight into it: `if (cnt == limit)`,
  `else if (~i_en)`, rather than through an extra named signal.
- Bit Selects are written where they are read: `case (addr[2:1])`, `o_y = i_data[1:0]`.
  A slice you named in the diagram keeps its name.
- Nets you did not name are named for what they do: the register they control and its
  pin (`tx_cntr_clr`), or the comparison they make (`addr_eq_4007`, `a_gt_b`), rather
  than after the blocks they join.

### Coding style
**Edit > Coding Style...** chooses the conventions the generator follows: naming, the
form of generate loops, reset handling, module substitutes, and comments. With the
default style, every port gets a short comment saying what it carries, and there is a
one-line comment above each state machine, generate loop and inline block. Inside an
`always_ff`, which several blocks often share, each block's `if` / `else if` chain gets
its own comment just before it. Give a data port a **Description** to choose its comment
yourself. Clocks, resets, enables and selects get theirs automatically. Your own blocks
follow the style's register delay like the built-in ones.

To SV needs no Claude: the generator alone writes complete code, checks its syntax, and
the 35 example projects simulate equal to their hand-written references. Claude adds a
review on top.

## 6. Bringing existing work in

**File > Import From Existing**:

- **CIDA project (.cida)** (Ctrl+O) opens a saved project.
- **Import RTL** (Ctrl+Shift+I) draws existing SystemVerilog files (a top module and its
  submodules) as an overview diagram, one tab per module. Datapaths become real blocks.
  Complex logic is kept as overview **Logic Blocks**, and Claude can draw a state diagram
  or flow chart beside them.
- **draw.io XML** adds the pages of a draw.io file as new tabs. Shapes with no match
  arrive as dashed placeholders that keep their look. **New in v0.4.0:** pictures inside
  a draw.io file come in as pictures.
- **From Image...** (**New in v0.4.0**, needs Claude): see below.

**File > Export As** saves the visible tabs as **draw.io XML** (one page per tab; when
re-imported, the blocks come back exactly as they were) or the current tab as **PNG** or
**JPEG**.

### From Image: architecture pictures to working RTL
**New in v0.4.0.** Give Claude the pictures of a design and it builds:

- working, synthesizable **SystemVerilog** (one file per module, checked until it
  compiles without errors),
- an **RTL schematic** that generates that code (one tab per module, made of real RTL
  blocks, so To SV works on it), and
- an **overview block diagram** for people, which need not be synthesizable.

How to use it:

1. Add the pictures of one design: a top-level block diagram, detail drawings of its
   blocks, state diagrams. Say in a few words what each one shows.
2. In **Notes for Claude**, add what the pictures do not say: how many copies of a
   datapath drawn once there are (samples per clock, channels), data formats, clocks and
   resets, which blocks already exist, and parts of the drawing you know are wrong.
3. Name the design (the top module), choose what to make, and choose an **output folder**
   for Claude's files. Pick one outside your source repositories.
4. **Start with Claude.** The work happens in the **Claude** tab. Claude reads each
   picture once, with one helper per picture working at the same time, writes down what
   it sees, and works from that. With **Ask me about unclear parts before building**
   ticked, it stops after writing `interpretation.md` and asks its questions, each with
   the answer it would choose. Then it builds, and reports in `notes.md` what it changed,
   added or assumed, with suggestions.
5. Correct it in the chat. When a correction is a general rule about how you draw ("a
   dashed box is always external"), Claude offers to remember it. With your agreement it
   adds the rule to your learned rules, which every later import follows.

Large pictures are cut into full-resolution tiles so small labels stay readable. A
picture already imported is not read again on a later import unless it changed. The
rules Claude follows are in `IMAGE_IMPORT_GUIDELINES.md`, next to the coding style; you
can edit them. **Edit > Claude Setup...** has a model and effort for the picture-reading
helpers, so you can give them a faster model.

## 7. Pictures, your own shapes and libraries

### Pictures on the canvas
**New in v0.4.0:** **Insert > Image...** (also **Image** on the toolbar) puts pictures
(PNG, JPEG, GIF, BMP, WebP) on the canvas, for example a diagram to trace over or a
screenshot to annotate. You can also drop image files on the canvas, or paste a picture
from the clipboard with Ctrl+V. A picture moves, resizes, rotates and flips like any
shape, and wires can attach to it. It is saved inside the project file; very large
pictures are scaled down when inserted. draw.io export writes it as a draw.io picture.

### Your own shapes and libraries
- **File > New Library...** creates a library of your own. **Import Library...** adds a
  `.cidalib` file someone shared with you.
- **Add Shape** (toolbar) draws a new symbol: its outline, pins and details. An RTL shape
  can instantiate an existing SystemVerilog module of your own, with its ports, code and
  configuration options. An option can be a **Signal name** that names one of the
  block's outputs. The shape editor is a window of its own, so the rest of the
  application stays usable while it is open or minimised.
- **Edit > Edit Shape...** (also on the right-click menu) changes a shape in your
  library. Copies already placed, and new ones, follow the change. Shapes in the
  built-in libraries are read-only.
- Right-click a shape in the block window and choose **Move to Library** to move it into
  another library of yours. Blocks already placed keep finding it.
- Right-click a library's header for **Rename Library...** or **Delete Library...**.
  Blocks placed from a deleted library keep their look.
- **Right-click > Edit Connection Points...** moves, adds or removes the points where
  wires attach to *one placed shape*, without changing the library.
- **Rotate** (Ctrl+R), **Flip Horizontally / Vertically** (Ctrl+Shift+H / V) and
  **Bring Forward / Send Backward** are on the right-click menu. Flipping keeps text
  readable.

## 8. Working with Claude

The **Claude** tab in the bottom panel runs Claude Code with the diagram connected to it.
Ask in plain words, for example "draw an 8-bit counter with synchronous clear and an
enable", and Claude places the blocks, wires them, generates the SystemVerilog and fixes
any problems the checks report. After you edit the diagram yourself, Claude picks up
only your changes. Claude can also review the netlist and the code during **To SV** (see
section 5), and turn pictures into RTL (see section 6).

**Edit > Claude Setup...** chooses how Claude is started and signed in, using your own
Claude Code login. The model is picked from a dropdown, which also lists the models
your team settings name. Each task (the reviews, suggestions, import help and, new in
v0.4.0, reading pictures) can have its own model and effort.

While Claude reviews or suggests, the waiting window lists the steps (starting, sending
the request with the model's name, thinking, writing the answer with its size so far,
checking it) with a timer, so you can see it is making progress.

Claude is optional. Everything else in this guide works without it.

## 9. Diagrams that aren't RTL

The same canvas draws architecture sketches, PLL block diagrams, flow charts (with
General Blocks and Mixed Signal) and transistor-level schematics (with Analog). Shapes
from those libraries generate no code. Signal flow runs left to right, a feedback block
can be flipped to run right to left, and wire labels are written once, on the line
leaving the source.

## 10. Getting help: Report an Issue

**Help > Report an Issue...** (also on the toolbar) opens a form for a bug report or a
feature idea. The version you use and your system details are filled in for you.

- **Send Report** files the issue on GitHub under your own account, directly from the
  application. The first time, you sign in with a short code at
  github.com/login/device. The sign-in only allows filing issues, and it is stored
  encrypted for your Windows user. You can sign out from the same window.
- **Open on GitHub** opens the same form, pre-filled, in your browser. Use it if your
  network only lets the browser reach GitHub.
- **Copy Report** puts the text on the clipboard.

---

### Keyboard shortcuts

| Action | Keys | Action | Keys |
|---|---|---|---|
| New Project | Ctrl+N | To SV | Ctrl+G |
| New Tab | Ctrl+T | Auto Layout | Ctrl+Shift+L |
| Open project | Ctrl+O | Fit to Window | Ctrl+F |
| Save / Save As | Ctrl+S / Ctrl+Shift+S | Show/hide terminal | Ctrl+` |
| Import RTL | Ctrl+Shift+I | Open Module Tab | Ctrl+E |
| Undo / Redo | Ctrl+Z / Ctrl+Y | Rotate | Ctrl+R |
| Cut / Copy / Paste | Ctrl+X / C / V | Flip H / V | Ctrl+Shift+H / V |
| Select All | Ctrl+A | Delete | Del |
| Settings | Ctrl+, | | |
