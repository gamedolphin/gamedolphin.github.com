# Personal Website 

![Github Deploy Status](https://github.com/gamedolphin/gamedolphin.github.com/actions/workflows/deploy.yaml/badge.svg)

This repository contains the source for my [personal website](https://www.sandeepnambiar.com).

The content has been ported from the old jekyll repository found [here](https://www.github.com/gamedolphin/gamedolphin.old.github.com).

## Description

The website is deployed on [github pages](https://pages.github.com/) and is built using [zola](https://www.getzola.org/).

The theme is a fork of [no style, please!](https://github.com/atgumx/no-style-please).

On each `v*` tag, a github action is run that replaces the contents on the [gh-pages branch](https://github.com/gamedolphin/gamedolphin.github.com/tree/gh-pages) and then github pages automatically deploys it. 

## Getting Started

### Dependencies

* git clone with submodules (to get the theme)
* zola

### Local Dev

```
zola serve
```

## Version History
* 1.1.0
    * My personal setup and this website described in a small post.
* 1.0.0 - 1.0.5
    * Fixing github deployment.
    * Font fixes.
    * Size optimizations.
* 0.0.0
    * Initial porting of the website.

## License

This project is licensed under the MIT License - see the LICENSE.txt file for details

## Acknowledgments

Inspiration, code snippets, etc.
* [no style, please!](https://github.com/atgumx/no-style-please)
* [Fran's resume](https://frarees.github.io/)
