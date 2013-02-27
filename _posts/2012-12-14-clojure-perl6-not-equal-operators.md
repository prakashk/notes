Title: Comparing Clojure not= and Perl 6 != operators  
Date: Fri Dec 14 12:40:00 EST 2012  
Tags: Perl, Perl6, Clojure

In clojure, operators are functions and consequently expressions use prefix notation.

    ;; Clojure
    user> (= 5 5 6)
    false
    
In Perl 5, it is:

    # Perl5
    $ (5 == 5 && 5 == 6) ? "true" : "false"
    false
    
Perl6 is better, as it allows operators to be
[chained](http://feather.perl6.nl/syn/S03.html#Chained_comparisons):

    # Perl6 (Rakudo)
    > 5 == 5 == 6
    False
    
The appearance of the expressions is quite different. In Clojure, the
operator is specified once, followed by the list of operands. In
Perl6, the infix operator is used between every pair of operands.

Perl6 also offers
[reduction operators](http://feather.perl6.nl/syn/S03.html#Reduction_operators),
which make the expressions somewhat similar to Clojure
expressions. The operator being used is wrapped in square brackets,
and operates on a list of operands.

    # Perl6 (Rakudo)
    > [==] 5, 5, 6
    False
    
No surprises, so far.

Clojure's [`not=`](http://clojuredocs.org/clojure_core/clojure.core/not=) operator:

    Same as (not (= obj1 obj2))

Examples:

    ;; Clojure
    user> (not (= 5 5 6))
    true
    user> (not= 5 5 6)
    true
    
In Perl6, using the chained `!=` operator:

    # Perl6 (Rakudo)
    > not 5 == 5 == 6
    True
    > !(5 == 5 == 6)                # parenthesis needed, as == binds more tightly than !
    True
    > 5 != 5 != 6
    False                           # != is NOT same as not(==) !!

Using reduction operators:

    # Perl6 (Rakudo)
    > not[==] 5, 5, 6
    True
    > ![==] 5, 5, 6
    True
    > [!=] 5, 5, 6
    False

So, in Perl6, the behavior of the reduction operator `[!=]` is
consistent with that of the chained operator `!=`, which is a good
thing. However, it is not really same as `not[==]` or `![==]`.

In Clojure, `(not= a b c)` is exactly same as `(not (= a b
c))`. Something to remember.
