Title: Clojure: Search and Replace in multidimensional map  
Date: Thu Jan  3 16:22:17 2013  
Tags: Clojure

Came across an interesting little problem on [r/clojure](http://www.reddit.com/r/Clojure/comments/15jvcx/how_to_search_and_replace_in_multidimensional_map/):

> I have a map like this;
>  
> {:a [[1 2] [3 4] [5 6]] :b [[7 8] [9 10]] :c [[11 12]]}
>  
> Each value is a vector containing any number of subvectors, each of which contains exactly two ints.
>  
> I can't figure out how to find and replace one of the vector pairs. For example, if I wanted to find [7 8] and replace it with [17 18], the resulting map would be;
>  
> {:a [[1 2] [3 4] [5 6]] :b [[17 18] [9 10]] :c [[11 12]]}
>  
> Preserving the order isn't important.

Here's what I got:

    (def my-map {:a [[1 2] [3 4] [5 6]] :b [[7 8] [9 10]] :c [[11 12]]})
     
    (letfn [(my-replace [v a b]
              (vec (map #(if (= a %) b %) v)))]
      (into {}
            (for [[k v] my-map]
              {k (my-replace v [7 8] [17 18])})))

Of course, clojure already has [`replace`](http://clojuredocs.org/clojure_core/clojure.core/replace) function, directly applicable in this situation ([this solution used it](http://www.reddit.com/r/Clojure/comments/15jvcx/how_to_search_and_replace_in_multidimensional_map/c7nrzij)):

> clojure.core/replace  
> ([smap coll])  
>   Given a map of replacement pairs and a vector/collection, returns a
>   vector/seq with any elements = a key in smap replaced with the
>   corresponding val in smap

Using it, the `my-replace` helper function definition is no longer needed.

    (into {}
          (for [[k v] m]
               {k (replace {[7 8] [17 18]} v)}))

The main difference is the way the search and replace arguments are passed: `replace` expects them to be passed as a map, which allows one to pass multiple search/replace arguments together.

[Another interesting solution](http://www.reddit.com/r/Clojure/comments/15jvcx/how_to_search_and_replace_in_multidimensional_map/c7n9636) used the `reduce-kv` function, available in clojure 1.4:

> clojure.core/reduce-kv  
> ([f init coll])  
>   Reduces an associative collection. f should be a function of 3
>   arguments. Returns the result of applying f to init, the first key
>   and the first value in coll, then applying f to that result and the
>   2nd key and value, etc. If coll contains no entries, returns init
>   and f is not called. Note that reduce-kv is supported on vectors,
>   where the keys will be the ordinals.

    (defn replace
      [data replace with]
      (reduce-kv (fn [acc k v]
                        (let [new-v (map #(if (= replace %) with %) v)]
                          (assoc acc k new-v))) data data))
                      
I still need to be more comfortable with `reduce` and friends. Also, for some reason, `assoc` never comes to mind when thinking about possible approaches.
