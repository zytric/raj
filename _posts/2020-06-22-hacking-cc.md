---
layout: post
title: Hacking C&C
category: Code
tags: Hacking C&C
---

My colleague (working as an Test Manager) and I were talking about gaming memories. A couple of minutes later we were battling [C&C](https://www.adityaravishankar.com/projects/games/command-and-conquer) (1997) in the browser. [A guy](https://www.adityaravishankar.com/2011/11/command-and-conquer-programming-an-rts-game-in-html5-and-javascript) had actually recrated the game in HTML5 and JavaScript, impressive.

We played a game against each other, multiplayer mode. That was a real retro feeling. However...

*"You have lost the match."*

``sounds.play("mission_failure");``

What!? Losing? Against my collegue? That sucks. How should I live with myself? Okey. Let's move on with life, its just stupid 23 year old  game. A couple of hours later, i really couldn't let it go. 

Let's look under the hood. A older version of the source code were available on [github](https://github.com/adityaravishankar/command-and-conquer). I analyzed the source code. Intersting to read the source code of the game I were playing as a kid written in 15.000 lines of JavaScript code. In Github it looks like I am 7 years late to the party. Now I want to look at the actual code (not open source), that we were playing. The script [https://www.adityaravishankar.com/projects/games/command-and-conquer/release/cnc-0.8.3.b.js](cnc-0.8.3.b.js) contained everything intresting (not audio and images).

Let's see if the game is "hackable". I came up with the following code for not displaying any fog on the map. Chrome console:
`fog.draw = function() { // no more fog rendering };`

Wola! It worked. I can see the full map.

Okey, from now on let's try to run a own version of the script, which I can "hack"/fiddle with. I copied the JavaScript, prettified, add a redirect in Chrome which redirected the:
`/release/cnc-0.8.3.b.js -> https://xxx.xx/myCC.js`

