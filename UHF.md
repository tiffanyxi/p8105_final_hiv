combined\_dataset
================

``` r
UHF_zipcode = 
  read_csv("./data/appb.csv") %>% 
  slice(-43) %>% 
  select(-Borough) %>% 
  rename("UHF" = "UHF Neighborhood") %>% 
  janitor::clean_names()
```

    ## Parsed with column specification:
    ## cols(
    ##   Borough = col_character(),
    ##   `UHF Neighborhood` = col_character(),
    ##   `Zip Code` = col_character()
    ## )

``` r
raw_hiv = GET("https://data.cityofnewyork.us/resource/9n66-rbk3.csv") %>% 
  content("parsed") %>% 
  janitor::clean_names()
```

    ## Parsed with column specification:
    ## cols(
    ##   age = col_character(),
    ##   aids_diagnoses = col_integer(),
    ##   aids_diagnosis_rate = col_double(),
    ##   borough = col_character(),
    ##   concurrent_diagnoses = col_integer(),
    ##   death_rate = col_double(),
    ##   deaths = col_integer(),
    ##   gender = col_character(),
    ##   hiv_diagnoses = col_integer(),
    ##   hiv_diagnosis_rate = col_double(),
    ##   hiv_related_death_rate = col_double(),
    ##   linked_to_care_within_3_months = col_integer(),
    ##   non_hiv_related_death_rate = col_double(),
    ##   plwdhi_prevalence = col_double(),
    ##   race = col_character(),
    ##   uhf = col_character(),
    ##   viral_suppression = col_integer(),
    ##   year = col_integer()
    ## )

``` r
combine_hiv = 
  right_join(UHF_zipcode, raw_hiv, by = "uhf") %>%
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
r = GET('http://data.beta.nyc//dataset/3bf5fb73-edb5-4b05-bb29-7c95f4a727fc/resource/6df127b1-6d04-4bb7-b983-07402a2c3f90/download/f4129d9aa6dd4281bc98d0f701629b76nyczipcodetabulationareas.geojson')
nyc_zipcode = readOGR(content(r,'text'), 'OGRGeoJSON', verbose = F)
```

    ## No encoding supplied: defaulting to UTF-8.

``` r
zipcode_lat_lng = nyc_zipcode@data %>% 
  select(zipcode = postalCode, longitude, latitude) %>% 
  mutate(zipcode = as.character(zipcode))
  
combine_hiv1 = 
  right_join(zipcode_lat_lng, combine_hiv, by = "zipcode") %>% 
  mutate(longitude = as.numeric(longitude), latitude = as.numeric(latitude)) %>% 
  group_by(uhf) %>% 
  summarise(lng = mean(longitude),
            lat = mean(latitude)) %>% 
  filter(!(uhf == "Pelhem - Throgs Neck"))
```
