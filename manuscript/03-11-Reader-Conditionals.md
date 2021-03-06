### Умови читача

Ця особливість мови дає можливість діалектам Clojure використовувати код на різних платформах та мати частини коду, специфічні до кожної з цих платформ.

Умови читача можна використовувати лише у файлах `.cljc`. Тому усі файли, в яких ви хочете використати умови читача, вам треба перейменувати у `.cljc`.

#### Стандартна умова (`#?`)

Існує два типи умов читача: стандартна та розгортуюча. Стандартна умова читача поводиться приблизно так само, як конструкція `cond`, але синтаксис виглядає по–іншому:

```clojure
(defn parse-int
  [v]
  #?(:clj  (Integer/parseInt s)
     :cljs (js/parseInt s)))
```

Як ви можете бачити макрос `#?` дуже схожий на `cond`, але відрізняється тим, що умови представлені ключовими словами, що ідентифікують платформу. `:cljs` означає, що цей код буде використаний лише для _ClojureScript_, а `:clj` — для _Clojure_. Перевагою такого підходу є те, що умови читача обчислюються на етапі компіляції, тому у скомпільованому коді буде лише код для конкретної платформи.

#### Розгортуюча умова (`#?@`)

Розгортуюча умова читача працює так само, як і стандартна, але також дозволяє розгортати списки у окремі форми. Синтаксис макросу `#?@` виглядає наступним чином:

```clojure
(defn make-list
  []
  (list #?@(:clj  [5 6 7 8]
            :cljs [1 2 3 4])))

;; On ClojureScript
(make-list)
;; => (1 2 3 4)
```

Після прочитання коду компілятор _ClojureScript_ залишить лише код для _ClojureScript_:

```clojure
(defn make-list
  []
  (list 1 2 3 4))
```

Розгортуюча умова читача не може бути використана для розгорнення декількох форм на вищому рівні простору імен:

```clojure
#?@(:cljs [(defn func-a [] :a)
           (defn func-b [] :b)])
;; => #error "Reader conditional splicing not allowed at the top level."
```

Для цього можна використати стандартну умову для кожної форми окремо, або загорнути їх у блок `do`:

```clojure
#?(:cljs (defn func-a [] :a))
#?(:cljs (defn func-b [] :b))

;; Or

#?(:cljs
   (do
     (defn func-a [] :a)
     (defn func-b [] :b)))
```

#### Додаткові матеріали

* http://clojure.org/guides/reader_conditionals
* https://danielcompton.net/2015/06/10/clojure-reader-conditionals-by-example
* https://github.com/funcool/cuerdas (приклад невеликого проекту, який використовує умови читача)
