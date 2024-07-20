---
# You can also start simply with 'default'
theme: dracula

# some information about your slides (markdown enabled)
title: Introduction to Nix

# apply unocss classes to the current slide
class: text-center
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# https://sli.dev/guide/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/guide/syntax#mdc-syntax
mdc: true

download: true

defaults:
  layout: default

layout: intro
---

# Introduction to Nix

<img class="h-40 nix-snowflake" src="/nix-snowflake-white.svg" />

<style>
  .nix-snowflake { margin: 0 auto };
</style>


---

# What is Nix?

<br />

<div v-click>
  <h3>🧙 Functional programming language</h3>
  <p class="description">"It's just JSON with functions."</p>
</div>
<br />
<div v-click>
  <h3>📦 Package manager</h3>
  <p class="description">The largest software repository on the planet with more than 100.000 packages</p>
</div>
<br />
<div v-click>
  <h3>🐧 Operating system (NixOS)</h3>
  <p class="description">Linux distribution built around the Nix package manager</p>
</div>

<style>
  .description {
    margin-left: 2.5rem;
  }
</style>

---
layout: two-cols
---

# Why is it cool?

<div v-click>
  <h3>Reproducible</h3>
  Nix aims to provide fully reproducible results.<br />
  No more "It works on my machine".

  <img class="h-80 mt-5" src="/works-on-my-machine.png" />
</div><br />

::right::

# &nbsp;

<div v-click>
  <h3>Declarative</h3>
  Instead of writing imperative scripts like <code>sudo apt-get install this; sudo apt-get install that;</code> you write declarative configs and let Nix take care of the rest.
</div><br />

<div v-click>
  <h3>Crazy powerful</h3>
  Nix is a complete progamming language designed around software packaging and you can do insane things with it. Example later 😁
</div><br />

<div v-click>
  <h3>Runs everywhere</h3>
  Nix can be installed nearly everwhere: Windows, MacOS, Linux (not only NixOS), Docker containers, Github runners, ...
</div>

---
layout: two-cols
---

# Pitfalls
<h5 class="subtitle">(and how to avoid them)</h5>

<br />
<br />

### Documentation confusion
There are two different main concepts:  
Channels & Flakes
  - Channels are the original "old" concept  
    Shell: `nix-<something>`
  - Flakes are the new way of doing things  
    Shell: `nix <something>`

👉 **We mainly care about flakes** 👈

### Installation

Use the [Determinate Systems Nix installer](https://github.com/DeterminateSystems/nix-installer) instead of the official one. Flakes are enabled by default and uninstalling is more easy.

::right::

<img class="h-80 ml-20 mt-20" src="/pitfall.png" />

<style>
  h1 { display: inline; margin: 0; }
  .subtitle { display: inline; margin: 0; margin-left: 1ch; }
</style>

---

# Benefits

- **All users ¹ use the same versions of their development tools**
- Users ¹ do not have to manually set up or update their environment
- Packages are fully isolated. Installing multiple versions of the same software is no problem ²
  ```bash
  $ which node
  /nix/store/74a6mmrgxzcg9axl1gmdz3j2y1bjd13f-nodejs-20.12.2/bin/node
  ```
- Switching between different environments can be fully automated via [direnv](https://direnv.net/) 😻

<br/>
<br/>

¹ __Users doesn't only mean developers. CI is a user, too__  
² __Really, it isn't__

---
layout: quote
---

That slide you should remember ™
# **flake.nix** & **flake.lock**
# are like
# **package.json** & **package-lock.json**
# for your environment

---

# Useful commands

| command | description |
| --- | --- |
| `nix develop` | Start a development shell |
| `nix run` | Run package - can also use URLs to run things directly off the web |
| `nix search nixpkgs <name>`| Search the package registry for `<name>`¹ |
| `nix flake update` | Update packages and `flake.lock` lockfile |

<br/>
<br/>

¹ __Alternatively, you can use the online search at [search.nixos.org](https://search.nixos.org)__

---

# Anatomy of a Flake ❄️

```nix {all}{maxHeight:'420px',lines:true}
{
  description = "Example flake for a node and typescript project";

  inputs = {
    nixpkgs.url = "github:nixos/nixpkgs/nixos-24.05";
    flake-utils.url = "github:numtide/flake-utils";
  };

  outputs = { self, nixpkgs, flake-utils }:
    flake-utils.lib.eachDefaultSystem (system:
      let
        pkgs = nixpkgs.legacyPackages.${system};
      in
      {
        devShells = {
          default = pkgs.mkShell {
            buildInputs = [
              pkgs.colima
              pkgs.cowsay # moo
              pkgs.docker-client
              pkgs.git
              # pkgs.nodejs
              # You can set the major version of Node.js to a specific one instead
              # of the default version
              pkgs.nodejs_20
              # You can choose pnpm, yarn, or none (npm).
              pkgs.nodePackages.pnpm
              pkgs.nodePackages.typescript
              pkgs.nodePackages.typescript-language-server
            ];

            shellHook = ''
              cowsay "Welcome to your dev environment!"

              if [ ! -d "node_modules" ]; then
                echo ""
                echo "node_modules not found, installing..."
                pnpm install
              fi

              echo ""
              echo "Helpful commands:"
              echo "pnpm dev - start development server"
              echo "pnpm test - run tests"
              echo "pnpm build - build the application"
            '';
          };
        };
      });
}
```

---

# Crazy stuff 🤪

[NixThePlanet](https://github.com/MatthewCroughan/NixThePlanet) is a flake that lets you install and run Mac OS X  
as well as old Windows versions with a **single nix command**.

<v-switch>
  <template #0>
    <img class="h-90 mt-5" src="/screenshot-win311.png" />
  </template>
  <template #1>
    <Youtube id="fFIDf8ZWX2w?start=205" width="640" height="360" />
  </template>
</v-switch>

---
layout: image
image: /would-you-like-to-know-more.png
backgroundSize: contain
---

---
layout: two-cols
---

<img class="h-80 mt-20" src="/desire-to-know-more-intensifies.gif" />

::right::

<br />

### More

- Official homepage  
  https://nixos.org/

- Zero to Nix, "Your guide to learning Nix and flakes"  
  https://zero-to-nix.com/

- Flakes schema explained  
  https://nixos.wiki/wiki/Flakes

- **home-manager** for your CLI apps and dotfiles  
  https://nix-community.github.io/home-manager/

  Setup guide for **home-manager** on Mac OS X  
  https://dev.to/synecdokey/nix-on-macos-2oj3

---
layout: end
---

# Thank you!
# 🙇‍♀️
