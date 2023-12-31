# cta-line-ridership

Libraries.

``` r
library(RSocrata)
library(tidyverse)
library(here)
library(scales)
library(ggforce)
library(showtext)
```

Import data.

``` r
monthly_riders <- read.socrata("https://data.cityofchicago.org/resource/t2rn-p8d7.csv")
```

Filter to 2022, make column names consistent, group by the station and
sum their monthly totals for the year

``` r
yearly_riders_2022 <- monthly_riders %>% 
  filter(month_beginning >= "2022-01-01" & month_beginning <= "2023-01-01") %>% 
  rename(station_name = stationame, month_total = monthtotal) %>% 
  group_by(station_name) %>% 
  summarize(yearly_riders = sum(month_total)) %>% 
  arrange(desc(yearly_riders))
```

Selecting only stations with 1.5M+ riders in 2022 and putting them in
descending order.

``` r
top_yearly_riders_2022 <- yearly_riders_2022 %>% 
  filter(yearly_riders > 1500000) %>% 
  mutate(station_name = reorder(station_name, desc(yearly_riders)))
```

Graphing it B)

``` r
ggplot(
    top_yearly_riders_2022
  ) +
  geom_bar(
    aes(x = station_name, 
        y = yearly_riders,
        fill = station_name),
    stat = "identity"
  ) +
  labs(
    title = "The Red Line Dominates 2022's Most-Ridden CTA\nStations",
    x = "",
    y = ""
  ) +
  scale_y_continuous(
    labels = scales::label_number(scale_cut = cut_short_scale()),
    expand = c(0,0)
# Manual "M" label below
#   labels = function(x) {
#       paste0(round(x/1e6,1),"M")
#   }
  ) +
  scale_fill_manual(
    values = c(
                "#c60c30",
                "#00a1de",
                "gray",
                "#c60c30",
                "gray",
                "gray",
                "gray",
                "#c60c30",
                "#00a1de",
                "gray",
                "#c60c30",
                "#c60c30"
              )
  ) +
  theme(plot.background = element_rect(color = "gray10",
                                       fill = "gray10"),
        panel.background = element_blank(),
        
        panel.grid.major.x = element_blank(),
        panel.grid.major.y = element_line(linewidth = 0.25),
        
        plot.title.position = "plot",
        plot.title = element_text(color = "white",
                                  family = "Space Grotesk",
                                  face = "bold",
                                  size = 20),
        
        axis.text = element_text(color = "gray60",
                                 family = "Space Grotesk"),
        axis.text.x = element_text(
                        angle = 45,
                        hjust = 1),
        
        legend.position = "none",
        plot.margin = margin(10, 10, 0, 10)
  )
```

![](L-ridership_graph_files/figure-commonmark/highest%20ridership%20graph-1.png)

``` r
ggsave(here("L-ridership_graph", "plots", "station-ridership_bar-graph.png"), width = 8, units = "in")
```

    Saving 8 x 5.33 in image

First defined every station part of each line/the loop. Then used those
vectors to give them a new value in a new column `line`. Finally grouped
by `line` and summed their ridership (and sorted).

``` r
red_stations <- c('Jarvis', 'Morse', 'Loyola', 'Granville', 'Thorndale', 'Bryn Mawr', 'Berwyn', 'Argyle', 'Lawrence', 'Wilson', 'Sheridan', 'Addison-North Main', 'North/Clybourn', 'Clark/Division', 'Chicago/State', 'Grand/State', 'Lake/State', 'Monroe/State', 'Jackson/State', 'Harrison', 'Cermak-Chinatown', 'Sox-35th-Dan Ryan', '47th-Dan Ryan', 'Garfield-Dan Ryan', '63rd-Dan Ryan', '69th', '79th', '87th', '95th/Dan Ryan')
purple_stations <- c('Linden', 'Central-Evanston', 'Noyes', 'Foster', 'Davis', 'Dempster', 'Main', 'South Boulevard')
yellow_stations <- c('Dempster-Skokie', 'Oakton-Skokie')
blue_stations <- c("O'Hare Airport", 'Rosemont', 'Cumberland', "Harlem-O'Hare", 'Jefferson Park', "Montrose-O'Hare", "Irving Park-O'Hare", "Addison-O'Hare", "Belmont-O'Hare", 'Logan Square', 'California/Milwaukee', 'Western/Milwaukee', 'Damen/Milwaukee', 'Division/Milwaukee', 'Chicago/Milwaukee', 'Grand/Milwaukee', 'Washington/Dearborn', 'Monroe/Dearborn', 'Jackson/Dearborn', 'LaSalle', 'Clinton-Forest Park', 'UIC-Halsted', 'Racine', 'Medical Center', 'Western-Forest Park', 'Kedzie-Homan-Forest Park', 'Pulaski-Forest Park', 'Cicero-Forest Park', 'Austin-Forest Park', 'Oak Park-Forest Park', 'Harlem-Forest Park', 'Forest Park')
pink_stations <- c('Polk', '18th', 'Damen-Cermak', 'Western-Cermak', 'California-Cermak', 'Kedzie-Cermak', 'Central Park', 'Pulaski-Cermak', 'Kostner', 'Cicero-Cermak', '54th/Cermak')
green_stations <- c('Harlem-Lake', 'Oak Park-Lake', 'Ridgeland', 'Austin-Lake', 'Central-Lake', 'Laramie', 'Cicero-Lake', 'Pulaski-Lake', 'Conservatory', 'Kedzie-Lake', 'California-Lake', 'Cermak-McCormick Place', '35-Bronzeville-IIT', 'Indiana', '43rd', '47th-South Elevated', '51st', 'Garfield-South Elevated', 'King Drive', 'East 63rd-Cottage Grove', 'Halsted/63rd', 'Ashland/63rd')
brown_stations <- c('Kimball', 'Kedzie-Brown', 'Francisco', 'Rockwell', 'Western-Brown', 'Damen-Brown', 'Montrose-Brown', 'Irving Park-Brown', 'Addison-Brown', 'Paulina', 'Southport', 'Wellington', 'Diversey', 'Armitage', 'Sedgwick', 'Chicago/Franklin', 'Merchandise Mart')
orange_stations <- c('Midway Airport', 'Pulaski-Orange', 'Kedzie-Midway', 'Western-Orange', '35th/Archer', 'Ashland-Orange', 'Halsted-Orange')
loop_stations <- c('Washington/Wells', 'Quincy/Wells', 'LaSalle/Van Buren', 'Library', 'Adams/Wabash', 'Washington/Wabash', 'State/Lake', 'Clark/Lake')
# sorry for that
# green / pink transfers: 'Ashland-Lake', 'Morgan-Lake', 'Clinton-Lake'

totals_2022 <- yearly_riders_2022 %>% 
  mutate(
    line = case_when(
              station_name %in% red_stations ~ "Red",
              station_name %in% purple_stations ~ "Purple",
              station_name %in% yellow_stations ~ "Yellow",
              station_name %in% blue_stations ~ "Blue",
              station_name %in% pink_stations ~ "Pink",
              station_name %in% green_stations ~ "Green", 
              station_name %in% brown_stations ~ "Brown",
              station_name %in% orange_stations ~ "Orange",
              station_name %in% loop_stations ~ "Loop",
              TRUE ~ "Transfer Stations"
           )
  ) %>% 
  group_by(line) %>% 
  summarize(total_riders = sum(yearly_riders)) %>% 
  mutate(line = reorder(line, desc(total_riders)))

# Adding a line break to the "Transfer Station" label so it doesn't look
# messy on the graph.
labels <- c("Red" = "Red", 
            "Blue" = "Blue", 
            "Loop" = "Loop", 
            "Brown" = "Brown", 
            "Transfer Stations" = "Transfer\nStations", 
            "Green" = "Green", 
            "Orange" = "Orange", 
            "Pink" = "Pink", 
            "Purple" = "Purple", 
            "Yellow" = "Yellow")
```

Graph it B)

``` r
ggplot(totals_2022) +
  geom_bar(
    aes(x = line, 
        y = total_riders,
        fill = line),
    stat = "identity"
  ) +
  labs(
    title = "The Red & Blue Lines Have Far and Away the Highest\n2022 Ridership of All CTA 'L' Lines",
    y = "Total Riders",
    x = ""
  ) +
  scale_y_continuous(
    labels = scales::label_number(scale_cut = cut_short_scale()), # turns into "1M", "2M", etc
    expand = c(0,0) # forces start at origin
  ) +
  scale_fill_manual(
    values = c("Red" = "#c60c30",
               "Blue" = "#00a1de",
               "Transfer Stations" = "#8a7576",
               "Brown" = "#62361b",
               "Loop" = "gray40",
               "Green" = "#009b3a",
               "Orange" = "#ed831f",
               "Pink" = "#e27ea6",
               "Purple" = "#522398",
               "Yellow" = "#f9e300")
  ) +
  scale_x_discrete(
    labels = labels
  ) +
  theme(plot.background = element_rect(color = "gray10",
                                       fill = "gray10"),
        panel.background = element_blank(),
        
        panel.grid.major.x = element_blank(),
        panel.grid.major.y = element_line(linewidth = 0.25),
        
        plot.title.position = "plot",
        plot.title = element_text(color = "white",
                                  family = "Space Grotesk",
                                  face = "bold",
                                  size = 20),
        
        axis.title.y = element_text(color = "gray75",
                                    family = "Space Grotesk",
                                    face = "bold"),
        axis.text = element_text(color = "gray60",
                                 family = "Space Grotesk"),
        axis.text.x = element_text(
                        angle = 45,
                        hjust = 1),
        
        legend.position = "none",
        plot.margin = margin(10, 10, 0, 10)
  ) +
  
  # transfer stations segments
  annotate(
    geom = "segment",
    x = 6,
    xend = 5,
    y = 17500000,
    yend = 17500000,
    color = "white"
  ) +
  annotate(
    geom = "segment",
    x = 5,
    xend = 5,
    y = 17500000,
    yend = 6000000,
    color = "white"
  ) +
  geom_mark_circle(
    aes(x = 5, 
        y = 6000000),
    expand = 0.01,
    color = "white", 
    fill = "white",
    alpha = 1,
  ) +
  
  # red line segments
  annotate(
    geom = "segment",
    x = 1,
    xend = 6,
    y = 26000000,
    yend = 26000000,
    color = "white"
  ) +
  annotate(
    geom = "segment",
    x = 6,
    xend = 6,
    y = 26000000,
    yend = 17500000,
    color = "white"
  ) +
  geom_mark_circle(
    aes(x = 1, 
        y = 26000000),
    expand = 0.01,
    color = "white", 
    fill = "white",
    alpha = 1,
  ) +
  
  # annotation
  annotate(
    geom = "label",
    x = 5.5,
    y = 17500000,
    color = "white",
    fill = "gray17",
    family = "Space Grotesk",
    label = "Most transfer stations lie on \nthe Red Line, meaning its \nridership is likely even higher!",
    hjust = 0,
    label.size = NA # remove border from surrounding rectangle
  )
```

    Warning: Using the `size` aesthetic in this geom was deprecated in ggplot2 3.4.0.
    ℹ Please use `linewidth` in the `default_aes` field and elsewhere instead.

![](L-ridership_graph_files/figure-commonmark/Ridership%20by%20CTA%20Line%20Bar%20Graph-1.png)

``` r
ggsave(here("L-ridership_graph", "plots", "L-ridership_bar-graph.png"), width = 8, units = "in")
```

    Saving 8 x 5.33 in image

``` r
yearly_line_riders_2022 <- yearly_riders_2022 %>% 
  mutate(
    line = fct(case_when(
              station_name %in% red_stations ~ "Red",
              station_name %in% purple_stations ~ "Purple",
              station_name %in% yellow_stations ~ "Yellow",
              station_name %in% blue_stations ~ "Blue",
              station_name %in% pink_stations ~ "Pink",
              station_name %in% green_stations ~ "Green", 
              station_name %in% brown_stations ~ "Brown",
              station_name %in% orange_stations ~ "Orange",
              station_name %in% loop_stations ~ "Loop",
              TRUE ~ "Transfer Stations"
           ))
  ) %>% 
  filter(yearly_riders != 0) %>% 
  mutate(
    line = fct_reorder(line, yearly_riders, .fun = "median") # sort lines by median station ridership
  )
```

``` r
ggplot(
  yearly_line_riders_2022,
  aes(x = fct_rev(line),
      y = yearly_riders,
      group = line,
      color = line)
  ) +
  geom_boxplot(
    aes(fill = line),
    alpha = 0.5,
    outlier.shape = NA,
    show.legend = FALSE
  ) +
  geom_jitter(
    alpha = 0.7,
    size = 3.5,
    show.legend = FALSE
  ) +
  labs(
    title = "Loop and Non-Loop Transfer Stations Have Higher Median\n2022 Ridership Than Single-Line Stations",
    y = "Yearly Riders"
  ) +
  scale_y_continuous(
    labels = scales::label_number(scale_cut = cut_short_scale()), # turns into "1M", "2M", etc
    expand = c(0,0) # forces start at origin
  ) +
  scale_x_discrete(
    labels = labels
  ) +
  coord_cartesian(
    ylim = c(0, 3250000)
  ) +
  scale_color_manual(
    values = c("Red" = "#c60c30",
               "Blue" = "#00a1de",
               "Transfer Stations" = "#8a7576",
               "Brown" = "#62361b",
               "Loop" = "gray40",
               "Green" = "#009b3a",
               "Orange" = "#ed831f",
               "Pink" = "#e27ea6",
               "Purple" = "#522398",
               "Yellow" = "#f9e300")
  ) +
  scale_fill_manual(
    values = c("Red" = "#c60c30",
               "Blue" = "#00a1de",
               "Transfer Stations" = "#8a7576",
               "Brown" = "#62361b",
               "Loop" = "gray40",
               "Green" = "#009b3a",
               "Orange" = "#ed831f",
               "Pink" = "#e27ea6",
               "Purple" = "#522398",
               "Yellow" = "#f9e300")
  ) +
  theme(plot.background = element_rect(color = "gray10",
                                       fill = "gray10"),
        panel.background = element_blank(),
        
        panel.grid.major.x = element_blank(),
        panel.grid.major.y = element_line(linewidth = 0.25),
        
        plot.title.position = "plot",
        plot.title = element_text(color = "white",
                                  family = "Space Grotesk",
                                  face = "bold",
                                  size = 19),
        
        axis.title.x = element_blank(),
        axis.title.y = element_text(color = "gray75",
                                    family = "Space Grotesk",
                                    face = "bold"),
        axis.text = element_text(color = "gray60",
                                 family = "Space Grotesk"),
        axis.text.x = element_text(
                        angle = 45,
                        hjust = 1),
        
        legend.position = "none",
        plot.margin = margin(10, 10, 0, 10)
  )
```

![](L-ridership_graph_files/figure-commonmark/Ridership%20by%20CTA%20Line%20&%20Station%20Box/Scatterplot-1.png)

``` r
ggsave(here("L-ridership_graph", "plots", "L-ridership_scatterbox.png"), width = 8, units = "in")
```

    Saving 8 x 5 in image
