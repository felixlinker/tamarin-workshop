# Tamarin Workshop

This repository provides files for a 2-4h Tamarin workshop.
Note that all exercises are self-contained and can be solved for individual training as well.
Make sure that you use the cheat sheet (`chetsheet.md`) included in this repository and the [official documentation](https://tamarin-prover.github.io/manual/master/book/001_introduction.html).

The workshop structure is planned as follows:

- 20 minute presentation: Introduction to Tamarin and live demo of minimal example (`solutions/TCP.spthy`).
- 100 minutes: Working on exercises 1+2.
- 15 minute presentation: How has Tamarin been applied in the past?
How is it to analyze a real-world specification?
- Rest of the time: Working on exercise 3.

## Materials

This repository contains the following materials.

- Each folder `/ex...` contains the material for the respective exercise.
- The folder `/solutions` contains the respective solutions.
It contains solutions by me (Felix), but also by community members!
- `cheatsheet.md` is an incomplete reference for the Tamarin syntax.
- The file `Tamarin basics - dependency graphs.pdf` contains a helpful illustration of the GUI output that [Cas Cremers](https://cispa.de/en/people/cas.cremers) created.
- Finally, Tamarin is [well-documented online](https://tamarin-prover.github.io/manual/master/book/001_introduction.html) ([pdf version](https://tamarin-prover.github.io/manual/master/tex/tamarin-manual.pdf)) and we encourage you to use the documentation!

## Workshop Preparation

If you plan to attend this workshop in person, we suggest that you:

1. Either clone or download this repository. The folders `ex1/` and `ex2/` contain skeleton files that you should modify.
2. Install Tamarin (see [Installation](#installation)).

Depending on your editor, you might prefer the preview for the exercise instructions here on GitHub to get proper markdown rendering.

## Installation

Here's a tl;dr version of the [full installation instructions](https://tamarin-prover.github.io/manual/master/book/002_installation.html).

For Windows, use [WSL](https://learn.microsoft.com/windows/wsl/install).

If you want to use syntax highlighting, we suggest using [Visual Studio Code](https://code.visualstudio.com/) with [this extension](https://marketplace.visualstudio.com/items?itemName=gilcu3.tamarin) (search for extension with ID `gilcu3.tamarin`).

There is also syntax highlighting for [Sublime Text 3](https://github.com/tamarin-prover/editor-sublime) available.
You can install syntax highlighting using "Package Control."

### Manually

1. Install graphviz: `sudo apt install graphviz`
2. Install [Maude](https://github.com/SRI-CSL/Maude/releases/tag/Maude3.3.1) (click link, download, unzip, and put on path). Note: rename `maude.linux` to `maude`.
3. Install [Tamarin](https://github.com/tamarin-prover/tamarin-prover/releases/tag/1.8.0) (click link, download `tamarin-prover-1.8.0-linux64-ubuntu.tar.gz`, unzip, and put on path).

### Using Brew

This method works on all platforms (provided you use WSL on Windows).

1. Install [brew](https://brew.sh/).
2. Run `brew install tamarin-prover/tap/tamarin-prover`.

If brew exits with the error "Too many open files," rerunning the command usually fixes it.

## Launching Tamarin

For this workshop, you will want to launch tamarin with certain folders.
For example, to launch Tamarin for the first exercise, execute this command:

```sh
tamarin-prover interactive ex1/
```

Make sure that you check the output for warnings.
If there are none, navigate to http://127.0.0.1:3001 to see the GUI.

Note that you need to restart Tamarin whenever you changed your model (i.e., you changed a `*.spthy` file)!
