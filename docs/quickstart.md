# Quickstart Reference
## Contents
* [New Game](#start-a-game)
* [Add Players](#adding-some-players)
* [Playing](#playing)
* [Actions](#actions)

## Start a game
Example bash functions for some game config/playing exist in the `/resources` directory, but here you go:

### Request
``` bash
curl \
-X POST -H "Content-Type: application/json" \
-d '{"friendly-name": "YOUR NEW GAME"}'\
$DICE_HOST/games
```
Names are optional; a generated one will be passed to you if you don't provide one.
`$DICE_HOST` would be `localhost:3000` or whatever you have tunnelling to your server.

### Response
This will return you some JSON:

``` JSON
{
  "game-id": "04d209d5-3b9c-4d22-8559-5a8de14f13ad",
  "players": {},
  "friendly-name": "YOUR NEW GAME",
  "turn": 0,
  "pending-points": 0,
  "pending-dice": 0
}
```

## Adding some players
You need some players to start a game, but players can join an ongoing game as well:
### Request
``` bash
curl \
-X POST -H "Content-Type: application/json" \
-d '{"name": "some name"}' \
$DICE_HOST/games/$GAME_ID/players
```
### Response

``` JSON
{
  "message": "You've been added, some name. Use your player-id for rolling/passing.",
  "player-id": "2b9de4bc-a0e1-49b1-b35c-f625050ee548"
}
```
The `player-id` provided in the response is your 'secret token' that allows you to roll/keep/pass, so
anyone with this token can play for you. It won't be provided in basic game responses/fetches to other players.

## Playing
### Start
When you're ready, you must initiate the game with a `PUT` to `/games/$GAME_ID/start`.

### State
You can check who's turn it is and get other game-state info at any point with a
`GET` from `/games/$GAME_ID`.

## Actions
The actions available for the turn-player (`POST /games/$GAME_ID/players/$PLAYER_ID/:action-name`):
* roll - rolls all 6 dice unless `steal` parameter is passed in json post data, or you've already rolled
* keep - pick dice to score with and either keep rolling or pass under `keepers` param in json post body
* pass - freezes your current score and passes remaining dice to the next player