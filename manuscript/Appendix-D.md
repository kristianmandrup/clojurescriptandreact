# Clojure environment setup

## Build tool

- Leinigen (declarative)
- Boot (programmable)

### Leinigen

[Leinigen](http://leiningen.org/) was the first build manager for Clojure. It was designed to be entirely declarative in nature, as that was the standard of the day at the time (Make, Ant, Grunt, ...).

[Leiningen Versus the Ants](https://en.wikipedia.org/wiki/Leiningen_Versus_the_Ants)

A Leinigen project config file looks something like this:

```cljs
(defproject leiningen.org "1.0.0"
  :description "Generate static HTML for http://leiningen.org"
  :dependencies [[enlive "1.0.1"]
                 [cheshire "4.0.0"]
                 [org.markdownj/markdownj "0.3.0-1.0.2b4"]]
  :main leiningen.web)
```

#### Installation

- Download the [lein](https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein) script (or on Windows [lein.bat](https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein.bat))
- Place it on your `$PATH` where your shell can find it (eg. `~/bin`)
- Set it to be executable (`chmod a+x ~/bin/lein`)
- Run `lein` in a shell and it will download the self-install package

### Boot

Boot was designed much later that Leinigen and was a response to recent trends with respect to build tools. A declarative tool is fine for simple build scripts that are static in nature. However with apps becoming ever more complex and having to build to various different environments, a pure declarative syntax can quickly become limiting and constrictive. 

Boot adresses this by providing composable functions and some basic conventions to build your custom build scripts from.
This allows for you to fully leberage the power of Clojure and create dynamic build scripts that react to their environment and context.

**Features**

- Write executable, self-contained scripts in Clojure and run them with or without a project context.
- Dynamically add dependencies from Maven repositories to the running script's class path.
- Managed filesystem tree provides a scoped, immutable, append-only interface.
- Fine-grained control of classloader isolation–run code in separate Clojure runtimes.
- Tasks are functions that return middleware which compose to form build pipelines.
- Tasks are not coupled via hardcoded file paths or magical keys in a global configuration map.
- Create new, ad-hoc tasks easily in the project, in the build script, or in the REPL.
- Compose build pipelines in the project, in the build script, in the REPL, or on the command line.
- Artifacts can never be stale–there is no need for a clean task.

#### Installation

Via [Homebrew](http://brew.sh/) on Mac OSX: `brew install boot-clj`
Via [Nix](http://nixos.org/nix/) on Unix: `nix — nix-env -i boot`