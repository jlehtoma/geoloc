
<!-- README.md is generated from README.Rmd. Please edit that file -->

# geoloc

The goal of geoloc is to provide access to the geolocation API from
modern web-browser, in order to get the location of the user in a Shiny
app.

> Note that this location will be provided only if the user agrees to
> share it.

As it relies on the browser HTML API, this won’t work with the RStudio
native viewer.

This package has been tested on Google Chrome 68.0.3440.106 and Firefox
61.0.2.

## Installation

You can install the dev version of {geoloc} from Github with:

``` r
remotes::install_github("ColinFay/geoloc")
```

## How it works

### Getting the data

The two functions (described below) launch a prompt that asks the user
if he is willing to share his/her location.

If the user agrees, the `lat` and `long` are sent back to shiny in
`input$geoloc_lon` and `input$geoloc_lat`.

The communication from the browser to Shiny may take some time (a matter
of seconds, though).

### Error

There are four cases where the location can fail. They are sent back to
Shiny as:

  - `"NOT_SUPPORTED"`: location not supported in the browser
  - `"PERMISSION_DENIED"`: the user did not allowed to be located
  - `"POSITION_UNAVAILABLE"`: the user location is not available
  - `"TIMEOUT"`: the operation timed-out

## Onload or With a button

There are two ways to get the user location:

### At page load time

You can get the location of the user as soon as the app loads with
`onload_geoloc`.

``` r
library(shiny)
library(leaflet)

ui <- fluidPage(
  geoloc::onload_geoloc(),
  leafletOutput("lf")
)

server <- function(input, output) {
  output$lf <- renderLeaflet({
    req(input$geoloc_lon)
    req(input$geoloc_lat)
    leaflet() %>%
      addTiles() %>%
      setView(as.numeric(input$geoloc_lon), as.numeric(input$geoloc_lat), zoom = 17) %>%
      addMarkers(as.numeric(input$geoloc_lon), as.numeric(input$geoloc_lat), label = "You're here!")
  })
}

shinyApp(ui, server)
```

### With a button

`button_geoloc` behaves a any `shiny::actionButton` (and takes the same
parameters), except that it launches the geolocation service when it is
clicked.

``` r
library(shiny)
library(leaflet)

ui <- fluidPage(
  h2("Where Am I?"),
  tags$p("Click the button to get your location"),
  geoloc::button_geoloc("id", "Get my Location"),
  tags$br(),
  leafletOutput("lf")
)

server <- function(input, output) {
  output$lf <- renderLeaflet({
    req(input$geoloc_lon)
    req(input$geoloc_lat)
    leaflet() %>%
      addTiles() %>%
      setView(as.numeric(input$geoloc_lon), as.numeric(input$geoloc_lat), zoom = 17) %>%
      addMarkers(as.numeric(input$geoloc_lon), as.numeric(input$geoloc_lat), label = "You're here!")
  })
}

shinyApp(ui, server)
```

## Code of Conduct

Please note that this project is released with a [Contributor Code of
Conduct](CODE_OF_CONDUCT.md). By participating in this project you agree
to abide by its terms.
