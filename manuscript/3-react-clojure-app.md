# React Native apps with Clojure

Now we are ready to re-configure our project to use Clojure instead of the default Javascript.

## Clojure build and package managers

[Boot](http://boot-clj.com/)

[Leinigen]() 

[Leinigen against the ants]()

## Figwheel

[Figwheel](https://github.com/bhauman/lein-figwheel) is a lein extension for hot reloading code changes to the browser while maintaining state. We will use Figwheel in order to get a smoother development experience. To get a sense of the power of fighweel, check out this [video](https://www.youtube.com/watch?v=j-kj2qwJa_E). 

The traditional way

- Write code
- Reload browser
- Lose application state
- Manipulate app back into state needed
- Test/verify behaviour
- Rinse and repeat

The figwheel way

- Make code changes
- Watch browser adapt to new code
- Test/verify behaviour
- Rinse and repeat

The trade off: get instantaneous feedback for writing reloadable code
Behavior of application has changed. State of application is unchanged.

Fighwheel was originally designed for leinigen, but there is a [figwheel-boot](https://github.com/aJchemist/boot-figwheel)

Load `boot-figwheel` in your boot script:

```cljs
(require 'boot-figwheel)
(refer 'boot-figwheel :rename '{cljs-repl fw-cljs-repl}) ; avoid some symbols
```

Create a figwheel boot task:

```cljs
(task-options!
     figwheel {:build-ids  ["dev"]
           :all-builds [{:id "dev"
                         :compiler {:main 'app.core
                                    :output-to "app.js"}
                         :figwheel {:build-id  "dev"
                                    :on-jsload 'app.core/main
                                    :heads-up-display true
                                    :autoload true
                                    :debug false}}]
           :figwheel-options {:repl true
                              :http-server-root "target"
                              :css-dirs ["target"]
                              :open-file-command "emacsclient"}})
```                              

Then use the figwheel task in your `dev` task:

```cljs
(deftask dev []
  (set-env! :source-paths #(into % ["src"]))
  (comp (repl) (figwheel)))
```


## Devcards

[Devcards](https://github.com/bhauman/devcards) are a set of macros which leverage React and Fighweel to hotload our code in the browser and display our functions, documentation, tests and function calls as nicely formatted cards so as to document our progress as we go along. Devcards makes ur get into the habbit of developing small composable functions that we can test independently and use to build up our application like a set of lego blocks.

## Testing

In Clojure we have several popular testing options. 

We will use property testing to let the data drive our tests and check if our app is valid given many variations and combinations of state. Traditional testing methods only test corner cases that the developer/tester thinks of and anticipates. Property based testing tries to automate testing a wide variety of data scenarios and thus makes me guarantee that we get our corner cases covered which we would otherwise not foresee.

## Docker file

A Docker file for the complete environment setup can be found here:
