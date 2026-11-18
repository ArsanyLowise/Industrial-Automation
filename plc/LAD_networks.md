# LAD networks — machining cell block

Ten networks, one block: `FB_MachiningCell` (SCL source in `FB_MachiningCell.scl`). Tags come from
`machining_center_tags.csv`. Line bits from the base program: `M0.0` run, `M0.1` fault.

Static memory in the instance DB: `#xRun`, `#xFault`, `#xBusyOld`, `#xResetOld`, `#xResetEdge`,
`#iParts`.

---

## NW1 — reset clears the fault

```
  "Machining Center 1 (Reset)"     P_TRIG            #xFault
 ───────| |────────[ M1.7 ]────────( P )──────────────( R )
```

Edge triggered: holding the button does not keep clearing new faults.

## NW2 — fault latch

```
  "Machining Center 1 (Has Error)"                   #xFault
 ─────────| |──────────────────────────────────────────( S )
```

Dominant: while set, every output below drops.

## NW3 — run latch

```
  "Machining Center 1 (Start)"  "Machining Center 1 (Stop)"  #xFault  M0.1  M0.0   #xRun
 ─────────| |──────────┬──────────────|/|──────────────|/|─────|/|────| |─────( )
                       │
      #xRun            │
 ─────────| |──────────┘
```

## NW4 — part type

```
  "Machining Center 1 (Produce Lids)"                "Machining Center 1 (Produce Lids)"
 ───────────── level signal ─────────────────────────────( )
```

TRUE = lids, FALSE = bases. Keep it steady for a production run.

## NW5 — start / stop commands

```
      #xRun       #xFault                            "Machining Center 1 (Start)"
 ─────────| |────────|/|──────────────────────────────────( )
```

```
      #xRun                                          "Machining Center 1 (Stop)"
 ─────────|/|──────────────────────────────────────────────( )
```

## NW6 — reset pulse

```
  "Machining Center 1 (Reset)"   P_TRIG              "Machining Center 1 (Reset)"
 ─────────| |────────[ M1.6 ]────( P )──────────────────────( )
```

## NW7 — finished-part counter

```
  "Machining Center 1 (Is Busy)"  N_TRIG                   ADD_I
 ─────────| |────────[ #xBusyOld ]─( N )──────[ #iParts + 1 → #iParts ]
```

One count per part, on the falling edge when the robot releases the exit bay.

## NW8 — progress mirror (optional)

```
  "Machining Center 1 (Progress)"                     "Digital Display 1"
 ───────────── move Int ───────────────────────────────────( )
```

Delete this network if the displays stay reserved for the warehouse counters.

## NW9 — infeed belt

```
      #xRun    "Machining Center 1 (Is Busy)"   #xFault     "CNC Infeed Conveyor"
 ─────────| |─────────────|/|────────────────────|/|─────────────( )
```

## NW10 — outfeed belt

```
      #xRun       #xFault                             "CNC Outfeed Conveyor"
 ─────────| |──────────|/|─────────────────────────────────( )
```

---

## OB1 call

```scl
"DB_MachiningCell"(xEnable      := %M0.0,
                   xLineFault   := %M0.1,
                   xStart       := "Machining Center 1 (Start)",
                   xStop        := "Machining Center 1 (Stop)",
                   xReset       := "Machining Center 1 (Reset)",
                   xProduceLids := TRUE,
                   xIsBusy      := "Machining Center 1 (Is Busy)",
                   xHasError    := "Machining Center 1 (Has Error)",
                   xDoorOpened  := "Machining Center 1 (Opened)",
                   iProgress    := "Machining Center 1 (Progress)",
                   xCmdStart    => "Machining Center 1 (Start)",
                   xCmdStop     => "Machining Center 1 (Stop)",
                   xCmdReset    => "Machining Center 1 (Reset)",
                   xCmdProduce  => "Machining Center 1 (Produce Lids)",
                   xInfeedBelt  => "CNC Infeed Conveyor",
                   xOutfeedBelt => "CNC Outfeed Conveyor",
                   xReady       => %M20.0,
                   iParts       => %MW30);
```
