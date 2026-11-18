# Smart Factory Cell: Factory I/O scene and Siemens PLC program

A simulated production cell. Parts are sorted by colour and type, assembled from a base and a lid,
stored in a matrix warehouse and retrieved on request. A robot feeding a CNC machine supplies machined
parts to the line.

The project is a Factory I/O scene plus the Siemens S7-1200 program that drives it. The scene is a
normal My Scenes file and the program is a normal TIA Portal source, not a recording.

```
smart_factory.factoryio         Factory I/O scene (138 base objects + 3 cell objects)
plc/FB_MachiningCell.scl        machining cell block, importable into TIA Portal
plc/machining_center_tags.csv   tag table for that block
plc/LAD_networks.md             the same logic written out for LAD entry
line-overview.jpg               production line, overview
sorter-field.jpg                sorter field and the machining cell
```

## Screenshots

![Production line overview](line-overview.jpg)

![Sorter field and the machining cell](sorter-field.jpg)

## Process

```
raw parts -> sorter (6 lanes: 3 colours x base/lid) -> assembly x3 -> matrix warehouse (9x6)
                                    ^
                     machining cell: robot + CNC, lids 6 s / bases 3 s
```

| Stage | Equipment | Tags |
| --- | --- | --- |
| Infeed | emitter, entry conveyor, vision sensor | `Emitter 1 (Emit)`, `Entry Conveyor`, `Vision Sensor 1 (Value)` |
| Sorting | six pivot arm sorters, one per colour and part type | `Metal Base Arm`, `Metal Base Belt`, etc. |
| Assembly | three two axis pick and place stations with clamps | `MB (Clamp)`, `Two-Axis Pick & Place 1 (Grab)`, etc. |
| Machining | one robot and one CNC (Factory I/O Machining Center) | `Machining Center 1 (Start)`, `(Is Busy)`, `(Progress)`, etc. |
| Storage | stacker crane and 9 x 6 rack | `Stacker Crane 1 Target Position`, `(Left)`, `(Right)`, `Lift` |
| Operator | emergency stop, start and stop buttons, status displays | `Emergency Stop 1`, `Start Button 1`, `Digital Display 1..3` |

## Requirements

- Factory I/O 2.4 or later with the Siemens driver.
- TIA Portal V15.1 or later and an S7-1200. PLCSIM or PLCSIM Advanced is enough.

## Running it

1. Copy `smart_factory.factoryio` to `Documents\Factory IO\My Scenes`.
2. Open it in Factory I/O and select File > Drivers > Siemens S7-PLCSIM.
3. In TIA Portal create an S7-1200 project and paste the rows of `plc/machining_center_tags.csv` into
   the default tag table (PLC tags > paste rows).
4. Add `plc/FB_MachiningCell.scl` as an external source file, right click it and choose Generate
   blocks. Then call the block from OB1. The call is at the bottom of `plc/LAD_networks.md`.
5. Export the Factory I/O tags to Siemens with File > Export > Siemens and compare the addresses with
   the tag table. The names are what matter, the addresses only need to match your export.
6. Download the program, put the CPU in RUN, run the scene and press Start.

## Machining cell

The cell is Factory I/O's Machining Center. An articulated robot loads raw material into a CNC, waits
for the cycle and puts the finished part on the exit bay. It produces parts, it does not machine
finished products, so it stands upstream of the line. The colour comes from the raw material. The PLC
only chooses between lid and base with `Machining Center 1 (Produce Lids)`.

`plc/FB_MachiningCell.scl` latches run and stop, latches a fault from `(Has Error)` and clears it on
reset, selects the part type, counts finished parts on the falling edge of `(Is Busy)` and keeps a
ready bit. The infeed belt only runs while the station is free. The logic is ten networks, written in
LAD form in `plc/LAD_networks.md` and in SCL for direct import.

### Placement

The cell stands at the end of the line, clear of the sorter field, so it reads as its own station.
Cell at X 430, Z 172, infeed belt at X 430, Z 202, outfeed belt at X 430, Z 142. The belts sit 30
units either side of the cell, which is the bay spacing of the Machining Center, so they line up with
the entry and exit bays.

To move the station, select the cell and the two belts and drag them together, or edit the three
`<Position>` values above the `<Drivers>` block in the scene file:

```xml
<Position X="430" Y="9" Z="172" />   <!-- MachiningCenter -->
<Position X="430" Y="3" Z="202" />   <!-- CNC Infeed Conveyor  -->
<Position X="430" Y="3" Z="142" />   <!-- CNC Outfeed Conveyor -->
```

Keep the 30 unit offset between the cell and the belts if you place it somewhere else.

## Notes

The cell is not connected to the sorter lanes by conveyor. Both belts stop about 20 units short of
the nearest lane, so add a curved or 6 m conveyor if you want parts to move between the cell and the
line without picking them up by hand.

The addresses in `plc/machining_center_tags.csv` are the ones Factory I/O gives in this scene, based
on tag order. Run your own File > Export > Siemens and check them, because the export renumbers when
objects are added or removed. The tag names stay the same, so only the addresses need remapping.

There is no HMI, no alarms, no trends and no recipes. Sorting, assembly and storage logic from the
base scene is unchanged.

The scene has 141 objects and 164 tags, with no duplicate tag names. It is a plain Factory I/O scene
file, nothing is generated or patched when it loads.

## Credits

The base cell, meaning the sorting, the assembly, the matrix warehouse and its PLC structure, is the
work of three Mechatronics Engineering students at Sana'a University: Fares Hilal Ahmed Haider, Luay
Ahmed Yahya Zayed and Ayman Mrwan Mohammed Al-Reazqee. See `ATTRIBUTION.md`. The machining cell, its
tags, its block and this documentation were added for this project.
