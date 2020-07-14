---
layout: post
title: Hacking Command & Conquer
category: Code
published: false
tags: Hacking C&C
---

My colleague and I were talking about retro games. A couple of minutes later we were battling a browser version of [Command & Conquer](https://www.adityaravishankar.com/projects/games/command-and-conquer) (1997). [A guy](https://www.adityaravishankar.com/2011/11/command-and-conquer-programming-an-rts-game-in-html5-and-javascript) actually recrated the game in HTML5 and JavaScript. Impressive 👌.

We played a game against each other, multiplayer mode. A couple of minutes later...

*"You have lost the match."* 

I lost the game 😭. Whatever, it's just a stupid 20 year old game someone recreated in JavaScript with an Node.js server. Hm... a client-side game? Let's look in to that.

Acording to Pablo Picasso we should
`“Learn the rules like a pro, so you can break them like an artist.”`

A older version of the source code were available on [Github](https://github.com/adityaravishankar/command-and-conquer). I analyzed the source code. Awesome to read the source code of the game (a rewrite of it) I was playing as a kid, written in just 15.000 lines. In Github it looks like I'm late to the party. No code changes last 7 years. But. I'm here now. Now I want to look at the actual code (not the open source). The online script were rewritten but not as open source. The script [cnc-0.8.3.b.js](https://www.adityaravishankar.com/projects/games/command-and-conquer/release/cnc-0.8.3.b.js) contained everything for the game client (not audio and images). Interesting!

Hey are people acutally still playing this game? Yes. Sometimes it takes a few minutes to find an opponent.

Let's see if the game is "hackable" 🧐. I came up with the following proof of concept for not displaying any fog on the map. Running directly in Chrome console:
```javascript
fog.draw = function() { };
```

Wola! It worked. I can see the full map by removing replacing the fog.draw() function. Enemy base and units are visible as well! 
For winning a game. I wouldn't come far with this. I stayed away from online gaming since addiction to Unreal Tournament in 1999. So winning an RTS game may be hard.

Okey, what if we can intercept the browsers request for cnc-0.8.3.b.js and run our own version of the script. This one we can fiddle with. Let's copy the JavaScript, prettify it, upload it to a own server, add a redirect (with a Chrome add-on) in the browser which redirected to mine script, instead of the website script like:

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

Like a laser in opponents base.

![Build anywhere](/public/images/cc-build.png "Build anywhere")

Allright. This would made you opponent lose his fighting spirit and think you are a "f*cking noob cheater".

There are some "prevention" in the client which not allow you going to crazy. A game hash like *_1487_461_8_459_3_549_6_598_606_* is sent with each server sendCommand genereated in the following fashion:
```javascript
var gameHash = "_" + game.gameTick + "_" + game.items.length + "_" + game.infantry.length + "_" + (game.infantry.length ? game.infantry[game.infantry.length - 1].uid + "_" : "0_") + game.vehicles.length + "_" + (game.vehicles.length ? game.vehicles[game.vehicles.length - 1].uid + "_" : "0_") + game.buildings.length + "_" + (game.buildings.length ? game.buildings[game.buildings.length - 1].uid + "_" : "0_") + game.counter + "_";
```

This isn't actually a hack preventation, it is a sync validation between the state between the players which send a "report_desync" command and report the current state to the server including the game items. The game will be over and you will get a message saying:
´Your game has gone out of sync with the other player. The administrator has been notified and he will review your game as soon as possible.´

It's cool how the game is built in a simple immutable way with game-ticks where all actions are pushed into the game state, for all players. These commands are replayd by all players and hopefully all clients have the save sync hash, else the game is ended. By executing the following in the console you can see the game state:

![Console](/public/images/cc-save.png "Console")

Hm... wounder if we can control opponents units by sending a command for his units. Let's try stopping the enemy harvester from harvesting, and just standing there.

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

I will stop fooling around here. We have enough knowlage winning a game. I tried a couple of games and replayed them. Now, as a high lever player 😈.


## Final words

Yes, this is just a old proof-of-concept game I stumbled upon. Good work Adityaravi Shankar writing it! Fun and educating playing around with the game and its concepts. I guess increasing security in a pure browser game will be hard, even if putting more logic at server side. It will still be possible to build auto-builders and other tools. If you gonna be a evil cheater as me I suggest you to be kind to your opponents and dont ruin to much of their fun ❤.
