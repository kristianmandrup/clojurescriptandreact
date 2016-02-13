# React Native apps with Clojure

Now we are ready to re-configure our project to use Clojure instead of the default Javascript.

[Boot](http://boot-clj.com/)

## Immutable application state

One of the super powers of clojure is that it comes built in with Immutable data types only. This provides a number of benefits to modern
app development, namely:

- ...
- Undo/Redo

## Choosing a React wrapper

The Clojure community have developed several popular light-weight React wrapper frameworks, each with a particular flavor.

- Om.next by David Nolen
- Reagent
- Quiscient
- Rum (by @tonsky)

### Reagent

### Quiscient

### Om.next

Builds on the experience with the former [Om](https://github.com/omcljs/om/). The latest version uses a graph approach similar to [GraphQL](https://facebook.github.io/graphql/) from Facebook and [Falcor](http://netflix.github.io/falcor/) by Netflix, where the client requests a graph of data from the server to be used to update the client view state.

Om supports features not currently present in React:

- Global state management facilities built in
- Components may have arbitrary data dependencies, not limited to props & state
- Component construction can be intercepted via :instrument. Simplifies debugging components and generic editors.
- Provides stream of all application state change deltas via :tx-listen. Simplifies synchronization online and offline.
- Customizable semantics. Fine grained control over how components store state, even for components outside of your control. Simplifies using Om components outside the Om framework, debugging, and adding event hooks not anticipated by original component designer.

```cljs
(ns example
  (:require [om.core :as om]
            [om.dom :as dom]))

(defn widget [data owner]
  (reify
    om/IRender
    (render [this]
      (dom/h1 nil (:text data)))))

(om/root widget {:text "Hello world!"}
  {:target (. js/document (getElementById "my-app"))})
```
