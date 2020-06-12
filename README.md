Project 1
================
David Weck
6/6/2020

  - [About JSON](#about-json)
  - [R Packages for JSON](#r-packages-for-json)
  - [Connecting to NHL API](#connecting-to-nhl-api)
  - [EDA on NHL Data](#eda-on-nhl-data)

# About JSON

JSON (short for JavaScript Object Notation) is a format that is used to
store and transfer data. It is text-only format that mirrors the syntax
of JavaScript objects, which allows it to be conveniently read and
written by people. It is comprised of key/value pairs and arrays, which
are structures ubiquitous across nearly all programming languages.
Additionally, JSON requires a relatively small amount of computer
overhead and can be interpreted by virtually all languages. It is these
characteristics that make JSON format an excellent way to store and
transfer data. [(Source)](https://www.json.org/json-en.html) All of
these characteristics also make JSON a very widely used method for data
exchange. Many web applications use JSON to transfer data back and forth
with servers, and APIs often store data in JSON format to allow for easy
retrieval from users. As mentioned earlier, nearly all programming
languages support JSON, further allowing widespread use of the format.
[(Source)](https://www.tutorialspoint.com/json/json_overview.htm)

# R Packages for JSON

There are 3 main packages for reading JSON data into R: `rjson`,
`RJSONIO`, and `jsonlite`. Information on `rjson` and `RJSONIO`,
respectively, can be found
[here](https://cran.r-project.org/web/packages/rjson/rjson.pdf) and
[here](https://cran.r-project.org/web/packages/RJSONIO/RJSONIO.pdf). In
this section, I will be focusing on `jsonlite`. `jsonlite` is an R
package with efficient tools for reading and creating JSON data. I have
chosen it because it is one of the best packages available for
communicating with and retrieving data from APIs through R. One of the
most important functions in `jsonlite` is `fromJSON`. This function
allows for smooth and fast retrieval of JSON data from a JSON string,
URL, or file. Another important function, which is also an argument to
`fromJSON`, is `flatten`. In JSON data, it is common to see data frames
within other data frames. The `flatten` function/argument converts JSON
data with this structure into the 2D structure usually desired for data
frames. There are many other functions that make `jsonlite` an excellent
package for reading JSON data into R. More information about `jsonlite`
can be found
[here](https://cran.r-project.org/web/packages/jsonlite/jsonlite.pdf)

# Connecting to NHL API

This section creates functions to pull data from the NHL API and uses
those functions to create data frames.

``` r
#Function to call /franchise
get_franchise <- function(){
  
  #Initializing variables and creating API call
  base_url <- 'https://records.nhl.com/site/api/' #Setting Base URL
  end <- 'franchise' #Setting end of url
  call <- paste0(base_url, end) #Pasting together
  
  #Creating get request, extracting text, and converting to R object
  get_req <- GET(call)
  text <- content(get_req, as = 'text')
  json <- fromJSON(text, flatten = TRUE)
  
  #Returning final data frame
  return(as.data.frame(json))
}
```

``` r
#Function to call /franchise-team-totals
get_franchise_totals <- function(){
  
  #Initializing variables and creating API call
  base_url <- 'https://records.nhl.com/site/api/' #Setting Base URL
  end <- 'franchise-team-totals' #Setting end of url
  call <- paste0(base_url, end) #Pasting together
  
  #Creating get request, extracting text, and converting to R object
  get_req <- GET(call)
  text <- content(get_req, as = 'text')
  json <- fromJSON(text, flatten = TRUE)
  
  #Returning final data frame
  return(as.data.frame(json))
}
```

``` r
#Function to call /franchise-season-records
get_season_records <- function(ID){
  
  if(is.numeric(ID) & ID %in% 1:38){
  
    #Initializing variables and creating API call
    base_url <- 'https://records.nhl.com/site/api/' #Setting Base URL
    end <- 'franchise-season-records?cayenneExp=franchiseId=' #Setting end of url
    call <- paste0(base_url, end, ID) #Pasting together
  
    #Creating get request, extracting text, and converting to R object
    get_req <- GET(call)
    text <- content(get_req, as = 'text')
    json <- fromJSON(text, flatten = TRUE)
  
    #Returning final data frame
    return(as.data.frame(json))
    
  } else stop('Please enter a valid franchise ID')
}
```

``` r
#Function to call /franchise-goalie-records
get_goalie_records <- function(ID){
  
  if(is.numeric(ID) & ID %in% 1:38){
  
    #Initializing variables and creating API call
    base_url <- 'https://records.nhl.com/site/api/' #Setting Base URL
    end <- 'franchise-goalie-records?cayenneExp=franchiseId=' #Setting end of url
    call <- paste0(base_url, end, ID) #Pasting together
  
    #Creating get request, extracting text, and converting to R object
    get_req <- GET(call)
    text <- content(get_req, as = 'text')
    json <- fromJSON(text, flatten = TRUE)
  
    #Returning final data frame
    return(as.data.frame(json))
    
  } else stop('Please enter a valid franchise ID')
}
```

``` r
#Function to call /franchise-skater-records
get_skater_records <- function(ID){
  
  if(is.numeric(ID) & ID %in% 1:38){
  
    #Initializing variables and creating API call
    base_url <- 'https://records.nhl.com/site/api/' #Setting Base URL
    end <- 'franchise-skater-records?cayenneExp=franchiseId=' #Setting end of url
    call <- paste0(base_url, end, ID) #Pasting together
  
    #Creating get request, extracting text, and converting to R object
    get_req <- GET(call)
    text <- content(get_req, as = 'text')
    json <- fromJSON(text, flatten = TRUE)
  
    #Returning final data frame
    return(as.data.frame(json))
    
  } else stop('Please enter a valid franchise ID')
}
```

``` r
#Creating Data Frames from API calls
franchise <- get_franchise()
franchise_totals <- get_franchise_totals()

#Initializing data frames
season_records <- goalie_records <- skater_records <- NULL

#Looping through all team IDs and combining datasets
for(ID in 1:38){
  
  season_records[[ID]] <- get_season_records(ID)
  
  goalie_records[[ID]] <- get_goalie_records(ID)
  
  skater_records[[ID]] <- get_skater_records(ID)
  
}

season_records <- bind_rows(season_records)
goalie_records <- bind_rows(goalie_records)
skater_records <- bind_rows(skater_records)

#Printing out the head of each data set
head(franchise)
```

    ##   data.id data.firstSeasonId data.lastSeasonId data.mostRecentTeamId
    ## 1       1           19171918                NA                     8
    ## 2       2           19171918          19171918                    41
    ## 3       3           19171918          19341935                    45
    ## 4       4           19191920          19241925                    37
    ## 5       5           19171918                NA                    10
    ## 6       6           19241925                NA                     6
    ##   data.teamCommonName data.teamPlaceName total
    ## 1           Canadiens           Montréal    38
    ## 2           Wanderers           Montreal    38
    ## 3              Eagles          St. Louis    38
    ## 4              Tigers           Hamilton    38
    ## 5         Maple Leafs            Toronto    38
    ## 6              Bruins             Boston    38

``` r
head(franchise_totals)
```

    ##   data.id data.activeFranchise data.firstSeasonId data.franchiseId
    ## 1       1                    1           19821983               23
    ## 2       2                    1           19821983               23
    ## 3       3                    1           19721973               22
    ## 4       4                    1           19721973               22
    ## 5       5                    1           19261927               10
    ## 6       6                    1           19261927               10
    ##   data.gameTypeId data.gamesPlayed data.goalsAgainst data.goalsFor
    ## 1               2             2937              8708          8647
    ## 2               3              257               634           697
    ## 3               2             3732             11779         11889
    ## 4               3              272               806           869
    ## 5               2             6504             19863         19864
    ## 6               3              515              1436          1400
    ##   data.homeLosses data.homeOvertimeLosses data.homeTies data.homeWins
    ## 1             507                      82            96           783
    ## 2              53                       0            NA            74
    ## 3             674                      81           170           942
    ## 4              46                       1            NA            84
    ## 5            1132                      73           448          1600
    ## 6             103                       0             1           137
    ##   data.lastSeasonId data.losses data.overtimeLosses data.penaltyMinutes
    ## 1                NA        1181                 162               44397
    ## 2                NA         120                   0                4266
    ## 3                NA        1570                 159               57422
    ## 4                NA         124                   0                5356
    ## 5                NA        2693                 147               85564
    ## 6                NA         263                   0                8132
    ##   data.pointPctg data.points data.roadLosses data.roadOvertimeLosses
    ## 1         0.5330        3131             674                      80
    ## 2         0.0039           2              67                       0
    ## 3         0.5115        3818             896                      78
    ## 4         0.0147           8              78                       0
    ## 5         0.5125        6667            1561                      74
    ## 6         0.0000           0             160                       0
    ##   data.roadTies data.roadWins data.shootoutLosses data.shootoutWins
    ## 1           123           592                  79                78
    ## 2            NA            63                   0                 0
    ## 3           177           714                  67                82
    ## 4            NA            64                   0                 0
    ## 5           360          1256                  66                78
    ## 6             7           107                   0                 0
    ##   data.shutouts data.teamId      data.teamName data.ties data.triCode data.wins
    ## 1           193           1  New Jersey Devils       219          NJD      1375
    ## 2            25           1  New Jersey Devils        NA          NJD       137
    ## 3           167           2 New York Islanders       347          NYI      1656
    ## 4             9           2 New York Islanders        NA          NYI       148
    ## 5           403           3   New York Rangers       808          NYR      2856
    ## 6            44           3   New York Rangers         8          NYR       244
    ##   total
    ## 1   104
    ## 2   104
    ## 3   104
    ## 4   104
    ## 5   104
    ## 6   104

``` r
head(season_records)
```

    ##   data.id data.fewestGoals data.fewestGoalsAgainst
    ## 1       8              155                     131
    ## 2      41               NA                      NA
    ## 3      36               NA                      NA
    ## 4      37               NA                      NA
    ## 5      10              147                     131
    ## 6       6              147                     172
    ##   data.fewestGoalsAgainstSeasons data.fewestGoalsSeasons data.fewestLosses
    ## 1                   1955-56 (70)            1952-53 (70)                 8
    ## 2                           <NA>                    <NA>                NA
    ## 3                           <NA>                    <NA>                NA
    ## 4                           <NA>                    <NA>                NA
    ## 5                   1953-54 (70)            1954-55 (70)                16
    ## 6                   1952-53 (70)            1955-56 (70)                13
    ##   data.fewestLossesSeasons data.fewestPoints data.fewestPointsSeasons
    ## 1             1976-77 (80)                65             1950-51 (70)
    ## 2                     <NA>                NA                     <NA>
    ## 3                     <NA>                NA                     <NA>
    ## 4                     <NA>                NA                     <NA>
    ## 5             1950-51 (70)                48             1984-85 (80)
    ## 6             1971-72 (78)                38             1961-62 (70)
    ##   data.fewestTies data.fewestTiesSeasons data.fewestWins
    ## 1               5           1983-84 (80)              25
    ## 2              NA                   <NA>              NA
    ## 3              NA                   <NA>              NA
    ## 4              NA                   <NA>              NA
    ## 5               4           1989-90 (80)              20
    ## 6               5           1972-73 (78)              14
    ##       data.fewestWinsSeasons data.franchiseId  data.franchiseName
    ## 1               1950-51 (70)                1  Montréal Canadiens
    ## 2                       <NA>                2  Montreal Wanderers
    ## 3                       <NA>                3    St. Louis Eagles
    ## 4                       <NA>                4     Hamilton Tigers
    ## 5 1981-82 (80), 1984-85 (80)                5 Toronto Maple Leafs
    ## 6               1962-63 (70)                6       Boston Bruins
    ##   data.homeLossStreak                             data.homeLossStreakDates
    ## 1                   7 Dec 16 1939 - Jan 18 1940, Oct 28 2000 - Nov 25 2000
    ## 2                   2                            Dec 22 1917 - Dec 26 1917
    ## 3                   5                            Jan 08 1931 - Feb 03 1931
    ## 4                   5                            Jan 03 1921 - Jan 29 1921
    ## 5                   7                            Nov 11 1984 - Dec 05 1984
    ## 6                  11                            Dec 08 1924 - Feb 17 1925
    ##   data.homePointStreak                            data.homePointStreakDates
    ## 1                   34                            Nov 01 1976 - Apr 02 1977
    ## 2                   NA                                                 <NA>
    ## 3                   12                            Dec 20 1922 - Feb 28 1923
    ## 4                    5                            Nov 29 1924 - Jan 01 1925
    ## 5                   18 Nov 28 1933 - Mar 10 1934, Oct 31 1953 - Jan 23 1954
    ## 6                   27                            Nov 22 1970 - Mar 20 1971
    ##   data.homeWinStreak                              data.homeWinStreakDates
    ## 1                 13 Nov 02 1943 - Jan 08 1944, Jan 30 1977 - Mar 26 1977
    ## 2                 NA                                                 <NA>
    ## 3                 10 Dec 30 1922 - Feb 28 1923, Nov 28 1925 - Jan 28 1926
    ## 4                  5                            Nov 29 1924 - Jan 01 1925
    ## 5                 13                            Jan 31 2018 - Mar 24 2018
    ## 6                 20                            Dec 03 1929 - Mar 18 1930
    ##   data.homeWinlessStreak                          data.homeWinlessStreakDates
    ## 1                     15                            Dec 16 1939 - Mar 07 1940
    ## 2                      2                            Dec 22 1917 - Dec 26 1917
    ## 3                      9                            Dec 11 1930 - Feb 03 1931
    ## 4                      5                            Jan 03 1921 - Jan 29 1921
    ## 5                     11 Dec 19 1987 - Jan 25 1988, Feb 11 2012 - Mar 29 2012
    ## 6                     11                            Dec 08 1924 - Feb 17 1925
    ##   data.lossStreak                                 data.lossStreakDates
    ## 1              12                            Feb 13 1926 - Mar 13 1926
    ## 2               5                            Dec 22 1917 - Jan 05 1918
    ## 3               9                            Dec 06 1930 - Jan 01 1931
    ## 4               7 Jan 10 1920 - Jan 28 1920, Jan 20 1923 - Feb 10 1923
    ## 5              10                            Jan 15 1967 - Feb 08 1967
    ## 6              11                            Dec 03 1924 - Jan 05 1925
    ##   data.mostGameGoals
    ## 1                 16
    ## 2                 10
    ## 3                 12
    ## 4                 10
    ## 5                 14
    ## 6                 14
    ##                                                                    data.mostGameGoalsDates
    ## 1                                                             Mar 03 1920 - MTL 16 @ QBD 3
    ## 2                                                             Dec 19 1917 - TAN 9 @ MWN 10
    ## 3                               Jan 21 1920 - QBD 1 @ SEN 12, Mar 07 1921 - HAM 5 @ SEN 12
    ## 4 Jan 31 1920 - TSP 6 @ QBD 10, Mar 10 1920 - SEN 4 @ QBD 10, Dec 05 1924 - HAM 10 @ TSP 3
    ## 5                                                             Mar 16 1957 - NYR 1 @ TOR 14
    ## 6                                                             Jan 21 1945 - NYR 3 @ BOS 14
    ##   data.mostGoals data.mostGoalsAgainst data.mostGoalsAgainstSeasons
    ## 1            387                   295                 1983-84 (80)
    ## 2             17                    37                 1917-18 (22)
    ## 3            138                   144                 1934-35 (48)
    ## 4             92                   177                 1919-20 (24)
    ## 5            337                   387                 1983-84 (80)
    ## 6            399                   306                 1961-62 (70)
    ##   data.mostGoalsSeasons data.mostLosses
    ## 1          1976-77 (80)              40
    ## 2          1917-18 (22)               5
    ## 3          1929-30 (44)              31
    ## 4          1920-21 (24)              20
    ## 5          1989-90 (80)              52
    ## 6          1970-71 (78)              47
    ##                     data.mostLossesSeasons data.mostPenaltyMinutes
    ## 1 1983-84 (80), 2000-01 (82), 2017-18 (82)                    1847
    ## 2                             1917-18 (22)                      27
    ## 3                             1934-35 (48)                     619
    ## 4                             1919-20 (24)                     339
    ## 5                             1984-85 (80)                    2419
    ## 6               1961-62 (70), 1996-97 (82)                    2443
    ##   data.mostPenaltyMinutesSeasons data.mostPoints data.mostPointsSeasons
    ## 1                   1995-96 (82)             132           1976-77 (80)
    ## 2                   1917-18 (22)               2           1917-18 (22)
    ## 3                   1926-27 (44)              64           1926-27 (44)
    ## 4                   1924-25 (30)              39           1924-25 (30)
    ## 5                   1989-90 (80)             105           2017-18 (82)
    ## 6                   1987-88 (80)             121           1970-71 (78)
    ##   data.mostShutouts   data.mostShutoutsSeasons data.mostTies
    ## 1                22               1928-29 (44)            23
    ## 2                 0               1917-18 (22)             0
    ## 3                15 1925-26 (36), 1927-28 (44)            13
    ## 4                 6               1924-25 (30)             1
    ## 5                13               1953-54 (70)            22
    ## 6                15               1927-28 (44)            21
    ##   data.mostTiesSeasons data.mostWins data.mostWinsSeasons data.pointStreak
    ## 1         1962-63 (70)            60         1976-77 (80)               28
    ## 2         1917-18 (22)             1         1917-18 (22)               NA
    ## 3         1928-29 (44)            30         1926-27 (44)               12
    ## 4         1924-25 (30)            19         1924-25 (30)                7
    ## 5         1954-55 (70)            49         2017-18 (82)               16
    ## 6         1954-55 (70)            57         1970-71 (78)               23
    ##       data.pointStreakDates data.roadLossStreak  data.roadLossStreakDates
    ## 1 Dec 18 1977 - Feb 23 1978                  10 Jan 16 1926 - Mar 13 1926
    ## 2                      <NA>                   3 Dec 29 1917 - Jan 05 1918
    ## 3 Jan 24 1928 - Feb 25 1928                   7 Nov 17 1934 - Dec 09 1934
    ## 4 Jan 12 1925 - Feb 04 1925                  12 Dec 27 1919 - Mar 08 1920
    ## 5 Nov 22 2003 - Dec 26 2003                  11 Feb 20 1988 - Apr 01 1988
    ## 6 Dec 22 1940 - Feb 23 1941                  14 Dec 27 1964 - Feb 21 1965
    ##   data.roadPointStreak data.roadPointStreakDates data.roadWinStreak
    ## 1                   23 Nov 27 1974 - Mar 12 1975                  8
    ## 2                   NA                      <NA>                 NA
    ## 3                    8 Nov 18 1926 - Dec 28 1926                  5
    ## 4                    3 Jan 12 1925 - Feb 04 1925                  3
    ## 5                   11 Dec 03 2016 - Jan 25 2017                  7
    ## 6                   16 Jan 11 2014 - Mar 30 2014                  9
    ##                                                           data.roadWinStreakDates
    ## 1                            Dec 18 1977 - Jan 18 1978, Jan 21 1982 - Feb 21 1982
    ## 2                                                                            <NA>
    ## 3                                                       Feb 04 1920 - Mar 06 1920
    ## 4                                                       Jan 12 1925 - Feb 04 1925
    ## 5 Nov 14 1940 - Dec 15 1940, Dec 04 1960 - Jan 05 1961, Jan 29 2003 - Feb 22 2003
    ## 6                                                       Mar 02 2014 - Mar 30 2014
    ##   data.roadWinlessStreak
    ## 1                     12
    ## 2                      3
    ## 3                     17
    ## 4                     12
    ## 5                     18
    ## 6                     14
    ##                                                       data.roadWinlessStreakDates
    ## 1                            Nov 26 1933 - Jan 28 1934, Oct 20 1951 - Dec 13 1951
    ## 2                                                       Dec 29 1917 - Jan 05 1918
    ## 3                                                       Dec 15 1932 - Mar 18 1933
    ## 4                                                       Dec 27 1919 - Mar 08 1920
    ## 5                                                       Oct 06 1982 - Jan 05 1983
    ## 6 Oct 12 1963 - Dec 14 1963, Dec 27 1964 - Feb 21 1965, Nov 09 1966 - Jan 07 1967
    ##   data.winStreak       data.winStreakDates data.winlessStreak
    ## 1             12 Jan 06 1968 - Feb 03 1968                  8
    ## 2             NA                      <NA>                 NA
    ## 3              9 Feb 11 1920 - Mar 08 1920                 NA
    ## 4              7 Jan 12 1925 - Feb 04 1925                 NA
    ## 5             10 Oct 07 1993 - Oct 28 1993                  6
    ## 6             14 Dec 03 1929 - Jan 09 1930                  5
    ##                                data.winlessStreakDates total
    ## 1 Nov 16 2019 - Dec 01 2019, Dec 28 2019 - Jan 09 2020     1
    ## 2                                                 <NA>     1
    ## 3                                                 <NA>     1
    ## 4                                                 <NA>     1
    ## 5                            Nov 09 2019 - Nov 19 2019     1
    ## 6                            Dec 05 2019 - Dec 12 2019     1

``` r
head(goalie_records)
```

    ##   data.id data.activePlayer data.firstName data.franchiseId data.franchiseName
    ## 1     261             FALSE        Patrick                1 Montréal Canadiens
    ## 2     294              TRUE          Carey                1 Montréal Canadiens
    ## 3     296             FALSE        Jacques                1 Montréal Canadiens
    ## 4     327             FALSE         George                1 Montréal Canadiens
    ## 5     414             FALSE       Stephane                1 Montréal Canadiens
    ## 6     437             FALSE           Jeff                1 Montréal Canadiens
    ##   data.gameTypeId data.gamesPlayed data.lastName data.losses
    ## 1               2              551           Roy         175
    ## 2               2              682         Price         250
    ## 3               2              556        Plante         133
    ## 4               2              318    Hainsworth          96
    ## 5               2                2         Fiset           1
    ## 6               2              161       Hackett          68
    ##                       data.mostGoalsAgainstDates data.mostGoalsAgainstOneGame
    ## 1                                     1995-12-02                            9
    ## 2                         2019-03-08, 2011-02-09                            8
    ## 3             1960-10-25, 1960-01-03, 1959-10-11                            8
    ## 4                                     1933-02-21                           10
    ## 5                                     2002-04-12                            5
    ## 6 2001-12-29, 2000-04-02, 2000-01-04, 1999-03-06                            6
    ##   data.mostSavesDates data.mostSavesOneGame data.mostShotsAgainstDates
    ## 1          1991-03-06                    49                 1995-02-27
    ## 2          2009-11-14                    53                 2009-11-14
    ## 3          1955-11-13                    52                 1955-11-13
    ## 4                <NA>                    NA                       <NA>
    ## 5          2002-04-12                    36                 2002-04-12
    ## 6          2000-12-16                    49                 2000-12-16
    ##   data.mostShotsAgainstOneGame data.mostShutoutsOneSeason
    ## 1                           53                          7
    ## 2                           55                          9
    ## 3                           52                          9
    ## 4                           NA                         22
    ## 5                           41                          0
    ## 6                           53                          5
    ##     data.mostShutoutsSeasonIds data.mostWinsOneSeason data.mostWinsSeasonIds
    ## 1                     19931994                     36               19911992
    ## 2                     20142015                     44               20142015
    ## 3 19561957, 19571958, 19581959                     42     19551956, 19611962
    ## 4                     19281929                     28               19261927
    ## 5                     20012002                      0               20012002
    ## 6                     19981999                     24               19981999
    ##   data.overtimeLosses data.playerId data.positionCode data.rookieGamesPlayed
    ## 1                  NA       8451033                 G                     47
    ## 2                  74       8471679                 G                     41
    ## 3                  NA       8450066                 G                     52
    ## 4                  NA       8449987                 G                     44
    ## 5                  NA       8446831                 G                     NA
    ## 6                  NA       8447449                 G                     NA
    ##   data.rookieShutouts data.rookieWins data.seasons data.shutouts data.ties
    ## 1                   1              23           12            29        66
    ## 2                   3              24           13            48         0
    ## 3                   5              33           11            58       107
    ## 4                  14              28            8            75        54
    ## 5                  NA              NA            1             0         0
    ## 6                  NA              NA            5             8        22
    ##   data.wins total
    ## 1       289    37
    ## 2       348    37
    ## 3       314    37
    ## 4       167    37
    ## 5         0    37
    ## 6        63    37

``` r
head(skater_records)
```

    ##   data.id data.activePlayer data.assists data.firstName data.franchiseId
    ## 1   16891             FALSE          712           Jean                1
    ## 2   16911             FALSE          688          Henri                1
    ## 3   16990             FALSE          422        Maurice                1
    ## 4   17000             FALSE          728            Guy                1
    ## 5   17025             FALSE           87          Chris                1
    ## 6   17054             FALSE          368          Steve                1
    ##   data.franchiseName data.gameTypeId data.gamesPlayed data.goals data.lastName
    ## 1 Montréal Canadiens               2             1125        507      Beliveau
    ## 2 Montréal Canadiens               2             1258        358       Richard
    ## 3 Montréal Canadiens               2              978        544       Richard
    ## 4 Montréal Canadiens               2              961        518       Lafleur
    ## 5 Montréal Canadiens               2              523         88         Nilan
    ## 6 Montréal Canadiens               2              871        408         Shutt
    ##                                                                        data.mostAssistsGameDates
    ## 1                                     1955-02-19, 1956-12-01, 1962-11-24, 1965-11-20, 1967-12-28
    ## 2                                                                         1963-01-12, 1964-02-01
    ## 3                                                                                     1954-01-09
    ## 4             1977-03-10, 1977-03-12, 1978-02-23, 1979-04-07, 1980-11-12, 1980-12-27, 1981-11-21
    ## 5 1981-12-12, 1983-01-06, 1983-11-23, 1985-02-24, 1986-02-01, 1986-10-25, 1986-10-30, 1987-04-05
    ## 6                                                 1978-12-02, 1979-02-04, 1979-10-25, 1980-12-03
    ##   data.mostAssistsOneGame data.mostAssistsOneSeason data.mostAssistsSeasonIds
    ## 1                       4                        58                  19601961
    ## 2                       5                        52                  19571958
    ## 3                       5                        36                  19541955
    ## 4                       4                        80                  19761977
    ## 5                       2                        16        19841985, 19861987
    ## 6                       4                        45                  19761977
    ##                                                                                                  data.mostGoalsGameDates
    ## 1                                                                                     1955-11-05, 1959-03-07, 1969-02-11
    ## 2                                                             1957-10-17, 1959-03-14, 1961-03-11, 1965-02-24, 1967-03-19
    ## 3                                                                                                             1944-12-28
    ## 4                                                                                                             1975-01-26
    ## 5 1980-11-22, 1981-11-11, 1983-11-09, 1983-12-03, 1984-02-23, 1985-02-07, 1985-02-23, 1985-12-27, 1986-03-04, 1986-03-08
    ## 6                                                                                                 1978-02-23, 1980-01-24
    ##   data.mostGoalsOneGame data.mostGoalsOneSeason data.mostGoalsSeasonIds
    ## 1                     4                      47                19551956
    ## 2                     3                      30                19591960
    ## 3                     5                      50                19441945
    ## 4                     4                      60                19771978
    ## 5                     2                      21                19841985
    ## 6                     4                      60                19761977
    ##   data.mostPenaltyMinutesOneSeason data.mostPenaltyMinutesSeasonIds
    ## 1                              143                         19551956
    ## 2                               91                         19601961
    ## 3                              125                         19541955
    ## 4                               51                         19721973
    ## 5                              358                         19841985
    ## 6                               51                         19801981
    ##                                                 data.mostPointsGameDates
    ## 1                                                             1959-03-07
    ## 2                                                             1957-10-17
    ## 3                                                             1944-12-28
    ## 4                                     1975-01-04, 1978-02-28, 1979-04-07
    ## 5                                                 1983-12-03, 1985-02-23
    ## 6 1975-12-06, 1978-12-02, 1979-02-04, 1979-10-25, 1980-01-24, 1981-10-27
    ##   data.mostPointsOneGame data.mostPointsOneSeason data.mostPointsSeasonIds
    ## 1                      7                       91                 19581959
    ## 2                      6                       80                 19571958
    ## 3                      8                       74                 19541955
    ## 4                      6                      136                 19761977
    ## 5                      3                       37                 19841985
    ## 6                      5                      105                 19761977
    ##   data.penaltyMinutes data.playerId data.points data.positionCode
    ## 1                1033       8445408        1219                 C
    ## 2                 932       8448320        1046                 C
    ## 3                1287       8448321         966                 R
    ## 4                 381       8448624        1246                 R
    ## 5                2248       8449883         175                 R
    ## 6                 400       8451354         776                 L
    ##   data.rookiePoints data.seasons total
    ## 1                34           20   788
    ## 2                40           20   788
    ## 3                11           18   788
    ## 4                64           14   788
    ## 5                15           10   788
    ## 6                16           13   788

# EDA on NHL Data

``` r
#Creating table of position code and most goals scored in one game
knitr::kable(table(skater_records$data.positionCode, skater_records$data.mostGoalsOneGame),
             caption = 'Table of Most Goals Scored in One Game by Each Position')
```

|   |    0 |    1 |    2 |   3 |   4 |  5 | 6 | 7 |
| - | ---: | ---: | ---: | --: | --: | -: | -: | -: |
| C |  813 | 1514 | 1190 | 530 | 101 | 16 | 5 | 1 |
| D | 1790 | 2830 |  861 | 104 |   9 |  1 | 0 | 0 |
| L |  804 | 1334 |  962 | 481 |  86 |  4 | 1 | 0 |
| R |  702 | 1181 |  966 | 480 |  87 | 18 | 0 | 0 |

Table of Most Goals Scored in One Game by Each Position

This table shows that most defenders only ever score 1 goal in a game.
We can also see that centers are more likely than other positions to
have games where they score a lot of goals.

``` r
#Filtering skaters to active skaters only
active <- filter(skater_records, data.activePlayer == TRUE)

#Creating table of position code by team
knitr::kable(table(active$data.franchiseName, active$data.positionCode),
             caption = 'Table of Position Count by Team')
```

|                       |  C |  D |  L |  R |
| --------------------- | -: | -: | -: | -: |
| Anaheim Ducks         | 23 | 21 | 15 | 15 |
| Arizona Coyotes       | 21 | 21 |  8 |  8 |
| Boston Bruins         | 28 | 22 | 13 | 11 |
| Buffalo Sabres        | 22 | 25 | 17 |  9 |
| Calgary Flames        | 17 | 22 | 12 |  6 |
| Carolina Hurricanes   | 24 | 18 | 12 |  6 |
| Chicago Blackhawks    | 20 | 25 | 13 | 11 |
| Colorado Avalanche    | 22 | 22 | 11 |  8 |
| Columbus Blue Jackets | 30 | 21 | 11 |  8 |
| Dallas Stars          | 17 | 25 | 10 | 10 |
| Detroit Red Wings     | 13 | 19 | 14 | 11 |
| Edmonton Oilers       | 19 | 26 | 12 |  8 |
| Florida Panthers      | 22 | 20 |  5 | 12 |
| Los Angeles Kings     | 20 | 21 | 11 |  9 |
| Minnesota Wild        | 21 | 19 |  7 |  9 |
| Montréal Canadiens    | 17 | 27 | 12 |  8 |
| Nashville Predators   | 19 | 20 |  9 | 11 |
| New Jersey Devils     | 20 | 19 | 13 | 11 |
| New York Islanders    | 15 | 19 | 10 | 10 |
| New York Rangers      | 22 | 22 | 11 |  9 |
| Ottawa Senators       | 28 | 19 | 12 | 15 |
| Philadelphia Flyers   | 22 | 17 |  8 | 11 |
| Pittsburgh Penguins   | 29 | 26 | 12 | 10 |
| San Jose Sharks       | 21 | 21 |  5 |  6 |
| St. Louis Blues       | 14 | 22 | 10 |  9 |
| Tampa Bay Lightning   | 22 | 15 |  6 |  8 |
| Toronto Maple Leafs   | 20 | 25 | 14 | 10 |
| Vancouver Canucks     | 22 | 23 |  9 |  7 |
| Vegas Golden Knights  | 14 | 14 | 10 |  5 |
| Washington Capitals   | 12 | 19 | 12 |  9 |
| Winnipeg Jets         | 16 | 21 | 14 |  8 |

Table of Position Count by Team

This table gives a roster breakdown of each team. It shows how many
players of each position, excluding goalie, each team has on their
current roster.

``` r
#Filtering  skater records down to active Lightning forwards with min 300 games
active <- skater_records %>% 
  filter(data.activePlayer == TRUE, 
         data.gamesPlayed >= 300, 
         data.positionCode != 'D',
         data.franchiseId == 31) %>%
  mutate(fullName = paste(data.firstName, data.lastName, sep = ' '), #Merging first and last name
         goalsPerGame = data.goals / data.gamesPlayed)   #Creating goals per game stat

#Creating plot object and bar chart of goals per game
g <- ggplot(active, aes(x = fullName, y = goalsPerGame))
g + geom_bar(aes(fill = fullName), stat = 'identity', show.legend = FALSE) + 
  coord_flip() +
  labs(x = 'Player', y = 'Goals Per Game', 
       title = 'Goals Per Game of Active Lightning Forwards (Min. 300 Games)')
```

![](README_files/figure-gfm/bar%20chart-1.png)<!-- -->

I created goals per game statistic to get an understanding of which
players are the most efficient scorers. Steven Stamkos and Nikita
Kucherov stand out as the most efficient goal scorers among active
Lightning players. Stamkos, remarkably, scores a goal every two games.

``` r
#Creating box-plot of log penalty minutes by position
#Using log scale because distribution is very skewed
h <- ggplot(skater_records, aes(x = data.positionCode, y = log(data.penaltyMinutes)))
h + geom_boxplot(aes(fill = data.positionCode), show.legend = FALSE) +
  scale_x_discrete(labels = c('Center', 'Defense', 'Left Wing', 'Right Wing')) +
  labs(x = 'Position', y = 'Log of Penalty Minutes', 
       title = 'Boxplots of Log Penalty Minutes by Position')
```

![](README_files/figure-gfm/boxplot-1.png)<!-- -->

This plot displays boxplots of log career penalty minutes by position. I
used log penalty minutes because the distribution of penalty minutes is
so heavily skewed. As you can see, it appears defenders generally
accumulate slightly more penalty minutes in their careers than other
positions. Centers appear to accumulate slightly less penalty minutes.
The player with the most penalty minutes in his career was a Right Wing.

``` r
#Filtering to include current teams only and creating per game stats
current <- franchise_totals %>%
  filter(is.na(data.lastSeasonId)) %>%
  mutate(penaltyMinutesPerGame = data.penaltyMinutes / data.gamesPlayed,
         winRatio = data.wins / data.gamesPlayed)

#Changing game type into a factor
current$data.gameTypeId <- as.factor(current$data.gameTypeId)
         
#Plotting scatterplot and fitted lines
l <- ggplot(current, aes(x = penaltyMinutesPerGame, y = winRatio))
l + geom_point(aes(color = data.gameTypeId)) +
  geom_smooth(aes(group = data.gameTypeId, color = data.gameTypeId), method = 'lm', se = FALSE) +
  scale_color_discrete(name = 'Game Type', 
                      labels = c('Regular Season', 'Playoff')) +
  labs(x = 'Penalty Minutes per Game', y = 'Franchise Win/Loss Ratio',
       title = 'Franchise Win/Loss Ratio Vs Penalty Minutes per Game')
```

![](README_files/figure-gfm/scatterplot-1.png)<!-- -->

From this plot, we can see that in the regular season, franchises with
more penalty minutes per game tend to have a lower overall win-loss
ratio. The opposite seems tends to be the case in the playoffs, although
the correlation is quite low in both cases.
