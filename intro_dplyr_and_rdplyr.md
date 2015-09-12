

Welcome to dplyr
========================================================
author: Tommy M O'Dell (tommy.odell@gmail.com)
date: September 10th, 2015
transition: rotate
transition-speed: fast
width: 1440
height: 900
 
dplyr?
========================================================
Lead Author: Hadley Wickham 
![alt text](hadley.jpg)
***
Friction-less manipulation of data frames in R

Package goals:
 
1. simple interface
2. good performance
3. same interface for many 'backends'

<!-- Notes:
- the ordering of the points is relevant (hadley will trade off performance for a clean interface)
- familiar with plyr? Think of dplyr as plyr specialised for data frames
-->
 
Data manipulation in base R
========================================================
type: section
 
Filter the rows of a data frame
========================================================
 
Keep rows where mpg > 30 and cyl >= 4

```r
mtcars[mtcars$mpg > 30 & mtcars$cyl >= 4,]
```

```
                mpg cyl disp  hp drat    wt gear
Fiat 128       32.4   4 78.7  66 4.08 2.200    4
Honda Civic    30.4   4 75.7  52 4.93 1.615    4
Toyota Corolla 33.9   4 71.1  65 4.22 1.835    4
Lotus Europa   30.4   4 95.1 113 3.77 1.513    5
```
 
Add or modify columns
========================================================
 
Create a new column for displacement per cylinder

```r
mtcars$disp_cyl <- mtcars$disp / mtcars$cyl
head(mtcars)
```

```
                   mpg cyl disp  hp drat    wt gear disp_cyl
Mazda RX4         21.0   6  160 110 3.90 2.620    4 26.66667
Mazda RX4 Wag     21.0   6  160 110 3.90 2.875    4 26.66667
Datsun 710        22.8   4  108  93 3.85 2.320    4 27.00000
Hornet 4 Drive    21.4   6  258 110 3.08 3.215    3 43.00000
Hornet Sportabout 18.7   8  360 175 3.15 3.440    3 45.00000
Valiant           18.1   6  225 105 2.76 3.460    3 37.50000
```
 
Sort a data frame by its values
========================================================
 

```r
mt <- mtcars[order(mtcars$mpg, mtcars$cyl),]
head(mt)
```

```
                     mpg cyl disp  hp drat    wt gear disp_cyl
Cadillac Fleetwood  10.4   8  472 205 2.93 5.250    3   59.000
Lincoln Continental 10.4   8  460 215 3.00 5.424    3   57.500
Camaro Z28          13.3   8  350 245 3.73 3.840    3   43.750
Duster 360          14.3   8  360 245 3.21 3.570    3   45.000
Chrysler Imperial   14.7   8  440 230 3.23 5.345    3   55.000
Maserati Bora       15.0   8  301 335 3.54 3.570    5   37.625
```
 
Select columns from a data frame
========================================================
 
Method 1 - Numerical indices

```r
head(mtcars[,1:4])
```

```
                   mpg cyl disp  hp
Mazda RX4         21.0   6  160 110
Mazda RX4 Wag     21.0   6  160 110
Datsun 710        22.8   4  108  93
Hornet 4 Drive    21.4   6  258 110
Hornet Sportabout 18.7   8  360 175
Valiant           18.1   6  225 105
```
Select columns from a data frame
========================================================
title:false
 
Method 2 - Named indices

```r
head(mtcars[,c('mpg','cyl','disp','gear')])
```

```
                   mpg cyl disp gear
Mazda RX4         21.0   6  160    4
Mazda RX4 Wag     21.0   6  160    4
Datsun 710        22.8   4  108    4
Hornet 4 Drive    21.4   6  258    3
Hornet Sportabout 18.7   8  360    3
Valiant           18.1   6  225    3
```
Summarise (aggregate) a data frame
========================================================

```r
aggregate(mpg ~ cyl + gear, data = mtcars, mean)
```

```
  cyl gear    mpg
1   4    3 21.500
2   6    3 19.750
3   8    3 15.050
4   4    4 26.925
5   6    4 19.750
6   4    5 28.200
7   6    5 19.700
8   8    5 15.400
```
What about CRAN?
========================================================
type: section

========================================================
Using `ddply` from **plyr** to get the mean mpg per cylinder and gear

```r
ddply(
  mtcars,         # data frame
  .(cyl, gear),   # grouping columns 
  summarise,      # type of
  mpg = mean(mpg) # aggregations
)
```

```
data frame with 0 columns and 0 rows
```

========================================================
Using `data.table` to get the mean mpg per cylinder and gear

```r
dtcars <- as.data.table(mtcars)
dtcars[, mean(mpg), by = list(cyl,gear)]
```

```
   cyl gear     V1
1:   6    4 19.750
2:   4    4 26.925
3:   6    3 19.750
4:   8    3 15.050
5:   4    3 21.500
6:   4    5 28.200
7:   8    5 15.400
8:   6    5 19.700
```

If it aint broke?
========================================================
type: section
Are any of these both **readable** and **fast**?
 
The dplyr promise
========================================================

99% of data manipulation can be described 6 key operations ('verbs') 

1. **filter**: filter the rows of a data frame
2. **mutate**: modify or create new columns
3. **group by**: set grouping variables
4. **summarise**: aggregate a data frame
5. **arrange**: sort columns of a data frame
6. **select**: select a set of columns



filter
========================================================

```r
filter(mtcars, mpg > 30, cyl >= 4)
```

```
Source: local data frame [4 x 8]

    mpg   cyl  disp    hp  drat    wt  gear disp_cyl
  (dbl) (dbl) (dbl) (dbl) (dbl) (dbl) (dbl)    (dbl)
1  32.4     4  78.7    66  4.08 2.200     4   19.675
2  30.4     4  75.7    52  4.93 1.615     4   18.925
3  33.9     4  71.1    65  4.22 1.835     4   17.775
4  30.4     4  95.1   113  3.77 1.513     5   23.775
```

mutate 
========================================================
(modify or create columns)
Same as previous

```r
mutate(mtcars, disp_cyl = disp/cyl)
```

```
Source: local data frame [32 x 8]

     mpg   cyl  disp    hp  drat    wt  gear disp_cyl
   (dbl) (dbl) (dbl) (dbl) (dbl) (dbl) (dbl)    (dbl)
1   21.0     6 160.0   110  3.90 2.620     4 26.66667
2   21.0     6 160.0   110  3.90 2.875     4 26.66667
3   22.8     4 108.0    93  3.85 2.320     4 27.00000
4   21.4     6 258.0   110  3.08 3.215     3 43.00000
5   18.7     8 360.0   175  3.15 3.440     3 45.00000
6   18.1     6 225.0   105  2.76 3.460     3 37.50000
7   14.3     8 360.0   245  3.21 3.570     3 45.00000
8   24.4     4 146.7    62  3.69 3.190     4 36.67500
9   22.8     4 140.8    95  3.92 3.150     4 35.20000
10  19.2     6 167.6   123  3.92 3.440     4 27.93333
..   ...   ...   ...   ...   ...   ...   ...      ...
```

========================================================
Multiple columns in one

```r
mutate(
  mtcars, 
  disp_cyl = disp/cyl,
  kw = hp/0.746
)
```

```
Source: local data frame [32 x 9]

     mpg   cyl  disp    hp  drat    wt  gear disp_cyl        kw
   (dbl) (dbl) (dbl) (dbl) (dbl) (dbl) (dbl)    (dbl)     (dbl)
1   21.0     6 160.0   110  3.90 2.620     4 26.66667 147.45308
2   21.0     6 160.0   110  3.90 2.875     4 26.66667 147.45308
3   22.8     4 108.0    93  3.85 2.320     4 27.00000 124.66488
4   21.4     6 258.0   110  3.08 3.215     3 43.00000 147.45308
5   18.7     8 360.0   175  3.15 3.440     3 45.00000 234.58445
6   18.1     6 225.0   105  2.76 3.460     3 37.50000 140.75067
7   14.3     8 360.0   245  3.21 3.570     3 45.00000 328.41823
8   24.4     4 146.7    62  3.69 3.190     4 36.67500  83.10992
9   22.8     4 140.8    95  3.92 3.150     4 35.20000 127.34584
10  19.2     6 167.6   123  3.92 3.440     4 27.93333 164.87936
..   ...   ...   ...   ...   ...   ...   ...      ...       ...
```

========================================================
Can even refer to newly created columns immediately...

```r
mutate(
  mtcars, 
  disp_cyl = disp/cyl,
  k_watt = hp/0.746,
  watts  = k_watt*1000
)
```

```
Source: local data frame [32 x 10]

     mpg   cyl  disp    hp  drat    wt  gear disp_cyl    k_watt     watts
   (dbl) (dbl) (dbl) (dbl) (dbl) (dbl) (dbl)    (dbl)     (dbl)     (dbl)
1   21.0     6 160.0   110  3.90 2.620     4 26.66667 147.45308 147453.08
2   21.0     6 160.0   110  3.90 2.875     4 26.66667 147.45308 147453.08
3   22.8     4 108.0    93  3.85 2.320     4 27.00000 124.66488 124664.88
4   21.4     6 258.0   110  3.08 3.215     3 43.00000 147.45308 147453.08
5   18.7     8 360.0   175  3.15 3.440     3 45.00000 234.58445 234584.45
6   18.1     6 225.0   105  2.76 3.460     3 37.50000 140.75067 140750.67
7   14.3     8 360.0   245  3.21 3.570     3 45.00000 328.41823 328418.23
8   24.4     4 146.7    62  3.69 3.190     4 36.67500  83.10992  83109.92
9   22.8     4 140.8    95  3.92 3.150     4 35.20000 127.34584 127345.84
10  19.2     6 167.6   123  3.92 3.440     4 27.93333 164.87936 164879.36
..   ...   ...   ...   ...   ...   ...   ...      ...       ...       ...
```

Group by and Summarise
========================================================

```r
mtcars <- group_by(mtcars, cyl, gear)
mtcars
```

```
Source: local data frame [32 x 8]
Groups:  [?]

     mpg   cyl  disp    hp  drat    wt  gear disp_cyl
   (dbl) (dbl) (dbl) (dbl) (dbl) (dbl) (dbl)    (dbl)
1   21.0     6 160.0   110  3.90 2.620     4 26.66667
2   21.0     6 160.0   110  3.90 2.875     4 26.66667
3   22.8     4 108.0    93  3.85 2.320     4 27.00000
4   21.4     6 258.0   110  3.08 3.215     3 43.00000
5   18.7     8 360.0   175  3.15 3.440     3 45.00000
6   18.1     6 225.0   105  2.76 3.460     3 37.50000
7   14.3     8 360.0   245  3.21 3.570     3 45.00000
8   24.4     4 146.7    62  3.69 3.190     4 36.67500
9   22.8     4 140.8    95  3.92 3.150     4 35.20000
10  19.2     6 167.6   123  3.92 3.440     4 27.93333
..   ...   ...   ...   ...   ...   ...   ...      ...
```

========================================================

```r
summarise(mtcars, mpg = mean(mpg)) # uses the grouping set previously
```

```
Source: local data frame [8 x 3]

    cyl  gear    mpg
  (dbl) (dbl)  (dbl)
1     4     3 21.500
2     6     3 19.750
3     8     3 15.050
4     4     4 26.925
5     6     4 19.750
6     4     5 28.200
7     6     5 19.700
8     8     5 15.400
```

========================================================
Multiple aggregations in one

```r
summarise(
  mtcars, 
  mpg = mean(mpg), 
  hp = mean(hp)
)
```

```
Source: local data frame [8 x 4]

    cyl  gear    mpg       hp
  (dbl) (dbl)  (dbl)    (dbl)
1     4     3 21.500  97.0000
2     6     3 19.750 107.5000
3     8     3 15.050 194.1667
4     4     4 26.925  76.0000
5     6     4 19.750 116.5000
6     4     5 28.200 102.0000
7     6     5 19.700 175.0000
8     8     5 15.400 299.5000
```
arrange 
========================================================

```r
arrange(mtcars, cyl, disp)
```

```
Source: local data frame [32 x 8]

     mpg   cyl  disp    hp  drat    wt  gear disp_cyl
   (dbl) (dbl) (dbl) (dbl) (dbl) (dbl) (dbl)    (dbl)
1   33.9     4  71.1    65  4.22 1.835     4   17.775
2   30.4     4  75.7    52  4.93 1.615     4   18.925
3   32.4     4  78.7    66  4.08 2.200     4   19.675
4   27.3     4  79.0    66  4.08 1.935     4   19.750
5   30.4     4  95.1   113  3.77 1.513     5   23.775
6   22.8     4 108.0    93  3.85 2.320     4   27.000
7   21.5     4 120.1    97  3.70 2.465     3   30.025
8   26.0     4 120.3    91  4.43 2.140     5   30.075
9   21.4     4 121.0   109  4.11 2.780     4   30.250
10  22.8     4 140.8    95  3.92 3.150     4   35.200
..   ...   ...   ...   ...   ...   ...   ...      ...
```

========================================================

```r
arrange(mtcars, cyl, desc(disp)) # desending!
```

```
Source: local data frame [32 x 8]

     mpg   cyl  disp    hp  drat    wt  gear disp_cyl
   (dbl) (dbl) (dbl) (dbl) (dbl) (dbl) (dbl)    (dbl)
1   24.4     4 146.7    62  3.69 3.190     4   36.675
2   22.8     4 140.8    95  3.92 3.150     4   35.200
3   21.4     4 121.0   109  4.11 2.780     4   30.250
4   26.0     4 120.3    91  4.43 2.140     5   30.075
5   21.5     4 120.1    97  3.70 2.465     3   30.025
6   22.8     4 108.0    93  3.85 2.320     4   27.000
7   30.4     4  95.1   113  3.77 1.513     5   23.775
8   27.3     4  79.0    66  4.08 1.935     4   19.750
9   32.4     4  78.7    66  4.08 2.200     4   19.675
10  30.4     4  75.7    52  4.93 1.615     4   18.925
..   ...   ...   ...   ...   ...   ...   ...      ...
```

select
========================================================


```r
select(mtcars, 1:4)
```

```
Source: local data frame [32 x 4]

     mpg   cyl  disp    hp
   (dbl) (dbl) (dbl) (dbl)
1   21.0     6 160.0   110
2   21.0     6 160.0   110
3   22.8     4 108.0    93
4   21.4     6 258.0   110
5   18.7     8 360.0   175
6   18.1     6 225.0   105
7   14.3     8 360.0   245
8   24.4     4 146.7    62
9   22.8     4 140.8    95
10  19.2     6 167.6   123
..   ...   ...   ...   ...
```

========================================================

```r
select(mtcars, mpg, cyl, disp, gear)
```

```
Source: local data frame [32 x 4]

     mpg   cyl  disp  gear
   (dbl) (dbl) (dbl) (dbl)
1   21.0     6 160.0     4
2   21.0     6 160.0     4
3   22.8     4 108.0     4
4   21.4     6 258.0     3
5   18.7     8 360.0     3
6   18.1     6 225.0     3
7   14.3     8 360.0     3
8   24.4     4 146.7     4
9   22.8     4 140.8     4
10  19.2     6 167.6     4
..   ...   ...   ...   ...
```

========================================================

```r
select(mtcars, mpg:hp)
```

```
Source: local data frame [32 x 4]

     mpg   cyl  disp    hp
   (dbl) (dbl) (dbl) (dbl)
1   21.0     6 160.0   110
2   21.0     6 160.0   110
3   22.8     4 108.0    93
4   21.4     6 258.0   110
5   18.7     8 360.0   175
6   18.1     6 225.0   105
7   14.3     8 360.0   245
8   24.4     4 146.7    62
9   22.8     4 140.8    95
10  19.2     6 167.6   123
..   ...   ...   ...   ...
```

========================================================

```r
select(mtcars, contains('a'))
```

```
Source: local data frame [32 x 2]

    drat  gear
   (dbl) (dbl)
1   3.90     4
2   3.90     4
3   3.85     4
4   3.08     3
5   3.15     3
6   2.76     3
7   3.21     3
8   3.69     4
9   3.92     4
10  3.92     4
..   ...   ...
```

========================================================

```r
select(mtcars, starts_with('d'))
```

```
Source: local data frame [32 x 3]

    disp  drat disp_cyl
   (dbl) (dbl)    (dbl)
1  160.0  3.90 26.66667
2  160.0  3.90 26.66667
3  108.0  3.85 27.00000
4  258.0  3.08 43.00000
5  360.0  3.15 45.00000
6  225.0  2.76 37.50000
7  360.0  3.21 45.00000
8  146.7  3.69 36.67500
9  140.8  3.92 35.20000
10 167.6  3.92 27.93333
..   ...   ...      ...
```

========================================================

```r
select(mtcars, -starts_with('d'))
```

```
Source: local data frame [32 x 5]

     mpg   cyl    hp    wt  gear
   (dbl) (dbl) (dbl) (dbl) (dbl)
1   21.0     6   110 2.620     4
2   21.0     6   110 2.875     4
3   22.8     4    93 2.320     4
4   21.4     6   110 3.215     3
5   18.7     8   175 3.440     3
6   18.1     6   105 3.460     3
7   14.3     8   245 3.570     3
8   24.4     4    62 3.190     4
9   22.8     4    95 3.150     4
10  19.2     6   123 3.440     4
..   ...   ...   ...   ...   ...
```

Putting it all together
========================================================
type: section

========================================================
Let's say during our exploratory analysis we want to create new column, fitler on that new column, 
then get the mean of that new column for each cylinder and gear


```r
mt <- mutate(mtcars, disp_cyl = disp/cyl)
mt <- filter(mt, disp_cyl > 30, mpg < 25)
mt <- group_by(mt, cyl, gear)
```


```r
summarise(mt, avg_d_cyl = mean(disp_cyl), min_d_cyl = min(disp_cyl))
```

```
Source: local data frame [5 x 4]

    cyl  gear avg_d_cyl min_d_cyl
  (dbl) (dbl)     (dbl)     (dbl)
1     4     3  30.02500    30.025
2     6     3  40.25000    37.500
3     8     3  44.70208    34.475
4     4     4  34.04167    30.250
5     8     5  40.75000    37.625
```



========================================================

That's a lot of repetition, and we now have an extra variable **mt** sitting around taking up space...

If we just want to print the answer without intermediary variables...

```r
summarise(group_by(filter(mutate(mtcars, disp_cyl = disp/cyl), disp_cyl > 30,  mpg > 23), cyl, gear), avg_d_cyl = mean(disp_cyl), min_d_cyl = min(disp_cyl))
```

```
Source: local data frame [2 x 4]

    cyl  gear avg_d_cyl min_d_cyl
  (dbl) (dbl)     (dbl)     (dbl)
1     4     4    36.675    36.675
2     4     5    30.075    30.075
```
(Say what???!)

========================================================
We can make that a bit easier to read... but not great

```r
summarise(
  group_by(
    filter(
      mutate(mtcars, disp_cyl = disp/cyl),
      disp_cyl > 30, 
      mpg > 23
    ),
    cyl,
    gear
  ),
  avg_d_cyl = mean(disp_cyl),
  min_d_cyl = min(disp_cyl)
)
```

```
Source: local data frame [2 x 4]

    cyl  gear avg_d_cyl min_d_cyl
  (dbl) (dbl)     (dbl)     (dbl)
1     4     4    36.675    36.675
2     4     5    30.075    30.075
```
(Butt ugly!)

Ceci n'est pas une pipe
=======================================================
type: section


=======================================================
Our last example was barely readable. What can we do? **Pipes** to the rescue!

  * Introduced through the **magrittr** package and **dplyr** package around the same time
  * Inspired by unix pipes, and F-sharp pipes

A pipe ('`%>%`') takes the left-hand side and passes it to the right-hand side as the first argument.

=======================================================
Without pipes...

```r
summarise(
  group_by(
    filter(
      mutate(
        mtcars, 
        disp_cyl = disp/cyl
      ),
      disp_cyl > 30, 
      mpg > 23
    ),
    cyl,
    gear
  ),
  avg_d_cyl = mean(disp_cyl),
  min_d_cyl = min(disp_cyl)
)
```

```
Source: local data frame [2 x 4]

    cyl  gear avg_d_cyl min_d_cyl
  (dbl) (dbl)     (dbl)     (dbl)
1     4     4    36.675    36.675
2     4     5    30.075    30.075
```
***
With pipes!

```r
mtcars %>% 
  mutate(disp_cyl = disp/cyl) %>% 
  filter(disp_cyl>30, mpg>23) %>% 
  group_by(cyl, gear) %>% 
  summarise(
    avg_d_cyl = mean(disp_cyl),
    min_d_cyl = min(disp_cyl)
  )
```

```
Source: local data frame [2 x 4]

    cyl  gear avg_d_cyl min_d_cyl
  (dbl) (dbl)     (dbl)     (dbl)
1     4     4    36.675    36.675
2     4     5    30.075    30.075
```

That's not where it ends...
==========================
type: section


==========================
Let's load up a bigger dat set

```r
library(hflights) # to load the flights data set
tbl_df(hflights)
```

```
Source: local data frame [227,496 x 21]

    Year Month DayofMonth DayOfWeek DepTime ArrTime UniqueCarrier
   (int) (int)      (int)     (int)   (int)   (int)         (chr)
1   2011     1          1         6    1400    1500            AA
2   2011     1          2         7    1401    1501            AA
3   2011     1          3         1    1352    1502            AA
4   2011     1          4         2    1403    1513            AA
5   2011     1          5         3    1405    1507            AA
6   2011     1          6         4    1359    1503            AA
7   2011     1          7         5    1359    1509            AA
8   2011     1          8         6    1355    1454            AA
9   2011     1          9         7    1443    1554            AA
10  2011     1         10         1    1443    1553            AA
..   ...   ...        ...       ...     ...     ...           ...
Variables not shown: FlightNum (int), TailNum (chr), ActualElapsedTime
  (int), AirTime (int), ArrDelay (int), DepDelay (int), Origin (chr), Dest
  (chr), Distance (int), TaxiIn (int), TaxiOut (int), Cancelled (int),
  CancellationCode (chr), Diverted (int)
```


==========================


```r
hflights %>% 
  mutate(ArrEarly = ArrDelay < 0) %>% 
  filter(DepDelay < 2*60, Distance > 200) %>% 
  group_by(UniqueCarrier, ArrEarly) %>% 
  summarise(MeanArrDelay = mean(ArrDelay, na.rm = TRUE))
```

```
Source: local data frame [30 x 3]

   UniqueCarrier ArrEarly MeanArrDelay
           (chr)    (lgl)        (dbl)
1             AA    FALSE     19.33860
2             AS    FALSE     19.62941
3             B6    FALSE     29.03906
4             CO    FALSE     17.69463
5             DL    FALSE     19.51287
6             EV    FALSE     23.97193
7             F9    FALSE     14.80691
8             FL    FALSE     18.91988
9             MQ    FALSE     24.94912
10            OO    FALSE     19.25902
..           ...      ...          ...
```

==========================


```r
step1 <- . %>%  
  mutate(ArrEarly = ArrDelay < 0) %>% 
  filter(DepDelay < 2*60, Distance > 200) 

step2 <- . %>% 
  group_by(UniqueCarrier, ArrEarly) %>% 
  summarise(MeanArrDelay = mean(ArrDelay, na.rm = TRUE))

hflights %>% step1 %>% step2
```

```
Source: local data frame [30 x 3]

   UniqueCarrier ArrEarly MeanArrDelay
           (chr)    (lgl)        (dbl)
1             AA    FALSE     19.33860
2             AS    FALSE     19.62941
3             B6    FALSE     29.03906
4             CO    FALSE     17.69463
5             DL    FALSE     19.51287
6             EV    FALSE     23.97193
7             F9    FALSE     14.80691
8             FL    FALSE     18.91988
9             MQ    FALSE     24.94912
10            OO    FALSE     19.25902
..           ...      ...          ...
```

Oops.. why the NAs?
===========================


```r
na_filter <- . %>% filter(!is.na(ArrDelay), !is.na(DepDelay))

hflights %>% na_filter %>% step1 %>% step2
```

```
Source: local data frame [30 x 3]

   UniqueCarrier ArrEarly MeanArrDelay
           (chr)    (lgl)        (dbl)
1             AA    FALSE     19.33860
2             AS    FALSE     19.62941
3             B6    FALSE     29.03906
4             CO    FALSE     17.69463
5             DL    FALSE     19.51287
6             EV    FALSE     23.97193
7             F9    FALSE     14.80691
8             FL    FALSE     18.91988
9             MQ    FALSE     24.94912
10            OO    FALSE     19.25902
..           ...      ...          ...
```

Can we feed this to ggplot?
===========================


```r
hflights %>% 
  na_filter %>%  # remove NAs step
  step1 %>%      # mutate and filter step  
  step2 %>%      # aggregate step
  ggplot(aes(x= UniqueCarrier, y = MeanArrDelay)) + 
    geom_bar(stat = "identity")
```

![plot of chunk unnamed-chunk-37](intro_dplyr_and_rdplyr-figure/unnamed-chunk-37-1.png) 


What about pivoting with tidyr?
===========================


```r
hflights %>% 
  na_filter %>%  # remove NAs step
  step1 %>%      # mutate and filter step  
  step2 %>%      # aggregate step
  spread(ArrEarly, MeanArrDelay)
```

```
Source: local data frame [15 x 3]

   UniqueCarrier    FALSE       TRUE
           (chr)    (dbl)      (dbl)
1             AA 19.33860 -11.542683
2             AS 19.62941 -12.921875
3             B6 29.03906 -13.862944
4             CO 17.69463 -10.191859
5             DL 19.51287 -10.812746
6             EV 23.97193 -12.333847
7             F9 14.80691  -6.843373
8             FL 18.91988 -10.183298
9             MQ 24.94912 -11.759738
10            OO 19.25902  -9.804896
11            UA 21.36967 -11.833333
12            US 15.01725 -11.464231
13            WN 18.72907  -8.483101
14            XE 18.69617  -7.952418
15            YV 16.85366 -10.216216
```

... let's write this to disk
===========================


```r
hflights %>% 
  na_filter %>%  
  step1 %>%      
  step2 %>%      
  spread(ArrEarly, MeanArrDelay) %>% 
  write.csv("my_flight_data.csv")    # write to disk 
```


Magrittr 'tee' operator ('%T>%')
===========================

In the statement below, the tee operator finishes the transformation
after '`spread`', but simultaneously pipes the output to the '`write.csv`' function.


```r
result <-
  hflights %>% na_filter %>% step1 %>% step2 %>%      
  spread(ArrEarly, MeanArrDelay) %T>% 
  write.csv("my_flight_data.csv")    # write to disk, and assign!
```

Also handy for printing while assigning


```r
result <-
  hflights %>% na_filter %>% step1 %>% step2 %>%      
  spread(ArrEarly, MeanArrDelay) %T>% 
  print   # print result, and assign too!
```

```
Source: local data frame [15 x 3]

   UniqueCarrier    FALSE       TRUE
           (chr)    (dbl)      (dbl)
1             AA 19.33860 -11.542683
2             AS 19.62941 -12.921875
3             B6 29.03906 -13.862944
4             CO 17.69463 -10.191859
5             DL 19.51287 -10.812746
6             EV 23.97193 -12.333847
7             F9 14.80691  -6.843373
8             FL 18.91988 -10.183298
9             MQ 24.94912 -11.759738
10            OO 19.25902  -9.804896
11            UA 21.36967 -11.833333
12            US 15.01725 -11.464231
13            WN 18.72907  -8.483101
14            XE 18.69617  -7.952418
15            YV 16.85366 -10.216216
```

So dplyr looks great... is it fast? YES
===================================

1. dplyr is written in c++ for speed
2. it's *much* faster than base R
3. it's not quite as fast as `data.table` 
  * see this question on [stackoverflow](https://stackoverflow.com/questions/21435339/data-table-vs-dplyr-can-one-do-something-well-the-other-cant-or-does-poorly)
  * and detailed benchmarks [here](https://github.com/Rdatatable/data.table/wiki/Benchmarks-%3A-Grouping)



**BUT** `dplyr` is fast enough in most situations.

Hadley has purposely traded some speed for syntax, since the bottleneck is usually in your head. 

What is this black magic from dplyr?
==========================
type: section

Advanced R by Hadley Wickham
======================
![alt text](advr.jpg)
***
Available for free from:

http://adv-r.had.co.nz/

Amazingly clear and well written.
Easily worth the purchase of the paper copy.

A need...
==========================

* dealing with an old version of R (2.12) that didn't support `dplyr`
* just wanted to learn more about `dplyr`

'rdplyr' is born
==========================
type: section


==========================
I set about learnign the tricks behind the `dplyr` interface...
* surprisingly simple
* surprisingly brief



```r
devtools::install_github('datalove/rdplyr')
library(rdplyr)  # note: make sure you don't load dplyr itself afterward
```

filter
==========================
We saw earlier that basic filter in R looks like this


```r
mtcars[mtcars$mpg > 33,]
```

```
Source: local data frame [1 x 8]

    mpg   cyl  disp    hp  drat    wt  gear disp_cyl
  (dbl) (dbl) (dbl) (dbl) (dbl) (dbl) (dbl)    (dbl)
1  33.9     4  71.1    65  4.22 1.835     4   17.775
```
Let's make it a function (clearly this doesn't work)



```r
filter <- function(.data, expr) { .data[expr,] }
filter(mtcars, mpg > 33) # error!
```

```
        mpg cyl disp hp drat wt qsec vs am gear carb
NA       NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1     NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.2     NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.3     NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.4     NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.5     NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.6     NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.7     NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.8     NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.9     NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.10    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.11    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.12    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.13    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.14    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.15    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.16    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.17    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.18    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.19    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.20    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.21    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.22    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.23    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.24    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.25    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.26    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.27    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.28    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.29    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.30    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.31    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.32    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.33    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.34    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.35    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.36    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.37    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.38    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.39    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.40    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.41    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.42    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.43    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.44    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.45    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.46    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.47    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.48    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.49    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.50    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.51    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.52    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.53    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.54    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.55    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.56    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.57    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.58    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.59    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.60    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.61    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.62    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.63    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.64    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.65    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.66    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.67    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.68    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.69    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.70    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.71    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.72    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.73    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.74    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.75    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.76    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.77    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.78    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.79    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.80    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.81    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.82    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.83    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.84    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.85    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.86    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.87    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.88    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.89    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.90    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.91    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.92    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.93    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.94    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.95    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.96    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.97    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.98    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.99    NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.100   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.101   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.102   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.103   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.104   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.105   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.106   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.107   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.108   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.109   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.110   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.111   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.112   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.113   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.114   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.115   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.116   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.117   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.118   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.119   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.120   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.121   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.122   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.123   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.124   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.125   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.126   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.127   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.128   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.129   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.130   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.131   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.132   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.133   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.134   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.135   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.136   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.137   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.138   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.139   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.140   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.141   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.142   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.143   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.144   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.145   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.146   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.147   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.148   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.149   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.150   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.151   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.152   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.153   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.154   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.155   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.156   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.157   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.158   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.159   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.160   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.161   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.162   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.163   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.164   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.165   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.166   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.167   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.168   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.169   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.170   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.171   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.172   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.173   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.174   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.175   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.176   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.177   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.178   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.179   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.180   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.181   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.182   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.183   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.184   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.185   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.186   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.187   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.188   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.189   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.190   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.191   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.192   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.193   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.194   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.195   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.196   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.197   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.198   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.199   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.200   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.201   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.202   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.203   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.204   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.205   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.206   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.207   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.208   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.209   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.210   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.211   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.212   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.213   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.214   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.215   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.216   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.217   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.218   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.219   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.220   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.221   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.222   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.223   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.224   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.225   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.226   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.227   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.228   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.229   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.230   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.231   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.232   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.233   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.234   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.235   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.236   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.237   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.238   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.239   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.240   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.241   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.242   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.243   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.244   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.245   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.246   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.247   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.248   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.249   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.250   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.251   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.252   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.253   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.254   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.255   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.256   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.257   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.258   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.259   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.260   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.261   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.262   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.263   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.264   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.265   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.266   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.267   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.268   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.269   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.270   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.271   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.272   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.273   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.274   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.275   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.276   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.277   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.278   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.279   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.280   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.281   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.282   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.283   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.284   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.285   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.286   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.287   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.288   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.289   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.290   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.291   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.292   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.293   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.294   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.295   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.296   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.297   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.298   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.299   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.300   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.301   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.302   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.303   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.304   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.305   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.306   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.307   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.308   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.309   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.310   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.311   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.312   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.313   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.314   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.315   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.316   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.317   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.318   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.319   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.320   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.321   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.322   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.323   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.324   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.325   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.326   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.327   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.328   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.329   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.330   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.331   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.332   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.333   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.334   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.335   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.336   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.337   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.338   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.339   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.340   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.341   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.342   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.343   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.344   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.345   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.346   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.347   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.348   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.349   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.350   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.351   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.352   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.353   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.354   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.355   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.356   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.357   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.358   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.359   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.360   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.361   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.362   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.363   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.364   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.365   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.366   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.367   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.368   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.369   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.370   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.371   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.372   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.373   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.374   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.375   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.376   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.377   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.378   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.379   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.380   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.381   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.382   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.383   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.384   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.385   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.386   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.387   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.388   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.389   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.390   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.391   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.392   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.393   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.394   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.395   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.396   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.397   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.398   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.399   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.400   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.401   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.402   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.403   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.404   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.405   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.406   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.407   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.408   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.409   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.410   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.411   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.412   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.413   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.414   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.415   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.416   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.417   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.418   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.419   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.420   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.421   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.422   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.423   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.424   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.425   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.426   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.427   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.428   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.429   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.430   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.431   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.432   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.433   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.434   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.435   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.436   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.437   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.438   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.439   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.440   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.441   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.442   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.443   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.444   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.445   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.446   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.447   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.448   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.449   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.450   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.451   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.452   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.453   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.454   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.455   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.456   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.457   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.458   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.459   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.460   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.461   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.462   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.463   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.464   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.465   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.466   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.467   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.468   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.469   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.470   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.471   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.472   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.473   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.474   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.475   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.476   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.477   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.478   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.479   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.480   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.481   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.482   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.483   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.484   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.485   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.486   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.487   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.488   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.489   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.490   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.491   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.492   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.493   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.494   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.495   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.496   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.497   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.498   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.499   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.500   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.501   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.502   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.503   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.504   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.505   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.506   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.507   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.508   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.509   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.510   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.511   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.512   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.513   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.514   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.515   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.516   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.517   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.518   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.519   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.520   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.521   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.522   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.523   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.524   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.525   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.526   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.527   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.528   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.529   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.530   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.531   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.532   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.533   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.534   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.535   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.536   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.537   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.538   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.539   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.540   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.541   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.542   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.543   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.544   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.545   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.546   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.547   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.548   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.549   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.550   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.551   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.552   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.553   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.554   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.555   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.556   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.557   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.558   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.559   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.560   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.561   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.562   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.563   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.564   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.565   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.566   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.567   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.568   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.569   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.570   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.571   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.572   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.573   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.574   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.575   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.576   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.577   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.578   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.579   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.580   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.581   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.582   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.583   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.584   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.585   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.586   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.587   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.588   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.589   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.590   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.591   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.592   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.593   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.594   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.595   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.596   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.597   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.598   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.599   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.600   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.601   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.602   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.603   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.604   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.605   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.606   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.607   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.608   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.609   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.610   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.611   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.612   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.613   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.614   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.615   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.616   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.617   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.618   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.619   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.620   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.621   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.622   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.623   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.624   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.625   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.626   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.627   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.628   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.629   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.630   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.631   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.632   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.633   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.634   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.635   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.636   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.637   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.638   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.639   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.640   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.641   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.642   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.643   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.644   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.645   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.646   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.647   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.648   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.649   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.650   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.651   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.652   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.653   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.654   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.655   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.656   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.657   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.658   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.659   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.660   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.661   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.662   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.663   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.664   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.665   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.666   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.667   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.668   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.669   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.670   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.671   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.672   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.673   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.674   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.675   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.676   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.677   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.678   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.679   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.680   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.681   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.682   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.683   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.684   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.685   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.686   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.687   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.688   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.689   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.690   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.691   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.692   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.693   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.694   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.695   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.696   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.697   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.698   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.699   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.700   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.701   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.702   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.703   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.704   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.705   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.706   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.707   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.708   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.709   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.710   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.711   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.712   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.713   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.714   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.715   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.716   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.717   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.718   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.719   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.720   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.721   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.722   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.723   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.724   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.725   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.726   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.727   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.728   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.729   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.730   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.731   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.732   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.733   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.734   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.735   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.736   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.737   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.738   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.739   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.740   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.741   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.742   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.743   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.744   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.745   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.746   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.747   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.748   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.749   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.750   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.751   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.752   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.753   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.754   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.755   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.756   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.757   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.758   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.759   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.760   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.761   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.762   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.763   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.764   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.765   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.766   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.767   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.768   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.769   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.770   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.771   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.772   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.773   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.774   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.775   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.776   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.777   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.778   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.779   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.780   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.781   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.782   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.783   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.784   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.785   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.786   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.787   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.788   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.789   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.790   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.791   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.792   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.793   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.794   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.795   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.796   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.797   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.798   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.799   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.800   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.801   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.802   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.803   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.804   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.805   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.806   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.807   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.808   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.809   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.810   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.811   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.812   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.813   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.814   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.815   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.816   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.817   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.818   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.819   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.820   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.821   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.822   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.823   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.824   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.825   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.826   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.827   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.828   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.829   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.830   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.831   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.832   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.833   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.834   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.835   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.836   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.837   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.838   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.839   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.840   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.841   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.842   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.843   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.844   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.845   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.846   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.847   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.848   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.849   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.850   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.851   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.852   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.853   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.854   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.855   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.856   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.857   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.858   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.859   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.860   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.861   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.862   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.863   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.864   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.865   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.866   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.867   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.868   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.869   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.870   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.871   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.872   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.873   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.874   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.875   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.876   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.877   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.878   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.879   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.880   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.881   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.882   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.883   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.884   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.885   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.886   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.887   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.888   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.889   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.890   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.891   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.892   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.893   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.894   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.895   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.896   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.897   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.898   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.899   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.900   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.901   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.902   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.903   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.904   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.905   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.906   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.907   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.908   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.909   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.910   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.911   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.912   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.913   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.914   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.915   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.916   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.917   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.918   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.919   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.920   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.921   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.922   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.923   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.924   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.925   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.926   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.927   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.928   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.929   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.930   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.931   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.932   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.933   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.934   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.935   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.936   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.937   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.938   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.939   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.940   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.941   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.942   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.943   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.944   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.945   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.946   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.947   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.948   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.949   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.950   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.951   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.952   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.953   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.954   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.955   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.956   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.957   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.958   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.959   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.960   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.961   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.962   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.963   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.964   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.965   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.966   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.967   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.968   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.969   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.970   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.971   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.972   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.973   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.974   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.975   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.976   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.977   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.978   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.979   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.980   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.981   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.982   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.983   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.984   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.985   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.986   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.987   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.988   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.989   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.990   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.991   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.992   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.993   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.994   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.995   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.996   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.997   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.998   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.999   NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1000  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1001  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1002  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1003  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1004  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1005  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1006  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1007  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1008  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1009  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1010  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1011  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1012  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1013  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1014  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1015  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1016  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1017  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1018  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1019  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1020  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1021  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1022  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1023  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1024  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1025  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1026  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1027  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1028  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1029  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1030  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1031  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1032  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1033  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1034  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1035  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1036  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1037  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1038  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1039  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1040  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1041  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1042  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1043  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1044  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1045  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1046  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1047  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1048  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1049  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1050  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1051  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1052  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1053  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1054  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1055  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1056  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1057  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1058  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1059  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1060  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1061  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1062  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1063  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1064  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1065  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1066  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1067  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1068  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1069  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1070  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1071  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1072  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1073  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1074  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1075  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1076  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1077  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1078  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1079  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1080  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1081  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1082  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1083  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1084  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1085  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1086  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1087  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1088  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1089  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1090  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1091  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1092  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1093  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1094  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1095  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1096  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1097  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1098  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1099  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1100  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1101  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1102  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1103  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1104  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1105  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1106  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1107  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1108  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1109  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1110  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1111  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1112  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1113  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1114  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1115  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1116  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1117  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1118  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1119  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1120  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1121  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1122  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1123  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1124  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1125  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1126  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1127  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1128  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1129  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1130  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1131  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1132  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1133  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1134  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1135  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1136  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1137  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1138  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1139  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1140  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1141  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1142  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1143  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1144  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1145  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1146  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1147  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1148  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1149  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1150  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1151  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1152  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1153  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1154  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1155  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1156  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1157  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1158  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1159  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1160  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1161  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1162  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1163  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1164  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1165  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1166  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1167  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1168  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1169  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1170  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1171  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1172  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1173  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1174  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1175  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1176  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1177  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1178  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1179  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1180  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1181  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1182  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1183  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1184  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1185  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1186  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1187  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1188  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1189  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1190  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1191  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1192  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1193  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1194  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1195  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1196  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1197  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1198  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1199  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1200  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1201  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1202  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1203  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1204  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1205  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1206  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1207  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1208  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1209  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1210  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1211  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1212  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1213  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1214  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1215  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1216  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1217  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1218  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1219  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1220  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1221  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1222  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1223  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1224  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1225  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1226  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1227  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1228  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1229  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1230  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1231  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1232  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1233  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1234  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1235  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1236  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1237  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1238  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1239  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1240  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1241  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1242  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1243  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1244  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1245  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1246  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1247  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1248  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1249  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1250  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1251  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1252  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1253  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1254  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1255  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1256  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1257  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1258  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1259  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1260  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1261  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1262  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1263  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1264  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1265  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1266  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1267  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1268  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1269  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1270  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1271  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1272  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1273  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1274  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1275  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1276  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1277  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1278  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1279  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1280  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1281  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1282  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1283  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1284  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1285  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1286  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1287  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1288  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1289  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1290  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1291  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1292  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1293  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1294  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1295  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1296  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1297  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1298  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1299  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1300  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1301  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1302  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1303  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1304  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1305  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1306  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1307  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1308  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1309  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1310  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1311  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1312  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1313  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1314  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1315  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1316  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1317  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1318  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1319  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1320  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1321  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1322  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1323  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1324  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1325  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1326  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1327  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1328  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1329  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1330  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1331  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1332  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1333  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1334  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1335  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1336  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1337  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1338  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1339  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1340  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1341  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1342  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1343  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1344  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1345  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1346  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1347  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1348  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1349  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1350  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1351  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1352  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1353  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1354  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1355  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1356  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1357  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1358  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1359  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1360  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1361  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1362  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1363  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1364  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1365  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1366  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1367  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1368  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1369  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1370  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1371  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1372  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1373  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1374  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1375  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1376  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1377  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1378  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1379  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1380  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1381  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1382  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1383  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1384  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1385  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1386  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1387  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1388  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1389  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1390  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1391  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1392  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1393  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1394  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1395  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1396  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1397  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1398  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1399  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1400  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1401  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1402  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1403  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1404  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1405  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1406  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1407  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1408  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1409  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1410  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1411  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1412  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1413  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1414  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1415  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1416  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1417  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1418  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1419  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1420  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1421  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1422  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1423  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1424  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1425  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1426  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1427  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1428  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1429  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1430  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1431  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1432  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1433  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1434  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1435  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1436  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1437  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1438  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1439  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1440  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1441  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1442  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1443  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1444  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1445  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1446  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1447  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1448  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1449  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1450  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1451  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1452  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1453  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1454  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1455  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1456  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1457  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1458  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1459  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1460  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1461  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1462  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1463  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1464  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1465  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1466  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1467  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1468  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1469  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1470  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1471  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1472  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1473  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1474  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1475  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1476  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1477  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1478  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1479  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1480  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1481  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1482  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1483  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1484  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1485  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1486  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1487  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1488  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1489  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1490  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1491  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1492  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1493  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1494  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1495  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1496  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1497  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1498  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1499  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1500  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1501  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1502  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1503  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1504  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1505  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1506  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1507  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1508  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1509  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1510  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1511  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1512  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1513  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1514  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1515  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1516  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1517  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1518  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1519  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1520  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1521  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1522  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1523  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1524  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1525  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1526  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1527  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1528  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1529  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1530  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1531  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1532  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1533  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1534  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1535  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1536  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1537  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1538  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1539  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1540  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1541  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1542  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1543  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1544  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1545  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1546  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1547  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1548  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1549  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1550  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1551  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1552  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1553  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1554  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1555  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1556  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1557  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1558  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1559  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1560  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1561  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1562  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1563  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1564  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1565  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1566  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1567  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1568  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1569  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1570  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1571  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1572  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1573  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1574  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1575  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1576  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1577  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1578  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1579  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1580  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1581  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1582  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1583  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1584  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1585  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1586  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1587  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1588  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1589  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1590  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1591  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1592  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1593  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1594  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1595  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1596  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1597  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1598  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1599  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1600  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1601  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1602  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1603  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1604  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1605  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1606  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1607  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1608  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1609  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1610  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1611  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1612  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1613  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1614  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1615  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1616  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1617  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1618  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1619  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1620  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1621  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1622  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1623  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1624  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1625  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1626  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1627  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1628  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1629  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1630  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1631  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1632  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1633  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1634  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1635  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1636  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1637  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1638  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1639  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1640  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1641  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1642  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1643  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1644  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1645  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1646  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
NA.1647  NA  NA   NA NA   NA NA   NA NA NA   NA   NA
```

filter - what's going on?
==========================

```r
filter <- function(.data, expr) { print(expr) }
filter(mtcars, mpg > 33)        # computer says 'no'
```

```
       manufacturer model displ year   cyl trans drv   cty   hwy fl class
  [1,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
  [2,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
  [3,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
  [4,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
  [5,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
  [6,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
  [7,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
  [8,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
  [9,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [10,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [11,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [12,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [13,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [14,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [15,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [16,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [17,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [18,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [19,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [20,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [21,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [22,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [23,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [24,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [25,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [26,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [27,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [28,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [29,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [30,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [31,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [32,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [33,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [34,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [35,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [36,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [37,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [38,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [39,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [40,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [41,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [42,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [43,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [44,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [45,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [46,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [47,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [48,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [49,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [50,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [51,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [52,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [53,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [54,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [55,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [56,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [57,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [58,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [59,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [60,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [61,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [62,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [63,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [64,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [65,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [66,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [67,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [68,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [69,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [70,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [71,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [72,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [73,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [74,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [75,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [76,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [77,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [78,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [79,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [80,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [81,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [82,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [83,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [84,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [85,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [86,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [87,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [88,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [89,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [90,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [91,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [92,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [93,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [94,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [95,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [96,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [97,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [98,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
 [99,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[100,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[101,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[102,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[103,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[104,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[105,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE  TRUE NA    NA
[106,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE  TRUE NA    NA
[107,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE  TRUE NA    NA
[108,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[109,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[110,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[111,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[112,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[113,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[114,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[115,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[116,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[117,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[118,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[119,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[120,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[121,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[122,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[123,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[124,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[125,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[126,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[127,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[128,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[129,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[130,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[131,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[132,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[133,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[134,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[135,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[136,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[137,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[138,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[139,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[140,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[141,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[142,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[143,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[144,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[145,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[146,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[147,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[148,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[149,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[150,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[151,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[152,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[153,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[154,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[155,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[156,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[157,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[158,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[159,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[160,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[161,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[162,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[163,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[164,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[165,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[166,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[167,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[168,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[169,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[170,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[171,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[172,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[173,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[174,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[175,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[176,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[177,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[178,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[179,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[180,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[181,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[182,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[183,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[184,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[185,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[186,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[187,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[188,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[189,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[190,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[191,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[192,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[193,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[194,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[195,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[196,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE  TRUE NA    NA
[197,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE  TRUE NA    NA
[198,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE  TRUE NA    NA
[199,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[200,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[201,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[202,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[203,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[204,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[205,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[206,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[207,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[208,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[209,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[210,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[211,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[212,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[213,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE  TRUE NA    NA
[214,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[215,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[216,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[217,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[218,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[219,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[220,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[221,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[222,]           NA    NA FALSE TRUE FALSE    NA  NA  TRUE  TRUE NA    NA
[223,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE  TRUE NA    NA
[224,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[225,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[226,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[227,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[228,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[229,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[230,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[231,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[232,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[233,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
[234,]           NA    NA FALSE TRUE FALSE    NA  NA FALSE FALSE NA    NA
```

```r
filter(mrcars, mtcars$mpg > 33) # computer says 'no' 
```

```
 [1] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[12] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE FALSE FALSE
[23] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
```


==========================
The `mpg > 33` part is called an 'expression' in R. 

Expressions are evaluated as soon as they're encountered, so we need a special
function that doesn't evaluate an expression. 

**substitute()** takes an expression and returns an **unevaluated call**


```r
filter <- function(.data, expr) { print(substitute(expr)) }
filter(mtcars, mpg > 33) 
```

```
mpg > 33
```

How do we evaluate an unevaluated call? The **eval()** function!

```r
filter <- function(.data,expr){.data[eval(substitute(expr), envir = .data),]}
filter(mtcars, mpg > 33)
```

```
                mpg cyl disp hp drat    wt qsec vs am gear carb
Toyota Corolla 33.9   4 71.1 65 4.22 1.835 19.9  1  1    4    1
```

mutate
==============================
We'll take what we learned previously and apply it to `mutate`.

But mutate takes a **named expression** like `kw = hp/0.746` , whereas filter uses unnamed expressions like `mpg > 33`.

This causes some trouble:


```r
mutate <- function(.data, expr) { 1 }
mutate(mtcars, kw = hp/0.746)  
```

```
Error in mutate(mtcars, kw = hp/0.746) : unused argument (kw = hp/0.746)
```



To handle named arguments...use 'dots'
==============================


```r
mutate <- function(.data, ...) {
  args <- eval(substitute(alist(...))) # get list of named arguments
  print(1)
}
mutate(mtcars, kw = hp/0.746)
```

```
[1] 1
```
Okay no more errors, but doesn't do anything yet


=============================================

Version two...

```r
mutate <- function(.data, ...) {
  args <- eval(substitute(alist(...))) # get list of named arguments in '...'
  
  nm   <- names(args)[[1]]
  expr <- args[[1]]

  .data[,nm] <- eval(expr, envir = .data) # create column from expression
  .data
}
```
Does it work? Yes!

```r
mutate(mtcars, kw = hp/0.746) %>% head()
```

```
                   mpg cyl disp  hp drat    wt  qsec vs am gear carb
Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
                        kw
Mazda RX4         147.4531
Mazda RX4 Wag     147.4531
Datsun 710        124.6649
Hornet 4 Drive    147.4531
Hornet Sportabout 234.5845
Valiant           140.7507
```


=============================================

The `eval(substitute(alist(...)))` part of the code above actually sets us up
to accept **multiple** named expressions like below:


```r
mutate(mtcars, kw = hp/0.746, good_mpg = mpg > 30)
```

You can explore that on your own.


```r
rdplyr::mutate
```

```
function (.data, ...) 
{
    arg <- dots(...)
    nms <- names(arg)
    for (i in seq_along(arg)) {
        r <- arg[[i]]
        n <- nms[i]
        .data[, n] <- eval(r, .data)
    }
    .data
}
<environment: namespace:rdplyr>
```

Pipe ('%>%')
=============================================

In R, custom operators can be created like so:


```r
`%s%` <- function(a,b) paste0(a,b)
```

```r
'one' %s% 'two'
```

```
[1] "onetwo"
```

========================

So can we create a basic version of the pipe by doing this? YES!

```r
`%>%` <- function(x,f) f(x)

rnorm(100) %>% hist 
```

![plot of chunk unnamed-chunk-58](intro_dplyr_and_rdplyr-figure/unnamed-chunk-58-1.png) 
***
But it doesn't work for additional arguments

```r
rnorm(100) %>% 
  hist(main = "testing") 
```
```
Error in hist.default(main = "testing") : 
  argument "x" is missing, with no default
```


========================

To make it work, we'll have to actually reorder the arguments in the left and right sides of the pipe.

**match.call()** to the rescue!

We won't cover it now but the code is fairly simple


```r
rdplyr:::`%>%`
```

```
function (x, y) 
{
    cl <- match.call()
    lhs <- cl[[2]]
    rhs <- cl[[3]]
    if (length(rhs) == 1) {
        y(x)
    }
    else {
        rhsl <- as.list(rhs)
        rhsl <- c(rhsl[1], lhs, rhsl[2:length(rhsl)])
        eval(as.call(rhsl))
    }
}
<environment: namespace:rdplyr>
```

group_by
========================

```r
rdplyr::group_by
```

```
function (.data, ...) 
{
    arg <- dots(...)
    class(.data) <- c("grouped_df", class(.data))
    attr(.data, "grouping") <- arg
    .data
}
<environment: namespace:rdplyr>
```

summarise
========================

```r
rdplyr::summarise
```

```
function (.data, ...) 
{
    arg <- dots(...)
    nms <- names(arg)
    if (inherits(.data, "grouped_df")) {
        grp <- attr(.data, "grouping")
        pieces <- split(.data, lapply(grp, eval, .data), drop = TRUE)
        .data <- lapply(pieces, function(p) {
            dat <- p[1, as.character(grp)]
            for (i in seq_along(arg)) {
                dat[, nms[[i]]] <- eval(arg[[i]], p)
            }
            dat
        })
        .data <- do.call(rbind, .data)
        rownames(.data) <- NULL
        attr(.data, "grouping") <- grp
        .data
    }
}
<environment: namespace:rdplyr>
```

Further info...
==========================

Again, if you're interested, you can install the package:


```r
devtools::install_github('datalove/rdplyr')
library(rdplyr)  # note: make sure you don't load dplyr itself afterward
```

Or view the code:

https://github.com/datalove/rdplyr/blob/master/R/rdplyr.R

Or check out my half-baked blog posts on the subject here:

http://datalove.org/r/dplyr/2015/01/25/dplyr-100-lines-01-intro/
http://datalove.org/r/dplyr/2015/01/26/dplyr-100-lines-02-filter/
http://datalove.org/r/dplyr/2015/01/27/dplyr-100-lines-03-mutate/


Questions?
========================================================
type: prompt
