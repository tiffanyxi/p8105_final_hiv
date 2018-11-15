PUMS\_Data\_Dictionary\_2011-1015
================
Yizhi Ma
11/14/2018

``` r
pums_data = read.csv("./data/selected_pums.csv")
```

Current Problems
----------------

Column names meaning
--------------------

This data is selected based on the variables that we may need

PUMA00 --
Public use microdata area code 2000, which is the area code used before 2012.
-9 means this classifications is N/A for data collected after 2012.

PUMA10 --
Public use microdata area code 2010, which is the area code used after 2012.
-9 means this classifications is N/A for data collected prior to 2012.

ADJINC --
Adjustment factor for income and earnings dollar amounts
1073094 -- 2011 factor
1042852 -- 2012 factor
1025215 -- 2013 factor
1009585 -- 2014 factor
1001264 -- 2015 factor

PINCP --
Total persons's income

RAC3P05 --
Recoded detailed race code for data collected prior to 2012

RAC3P12 --
Recoded detailed race code for data collected in 2012 or later

For more info, you can check the data dictionarty "<https://www2.census.gov/programs-surveys/acs/tech_docs/pums/data_dict/PUMS_Data_Dictionary_2011-2015.pdf?#>"
