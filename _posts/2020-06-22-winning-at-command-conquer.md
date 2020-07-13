---
layout: post
title: Winning at Command & Conquer
category: Code
published: false
tags: Hacking C&C
---

My colleague and I were talking about retro games. A couple of minutes later we were battling [Command & Conquer](https://www.adityaravishankar.com/projects/games/command-and-conquer) (1997) in the browser. [A guy](https://www.adityaravishankar.com/2011/11/command-and-conquer-programming-an-rts-game-in-html5-and-javascript) had actually recrated the game in HTML5 and JavaScript, impressive.

We played a game against each other, multiplayer mode. A couple of minutes later...

*"You have lost the match."*

I lost the game 😢. Let's move on with with life, its just stupid 20 year old game someone recreated in JavaScript with an Node.js server. Hah! A couple of hours later... hm... a client-side game? Let's look in to that.

`“Learn the rules like a pro, so you can break them like an artist.”`
― Pablo Picasso

A older version of the source code were available on [Github](https://github.com/adityaravishankar/command-and-conquer). I analyzed the source code. Awesome to read the source code of the game (anyway a copy of it) I was playing as a kid, written in just 15.000 lines. In Github it looks like I'm late to the party, no code changes here last 7 years. Whatever, I'm here now. Now I want to look at the actual code (not the open source). To script were rewritten but not as open source. The script [cnc-0.8.3.b.js](https://www.adityaravishankar.com/projects/games/command-and-conquer/release/cnc-0.8.3.b.js) contained everything for the game client (not audio and images). Interesting!

Is people acutally still playing this game today? Yes. Sometimes it takes a couple of minutes to find an opponent but no problem to find a opponent.

Let's see if the game is "hackable". I came up with the following code for not displaying any fog on the map. Chrome console:
```javascript
fog.draw = function() { };
```

Wola! It worked. I can see the full map by removing replacing the fog.draw() function. Enemy base and units are visible as well! 
I wouldn't come far with this, I suck at RTS-games. I stayed away from online gaming since addiction to Unreal Tournament in 1999.

Okey, what if we can intercept the browsers request for cnc-0.8.3.b.js (the gaming client) and run a own version of the script, which we can fiddle with. Let's copy the JavaScript, prettify it, upload it to a own server, add a redirect (with a add-on) in the browser which redirected to mine script, instead of the website script like:

`/release/cnc-0.8.3.b.js -> https://mysite.com/my-custom-cc-client.js`

It worked! So let's go crazy. I need all help I can get. After a couple of hours studying the code I came up with the following hacks to be usefull.

Map hack were very convinient
```javascript
//this.context.fillStyle = "rgba(0,0,0,1)"; //old line
this.context.fillStyle = "rgba(0,0,0,0.5)";
```

Show radar, even without communications center is built
```javascript
  //if (sidebar.mapEnabled && sidebar.mapMode) {
  if (true) { //show radar always
```

Maphack and radar from start result:
![Maphack](/public/images/cc_map.png "Maphack")

What can we do more? Allow build buildings and turrets anywere on map
```javascript
//if (sidebar.canBuildHere) {
if (true) {
```

Like a laser into opponent base.
![Build anywhere](/public/images/cc-build.png "Build anywhere")

Allright. This would made you opponent lose his fighting spirit and think you are a "f*cking noob cheater".

There are some "prevention" in the client which not allow you going to crazy. A game hash like *_1487_461_8_459_3_549_6_598_606_* is sent with each server sendCommand genereated by:
```javascript
var gameHash = "_" + game.gameTick + "_" + game.items.length + "_" + game.infantry.length + "_" + (game.infantry.length ? game.infantry[game.infantry.length - 1].uid + "_" : "0_") + game.vehicles.length + "_" + (game.vehicles.length ? game.vehicles[game.vehicles.length - 1].uid + "_" : "0_") + game.buildings.length + "_" + (game.buildings.length ? game.buildings[game.buildings.length - 1].uid + "_" : "0_") + game.counter + "_";
```

This isn't actually a hack preventation, it is a sync validation which send a "report_desync" command and report the current state to the server including the game items. The game will be over and you will get a message saying:
´Your game has gone out of sync with the other player. The administrator has been notified and he will review your game as soon as possible.´

It's cool how the game is built in a simple immutable way with game-ticks where all actions are pushed into the game state, for all players. These commands are replayd by all players and hopefully all clients have the save sync hash, else the game is ended. By writing the following in the console you can see the game state:

![Build anywhere](/public/images/cc-save.png "Build anywhere")

Hm... wounder if we can move opponents units by sending a command for his units. Let's try stopping the enemy harvester from harvesting, and just standing there.

```javascript
var eh = game.items.filter(i => i.name == "harvester" && i.player != game.player);
for (const h of eh) {
    game.sendCommand([h.uid], { type: "move", to: { "x": h.x, "y": h.y }});
}
```

Yup. It works. We can even make opponent sell one of his refinerys by writing the following in the console.

```javascript
var refinery = game.items.filter(i => i.name == "refinery" && i.player != game.player)[0];
game.sendCommand([refinery.uid], { type: "sell" });
```

Okey, I will stop hacking here. We have enough knowlage winning a game. Let's go for a game against a opponent.


## Conclusion

Yes, this is just a proof-of-concept game. Compared with compiled code application, an application with the source code easily accesible for the user makes it tricky for making sure the user is well behaved and not fiddling with the client. Even if we move more logic to the server side, the door is wide open for creating tools benefitting the user. However very fun and educating playing around.

