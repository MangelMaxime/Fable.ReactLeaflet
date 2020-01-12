# Fable.ReactLeaflet

Fable bindings for [react-leaflet](https://react-leaflet.js.org/)

# Sample usage

```fsharp
module Client

open Elmish
open Elmish.React
open Fable.React
open Fable.React.Props
open Fulma
open Leaflet
open Fable.Core.JsInterop

module RL = ReactLeaflet

importAll "../../node_modules/leaflet/dist/leaflet.css"

Leaflet.icon?Default?imagePath <- "//cdnjs.cloudflare.com/ajax/libs/leaflet/1.3.1/images/"
type Marker = { info: string; link: string; position: LatLngExpression}
type Model = { Markers: Marker list }

type Msg =
    | None

let init () : Model * Cmd<Msg> =
    let initialModel = { Markers = [{ info = "info"; link = "www.twitter.com"; position = (Fable.Core.U3.Case3 (54.425, 18.59)) }]}
    initialModel, Cmd.none

let update (msg : Msg) (currentModel : Model) : Model * Cmd<Msg> =
    currentModel, Cmd.none  

let buildMarker (marker: Marker): ReactElement =
    RL.marker 
      [ 
        RL.MarkerProps.Position marker.position ] 
      [ RL.popup 
          [ RL.PopupProps.Key marker.link]
          [ Control.p 
              [] 
              [ label [] [ !!marker.info ] ]
            Control.p 
                [] 
                [ Button.a
                    [ Button.Size IsSmall
                      Button.Props [ Href (marker.link) ] ]
                    [ Icon.icon [ ]
                        [ ]
                      span [ ] [ str "Go to" ] ] ] ] ]     

let tile =
  RL.tileLayer 
    [ RL.TileLayerProps.Url "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
      RL.TileLayerProps.Attribution "&amp;copy <a href=&quot;http://osm.org/copyright&quot;>OpenStreetMap</a> contributors" ] 
    []

let mapElements model =
  let markers = model.Markers |> List.map buildMarker
  tile :: markers    

let view (model : Model) (dispatch : Msg -> unit) =
    Hero.hero [ Hero.Color IsPrimary; Hero.IsFullHeight ]
        [ Hero.head [ ]
            []

          Hero.body [ ]
            [ Container.container [ Container.Modifiers [ Modifier.TextAlignment (Screen.All, TextAlignment.Centered) ] ]
                [ Column.column
                    [ Column.Width (Screen.All, Column.Is9)
                      Column.Offset (Screen.All, Column.Is1) ]
                    [ Heading.p [ ]
                        [ str "Leaflet example" ]
                      RL.map [ RL.MapProps.Zoom 10.; RL.MapProps.Style [Height 500; MinWidth 200; Width Column.IsFull ]; RL.MapProps.Center model.Markers.[0].position  ] 
                        (mapElements model)
                                              
                       ] ] ] ]

#if DEBUG
open Elmish.Debug
open Elmish.HMR
#endif

Program.mkProgram init update view
#if DEBUG
|> Program.withConsoleTrace
#endif
|> Program.withReactBatched "elmish-app"
#if DEBUG
|> Program.withDebugger
#endif
|> Program.run
```

# In case of problems
When the map doesn't show up, or there are some errors in a console, maybe you forgot about one of the below:
1. set width/height of a map, otherwise, the map wouldn't show;
2. set map center, otherwise, the map would be grey;
3. add a tile layer;

```fsharp
ReactLeaflet.tileLayer 
    [ ReactLeaflet.TileLayerProps.Url "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
      ReactLeaflet.TileLayerProps.Attribution "&amp;copy <a href=&quot;http://osm.org/copyright&quot;>OpenStreetMap</a> contributors" ] 
    []
```
4. import leaflet styles;
```fsharp
importAll "../../node_modules/leaflet/dist/leaflet.css"
```
5. change the default imagepath for an icon, otherwise you would get errors in console;
```fsharp
Leaflet.icon?Default?imagePath <- "//cdnjs.cloudflare.com/ajax/libs/leaflet/1.3.1/images/"
```
6. do not forget to add to package.json leaflet packages (versions written below are only examples)
```javascript
{
    "leaflet": "1.6.0",
    "react-leaflet": "2.6.1"
}
```
