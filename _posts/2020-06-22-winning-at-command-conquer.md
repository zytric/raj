---
layout: post
title: Winning at Command & Conquer
category: Code
published: false
tags: Hacking C&C
---

My colleague and I were talking about gaming memories. A couple of minutes later we were battling [Command & Conquer](https://www.adityaravishankar.com/projects/games/command-and-conquer) (1997) in the browser. [A guy](https://www.adityaravishankar.com/2011/11/command-and-conquer-programming-an-rts-game-in-html5-and-javascript) had actually recrated the game in HTML5 and JavaScript, impressive.

We played a game against each other, multiplayer mode. That was a retro feeling. A couple of minutes later...

*"You have lost the match."*

I lost the game 😢. Losing sucks. How should I live with myself? Just kidding, let's move on with with life, its just stupid 20 year old game someone recreated in JavaScript with an Node.js server. Hah! A couple of hours later... hm... a client-side game? Let's look in to that.

A older version of the source code were available on [Github](https://github.com/adityaravishankar/command-and-conquer). I analyzed the source code. Awesome to read the source code of the game (anyway a copy of it) I was playing as a kid, written in just 15.000 lines. In Github it looks like Im late to the party, no code changes here last 7 years. Whatever, I'm here to party now. Now I want to look at the actual code (not the open source). To script were rewritten but not relased. The script [cnc-0.8.3.b.js](https://www.adityaravishankar.com/projects/games/command-and-conquer/release/cnc-0.8.3.b.js) contained everything for the game client (not audio and images). Interesting!

Is people acutally still playing this game? Yes. It takes a couple of minutes to find an opponent but people still play.

Let's see if the game is "hackable". I came up with the following code for not displaying any fog on the map. Chrome console:
```javascript
fog.draw = function() { };
```

Wola! It worked. I can see the full map by removing the fog.draw function. Enemy base and units are visible as well! However. I wouldn't come far with this, I suck at RTS-games. I stayed away from online gaming since Unreal Tournament (1999).

Okey, what if we can intercept the browser request for cnc-0.8.3.b.js (the gaming client) and run a own version of the script, which we fiddle with. Let's copy the JavaScript, prettify it, upload it to a own server, add a redirect (with a add-on) in the browser which redirected to mine script, instead of the website script like:

`/release/cnc-0.8.3.b.js -> https://mysite.com/my-custom-cc-client.js`

It worked! So let's go crazy. I need all help I can get. After a couple of hours I came up with the following hacks to be usefull.

Transparent fog
```javascript
//this.context.fillStyle = "rgba(0,0,0,1)";
this.context.fillStyle = "rgba(0,0,0,0.5)";
```

Can build buildings and turrets anywere on map
```javascript
//if (sidebar.canBuildHere) {
if (true) {
```

Show radar, even without communications center
```javascript
  //if (sidebar.mapEnabled && sidebar.mapMode) {
  if (true) { //show radar always
```

Allow building everything
```javascript
var buildings = {
    type: "buildings",
    list: {
        "temple-of-nod": {
            //dependency: ["construction-yard", "power-plant|advanced-power-plant", "refinery", "communications-center"],
            dependency: [], // Tada!
```

There are some "prevention" in the client which not allow going to crazy. A game hash like *_1487_461_8_459_3_549_6_598_606_* is sent with each server sendCommand genereated by:
```javascript
var gameHash = "_" + game.gameTick + "_" + game.items.length + "_" + game.infantry.length + "_" + (game.infantry.length ? game.infantry[game.infantry.length - 1].uid + "_" : "0_") + game.vehicles.length + "_" + (game.vehicles.length ? game.vehicles[game.vehicles.length - 1].uid + "_" : "0_") + game.buildings.length + "_" + (game.buildings.length ? game.buildings[game.buildings.length - 1].uid + "_" : "0_") + game.counter + "_";
```

If this hash isn't valid the server will send a "report_desync" command and report the current state to the server including the game.items. The game will be over and you will get a message saying:
´Your game has gone out of sync with the other player. The administrator has been notified and he will review your game as soon as possible.´

Hm.. wounder if we can move opponents units. Oh yes we can. Stop enemy harvester from harvesting, and just standing there.

```javascript
var eh = game.items.filter(i => i.name == "harvester" && i.player != game.player);
for (const h of eh) {
    game.sendCommand([h.uid], { type: "move", to: { "x": h.x, "y": h.y }});
}
```

Make opponent sell one of his refinerys
```javascript
var refinery = game.items.filter(i => i.name == "refinery" && i.player != game.player)[0];
game.sendCommand([refinery.uid], { type: "sell" });
```

## Conclusion

Yes, this is just a proof-of-concept game. Compared with compiled code application, an application with the source code easily accesible for the user makes it tricky for making sure the user is well behaved and not fiddling with the client. Even if we move more logic to the server side, the door is wide open for creating tools benefitting the user. 

