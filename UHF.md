combined\_dataset
================

``` r
UHF_zipcode = read_csv("./data/appb.csv") %>% 
  slice(-43) %>% 
  select(-Borough) %>% 
  rename("UHF" = "UHF Neighborhood")
```

    ## Parsed with column specification:
    ## cols(
    ##   Borough = col_character(),
    ##   `UHF Neighborhood` = col_character(),
    ##   `Zip Code` = col_character()
    ## )

``` r
data_hiv = read_csv("./data/DOHMH_HIV_AIDS_Annual_Report.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   Year = col_integer(),
    ##   Borough = col_character(),
    ##   UHF = col_character(),
    ##   Gender = col_character(),
    ##   Age = col_character(),
    ##   Race = col_character(),
    ##   `HIV diagnoses` = col_integer(),
    ##   `HIV diagnosis rate` = col_double(),
    ##   `Concurrent diagnoses` = col_integer(),
    ##   `% linked to care within 3 months` = col_integer(),
    ##   `AIDS diagnoses` = col_integer(),
    ##   `AIDS diagnosis rate` = col_double(),
    ##   `PLWDHI prevalence` = col_double(),
    ##   `% viral suppression` = col_integer(),
    ##   Deaths = col_integer(),
    ##   `Death rate` = col_double(),
    ##   `HIV-related death rate` = col_double(),
    ##   `Non-HIV-related death rate` = col_double()
    ## )

``` r
combine_hiv = 
  full_join(UHF_zipcode, data_hiv, by = "UHF") %>%
  janitor::clean_names() %>% 
  separate(zip_code, into = c("zipcode1", "zipcode2", "zipcode3", 
                              "zipcode4", "zipcode5", "zipcode6", "zipcode7", "zipcode8",
                              "zipcode9"), sep = ", ") %>% 
  gather(key = zip_code, value = zipcode_value, zipcode1:zipcode9) %>% 
  filter(!is.na(zipcode_value)) %>% 
  rename("zipcode" = "zipcode_value") %>% 
  select(zipcode, everything(), -zip_code)
```

``` r
location = read.csv("./zip_codes_states.csv") %>% 
  filter(state == "NY", county == "New York") %>% 
  select(zip_code, latitude, longitude) %>% 
  rename("zipcode" = "zip_code") %>% 
  mutate(zipcode = as.character(zipcode))
  
combine_hiv = full_join(location, combine_hiv, by = "zipcode")
```

``` r
write_csv(combine_hiv, "./data/combine_hiv_location.csv")
```
