# xTidalx
Archive of amazing Tidal things

// how do you randomly play a sample?;
d1 $ sometimesBy 0.2 (const silence) $ s "bd";
d1 $ degradeBy 0.2 $ s "bd";
d1 $ s "bd" # gain (choose [0,0,0,0,1]);
