# pwidget_layouts

Versioned pseudo-widget layout definitions used by the MasterBot ThinkOrSwim GUI automation tools.

## Purpose

`pwidget_layouts` stores YAML files that describe the geometry and hierarchy of GUI elements used by pseudo-widget based automation.

These layouts are consumed by projects such as: 

* `ToS_scanner`
* `ToS_gui_survey`
* `mb_tools`

The YAML files allow GUI automation code to refer to named pseudo-widgets rather than hard-coded screen coordinates scattered throughout application code.

Conceptually:

```text
ThinkOrSwim window
        |
        v
reference geometry
        |
        v
pseudo-widget YAML
        |
        v
named controls / regions
        |
        v
GUI automation
```

## Current files

The repository currently contains versioned scanner layouts such as:

```text
layout_scanner3_v1p0.yaml
layout_scanner3_v1p1.yaml
layout_scanner3_v1p1dev1.yaml
layout_scanner3_v1p1dev2.yaml
```

and corresponding pseudo-widget tree reports such as:

```text
pwidget_tree_layout_scanner3_v1p1dev1.txt
pwidget_tree_layout_scanner3_v1p1dev2.txt
```

The YAML files are the layout definitions.

The `pwidget_tree_*.txt` files are human-readable tree dumps used for inspection and debugging.

## Selecting a layout

MasterBot/ThinkOrSwim tools normally select the active layout through:

```text
MB_PWIDGET_YAML
```

Example:

```cmd
set MB_PWIDGET_YAML=C:\Users\DanLa\Documents\github\pwidget_layouts\layout_scanner3_v1p1dev2.yaml
```

Persistent Windows configuration may instead be set with:

```cmd
setx MB_PWIDGET_YAML "C:\Users\DanLa\Documents\github\pwidget_layouts\layout_scanner3_v1p1dev2.yaml"
```

Configuration precedence is normally provided by `mb_tools`:

```text
project .env
    >
Windows environment
    >
mb_tools defaults.env
```

## Version naming

Current filenames use a convention such as:

```text
layout_scanner3_v1p1dev2.yaml
```

where:

```text
scanner3
    identifies the target scanner/layout family

v1p1
    major/minor layout version

dev2
    development revision
```

A development layout should normally be validated before being promoted to a stable version.

## What a pseudo-widget layout contains

A layout describes named GUI regions relative to one or more application windows.

Typical pseudo-widgets may represent:

* buttons;
* menu items;
* edit fields;
* scanner controls;
* Watchlist controls;
* export dialogs;
* import dialogs;
* other clickable or inspectable regions.

The automation layer can then request a named pseudo-widget such as:

```text
btn_wl_export_menu
```

instead of embedding a fixed coordinate directly in application code.

## ThinkOrSwim use

`ToS_scanner` uses these definitions for ThinkOrSwim GUI automation.

Examples of GUI operations that depend on pseudo-widget geometry include:

* scanner export;
* Watchlist export;
* Watchlist ADD;
* Watchlist REPLACE;
* interaction with the Symbols Import dialog;
* interaction with export dialogs.

Because these are GUI operations, correct layout selection is essential.

## Reference geometry

Pseudo-widget coordinates are typically defined relative to expected application-window geometry.

Related environment settings may include:

```text
MB_WIN_MAIN_REF_WIDTH
MB_WIN_MAIN_REF_HEIGHT
```

as well as configured ThinkOrSwim window-title patterns.

If a ThinkOrSwim window changes size, position, scale, or layout, the pseudo-widget geometry may need to be revalidated.

## DPI and display considerations

GUI automation can be affected by:

* Windows display scaling;
* screen resolution;
* application window size;
* ThinkOrSwim layout changes;
* font or UI scaling;
* dialog positioning;
* multiple-monitor arrangements.

A layout that works on one machine or display configuration should not automatically be assumed correct on another.

## Validation

Before using a new or modified layout for live GUI automation, validate it with the survey/debug tools.

Useful tools include:

```text
ToS_gui_survey
mb-window-survey
mb-pwidget-tree
```

`ToS_gui_survey` is useful for measuring and inspecting ThinkOrSwim windows and pseudo-widget geometry.

`mb-pwidget-tree` can be used to display the pseudo-widget hierarchy.

Generated tree reports may be saved in this repository for comparison with the corresponding YAML layout.

## Suggested development workflow

A typical layout-editing workflow is:

```text
1. Copy an existing working YAML layout.

2. Give the copy a new development version name.

3. Modify only the required pseudo-widget definitions.

4. Inspect the layout with ToS_gui_survey / mb-pwidget-tree.

5. Test individual GUI actions.

6. Test the affected ToS_scanner workflow.

7. Commit the YAML and updated tree report together.

8. Promote to a stable layout version only after validation.
```

## Relationship to application code

This repository intentionally contains layout data rather than ThinkOrSwim automation logic.

The separation is:

```text
pwidget_layouts
    geometry / pseudo-widget definitions

mb_tools
    pseudo-widget and window utilities

ToS_gui_survey
    geometry inspection / validation

ToS_scanner
    application behavior / GUI automation
```

This allows GUI geometry to evolve without requiring every coordinate change to be embedded directly in scanner code.

## Known limitations

Pseudo-widget automation remains dependent on the visible GUI.

Failures can occur if:

* a window is missing;
* a dialog appears in an unexpected location;
* another window covers the intended control;
* the active layout does not match the current ThinkOrSwim configuration;
* Windows scaling changes;
* ThinkOrSwim changes its UI.

A known example is the ThinkOrSwim `Symbols Import` dialog being partially obscured by another application window, causing an automated click to hit the wrong target.

For critical operations, downstream application logic should verify the resulting state rather than assuming a successful click.

## Repository scope

This repository should contain:

* pseudo-widget YAML layout definitions;
* useful human-readable layout/tree reports;
* documentation directly related to those layouts.

Application code should normally remain in the consuming repositories.

## Related repositories

### ToS_scanner

Consumes pseudo-widget layouts for live ThinkOrSwim scanner and Watchlist automation.

### ToS_gui_survey

Used to inspect and validate ThinkOrSwim GUI geometry.

### mb_tools

Provides reusable window and pseudo-widget utilities.

### schwab_watchlists

Uses the resulting ThinkOrSwim automation indirectly through the MasterBot Watchlist coordinator application.

## Security

Pseudo-widget layouts should contain GUI geometry and names only.

Do not store:

* passwords;
* brokerage credentials;
* API keys;
* account information;
* private tokens.

## Development status

The repository is actively used by the current ThinkOrSwim automation proof of concept.

Layouts may continue to change while the scanner and Watchlist-control architecture is being refined.
