---
title: "Literate System Configuration"
taxonomies:
  tags: ["blog", "website", "nixos", "emacs", "org"]
extra:
  comments: true
---

I use [nixos](https://nixos.wiki/) to setup my Linux workstations. This provides an immutable, stable system.
And when I break it, I can just revert to the previous stable version.

I might have destroyed my linux knowledge because of this. I now fix things through Nix configuration instead.

Over the holidays, I also spent some time learning to use [org mode](https://orgmode.org/) inside Emacs.
It's a bunch of things packed into a super powerful built-in plugin.
I have seen many "literate"  emacs configurations in the [wild](https://protesilaos.com/codelog/2023-12-18-emacs-org-advanced-literate-conf/).
They are just long org documents that also behave as the configuration file for emacs itself.

Well, if you can configure emacs using this, then surely you can configure your whole system if you combine it with nixos.
And that is exactly what I did.

You can find my literate system configuration [here](https://github.com/gamedolphin/system).
It describes (and configures) all the software for both my workstations (the wfh desktop and travel laptop).
I also managed to integrate [sops](https://github.com/Mic92/sops-nix) this time around and so the repo actually completely configures my system, *including* secrets like ssh and api keys.

The expected workflow for me, going forward, is to edit the README file to make my changes and then run `org-tangle` to generate the nix files.
Once the nix files are ready, I can do the usual `nixos-rebuild` to apply the changes.

I have added a snippet at the bottom of the README that actually adds a "DO NOT MODIFY" header to all the generated files.
This should be fair warning, mainly for me, to not edit these directly, lest I forget and risk losing those changes.

``` emacs-lisp
  (progn
    (defun add-tangle-headers ()
      (message "running in %s" (buffer-file-name))
      (when (string= (file-name-extension (buffer-file-name)) "nix")
        (goto-char (point-min))
        (insert "# WARNING : This file was generated by README.org\n# DO NOT MODIFY THIS FILE!\n# Any changes made here will be overwritten.\n")
        (save-buffer))
      (save-buffer))
    (add-hook 'org-babel-post-tangle-hook 'add-tangle-headers))
```

### Github CI/CD

Finally, the github repo also has a [github action](https://github.com/gamedolphin/system/actions/workflows/tangle.yml) configured to run the tangling automatically on changing the README.
That will keep the generated files up to date in case I forget to push them.
I could have skipped adding the generated files BUT nix flakes behaves wierdly if some files are in git, and others are not.
It just makes life easier when applying the changes.

The only addition here is that the tangling command has to allow the header insertion code to run. That is why the command looks this long in the action.

``` bash
emacs README.org --batch --eval "(setq org-confirm-babel-evaluate nil)" --eval "(progn (require 'org) (org-babel-goto-named-src-block \"startup\") (org-babel-execute-src-block))" -f org-babel-tangle
```

This should also keep me from breaking the tangling setup.

### Just read it
You can just read the file and see how I've setup my computer. You can also just read the raw file to see how I've setup org mode tangling! This setup gives me a documented, reproducible system configuration that's easy to maintain and share. Cheers!