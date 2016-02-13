## Posh and Polish

Now that we have the basic infrastructure in place for the client, we can add some extra *posh and polish.
In this chapter we will integrate the libary [Posh](https://github.com/mpdairy/posh), written by Matt Parker (aka. @mpdairy).

### Adding Posh

Posh is a small focused library, which lets us:

- listen to the transaction log of DataScript
- detect when certain data patterns occur 
- trigger a reaction

The reaction will usually be to update the global state atom but it could also involve requesting additional data from the server or any number of other reactions.

### Setup

Add dependency to posh in `project.clj`

```cljs
[posh "0.3.4"]
```

Require Posh in app file:

```cljs
(:require 
        [reagent.core :as r]
        [posh.core :refer [pull q db-tx pull-tx q-tx after-tx! transact! posh!]]
        [datascript.core :as d]))
```cljs

Create a posh enables connection

```cljs
(defonce conn (d/create-conn))
(posh! conn)
```

### Brief API overview

`(pull [conn] [pull pattern] [entity id])`

`pull` retrieves the data specified in pull-pattern for the entity with `entity-id` and triggers a reaction. 

It is similar to Datascript's `pull` except it:
- takes a `conn` instead of a `db`.
- pulls new data only if data matching its pattern has changed 

Example: `(pull conn '[:person/name :person/age] 1234)`

Notice that we escape the 2nd argument to ensure it is not evaluated.

You can fine-tune when pull attempts to pull new data via `pull-tx`

`(pull-tx [conn] [tx pattern] [pull pattern] [entity id])`

Here `tx-pattern` is a datom matching pattern for the transaction log. 

`(q [conn] [query] & args)`

`q` queries for data from the database according to the rules specified in the query.
Similar to `pull`, on any DB state change, `q` will pattern match on transacted datoms.
On a match `q` runs a Datascript query and compares the new result to the old. 
If it is different a `reaction` is triggered.

`q` looks at the query pattern and tries to make a pattern to identify relevant tx datoms. If there is anything complex in the query, such as function calls or `get-else`, it will have a non-restrictive pattern and will run the query whenever there has been a database change. To specify the matching pattern by hand, you can use `q-tx`:

`(q-tx [conn] [tx pattern] [query] & args)`

Example query:

```cljs
(q conn '[:find [?name ...]
          :in $ ?old
          :where
          [?p :person/age ?age]
          [(< ?age ?old)]
          [?p :person/name ?name]]
   old-age)
```

In order to execute the query, you must dereference it using `@`.

## Action/reaction flow

Posh is data reactive and triggers named reactions like `:new-todo` in this example:

`(p/reaction :new-todo, todo-item)`

The named reaction is registered with a reaction handler. 
If using Reagent, we could simply forward each reaction to a similar named reaction.

```cljs
(p/register-reaction :new-todo [item]
    (r/reaction :new-todo [item]))
```

Advice: If we repeat this pattern for many reactions, we could create a macro to create this proxying for us.


