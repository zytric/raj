---
layout: post
title: Hacking Command & Conquer (1997)
category: Code
tags: Hacking C&C
---

My colleague (working as an Test Manager) and I were talking about gaming memories. A couple of minutes later we were battling [C&C](https://www.adityaravishankar.com/projects/games/command-and-conquer) (1997) in the browser. [A guy](https://www.adityaravishankar.com/2011/11/command-and-conquer-programming-an-rts-game-in-html5-and-javascript) had actually recrated the game in HTML5 and JavaScript, impressive.

We played a game against each other, multiplayer mode. That was a real retro feeling. However...

*"You have lost the match."*

``sounds.play("mission_failure");``

I lost the game. What!? Losing? That sucks. How should I live with myself? Okey. Let's move on with with life, its just stupid 23 year old game someone recreated in JavaScript. A couple of hours later... hm.. in JavaScript? Let's look in to that...

A older version of the source code were available on [Github](https://github.com/adityaravishankar/command-and-conquer). I analyzed the source code. Intersting and fun to read the source code of the game I was playing as a kid, written in just 15.000 lines. In Github it looks like I'm 7 years late to the party. Whatever, I'm here now. Now I want to look at the actual code (not open source), that we were playing. The script [cnc-0.8.3.b.js](https://www.adityaravishankar.com/projects/games/command-and-conquer/release/cnc-0.8.3.b.js) contained everything for the game client (not audio and images).

Let's see if the game is "hackable". I came up with the following code for not displaying any fog on the map. Chrome console:
```javascript
fog.draw = function() { }; // no more fog rendering >:)
```

Wola! It worked. I can see the full map. Enemy base and units as well! However. I wouldn't come far with this, I suck at RTS-games. Stayed away from online gaming since Unreal Tournament (1999) to not get adicted again.

Okey, wounde if we can intercept the browser request and run a own version of the script, which we fiddle with. Let's copy the JavaScript, prettify it, upload it to a url, add a redirect (with a add-on) in Chrome which redirected to mine script, instead of the website script:

`/release/cnc-0.8.3.b.js -> https://xxxx.com/my-custom-cc-client.js`

It worked! So let's go crazy. I need all help I can get. After a couple of hours I came up with the following hacks.

Hack #1 - Transparent fog:
```javascript
//this.context.fillStyle = "rgba(0,0,0,1)";
this.context.fillStyle = "rgba(0,0,0,0.5)";
```

Hack #2 - Can build buildings and turrets anywere on map:
```javascript
//if (sidebar.canBuildHere) {
if (true) {
```

Hack #3 - Show radar, even without communications center
```javascript
  //if (sidebar.mapEnabled && sidebar.mapMode) {
  if (true) { //show radar always
```

Hack #4 - Allow building everything
```javascript
var buildings = {
    type: "buildings",
    list: {
        "temple-of-nod": {
            //dependency: ["construction-yard", "power-plant|advanced-power-plant", "refinery", "communications-center"],
            dependency: [], // Tada!
```
