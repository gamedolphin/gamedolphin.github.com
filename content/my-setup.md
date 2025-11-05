---
extra:
  comments: true
---
## Website
This website is built using [zola](https://www.getzola.org). The theme is [no-style-please](https://atgumx.gitlab.io/no-style-please/). The fonts are [iosevka](https://github.com/be5invis/Iosevka) variants. The colors are from the [catppuccin](https://catppuccin.com/).

On push to master to this [repository](https://github.com/gamedolphin/gamedolphin.github.com/), github actions trigger a zola build and push to the `gh-pages` branch. That branch name is special, because it then triggers a github pages deploy action to this website.

The iosevka font is trimmed down to about 12Kb using python font tools (inspired by [Xe](https://xeiaso.net/blog/iaso-fonts/)).

```bash
pyftsubset ./IosevkaAile-Regular.ttf \
--output-file=iosevkaaile-regular.woff2 \
--flavor=woff2 --no-hinting --desubroutinize \
--unicodes="U+20,U+21,U+23,U+27-29,U+2B-3A,U+40-50,U+52-57,U+59,U+61-7A,U+F6"
```

## Personal Setup

Most of my work and play happens on the following system :-

![system](/images/system.png)

### Software

As you might have noticed, I run Nixos and you can find my configuration in this [repository](https://github.com/gamedolphin/system).

I also exclusively use [emacs](https://www.gnu.org/software/emacs/) and my emacs [config](https://github.com/gamedolphin/system/tree/master/home/emacs) is now part of the nixos setup.

All the other computers that I use (office workstation, laptop) also conventiently share the same configuration so all my workstations have the same consistent experience, always.

This website shares my emacs theme as well as my desktop theme. These colors form the majority of almost all my computer interactions.

### Hardware

I have a single monitors, an AOC 32". I have the laptop mounted on a stand as a second monitor and [Varmilo Minilo 65%](https://varmilo.com/products/minilo65?variant=44504221745371) mechanical keyboard.

I record using the [Blue Yeti](https://www.logitechg.com/en-us/products/streaming-gear/yeti-premium-usb-microphone.988-000100.html) microphone.
