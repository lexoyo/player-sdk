# player-sdk
SDK to control and interact with the api.video HTML5 Player

# SDK usage

## Integration

First, include the SDK in your HTML file :
```html
    <head>
        ...
        <script src="https://<SDK location>/sdk.js" defer></script>
    </head>
```
We offer a hosted version of the SDK at https://embed.api.video/sdk.js

Then, once the `window.onload` event has been trigered, create your player using the `apiVideoSdk.create` method:
```html
    <script type="text/javascript">
        window.player = apiVideoSdk.create("#target", { 
            id: "<VIDEO_ID>", 
            // ... other optional options 
        });
    </script>
```

The `create` method takes 2 parameters:
- the ID of the DOM element in which you want to create the player
- An object containing the player options. The available options are the following:


Option name     | Mandatory             | Type      | Description
---:            | ---                   | ---       | ---
id              | **yes**               | string    | the id of the video
live            | no (default: false)   | boolean   | indicate that the video is a live one
autoplay        | no (default: false)   | boolean   | start playing the video as soon as it is loaded
muted           | no (default: false)   | boolean   | the video is muted

The `create` method returns a player instance that can be used to control the video playback, and to listen to player events.

## Controling the player

The instance obtained by calling to the `create` method has the following methods:

#### `play()` 
Start playing the video.
#### `pause()` 
Pause the video playback.
#### `mute()` 
Mute the video.
#### `unmute()` 
Unmute the video.
#### `seek(time: number)` 
Add/substract the given number of seconds to/from the playback time. 

Example:
```javascript
    player.seek(-15); // Go 15 seconds backward
``` 
#### `setVolume(volume: number)` 
Change the audio volume to the given value. From 0 to 1 (0 = muted, 1 = 100%).

Example:
```javascript
    player.volume(0.75); // Set the volume to 75% 
``` 
#### `destroy()` 
Destroy the player instance.

#### `addEventListener(event: string, callback: () => void)` 

Define a callback function that will be called when the given event is triggered by the player. 

Available events are the following:

Event name          | Description
---:                | --- 
ready               | The player is ready to play
firstplay           | The video started to play for the first time
play                | The video started to play (for the first time or after having been paused)
pause               | The video has been paused
ended               | The playback as reached the ended of the video
error               | An error occured
fullscreenchange    | The player goes to (or goes back from) full screen
playerresize        | The player size has changed
seeking             | The player is seeking

Example:
```javascript
    // listen to the 'play' event
    player.addEventListener('play', function() { 
        console.log('play event received'); 
    });
```

## Full example

```html
<html>
    <head>
        ...
        <script src="/sdk.js" defer></script>
    </head>

    <body>
        <div id="target"></div>

        <!-- buttons that call player methods to control the video playback -->
        <button onclick="javascript:player.play()"  id="play-btn">play</button>
        <button onclick="javascript:player.pause()" id="pause-btn" disabled>pause</button>
        <button onclick="javascript:player.mute()">mute</button>
        <button onclick="javascript:player.unmute()">unmute</button>
    </body>

    <script type="text/javascript">
        window.onload = function() {
            // create the player in the #target element
            window.player = apiVideoSdk.create("#target", {
                id: "123456"
            });

            // when the 'play' event is received, disable the 'play' button and enable the 'pause' button
            player.addEventListener('play', function() {
                document.getElementById('play-btn').disabled = true;
                document.getElementById('pause-btn').disabled = false;
            });

            // when the 'pause' event is received, disable the 'pause' button and enable the 'play' button
            player.addEventListener('pause', function() {
                document.getElementById('play-btn').disabled = false;
                document.getElementById('pause-btn').disabled = true;
            });
        };
    </script>
</html>
```

## Control an existing embedded player using the SDK

It's also possible to integrate the SDK in a page that already contains an embbeded player in order to control it and to listen to its events. Let's consider the following page :

```html
<html>
    <head>
        ...
    </head>

    <body>
        ...
        <!-- my embedded player -->
        <iframe src="//embed.api.video/vod/vi54sj9dAakOHJXKrUycCQZp" width="100%" height="100%" frameborder="0" allowfullscreen></iframe>
        ...
    </body>
</html>
```

To attach the SDK to this player, you'll have to make the following changed in your page:
    
- import the `sdk.js` script in your page,
- call  `apiVideoSdk.create` once the page is loaded.

Here is how the page will look like with these changes :

```html
<html>
    <head>
        ...
        <script src="/sdk.js" defer></script>
    </head>

    <body>
        ...
        <!-- my embedded player -->
        <iframe id="myPlayer" src="//embed.api.video/vod/vi54sj9dAakOHJXKrUycCQZp" width="100%" height="100%" frameborder="0" allowfullscreen></iframe>
        ...
    </body>

    <script type="text/javascript">
        window.onload = function() {
            // attach the sdk to the existing player
            window.player = apiVideoSdk.create("#myPlayer");

            // window.player can now be used to control the player as described above
        };
    </script>
</html>
```

URL Fragments
==================================

### Introduction

Some api.video Player's features may be activated with what's called `URL fragments`. 

In other words, it means that the Api Video embed (iFrame) source URL may be completed by query parameters introduced by a hash (#).

Here is the example of a Api Video embed code:
```
<iframe src="https://embed.api.video/vod/vi54sj9dAakOHJXKrUycCQZp" class="av_player" width="1280" height="720" frameborder="0" scrolling="no" allowfullscreen></iframe>
```

The source URL is `https://embed.api.video/vod/vi54sj9dAakOHJXKrUycCQZp`. 

Any fragment must be appened to the end of this URL after a hash `#`.

Example : `https://embed.api.video/vod/vi54sj9dAakOHJXKrUycCQZp#autoplay`

Multiple fragments may be concatenated with a semi-colon `;`.

Example : `https://embed.api.video/vod/vi54sj9dAakOHJXKrUycCQZp#autoplay;api`


### Autoplay

To launch video as soon as the player can, use `#autoplay`.


### Sequence

To start a video from `x`, use: `#t=x`.

To start a video from `x` and pause it at `y`, use: `#t=x,y`.

To start from the beginning and pause at `y`, use: `#t=,y`.

Time may be expressed in the following formats:
- `ss` (eg. `120` for 2 minutes)
- `mm:ss` (ex. `2:30` for two minutes and a half)
- `hh:mm:ss` (ex. `1:30:00` for one hour and a half)


### Allow API

To allow player to listen to API calls, use `#api`.


### Hide poster title

To hide the Player's title that is displayed on the bottom left corner of the poster, use `#hide-title`.


### Hide controls

To hide the Player's control bar, use `#hide-controls`.
Caution as you should integrate your own controls if you prevent users from accessing Api Video Player native ones.

### Force loop

To hide the Player's control bar, use `#loop`.
Caution as you should integrate your own controls if you prevent users from accessing Api Video Player native ones.


### Complex example

Here is a URL fragment that automatically launches a sequence (from 10 seconds to 15 seconds) and enables API calls:
```
<iframe src="https://embed.api.video/vod/vi54sj9dAakOHJXKrUycCQZp#t=10,15;autoplay;api;hide-title;hide-controls;loop" class="av_player" width="1280" height="720" frameborder="0" scrolling="no" allowfullscreen></iframe>
```
