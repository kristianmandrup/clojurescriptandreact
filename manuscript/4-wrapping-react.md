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

## Reagent

Reagent provides a simple interface between ClojureScript and React which lets you define React components using plain ClojureScript functions and data to describe your UI.

A simple Reagent component:

```cljs
(defn simple-component []
  [:div
   [:p "I am a component!"]
   [:p.someclass
    "I have " [:strong "bold"]
    [:span {:style {:color "red"}} " and red "] "text."]])
```

You can compose components like this:

```cljs
(defn hello-component [name]
  [:p "Hello, " name "!"])

(defn say-hello []
  [hello-component "world"])
```

### State management

Reagent uses its own version of an immutable `atom`. 
It works exactly like the one in `clojure.core`, except that it has a deref trigger mechanism. 
Any component that uses (derefs) this `atom` is re-rendered when the atom changes value.

```cljs
(ns example
  (:require [reagent.core :as r]))

;; use reagent atom as global state
;; simple counter initialized to 0
(def click-count (r/atom 0)) 

(defn counting-component []
  [:div
   "The atom " [:code "click-count"] " has value: "

   ;; deref the @click-count atom, 
   ;; sets up change listener for re-render
   @click-count ". "

   [:input {:type "button" :value "Click me!"
            ;; increase click count
            ;; atomically swap the click-count atom with new value
            :on-click #(swap! click-count inc)}]])
```

Reagent supports most of React’s API, but there is really only one entry-point that is necessary for most applications: `reagent.core/render-component`

### Performance

Mounted components are only re-rendered when their parameters have changed. The change could come from a deref’ed `atom`, the arguments passed to the component or component state.

All of these are checked for changes with `identical?` which is a pointer comparison, so the overhead is very low.

## Om.next

Om is a framework built by the father of ClojureScript, David Nolen. Om/next builds on the experience with the first version of [Om](https://github.com/omcljs/om/). Om used the concept of cursors to only expose part of the global state to each component.

The latest version of Om discards the use of cursors and takes a very different route. It now uses a query approach using graph data similar to [GraphQL](https://facebook.github.io/graphql/) from Facebook and [Falcor](http://netflix.github.io/falcor/) by Netflix. 
The client instead requests a graph of data from the server, which is normalized and used to update the client app state, which triggers a re-render of affected view components.

Om.next features:

- Global state management facilities built in
- Components may have arbitrary data dependencies, not limited to props & state
- Component construction can be intercepted via :instrument. 
- Simplifies debugging components and generic editors.
- Provides stream of all application state change deltas via :tx-listen. 
- Simplifies synchronization online and offline.
- Customizable semantics. Fine grained control over how components store state, even for components outside of your control. 
- Simplifies using Om components outside the Om framework, debugging, and adding event hooks not anticipated by original component designer.

Om.next uses protocols such as `om/IRender` and ...

A simple Om.next component called `my-widget`:

```cljs
(ns example
  (:require [om.core :as om]
            [om.dom :as dom]))

(defn widget [data owner]
  (reify
    om/IRender
    (render [this]
      (dom/h1 nil (:text data)))))
```

Mount the om/root `my-widget` component on the DOM element `my-app`

```cljs
(om/root widget {:text "Hello world!"}
  {:target (. js/document (getElementById "my-app"))})
```