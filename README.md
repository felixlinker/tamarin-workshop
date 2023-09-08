# Tamarin Workshop

This repository provides files for a 2h Tamarin workshop.

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
2. Run `brew install tamarin-prover/tap/tamarin-prover
`.

If brew exits with the error "Too many open files," rerunning the command usually fixes it.

## Launching Tamarin

For this workshop, you will want to launch tamarin with certain folders.
For example, to launch Tamarin for the first exercise, execute this command:

```sh
tamarin-prover interactive ex1/
```

Make sure that you check the output for warnings.
If there are none, navigate to http://127.0.0.1:3001 to see the GUI.
