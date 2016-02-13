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

### Om vs Reagent comparison

Om and Reagent make different design decisions that result in different tradeoffs, each with its own strength and weaknesses. Which of these libraries is better primarily depends on the problem you're solving.

The biggest difference between Om and Reagent is that Om is highly prescriptive in regards to state management in order to ensure that components are reusable. It's an anti-pattern for Om components to manipulate the global state directly or by calling functions to do so. Instead, components are expected to communicate using core.async channels. This is done to ensure high modularity of the components. Reagent leaves this part of the design up to you and allows using a combination of global and local states as you see fit.

Om takes a data centric view of the world by being agnostic about how the data is rendered. It treats the React DOM and Om components as implementation details. This decision often results in code that's verbose and exposes incidental details to the user. These can obviously be abstracted, but Om does not aim to provide such an abstraction and you'd have to write your own helpers as seen with Prismatic and om-tools.

On the other hand, Reagent provides a standard way to define UI components using Hiccup style syntax for DOM representation. Each UI component is a data structure that represents a particular DOM element. By taking a DOM centric view of the UI, Reagent makes writing composable UI components simple and intuitive. The resulting code is extremely succinct and highly readable.

Om.next is an evolution of Om which should fix many issues with Om, however it still feels a bit too intrusive, opinionated and "heavy handed". We much prefer a more light-weight, flexible solution that is easier to customize and extends as needed. We will therefore use Reagent as the base for the remainder of this book.

### A simple Reagent app

Example taken from [Building SPA with Reagent](http://yogthos.net/posts/2014-07-15-Building-Single-Page-Apps-with-Reagent.html)

TODO: Change example!!

First we reference `reagent` in our namespace definition.

```cljs
(ns main.core
 (:require [reagent.core :as reagent :refer [atom]]))
```

Then we create a main container 

```cljs
(defn home []
  [:div
    [:div.page-header [:h1 "My form"]]])
```    

We can now render this component on the page by calling the render-component function.

```cljs
(reagent/render-component [home]
  (.getElementById js/document "app"))
```

The components can be nested inside one another. To add a text field to our form we'll write a function to represent it and add it to our home component.

```cljs
(defn text-input [label]
  [:div.row
    [:div.col-md-2
      [:span label]]
    [:div.col-md-3
      [:input {:type "text" :class "form-control"}]]])

(defn home []
  [:div
    [:div.page-header [:h1 "My Form"]]
    [text-input "First name"]])
```

Notice that even though text-input is a function we're not calling it, but instead we're putting it in a vector. The reason for this is that we're specifying the component hierarchy. The components will be run by Reagent when they need to be rendered.

We can also easily extract the row into a separate component. Once again, we won't need to call the row function directly, but can treat the component as data and leave it up to Reagent when it should be evaluated.

```cljs
(defn row [label & body]
  [:div.row
   [:div.col-md-2 [:span label]]
   [:div.col-md-3 body]])

(defn text-input [label]
  [row label [:input {:type "text" :class "form-control"}]])
```

We now have an input field that we can display. Next, we need to create a model and bind our component to it. Reagent allows us to do this using its atom abstraction over the React state. The Reagent atoms behave just like standard Clojure atoms. The main difference is that a change in the value of the atom causes any components that dereference it to be repainted.

Any time we wish to create a local or global state we create an atom to hold it. This allows for a simple model where we can create variables for the state and observe them as they change over time. Let's add an atom to hold the state for our application and a couple of handler functions for accessing and updating it.

```cljs
(def state (atom {:doc {} :saved? false}))

(defn set-value! [id value]
  (swap! state assoc :saved? false)
  (swap! state assoc-in [:doc id] value))

(defn get-value [id]
  (get-in @state [:doc id]))
```

We can now update our text-input component to set the state when the onChange event is called and display the current state as its value.

```cljs
(defn text-input [id label]
  [row label
   [:input
     {:type "text"
       :class "form-control"
       :value (get-value id)
       :on-change #(set-value! id (-> % .-target .-value))}]])

(defn home []
  [:div
    [:div.page-header [:h1 "My Form"]]
    [text-input :first-name "First name"]])
Let's add a save button to our form so that we can persist the state. For now, we'll simply log the current state to the console.

(defn home []
  [:div
    [:div.page-header [:h1 "My Form"]]
    [text-input :first-name "First name"]    
    [:button {:type "submit"
              :class "btn btn-default"
              :on-click #(.log js/console (clj->js @state))}
     "Submit"]])
```     
     
If we open the console, then we should see the current value of the :first-name key populated in our document whenever we click submit. We can now easily add a second component for the last name and see that it gets bound to our model in exactly the same way.

```cljs
(defn home []
  [:div
    [:div.page-header [:h1 "My Form"]]

    [text-input :first-name "First name"]
    [text-input :last-name "First name"]

    [:button {:type "submit"
              :class "btn btn-default"
              :onClick #(.log js/console (clj->js @state))}
     "Submit"]])
```

So far we've been using a global variable to hold all our state, while it's convenient for small applications this approach doesn't scale well. Fortunately, Reagent allows us to have localized states in our components.   

When the user clicks on an item in the list, we'd like to mark it as selected. Obviously, this is something that's only relevant to the list component and shouldn't be tracked globally. All we have to do to create a local state is to initialize it in a closure.

We'll implement the multi-select by creating a component to represent the list and another to represent each selection item. The list component will accept an id and a label followed by the selection items.

Each item will be represented by a vector containing the id and the value of the item, eg: [:beer "Beer"]. The value of the list will be represented by a collection of the ids of the currently selected items.

We will use a let binding to initialize an atom with a map keyed on the item ids to represent the state of each item.

```cljs
(defn selection-list [id label & items]
  (let [selections (->> items (map (fn [[k]] [k false])) (into {}) atom)]    
    (fn []
      [:div.row
       [:div.col-md-2 [:span label]]
       [:div.col-md-5
        [:div.row
         (for [[k v] items]
          [list-item id k v selections])]]])))
```

The item component will be responsible for updating its state when clicked and persisting the new value of the list in the document.

```cljs
(defn list-item [id k v selections]
  (letfn [(handle-click! []
            (swap! selections update-in [k] not)
            (set-value! id (->> @selections
                                (filter second)
                                (map first))))]
    [:li {:class (str "list-group-item"
                      (if (k @selections) " active"))
          :on-click handle-click!}
      v]))
```

Let's add an instance of the selection-list component to our form and see how it looks.

```cljs
(defn home []
  [:div
    [:div.page-header [:h1 "My Form"]]

    [text-input :first-name "First name"]
    [text-input :last-name "First name"]

    [selection-list :favorite-drinks "Favorite drinks"
     [:coffee "Coffee"]
     [:beer "Beer"]
     [:crab-juice "Crab juice"]]

    [:button {:type "submit"
              :class "btn btn-default"
              :onClick #(.log js/console (clj->js @state))}
     "Submit"]])
```

Finally, let's update our submit button to actually send the data to the server. We'll use the cljs-ajax library to handle our Ajax calls. Let's add the following dependency [cljs-ajax "0.2.6"] to our project.clj and update our namespace to reference it.

```cljs
(ns main.core
 (:require [reagent.core :as reagent :refer [atom]]
           [ajax.core :refer [POST]]))
```

With that in place we can write a save-doc function that will send the current state of the document to the server and set the state to saved on success.

```cljs
(defn save-doc []
  (POST (str js/context "/save")
        {:params (:doc @state)
         :handler (fn [_] (swap! state assoc :saved? true))}))
```

We can now update our form to either display a message indicating that the document has been saved or the submit button based on the value of the :saved? key in our state atom.

```cljs
(defn home []
  [:div
    [:div.page-header [:h1 "My Form"]]

    [text-input :first-name "First name"]
    [text-input :last-name "Last name"]
    [selection-list :favorite-drinks "Favorite drinks"
     [:coffee "Coffee"]
     [:beer "Beer"]
     [:crab-juice "Crab juice"]]

   (if (:saved? @state)
     [:p "Saved"]
     [:button {:type "submit"
              :class "btn btn-default"
              :onClick save-doc}
     "Submit"])])
```

Here are some screenshots of the final app:

INSERT SCREENSHOTS!