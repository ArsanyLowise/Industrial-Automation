# Attribution

The base cell in `smart_factory.factoryio` comes from a student project that was published for
educational use. That covers the six lane sorting by colour and part type, the lid and base assembly,
the 9 x 6 matrix warehouse with the stacker crane and the PLC tag structure that goes with it.

> **Smart Factory Simulation, Factory I/O and Siemens TIA Portal**
> Fares Hilal Ahmed Haider (202270029), Luay Ahmed Yahya Zayed (202270398),
> Ayman Mrwan Mohammed Al-Reazqee (202270324)
> Mechatronics Engineering, Sana'a University.

The scene here is a modified copy of that work. The original TIA Portal archive and the original
screenshots are not redistributed with it.

## Additions in this project

| Item | Author | Notes |
| --- | --- | --- |
| Machining cell objects (robot, CNC, infeed and outfeed belts) | this project | 3 objects, 10 tags, address block 90 and up |
| `plc/FB_MachiningCell.scl` | this project | run and fault latch, part type, counter, interlocks |
| `plc/LAD_networks.md`, `plc/machining_center_tags.csv` | this project | written from the scene file |
| `line-overview.jpg`, `sorter-field.jpg` | this project | screenshots of this scene |

No vendor binaries, licences or third party assets are included. Factory I/O and TIA Portal are
commercial products of their respective owners and are not part of this project.
