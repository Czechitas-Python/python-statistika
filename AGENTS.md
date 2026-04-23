# Content Structure Guide For Codex

## General Rules

- **Always create the actual MD files.** When adding a section or converting a notebook, write the `.md` file with the full content. Do not just describe what the file should contain or show it as a code block.

This repository is a web course about statistics in Python. Content is organized in a strict hierarchy driven by `entry.yml` files.

## Directory Hierarchy

```text
entry.yml                          <- course definition
python-statistika/                 <- chapter directory
  entry.yml                        <- chapter definition
  <lesson-name>/                   <- lesson directory
    entry.yml                      <- lesson definition
    <section-name>.md              <- section content page
    assets/                        <- images and CSV files for the lesson
    excs/
      <exercise-name>/             <- exercise with its own assets
        exercise.md
        assets/
      <exercise-name>.md           <- standalone exercise (no assets)
```

## `entry.yml` Files

### Course (`entry.yml` at root)

```yaml
title: Course title
lead: Short description
image: assets/image.svg
chapters:
  - chapter-directory-name
```

### Chapter (`<chapter>/entry.yml`)

```yaml
title: Chapter title
lead: Short description
lessons:
  - lesson-directory-name
```

### Lesson (`<chapter>/<lesson>/entry.yml`)

```yaml
title: Lesson title
lead: Short description
sections:
  - section-filename-without-extension
```

## Section MD Files

Section files have no frontmatter. They start directly with content.

```markdown
## Section Heading

Introductory text...

::fig[]{src=assets/image.png}

Explanation text with :term{cs="Czech term" en="English term"} inline terminology.

```python
import pandas as pd
data = pd.read_csv("file.csv")
```

## Cvičení

::exc[excs/exercise-name]
```

### Special MD Directives

| Directive | Purpose |
|-----------|---------|
| `::fig[]{src=assets/image.png}` | Embed an image |
| `::exc[excs/exercise-name]` | Embed an exercise |
| `:term{cs="..." en="..."}` | Inline bilingual term |

## Exercise Files (`exercise.md`)

Exercises use YAML frontmatter:

```markdown
---
title: Exercise Title
demand: 2
---

Exercise description text...

```python
# starter code
```

:::solution

**Solution explanation:**

```python
# solution code
```

:::
```

- `demand`: difficulty level (`1` = easy, `2` = medium, `3` = hard)
- The `:::solution ... :::` block is optional and hidden by default in the web UI.

## How To Add New Content

### Add a new section to an existing lesson

1. Create `<chapter>/<lesson>/<section-name>.md` with the section content.
2. Add the filename without `.md` to the `sections` list in `<chapter>/<lesson>/entry.yml`.
3. Place any images or CSV files in `<chapter>/<lesson>/assets/`.

### Add a new exercise to an existing lesson

With its own assets:

1. Create directory `<chapter>/<lesson>/excs/<exercise-name>/`.
2. Create `exercise.md` inside it with frontmatter.
3. Place data files and images in `<chapter>/<lesson>/excs/<exercise-name>/assets/`.
4. Reference it in a section with `::exc[excs/<exercise-name>]`.

Without assets (standalone):

1. Create `<chapter>/<lesson>/excs/<exercise-name>.md` with frontmatter.
2. Reference it in a section with `::exc[excs/<exercise-name>]`.

### Add a new lesson to an existing chapter

1. Create directory `<chapter>/<lesson-name>/`.
2. Create `entry.yml` inside it with `title`, `lead`, and `sections`.
3. Create section MD files referenced in the `sections` list.
4. Add `<lesson-name>` to the `lessons` list in `<chapter>/entry.yml`.

### Add a new chapter

1. Create directory `<chapter-name>/` alongside `python-statistika/`.
2. Create `entry.yml` inside it with `title`, `lead`, and `lessons`.
3. Create lesson subdirectories with their own `entry.yml` and section files.
4. Add `<chapter-name>` to the `chapters` list in the root `entry.yml`.

## Converting A Jupyter Notebook To A Section

Use a notebook-reading workflow that renders notebook cells and outputs directly. In this environment, inspect the `.ipynb` contents with available file-reading tools rather than relying on Claude-specific tooling.

### Step-by-step process

1. Read the notebook.

Inspect all cells from `path/to/notebook.ipynb`, including markdown, code, and outputs.

2. Handle data files.

Notebooks reference CSV files with a local path such as `pd.read_csv("house_prices.csv")`. The source files sit in the top repository directory. Copy them into the lesson's `assets/` directory:

```text
<lesson>/assets/house_prices.csv
```

Do not change the path in the code. Existing sections reference data files by filename only, with no directory prefix:

```python
# correct
data = pd.read_csv("house_prices.csv")

# wrong
data = pd.read_csv("assets/house_prices.csv")
```

3. Convert markdown cells.

Paste the cell content directly into the `.md` file with no unnecessary transformation.

4. Convert code cells.

Wrap the cell content in a fenced Python block:

```markdown
```python
import pandas as pd
data = pd.read_csv("house_prices.csv")
data.head()
```
```

5. Handle plot outputs.

When a code cell produces a plot:

1. Save the rendered plot image as a PNG in the lesson's `assets/` directory, for example `assets/plot_01.png`.
2. Insert it in the MD file immediately after the code block that generates it, using the repo image directive instead of standard markdown image syntax:

```markdown
::fig[]{src=assets/plot_01.png}
```

6. Register the new section.

Add the filename without `.md` to the `sections` list in the lesson's `entry.yml`.

### Example conversion

| Notebook cell | MD output |
|---------------|-----------|
| Markdown cell | Plain text and headings pasted as-is |
| Code cell with no output | ` ```python ... ``` ` block |
| Code cell with plot output | ` ```python ... ``` ` block followed by `::fig[]{src=assets/plot_N.png}` |
| Code cell with text output | ` ```python ... ``` ` block; include printed output as prose if useful, or omit it if self-evident |

## Extracting Exercises From A Jupyter Notebook

Lecture notebooks (`lekce.ipynb`) typically end with a `# Cvičení` section containing one or more exercises marked with `## Exercise Name` headings. Solutions are in the companion `reseni.ipynb`.

### Step-by-step process

1. Identify exercise cells.

Find all cells in `lekce.ipynb` that appear after the `# Cvičení` heading. Each `## Heading` marks a new exercise. A `## Bonus` heading marks bonus exercises.

2. Create exercise files.

For each exercise create `excs/<slugified-name>/exercise.md` with an `assets/` subfolder if the exercise references data files:

```markdown
---
title: Exercise Title
demand: 2
---

Exercise description text...

```python
# starter code if provided
```
```

- Slugify the exercise name: lowercase, hyphens, no diacritics, for example `## NBA` -> `nba`
- Set `demand: 2` for regular exercises and `demand: 3` for bonus exercises

3. Add solutions from `reseni.ipynb`.

Find the corresponding cells in `reseni.ipynb` and append them inside a `:::solution` block:

```markdown
:::solution

Explanatory text if any from markdown cells in `reseni.ipynb`...

```python
# solution code from reseni.ipynb
```

:::
```

4. Reference exercises from the section.

At the end of the last section MD file, add:

```markdown
## Cvičení

::exc[excs/exercise-name]
::exc[excs/exercise-name-2]

#### Bonusy

::exc[excs/bonus-exercise-name]
```

- Regular exercises go directly under `## Cvičení`
- Bonus exercises go under `#### Bonusy`
- Each `::exc[]` is on its own line

5. Copy data files.

Data files referenced in exercise cells go into `excs/<exercise-name>/assets/`. Code references use just the filename with no path prefix.

## Naming Conventions

- Use lowercase Czech words with hyphens and no diacritics: `testovani-hypotez`, `korelace-regrese`
- Section names map directly to filenames: `section: formulace` -> `formulace.md`
- Asset files use descriptive names with underscores, for example `student_results.csv`, `ada_01.png`
