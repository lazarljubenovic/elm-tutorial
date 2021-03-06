> This page covers Elm 0.18

# Main view

Our main application view needs to show different pages as we change the browser location.

Change __src/View.elm__ to:

```elm
module View exposing (..)

import Html exposing (Html, div, text)
import Messages exposing (Msg(..))
import Models exposing (Model)
import Players.Edit
import Players.List
import Players.Models exposing (PlayerId)
import Routing exposing (Route(..))


view : Model -> Html Msg
view model =
    div []
        [ page model ]


page : Model -> Html Msg
page model =
    case model.route of
        PlayersRoute ->
            Html.map PlayersMsg (Players.List.view model.players)

        PlayerRoute id ->
            playerEditPage model id

        NotFoundRoute ->
            notFoundView


playerEditPage : Model -> PlayerId -> Html Msg
playerEditPage model playerId =
    let
        maybePlayer =
            model.players
                |> List.filter (\player -> player.id == playerId)
                |> List.head
    in
        case maybePlayer of
            Just player ->
                Html.map PlayersMsg (Players.Edit.view player)

            Nothing ->
                notFoundView


notFoundView : Html msg
notFoundView =
    div []
        [ text "Not found"
        ]
```

---

### Showing the correct view

```elm
page : Model -> Html Msg
page model =
    case model.route of
        PlayersRoute ->
            Html.map PlayersMsg (Players.List.view model.players)

        PlayerRoute id ->
            playerEditPage model id

        NotFoundRoute ->
            notFoundView
```

Now we have a function `page` which has a case expression to show the correct view depending on what is in `model.route`.

When the player edit route matches (e.g. `#players/2`) we will get the player id from the route i.e. `PlayerRoute playerId`.

### Finding the player

```elm
playerEditPage : Model -> PlayerId -> Html Msg
playerEditPage model playerId =
    let
        maybePlayer =
            model.players
                |> List.filter (\player -> player.id == playerId)
                |> List.head
    in
        case maybePlayer of
            Just player ->
                Html.map PlayersMsg (Players.Edit.view player)

            Nothing ->
                notFoundView
```

We have the `playerId`, but we might not have the actual player record for that id. We filter the players' list by that id and have a case expression that show the correct view depending if the player is found or not.

### notFoundView

`notFoundView` is shown when no route matches. Note the type `Html msg` instead of `Html Msg`. This is because this view doesn't produce any messages so can use a generic type variable `msg` instead of and specific type `Msg`.
