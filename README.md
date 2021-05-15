Stephen Curry's Impact on the Game of Basketball

by Joseph Schoenbaum

Introduction: It's a general agreement in the basketball community that Stephen Curry is the greatest shooter of all time. Just this week, Damian Lillard, who many consider the current 2nd best shooter in the world behind Curry, stated, “Steph is right behind Magic (highly regarded as the best point guard to ever play an NBA game) in the way he has changed the game and the excitement with his style of play”. That is incredible respect from one of Curry's main rivals in the NBA. In mainstream media, it seems as if everyone agrees Curry is a great player, but people don't seem to give him the credit for how much he has impacted the NBA game. An NBA game played in 2010 looks like a completely different sport compared to an NBA game played today. Sure, you can spit out Curry's points per game average, his NBA all-time free throw percentage (90.7%), or his 3 championships, but those are elementary statistics that only show a sliver of Curry's impact. I want to help people better understand the game of basketball, and I'm going to do my best to portray why I believe Stephen Curry is one of the most influential players to steph (haha see what I did there) foot onto an NBA court.

    #We will start by importing the libraries necessary to do the analysis
    import requests as req
    from bs4 import BeautifulSoup
    import pandas as pd
    import numpy as np
    import matplotlib.pyplot as plt
    import statsmodels.formula.api as sm
    from sklearn import linear_model
    
Source: The website I will be gathering data from is called https://www.basketball-reference.com. The site has statistics about every NBA season dating back to 1946.

First we will take a look at Stephen Curry's individual seasons throughout his career from 2010-2021.

This is done by web scraping the specific html page. We parse through the html with our libraries to find the tables we want. Then we will parse through the table for the specific data we want.

To start my analysis I want to show Stephen Curry's career relative to other players (mainly WRT 3-point statistics). I am getting my data from the source stated above. The first tables I want are season statisitcs such as player name, season year, age, games played, 3-pointers made, 3-pointers attempted, 3-point percentage, and points. I will get the data for these for each season Curry has been in the NBA (2010-2021). The tables I'm retrieving from my source has a lot of data. I am removing all rows except for the ones stated above.

Next we want the most important data points to show how incredible Curry's numbers are. We are going to filter out every player who doesn't average 2 made 3-pointers per game and also any player who has not played over a quarter of the season (due to injury or other circumstances). This will leave us with the best NBA shooters in the world to compare to Curry.

In the cell below we collected our 12 tables we made for each year and placed them into the years_data list. Then I sorted the list by 3-pointers made. This will give us 10 of the best shooters in the world in our table to allow us to see our data points before graphing them for comparison.

Then I graphed each data point with 3P% as the x value and 3PM as the y value. while in the earlier years I didn't see a definitively linear relationship, I thought it would be interesting to see the regression line because in recent years there does seem to be a more serious linear relationship.

After all that, I want to take all seasons from 2010 to 2021 and combine them into a table. The regression line for this is very linear. This graph of all data points can show the totality of Curry's dominance.

Note: Steph Curry has played over 20 games in each season except 2020 (He will NOT be on the 2020 graph).

    #going through all nba season that Stephen Curry has played
    #putting all players who made more than 2 threes per game in that season and played more than a quarter of the season (20 of 82 games (2011-2012 was a shortened season so        there are less data points)) on a graph (3P% vs. 3PM) with the regresion line for the average of those players

    #year for url and place to store table for that year
    year = 2010
    years_data = []

    #loop through each url to get data
    for i in range(12):

    #web scraping and placing data in table
    season = req.get("https://www.basketball-reference.com/leagues/NBA_" + str(year) + "_per_game.html")
    season_tables = pd.read_html(season.text, flavor='bs4')
    curr_table = season_tables[0]
    three_table = pd.DataFrame(columns=["Rk", "Player", "Season", "Age", "G", "3P", "3PA", "3P%", "PTS"])

    #remove edge case
    curr_table = curr_table.replace("3P", "0")
    curr_table = curr_table.replace("3PA", "0")
    curr_table = curr_table.replace("3P%", "0")
    curr_table = curr_table.replace("PTS", "0")
    curr_table = curr_table.replace("G", "0")

    #create a better formatted table and add the data to that table
    for x, y in curr_table.iterrows():
        float_three = float(y['3P'])
        float_3percent = float(y['3P%'])
        float_3attempt = float(y['3PA'])
        float_points = float(y['PTS'])
        games = int(y['G'])
        if (float_three >= 2 and games >= 21):
            three_table.loc[len(three_table.index), 
                            ["Rk", "Player", "Season", "Age", "G", "3P", "3PA", "3P%", "PTS"]] = [y["Rk"], y['Player'], year, 
                                                                                             y['Age'], games, float_three, float_3attempt, 
                                                                                             float_3percent, float_points]
    
    #regression line parameters
    three_table = three_table.sort_values(by='3P', ascending=False)
    list_3percent = [ [i] for i in three_table['3P%'].values]
    
    #print table
    print(three_table.head(10))
    print('\n')
    
    #reg line
    lm = linear_model.LinearRegression()
    eq = lm.fit(list_3percent, three_table['3P'].values)
    
    #plot data
    plt.figure(figsize=(15, 10))
    plt.scatter(three_table['3P%'], three_table['3P'], label=x)
    plt.plot(three_table['3P%'], lm.predict(list_3percent), color='red')
    plt.title(str(year) + ": 3-point field goal percentage vs. 3-point field goals made")
    plt.xlabel("3P%")
    plt.ylabel("3PM")
    
    #add table to list and go to next year
    years_data.append(three_table)
    year = year + 1
    
    #show specific popluar players' data points to compare to Curry through the years
    for i in range(three_table.shape[0]):
        if(three_table['Player'].tolist()[i] == "Stephen Curry" or three_table['Player'].tolist()[i] == "James Harden" or 
           three_table['Player'].tolist()[i] == "Damian Lillard" or three_table['Player'].tolist()[i] == "J.J. Redick" or 
           three_table['Player'].tolist()[i] == "Ray Allen" or three_table['Player'].tolist()[i] == "Klay Thompson" or 
           three_table['Player'].tolist()[i] == "Kevin Durant" or three_table['Player'].tolist()[i] == "Jason Kidd" or 
           three_table['Player'].tolist()[i] == "Paul George" or three_table['Player'].tolist()[i] == "Buddy Hield"):
            plt.annotate(three_table['Player'].tolist()[i], (three_table['3P%'].tolist()[i], three_table['3P'].tolist()[i]))
    plt.tight_layout()
    
     Rk            Player Season Age   G   3P  3PA    3P%   PTS
2    62      Aaron Brooks   2010  25  82  2.5  6.4  0.398  19.6
7   165     Danny Granger   2010  26  62  2.5  7.1  0.361  24.1
6   145  Danilo Gallinari   2010  21  81  2.3    6  0.381  15.1
9   257     Rashard Lewis   2010  30  72  2.3  5.9  0.397  14.1
14  431       Mo Williams   2010  27  69  2.3  5.4  0.429  15.8
1    45  Chauncey Billups   2010  33  73  2.2  5.6  0.386  19.5
8   245       Jason Kidd*   2010  36  80  2.2  5.2  0.425  10.3
3    96    Jamal Crawford   2010  29  79  2.1  5.4  0.382    18
4   100     Stephen Curry   2010  21  80  2.1  4.8  0.437  17.5
5   142     Channing Frye   2010  26  81  2.1  4.8  0.439  11.2


     Rk            Player Season Age   G   3P  3PA    3P%   PTS
14  351  Jason Richardson   2011  30  25  2.5  5.9  0.419  19.3
18  448     Dorell Wright   2011  25  82  2.4  6.3  0.376  16.4
13  351  Jason Richardson   2011  30  80  2.4    6  0.395  15.6
15  351  Jason Richardson   2011  30  55  2.3    6  0.384  13.9
3    19    Gilbert Arenas   2011  29  21  2.2  6.9  0.324  17.3
12  276      Kevin Martin   2011  27  80  2.2  5.7  0.383  23.5
10  153     Channing Frye   2011  27  77  2.2  5.7   0.39  12.7
1    16     Ryan Anderson   2011  22  64  2.1  5.3  0.393  10.6
0     9        Ray Allen*   2011  35  80  2.1  4.7  0.444  16.5
6    46  Chauncey Billups   2011  34  51  2.1  4.7  0.441  16.5


     Rk           Player Season Age   G   3P  3PA    3P%   PTS
1    16    Ryan Anderson   2012  23  61  2.7  6.9  0.393  16.1
11  332      Steve Novak   2012  28  54  2.5  5.2  0.472   8.8
0     8       Ray Allen*   2012  36  46  2.3  5.1  0.453  14.2
12  412      Jason Terry   2012  34  63  2.2  5.8  0.378  15.1
2   102    Stephen Curry   2012  23  26  2.1  4.7  0.455  14.7
8   250      Joe Johnson   2012  30  60  2.1  5.4  0.388  18.8
13  424  Marcus Thornton   2012  24  51  2.1  6.1  0.345  18.7
14  460   Deron Williams   2012  27  55  2.1  6.2  0.336    21
3   126     Kevin Durant   2012  23  66    2  5.2  0.387    28
4   153       Randy Foye   2012  28  65    2  5.1  0.386    11


     Rk           Player Season Age   G   3P  3PA    3P%   PTS
5    97    Stephen Curry   2013  24  78  3.5  7.7  0.453  22.9
0    15    Ryan Anderson   2013  24  81  2.6  6.9  0.382  16.2
21  416    Klay Thompson   2013  22  82  2.6  6.4  0.401  16.6
13  249      Kyle Korver   2013  31  74  2.6  5.6  0.457  10.9
6   105   Carlos Delfino   2013  30  67  2.4  6.3  0.375  10.6
16  282  Wesley Matthews   2013  26  69  2.4  6.2  0.398  14.8
1    16  Carmelo Anthony   2013  28  67  2.3  6.2  0.379  28.7
2    34    Nicolas Batum   2013  24  73  2.3  6.1  0.372  14.3
20  354      J.J. Redick   2013  28  50  2.3    6   0.39  15.1
14  264   Damian Lillard   2013  22  82  2.3  6.1  0.368    19


     Rk           Player Season Age   G   3P  3PA    3P%   PTS
8   111    Stephen Curry   2014  25  78  3.3  7.9  0.424    24
0    16    Ryan Anderson   2014  25  22    3  7.5  0.409  19.8
26  430    Klay Thompson   2014  23  81  2.8  6.6  0.417  18.4
17  273   Damian Lillard   2014  23  82  2.7  6.8  0.394  20.7
25  402       J.R. Smith   2014  28  74  2.6  6.5  0.394  14.5
16  255      Kyle Korver   2014  32  71  2.6  5.5  0.472    12
13  177     Gerald Green   2014  28  82  2.5  6.2    0.4  15.8
20  289  Wesley Matthews   2014  27  82  2.5  6.2  0.393  16.4
18  278       Kevin Love   2014  25  77  2.5  6.6  0.376  26.1
19  279       Kyle Lowry   2014  27  79  2.4  6.3   0.38  17.9


     Rk            Player Season Age   G   3P  3PA    3P%   PTS
5   112     Stephen Curry   2015  26  80  3.6  8.1  0.443  23.8
22  442     Klay Thompson   2015  24  77  3.1  7.1  0.439  21.7
15  300   Wesley Matthews   2015  28  60  2.9  7.4  0.389  15.9
13  266       Kyle Korver   2015  33  75  2.9    6  0.492  12.1
20  415        J.R. Smith   2015  29  46  2.8  7.3   0.39  12.7
3    82     Isaiah Canaan   2015  23  22  2.7  7.4  0.364  12.6
18  381       J.J. Redick   2015  30  78  2.6  5.9  0.437  16.4
9   197      James Harden   2015  25  81  2.6  6.9  0.375  27.4
4   105  Robert Covington   2015  24  70  2.4  6.4  0.374  13.5
6   139      Kevin Durant   2015  26  27  2.4  5.9  0.403  25.4


     Rk          Player Season Age   G   3P   3PA    3P%   PTS
6   105   Stephen Curry   2016  27  79  5.1  11.2  0.454  30.1
26  425   Klay Thompson   2016  25  80  3.5   8.1  0.425  22.1
15  266  Damian Lillard   2016  25  75  3.1   8.1  0.375  25.1
11  177    James Harden   2016  26  82  2.9     8  0.359    29
17  273      Kyle Lowry   2016  29  77  2.8   7.1  0.388  21.2
22  369     J.J. Redick   2016  31  75  2.7   5.6  0.475  16.3
23  402      J.R. Smith   2016  30  77  2.6   6.6    0.4  12.4
7   126    Kevin Durant   2016  27  72  2.6   6.7  0.387  28.2
9   153     Paul George   2016  25  81  2.6     7  0.371  23.1
10  160     Eric Gordon   2016  27  45  2.5   6.5  0.384  15.2


     Rk          Player Season Age   G   3P  3PA    3P%   PTS
10   98   Stephen Curry   2017  28  79  4.1   10  0.411  25.3
37  428   Klay Thompson   2017  26  78  3.4  8.3  0.414  22.3
15  158     Eric Gordon   2017  28  75  3.3  8.8  0.372  16.2
36  425   Isaiah Thomas   2017  27  76  3.2  8.5  0.379  28.9
28  274      Kyle Lowry   2017  30  60  3.2  7.8  0.412  22.4
16  172    James Harden   2017  27  81  3.2  9.3  0.347  29.1
38  452    Kemba Walker   2017  26  79    3  7.6  0.399  23.2
3    36    Bradley Beal   2017  23  77  2.9  7.2  0.404  23.1
26  266  Damian Lillard   2017  26  75  2.9  7.7   0.37    27
0    15   Ryan Anderson   2017  28  72  2.8    7  0.403  13.6


     Rk           Player Season Age   G   3P  3PA    3P%   PTS
12  120    Stephen Curry   2018  29  51  4.2  9.8  0.423  26.4
23  194     James Harden   2018  28  72  3.7   10  0.367  30.4
20  180      Eric Gordon   2018  29  69  3.2  8.8  0.359    18
18  173      Paul George   2018  27  79  3.1  7.7  0.401  21.9
36  298   Damian Lillard   2018  27  73  3.1  8.6  0.361  26.9
55  480    Klay Thompson   2018  27  73  3.1  7.1   0.44    20
38  305       Kyle Lowry   2018  31  78  3.1  7.6  0.399  16.2
57  498     Kemba Walker   2018  27  80  2.9  7.5  0.384  22.1
15  146  Wayne Ellington   2018  30  77  2.9  7.5  0.392  11.2
33  239     Kyrie Irving   2018  25  60  2.8  6.8  0.408  24.4


     Rk            Player Season Age   G   3P   3PA    3P%   PTS
15  124     Stephen Curry   2019  30  69  5.1  11.7  0.437  27.3
29  206      James Harden   2019  29  78  4.8  13.2  0.368  36.1
22  183       Paul George   2019  28  77  3.8   9.8  0.386    28
32  224       Buddy Hield   2019  26  82  3.4   7.9  0.427  20.7
66  497      Kemba Walker   2019  28  82  3.2   8.9  0.356  25.6
58  418       J.J. Redick   2019  34  76  3.2     8  0.397  18.1
23  190       Eric Gordon   2019  30  68  3.2   8.8   0.36  16.2
64  477     Klay Thompson   2019  28  78  3.1   7.7  0.402  21.5
41  305    Damian Lillard   2019  28  80    3     8  0.369  25.8
61  434  D'Angelo Russell   2019  22  81  2.9   7.8  0.369  21.1


     Rk            Player Season Age   G   3P   3PA    3P%   PTS
27  198      James Harden   2020  30  68  4.4  12.4  0.355  34.3
42  297    Damian Lillard   2020  29  66  4.1  10.2  0.401    30
30  218       Buddy Hield   2020  27  72  3.8   9.6  0.394  19.2
59  424   Duncan Robinson   2020  25  73  3.7   8.3  0.446  13.5
2    41     Dāvis Bertāns   2020  27  54  3.7   8.7  0.424  15.4
63  435  D'Angelo Russell   2020  23  33  3.6   9.7  0.374  23.6
25  180   Devonte' Graham   2020  24  63  3.5   9.3  0.373  18.2
62  435  D'Angelo Russell   2020  23  45  3.5   9.6  0.367  23.1
76  525        Trae Young   2020  21  60  3.4   9.5  0.361  29.6
23  172       Paul George   2020  29  48  3.3   7.9  0.412  21.5


     Rk            Player Season Age   G   3P   3PA    3P%   PTS
18  115     Stephen Curry   2021  32  62  5.3  12.6  0.421  31.8
55  296    Damian Lillard   2021  30  66  4.1  10.5  0.388  28.9
43  217       Buddy Hield   2021  28  70    4  10.2  0.394  16.6
59  327       CJ McCollum   2021  29  46  3.6   8.9  0.399  23.1
4    39     Malik Beasley   2021  24  37  3.5   8.7  0.399  19.6
73  431   Duncan Robinson   2021  26  70  3.5   8.5  0.407  13.1
54  285       Zach LaVine   2021  25  58  3.4   8.2  0.419  27.4
62  347  Donovan Mitchell   2021  24  53  3.4   8.7  0.386  26.4
74  440      Terry Rozier   2021  26  67  3.3   8.3  0.391  20.5
81  498     Fred VanVleet   2021  26  52  3.3   9.2  0.366  19.6














As you can see during the first 3 season of Curry's career he only shot around 5 threes a game resulting in about 2 made three pointers a game. That is a 40% success rate. For reference, anything above 40% is an above average 3-point percentage (Curry has never had a season under 40% 3P%). Then due to many factors Curry started shooting more three and hasn't looked back since.

Impressively, Curry was top 10 in 3PM 10 out of the 12 season he's been in the NBA and the two season he missed were due to injury. No other player appeared more than 6 times. In addition, he was in 1st place 8 of the 10 season he was on the list. That is unprecedented! The graphs paint an even clearer picture of just how good Steph Curry is. From 2013 onward, Steph Curry is in a class of his own. He is a true outlier from the data. The thing that makes this so incredible is this isn't normal NBA player data. I picked the best 3-point shooters from each year and compared them to Curry, yet he's still an outlier from the rest of the best 3-point shooters in the world.

    #combine all data from last cell

    #start by adding 1st table to a new table and appending on the rest
    big_table = years_data[0]
    for i in range(len(years_data)):
        big_table = big_table.append(years_data[i], ignore_index=True)

    #linear regression
    three_percent = [ [i] for i in big_table['3P%'].values]

    lr = linear_model.LinearRegression()
    equation = lr.fit(three_percent, big_table['3P'].values)

    #plotting data values
    plt.figure(figsize=(15, 10))
    plt.scatter(big_table['3P%'], big_table['3P'], label=x)
    plt.plot(big_table['3P%'], lr.predict(three_percent), color='red')
    plt.title("3-point field goal percentage vs. 3-point field goals made from 2010 - 2021")
    plt.xlabel("3P%")
    plt.ylabel("3PM")

    year = 2010
    #show Steph Curry's data points
    for i in range(big_table.shape[0]):
        if(big_table['Player'].tolist()[i] == "Stephen Curry"):
            plt.annotate(big_table['Player'].tolist()[i] + "\n" + str(year), (big_table['3P%'].tolist()[i], big_table['3P'].tolist()[i]))
            year = year + 1
    plt.tight_layout()

To wrap up Curry's individual statistical analysis I took all data points from our graphs above and made a big graph to see how Curry stacked up throughout the years. Not suprisingly, Steph curry is basically competing with himself. He holds 9 of the most efficent 3-point season to date. This graph could be a bit misleading though because from 2010-2021 3-point shooting has increased at an incredible rate. The main reason teams are shooting more three is because of how Steph Curry destroyed them in his earlier years. This makes the older data points seem weaker and less impressive since they shot less 3-pointers per game in 2010 compared to 2021. Even still, older data points on Curry hold up relatively well as show above.

Hopefully the graphs and tables above show how impressive Curry has been throughout his Career. To speak more on the increase in 3-pointers and to gain a better wholistic view of how Curry changed the NBA game we are going to look at 3-pointers made vs. 3-pointers attempted by each team each season Curry has been in the league. Then to sum it up we will take the league averages of the 3-pointers attempted for that particular season for the duration of Curry's career (2010-2021).

For this table I had to switch sources because NBA references tables for teams was commented out in their HTML so I couldn't get the table correctly formatted. Instead I used ESPN (https://www.espn.com/). This site is public and has a lot of data on any popular sport. I'm going to be taking team seasonal statistics for each season Curry has been in the league.

    #year and averages list for each season
    year = 2010
    averages = []

    #loop through season
    for i in range(12):
        #web scraping and creating a dataframe
        team_stats = pd.DataFrame(columns=["Rk", "Team", "3P", "3PA", "3P%", "PTS"])
        teams = req.get("https://www.espn.com/nba/stats/team/_/season/" + str(year) + "/seasontype/2")
        teams_tables = pd.read_html(teams.text, flavor='bs4')
        stats = teams_tables[1]
    
    #getting average 3PA per season
    average = 0
    for x in range(30):
        average = stats['3PA'][x] + average
    average = average / 30
    averages.append(average)
    
    #plotting the 3PA and 3PM per season
    plt.figure(figsize=(15, 10))
    plt.scatter(stats['3PA'], stats['3PM'], label=x)
    plt.title(str(year) + ": 3-point field goals attempted vs. 3-point field goals made by each team")
    plt.xlabel("3PA")
    plt.ylabel("3PM")
    
    #going to next year
    year = year + 1

    #plot averages of 3PA from 2010-2021
    plt.figure(figsize=(15, 10))
    plt.scatter([2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018, 2019, 2020, 2021], averages, label=x)
    plt.title("League average 3-point attempts per game from 2010 - 2021")
    plt.xlabel("Year")
    plt.ylabel("Average 3PA per game")
    Text(0, 0.5, 'Average 3PA per game')













So as you can see there is a very linear relationship between the amount of 3-pointers a team attempts and the amount of 3-pointers they make. That would make a lot of sense logically. If you're good at shooting 3's take more 3's. Importantly, as the years go own you can see both the 3PA and 3PM increase. To paint a better picture of that I took the sum of all season WRT 3PA. In 2010 the league average 3PA was around 18 3's a game. By 2021 the league average 3PA is just under 35 3PA a game. That is twice as many 3-pointers attempted as a league in just over a decade. Stephen Curry and his team's success showed other teams how you can win and all the other teams tried to copy what he was doing, except they weren't as good at it.

It's important to note that (based on the individual statisical data on Steph Curry) Steph Curry's breakout season was 2013. The exact time the NBA started to see a noticable increase in 3PA per game. The 3PA increased even faster in 2016-2021 because by 2016 the league had caught up to Curry and their playstyle and everyone else wanted to shoot just as many 3's (similar to the moneyball effect in baseball, the Oakland A's were an outlier for a while, but eventually the other teams copied them and took their strategy so they were less effective themselves). The interesting thing though is while teams have started copying Curry and his team's playstyle, he is still an outlier among the best 3-point shooters in the league (even in 2021, look at the graph again).

I hope my data and graphs show how not just how incredible a basketball player Stephen Curry is, but, more importantly, I hope my data showed how much Stephen Curry has changed the game. In just half a decade Curry's presence in the NBA resulted in an offensive explosion from the 3-point line. I understand Stephen Curry is not the only reason for the increase in three-pointers, but I agrue he is at least one of the most important reasons for the changes in the game, if not the most important.

So far in his carrer Curry has won 3 championships, has the record for almost every 3-point statistic in the NBA, and has been the driving catalyst for the way the NBA is played today. I'll leave you with this incredible stat: Games with 11 or more 3-pointers made: Stephen Curry - 12, everyone else in NBA history combined - 14. He's the only NBA player that can make you scream at your TV in disbelief because he does things no body else has ever seen before.

I've shown a lot about Steph Curry here. Unfortunately, I could only cover his skill as a 3-point shooter. It is very underrated how much better he makes his teammates and how he opens up the offense for his team because everyone is so focused on him. He has various other elite NBA skills. If you want more information on Stephen Curry and his impact on basketball I urge you to look at some of the resources linked below. Hope you enjoyed!

https://www.youtube.com/watch?v=bvTPxCOfjdE&ab_channel=ThinkingBasketball

https://www.basketball-reference.com/players/c/curryst01.html

https://www.youtube.com/watch?v=dfs1Lq-yP8A&ab_channel=TheRinger

 
