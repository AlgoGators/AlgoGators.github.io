# AlgoGators Documentation
The AlgoGators documention can be view [here](https://algogators.github.io/). The documention has been built with the `mdBook` tool that allows for straightforward documention hosting.

## Dev Rules

### Cloning
When working with the AlgoBook clone the repository by running either:
```bash
git clone https://github.com/AlgoGators/AlgoGators.github.io.git algobook
```
**or**
```bash
git clone git@github.com:AlgoGators/AlgoGators.github.io.git algobook
```

This cloning operation renames the main directory to "algobook" instead of the GH Pages URL.

### Development
When making contributions to the "AlgoBook" work with a development branch off of a greater development branch of off of `main`, usually called `dev` or some variation. Understand when merging with main, you will kick-off the building and deployment workflows. We want to minimize how often we run these workflows and only merge our development branches when an iteration of development has finished, like a sprint. 

## Installation
To use and view the documentation, install `mdBook` either through `cargo` or a pre-compiled binary. The process of installation is within `mdBook`s documention.

## Local Viewing
To host and view the documentation, you must have `mdBook` installed.

To build:
```
mdbook build
```
And to serve:
```
mdbook serve --open
```
