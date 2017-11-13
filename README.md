# xTidalx
Archive of amazing Tidal things

// how do you play a sample randomly? 
```
d1 $ sometimesBy 0.2 (const silence) $ s "bd"
d1 $ degradeBy 0.2 $ s "bd"
d1 $ s "bd" # gain (choose [0,0,0,0,1])
```

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
whats your version doing there @yaxu? isnt that kind of like 
`ur'`
? bi was thinking about how to pattern my toggles and ending up thinking its basically `ur'` my problem with `ur'` is that it’s kind of hard to use a lot of functions

function junky that i am


yaxu
yes maybe similar to `ur'`. I'm too tired to think tbh but seem to be able to write code. Dangerous
