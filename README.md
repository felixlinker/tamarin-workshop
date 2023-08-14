# Tamarin Workshop

This repository provides files for a 2h Tamarin workshop.

## Installation

Here's a tl;dr version of the [full installation instructions](https://tamarin-prover.github.io/manual/master/book/002_installation.html):

1. Install [brew](https://brew.sh/).
2. Run `brew install tamarin-prover/tap/tamarin-prover
`.

For Windows, use [WSL](https://learn.microsoft.com/windows/wsl/install).

If you want to use syntax highlighting, we suggest using [Visual Studio Code](https://code.visualstudio.com/) with [this extension](https://marketplace.visualstudio.com/items?itemName=gilcu3.tamarin) (search for extension with ID `gilcu3.tamarin`).

## Launching Tamarin

For this workshop, you will want to launch tamarin with certain folders.
For example, to launch Tamarin for the first exercise, execute this command:

```sh
tamarin-prover interactive ex1/
```

Make sure that you check the output for warnings.
If there are none, navigate to http://127.0.0.1:3001 to see the GUI.
