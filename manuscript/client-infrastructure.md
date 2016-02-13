# Client app infrastructure

In this chapter we start building on the basic client app architecture while keeping our client logic to the minimum 
to reduce complexity and workload.

### Reagent

[Reagent](http://reagent-project.github.io/) is a React wrapper which auto-triggers React rendering whenever its global state (`r/atom`) is dereferenced.

### Adding Re-frame

[re-frame](https://github.com/Day8/re-frame) is a powerful infrastructure library which hooks into Reagent.

_"For all its considerable brilliance, Reagent (+ ReactJS) delivers only the 'V' part of a traditional MVC framework.
But apps involve much more than V. Where does the control logic go? How is state stored & manipulated? etc.
We read up on Flux, Pedestal App, Hoplon, OM, Elm, etc and re-frame is the architecture that emerged.
re-frame does have M, V, and C parts but they aren't objects, they are pure functions (or pure data), and they are wired together via reactive data flows."_

We can add re-frame using a [leinigen template](https://github.com/Day8/re-frame-template)

### Adding Rum to the cocktail

With Posh and Re-frame we have a very nice base architecture...

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
