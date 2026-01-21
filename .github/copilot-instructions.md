# AI Agent Instructions for "A voided room"

## Project Overview
This is a **Twine interactive fiction game** (v2.11.1, Harlowe v3.3.9 format) - a single-page HTML5 application that delivers a narrative-driven puzzle game entirely within `index.html`. The game is self-contained with embedded CSS, JavaScript, and game data.

## Key Architecture Patterns

### 1. Game Structure (Twine Format)
- **Format**: Harlowe v3.3.9 - a DSL for interactive fiction
- **Location**: Game data embedded in `<tw-storydata>` element within `index.html`
- **Passages**: Individual story nodes defined as `<tw-passagedata>` elements with unique `pid` and `name` attributes
- **Links**: Created using `[[Display Text]]` or `[[Display Text|Target Passage]]` syntax
- **Example**: `[[Red door]]` creates a clickable link; `[[Enter door|Look around]]` navigates to "Look around" passage

### 2. Game State Management
**State Variables**: Used to track player progress and enable/disable content
- `$hasycube` - Boolean tracking yellow cube acquisition
- `$leverflicked` - Boolean for lever interaction status  
- `$buttonpressed` - Boolean for button puzzle state

**Initialization**: Variables set in "Start" passage (pid=6):
```
(set: $hasycube to false) (set: $leverflicked to false)(set:$buttonpressed to false)
```

**Conditional Logic**: Uses Harlowe `(if:)` macro for branching:
```
(if: $hasycube)[
    [[Place cube|Yellow cube]]
]
(else:)[
    [[Return|Black door]]
]
```

### 3. Game Flow Architecture
- **Entry Point**: Passage "Start" (pid=6, startnode attribute)
- **Core Hub**: "Start1" (pid=1) - branch point to Black door and Red door
- **Puzzle Interdependencies**:
  - Yellow cube (found at pid=9) unlocks Cube interaction (pid=11)
  - Lever activation (pid=17, sets `$leverflicked`) unlocks sphere rolling (pid=14-15)
  - Button press (pid=23, sets `$buttonpressed`) enables triangle descent (pid=12, 24)
- **Ending Paths**: All lead to either "Rolly" (pid=15), "Intriangle" (pid=24), or "Next" (pid=28) with geometry images

### 4. Content Patterns
- **Imagery**: External images embedded in some passages using HTML divs with `<img>` tags
  - Geometry Prism (Rolly ending)
  - Triangle icon (Intriangle ending)
  - Polyhedron (Next ending)
- **Narrative Style**: Minimalist descriptive text with puzzle-driven progression
- **Navigation**: Every path includes return options (`[[Head back|Start1]]`, `[[Return|...]]`)

## Development Workflow

### Editing Game Content
**Tool**: Twine 2 editor (not direct HTML editing recommended)
- Open `index.html` in Twine 2 GUI to edit passages visually
- Manual HTML editing is **fragile** - requires careful XML structure within `<tw-storydata>`
- After editing in Twine, publish/export to update `index.html`

### Adding New Passages
When adding content:
1. Assign unique `pid` (highest current: 30)
2. Use unique `name` attribute (no duplicates)
3. Position attributes control editor layout (not gameplay)
4. Update referencing passages with new `[[Link|New Passage]]` entries
5. Consider state variable implications for puzzle logic

### Modifying State Logic
- State mutations: `(set: $variable to value)`
- Comparisons: `(if: $variable)[content](else:)[alt]`
- Boolean toggle pattern: Set true on trigger, check before unlocking dependent content

## Testing & Validation

### Game Walkthrough
Critical puzzle chain (validates state logic):
1. Start → Look around → Start1 (Black/Red door branches)
2. **Blue path**: Red door → Search shelves → Keep searching → grab yellow cube (`$hasycube = true`)
3. **Green path**: Red door → Enter staircase → Flick lever (`$leverflicked = true`)
4. **Button path**: Red door → dark room → Press button (`$buttonpressed = true`)
5. Only with all states true: Triangle→Sphere→Cube interactions fully unlock

### Common Issues
- **Links break silently** if target passage name misspelled (no error warnings in Harlowe)
- **State not persisting**: Variables must be set with `(set:)` not just referenced
- **Missing returns**: Dead ends (passages with no outbound links) trap players; always include exit options

## Key Files & References
- [index.html](index.html) - Single-file game container
- Twine 2: https://twinery.org/
- Harlowe 3 Documentation: https://twine2.neocities.org/

## Project Conventions
- **Naming**: Passage names are conversational ("Red door", "Sphere", "Keep searching") not technical IDs
- **Structure**: Hub-and-spoke topology with "Start1" as main hub
- **Scope**: Minimal - ~30 passages, ~3 core state variables, entirely client-side
- **No build process**: Game is publication-ready HTML; deployment is direct file hosting

## Maintenance Notes
- Repository: GitHub Pages (`.github.io` domain)
- No server-side logic or external API dependencies
- All game data embedded → keep `index.html` single file for deployment simplicity
- Git tracks HTML changes directly (uncommented state diffs may be verbose due to embedded CSS/JS)
