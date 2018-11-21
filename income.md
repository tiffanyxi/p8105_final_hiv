income\_data\_handling
================

``` r
library(tidyverse)
```

    ## ── Attaching packages ────────────────────────────────────────────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 3.0.0     ✔ purrr   0.2.5
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.8
    ## ✔ tidyr   0.8.1     ✔ stringr 1.3.1
    ## ✔ readr   1.1.1     ✔ forcats 0.3.0

    ## ── Conflicts ───────────────────────────────────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
pums_raw = read.csv("./data/selected_pums.csv")

zcta_to_puma = readxl::read_xls("./data/zcta10_to_puma10_nyc.xls", sheet = 2) %>% 
  select(zcta = zcta10, puma = puma10) %>% 
  mutate(puma = as.numeric(puma))

zip_to_zcta = readxl::read_xls("./data/zip_to_zcta10_nyc.xls", sheet = 2) %>% 
  select(zipcode, zcta = zcta5)
```

``` r
pums_data = 
  pums_raw %>% 
  select(puma = PUMA10, income = PINCP, year = ADJINC) %>% 
  filter(puma != -9) 

pums_data$year = recode(pums_data$year, 
                        "1042852" = "2012",
                        "1025215" = "2013",  
                        "1009585" = "2014", 
                        "1001264" = "2015")

pums_data = 
  pums_data %>% 
  group_by(year, puma) %>% 
  summarise(mid_income = median(income, na.rm = TRUE))
```

``` r
puma_to_zipcode = right_join(zip_to_zcta, zcta_to_puma, by = "zcta") %>% 
  select(puma, zipcode)

income_zipcode = right_join(pums_data, puma_to_zipcode, by = "puma") %>% 
  select(year, zipcode, mid_income)
```

Current Problems
----------------

tranfer PUMA10 code into locations.(Ref. found)

match PUMA names with the location in the HIV dataset.

cannot find name files for PUMA00.

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

Data from The ACS Public Use Microdata Sample files (PUMS), u can find it here "<https://factfinder.census.gov/faces/tableservices/jsf/pages/productview.xhtml?pid=ACS_pums_csv_2011_2015&prodType=document>"

This is the ZCTA10 to PUMA10 file "<http://faculty.baruch.cuny.edu/geoportal/resources/nyc_geog/nyc_puma_neighborhood.xls>"

This thi the ZIP code to ZCTA10 file "<http://faculty.baruch.cuny.edu/geoportal/resources/nyc_geog/zip_to_zcta10_nyc_revised.xls>"
