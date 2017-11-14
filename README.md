# xTidalx
Archive of amazing Tidal things

// how do you play a sample randomly? 
```
d1 $ sometimesBy 0.2 (const silence) $ s "bd"
d1 $ degradeBy 0.2 $ s "bd"
d1 $ s "bd" # gain (choose [0,0,0,0,1])
```

// Toggling functions
Credit to Jarm:
Experimenting with toggles for live performance (aka cheating)
```let toggle t f p = if (1 == t) then f $ p else id $ p

do
  let f1 = 0
      f2 = 0
      p1 = 0
  d1 $ slow 16
     $ toggle f1 (
         stut 4 0.75 (0.2) .
         striate 4
       )
     $ toggle f2 (
         every 5 (# del "0.4 0.2:0.02 0.2:0.6") .
         foldEvery [3,7] (# rvb "0.2 0.4:0.2 0.1")
       )
     $ toggle p1 (
         (# speed "[1.5 2 3]*4") .
         (# pan (foldEvery [2,3] (slow 1.5) sin)) .
         (# hpf' "[0.5e3 1.0e3]/2" 0.1)
       )
     $ sound (samples "fosc(9,16) fosc(3,8)" (irand 30))
     # gco "0.8:1:0"
```
solving the problem of trying to ‘toggle’ groups of functions and parameters

```do
  let a = patToList "0 0 0 0"
  d5 $ fast 1
     $ toggle (a!!0) (every 4 ((# speed "3") . (jux (rev))))
     $ toggle (a!!1) (every 8 (striate 4 . (# speed "-2")))
     $ toggle (a!!2) (# speed "2")
     $ toggle (a!!3) (
         (# hpf' "[2e3 1e3 0.5e3]/4" "0.1") .
         (# rvb' "[0.8 0.6 0.2]/3" "[0.2 0.4 0.8]/4")
       )
     $ sound "[fosc:37 fosc:38?](11,16) [[fosc:36]*6 fosc:35*4]/2"
     # gco' (ssinf 0.6 0.8 (1/10)) 5 8```
     
yaxu
here's an alternative that is more tidalish i think
```let toggles :: Pattern Int -> [Pattern a] -> Pattern a
    toggles p xs = unwrap $ (xs !!!) <$> p
    (!!!) xs n = xs !! (n `mod` length xs)
```
ah no that's a bit different
 ```d1 $ toggles "0 <1 2>"
  [sound "bd",
   sound "sn:2*4",
   sound "arpy(5,8)"
  ]
```
also useful though


jarm
what's your version doing there @yaxu? isnt that kind of like `ur'`? i was thinking about how to pattern my toggles and ending up thinking its basically `ur'` my problem with `ur'` is that it’s kind of hard to use a lot of functions

function junky that i am

yaxu
yes maybe similar to `ur'`. I'm too tired to think tbh but seem to be able to write code. Dangerous

// are if-then-else statements possible in tidal? 
```let toggle t f p = if (1 == t) then f $ p else id $ p```

```d1 $ do x <- scale 0.1 0.9 sine1
        if x > 0.7 then s "808bd*16" # room (pure x) else silence
```
(edited)


[2:30] 
jarm's 'toggle' example operates in the haskell domain rather than inside a tidal pattern


[2:31] 
the above attempt looks fairly straightforward but there's some fairly deep magic behind it


[2:32] 
in general structuring a tidal pattern with 'if-then-else' won't work, because tidal isn't at all based on that kind of step-by-step procedural logic


[2:35] 
that innocuous `do` creates this weird situation where you can operate on all the possible outcomes of a pattern that has potentially infinite detail.. that `x` doesn't represent one value in the pattern, but any one value that the pattern might return


oldmaninamerc [2:37 PM] 
yaxu, when you talk about the 'x' being different you mean because haskell is a functional language or specifically because of 'do' ?


[2:37] 
also thanks to you and jarm for that, it's really helpful.


yaxu [2:39 PM] 
yes it's to do with the functional nature of haskell


oldmaninamerc [2:40 PM] 
cool, yeah I thought so. Okay :grinning:


yaxu [2:40 PM] 
the `do` creates something called a monadic context which is a bit difficult to explain, partly because the terminology doesn't make much sense


[2:41] 
I'm unlikely to make much sense either because I haven't slept very much for a few days


oldmaninamerc [2:42 PM] 
appreciate your explanation above anyway, thanks so much. :+1:


yaxu [2:43 PM] 
basically your question is probably a *lot* more interesting than you might think. The example I give above is something I've not thought of doing before (edited)


[2:45] 
there's much unexplored territory in these monads


[2:47] 
 ```d1 $ do x <- slow 8 $ scale 0.1 0.9 sine1
        y <- rand
        (if y > 0.5 then ((# speed 2) . (chop 8)) else id) $ if x > 0.125 then s "808bd(3,8) 808mt*4" # room (pure x) else silence
```


oldmaninamerc [2:49 PM] 
well, not sure what you're thinking, but I was thinking how a parampattern could 'modulate' others


[2:49] 
yeah - just tried that code above - very cool


yaxu [2:50 PM] 
```d1 $ do x <- slow 8 $ scale 0.1 0.9 sine1
        y <- saw
        (if y > 0.5 then ((# speed 2) . (chop 8)) else id) $ if x > 0.125 then s "808bd(3,8) 808mt*4" # room (pure x) else silence
  # crush 5
```


[2:51] 
a parampattern can't really modulate another thing unless you first deconstruct it into a pattern of numbers again.. so you may as well start with a pattern of numbers


new messages
bgold [3:39 PM] 
Isn't conditioning a function off of `y <- rand` equivalent to `sometimes`?  Though the saw usage is interesting, and there are probably neat binary functions that could apply to patterns that aren't implemented yet.

yaxu [6:28 PM] 
Here's a thing to magically turn one connection into multiple connections
wondering whether this would help with the midi time drift issues
```let multiplex n f = do mv <- newMVar $ replicate n silence
                       return $ map (swap mv) [0 .. n-1]
                         where swap mv n v = do xs <- takeMVar mv
                                                let (h,t) = splitAt n xs
                                                    xs' = h ++ (v:t)
                                                putMVar mv xs'
                                                f $ stack xs'

(x1:x2:x3:x4:x5:x6:[]) <- multiplex 6 d1
```
