# Adding DataScript

Databases have a long history dating back to the 1975 with the first commercial commercially available RDBMS was Oracle, released in 1979.
Since then the relations database rules the world of IT untill the late 2000s when a new breed of DBs (known collectivly as NoSQL DBs) entered the scene and became increasingly popular. The NoSQL DBs consisted mainly of:

- Key value stores such as Redis
- Graph DBs such as Neo4J
- Document stores such as MongoDB

These Databases were specifically suited to non-relational data of various forms. Large apps often have complex domain models that mix elements of several of these types. Which DB to choose? Some apps would be polyglot, dividing the domain model across multiple different Databases. Clearly not the optimal solution.

With Clojure, [Datomic](http://www.datomic.com/) came onto the scene as an entirely new breed to better fit modern application requirements. Datomic is based around the concept of facts, which are triples consisting of Entity, Attribute and Value, similar to the [RDF](https://en.wikipedia.org/wiki/Resource_Description_Framework) model used for the [semantic web](https://en.wikipedia.org/wiki/Semantic_Web). 

...
This fact model also enables:
- multi-directional connections and traversal.
..

## Client Database

Why only have a Database on the server?

As most of the application logic in an SPA is now on the client, it makes sense to have an in-memory DB which acts as a cache
to the server DB. This has a number of benefits:

- Minimize request/response data roundtrips to the server
- Offline first
- ...

DataScript as a logical response...

[DataScript](https://github.com/tonsky/datascript) is an in-memory client database designed by [Nikita Prokopov](http://tonsky.me/).
It includes an API and feature set similar to Datomic, but using a simpler design and less feature complete. DataScript is however way more powerful than traditional DBs on the client, which are usually only simple Key/Value stores, such as [WebStorage](https://en.wikipedia.org/wiki/Web_storage) including `sessionStorage` and `localStorage`.

An immutable in-memory database and Datalog query engine in Clojure and ClojureScript.

DataScript databases are immutable and based on persistent data structures. In fact, they’re more like data structures than databases (think Hashmap). Unlike querying a real SQL DB, when you query DataScript, it all comes down to a Hashmap lookup. Or series of lookups. Or array iteration. There’s no particular overhead to it. You put a little data in it, it’s fast. You put in a lot of data, well, at least it has indexes. That should do better than you filtering an array by hand anyway. The thing is really lightweight.

The intention with DataScript is to be a basic building block in client-side applications that needs to track a lot of state during their lifetime. There’s a lot of benefits:

Central, uniform approach to manage all application state. Clients working with state become decoupled and independent: rendering, server sync, undo/redo do not interfere with each other.
Immutability simplifies things even in a single-threaded browser environment. Keep track of app state evolution, rewind to any point in time, always render consistent state, sync in background without locking anybody.
Datalog query engine to answer non-trivial questions about current app state.
Structured format to track data coming in and out of DB. Datalog queries can be run against it too.

### Usage

For lein project:

```cljs
:dependencies [
  [org.clojure/clojure "1.7.0"]
  [org.clojure/clojurescript "1.7.228"]
  [datascript "0.15.0"]
]
```

```cljs
(require '[datascript.core :as d])
```

### Database Schema

```cljs
;; Implicit join, multi-valued attribute
(let [schema {:aka {:db/cardinality :db.cardinality/many}}
      conn   (d/create-conn schema)]
```

### Adding data 

The `:db/id -1` is a temporary (virtual) ID that you can reference from other entitities to ensure they are linked in the DB.
Also notice that the attribute `:aka` has a value that is an Array, ie. `:db.cardinality/many`

```
    (d/transact! conn [ { :db/id -1
                        :name  "Maksim"
                        :age   45
                        :aka   ["Maks Otto von Stirlitz", "Jack Ryan"] } ])
```

### Datalog queries

```cljs
  (d/q '[ :find  ?n ?a
          :where [?e :aka "Maks Otto von Stirlitz"]
                 [?e :name ?n]
                 [?e :age  ?a] ]
       @conn))

;; => #{ ["Maksim" 45] }
```

### Feature set overview 

**The following features are supported**

- Database as a value: each DB is an immutable value. New DBs are created on top of old ones, but old ones stay perfectly valid too
- Triple store model
- EAVT, AEVT and AVET indexes
- Multi-valued attributes via :db/cardinality :db.cardinality/many
- Lazy entities and :db/valueType :db.type/ref auto-expansion
- Database “mutations” via transact!
- Callback-based analogue to txReportQueue via listen!
- Direct index lookup and iteration via datoms and seek-datoms
- Filtered databases via filter
- Lookup refs
- Unique constraints, upsert
- Pull API

**Query engine features**

- Implicit joins
- Query over DB or regular collections
- Parameterized queries via :in clause
- Tuple, collection, relation binding forms in :in clause
- Query over multiple DB/collections
- Predicates and user functions in query
- Rules, recursive rules
- Aggregates
- Find specifications

**Interface differences**

- Conn is just an atom storing last DB value, use @conn instead of (d/db conn)
- Instead of #db/id[:db.part/user -100] just use -100 in place of :db/id or entity id
- Transactor functions can be called as [:db.fn/call f args] where f is a function reference and will take db as first argument
- Custom query functions and aggregates should be passed as source instead of being referenced by symbol (due to lack of resolve in CLJS)
- Custom aggregate functions are called via aggregate keyword: :find (aggregate ?myfn ?e) :in $ ?myfn
- Additional :db.fn/retractAttribute shortcut
- Transactions are not annotated by default with :db/txInstant

### Adding Posh

We should avoid having the Queries inlined in the components. Ideally components should be declarative and be mostly concerned with rendering their structure, not logic. Separation of concerns. Re-frame to the rescue!

### Adding Re-frame

### Adding Rum to the cocktail

With Posh and Reframe we have a very nice base architecture...

### Layout the Garden

Traditionally layout has been written in a peculiar "language" for designers called CSS. However... 
why not keep the layout in Clojure as well, just like the HTML?

Note: Make sure we have mentioned how Clojure is written in a data syntax, EDN. We want to use EDN in our entire architecture!

[Do we even need CSS anymore?](https://css-tricks.com/the-debate-around-do-we-even-need-css-anymore/)

[Radium](http://stack.formidable.com/radium/) is a set of tools to manage inline styles on React elements. It gives you powerful styling capabilities without CSS.

Eliminating CSS in favor of inline styles that are computed on the fly is a powerful approach, providing a number of benefits over traditional CSS:

- Scoped styles without selectors
- Avoids specificity conflicts
- Source order independence
- Dead code elimination
- Highly expressive

Say hi to [Garden](https://github.com/noprompt/garden), a Clojure CSS library. 

Writing CSS in EDN syntax almost looks the same. However we have the full power of Clojure to leverage in order to generate our CSS dynamically.

```cljs
(css [:a
 {:font-weight 'normal
  :text-decoration 'none}
 [:&:hover
  {:font-weight 'bold
   :text-decoration 'underline}]])
```

## Todo app

We will first build a simple Todo app to leverage the stack we have assembled.

[datascript-todo](https://github.com/tonsky/datascript-todo)


## Creating a chat application

Most modern apps are data driven and real time, with many users concurrently communicating and updating shared state.
We will build a chat app to show how we can achieve add real time communication with our stack.

[datascript-chat](http://tonsky.me/blog/datascript-chat/)



