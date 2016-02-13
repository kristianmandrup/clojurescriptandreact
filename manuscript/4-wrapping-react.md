# Wrapping React

Now that we have a simple  Clojure based React up and running, we can add additional power to our infrastructure.
React provides a basic highly performant View rendering engine, using Virtual DOM. 

However a data driven application needs more power:

The language [Elm](http://elm-lang.org/) inspired a new way of designing VDOM bases apps, using a single global immutable application state. Elm like Clojure, compiles to Javascript and has native support for immutable data structures.

By having a single immutable app state, it becomes easy to achieve undo/redo and thus traverse through application as a stream of signals/events that affect the app state over time, as the Elm inventor Evan Czaplicki explains in this [video](https://www.youtube.com/watch?v=Agu6jipKfYw).

This inspired Facebook to come up with their own pure javascript library [Immutable.js](https://facebook.github.io/immutable-js/) for Javascript developers to get the same benefits. The framework [Redux]() takes this a step further.

- [redux-immutable](https://github.com/gajus/redux-immutable)


[Relay](https://facebook.github.io/relay/) is a framework for Data driven apps, where each component contains a declarative query that defines the data which it needs. A framework such as GraphQL is then expected to use this query to fetch the data from the server and deliver the changeset to the component.

- Declare your data requirements using GraphQL and let Relay figure out how and when to fetch your data.
- Queries live next to the views that rely on them, so you can easily reason about your app. 
- Aggregates queries into efficient network requests to fetch only what you need.
- Mutate data on the client and server using GraphQL mutations. 
- Offers automatic data consistency, optimistic updates, and error handling

Om.next is designed with many of the same goals in mind.


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