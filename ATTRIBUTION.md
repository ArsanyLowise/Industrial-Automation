# Attribution

The base cell in `smart_factory.factoryio` — six-lane sorting by colour and type,
lid/base assembly, 9 x 6 matrix warehouse with stacker crane, and the corresponding PLC tag
structure — derives from a student project published for educational use:

> **Smart Factory Simulation — Factory I/O + Siemens TIA Portal**
> Fares Hilal Ahmed Haider (202270029), Luay Ahmed Yahya Zayed (202270398),
> Ayman Mrwan Mohammed Al-Reazqee (202270324)
> Mechatronics Engineering, Sana'a University.

That work is published for educational purposes. The scene file here is a modified copy, and the
original project's TIA Portal archive and screenshots are not redistributed with it.

## Additions in this project

| Item | Author | Notes |
| --- | --- | --- |
| Machining cell objects (robot + CNC, infeed and outfeed belts) | this project | 3 objects, 10 tags, address block 90+ |
| `plc/FB_MachiningCell.scl` | this project | run/fault latch, part type, counter, interlocks |
| `plc/LAD_networks.md`, `plc/machining_center_tags.csv` | this project | written from the scene file |
| `line-overview.jpg`, `sorter-field.jpg` | this project | screenshots of this scene |

No vendor binaries, licences or third-party assets are included. Factory I/O and TIA Portal are
commercial products of their respective owners and are not part of this project.
