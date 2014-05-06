Title: Frequency counting in Haskell  
Date: Tue May  6 17:32:25 2014  
Tags: haskell

While working on an exercise (to build a histogram from a list of
numbers) in
[Brent Yorgy's Haskell class](http://www.seas.upenn.edu/~cis194/lectures.html),
I needed to make frequency table.

I would reach for hashes, in general, if it were Perl. In the present
case, since I was dealing with only digits from 0 to 9, a 10-element
array indexed by the digit would be sufficient. So, I wanted to use a
list of numbers for frequency counts in Haskell as well.

So, I started with a list of ten zeroes:

    replicate 10 0

The problem, of course, is updating the counts as the input list of
digits is processed.

In Perl:

    my @digits = (...);
    my @frequency = (0) x 10;
    for my $digits (@digits) {
        $frequency[$digit]++;
    }

Haskell, being a pure language, doesn't allow for mutable arrays. So, here's one approach:

    -- increment nth element of a list of Ints
    incr :: Int -> [Int] -> [Int]
    incr n xs = take n xs ++ [(xs !! n) + 1] ++ drop (n+1) xs

    -- frequency of counts from a list of digits (0 .. 9 only)
    frequency :: [Int] -> [Int]
    frequency xs = foldl (\acc n -> incr n acc) (replicate 10 0) xs

Pretty awkward! There must be a better way.

Searching turned up this article:
[Frequency counting in Haskell](http://blog.malde.org/posts/frequency-counting.html). Added
to the reading list.
