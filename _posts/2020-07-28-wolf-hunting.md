---
layout: post
title: Wolf hunting strategy
category: Code
published: false
tags: wolf hunting
---

# Wolf hunting strategy

According to a [article](https://www.newscientist.com/article/mg21228354-700-wolf-packs-dont-need-to-cooperate-to-make-a-kill) a wolf pack can follow simple rules to pursue and  encircle its prey.

`Raymond Coppinger of Hampshire College in Amherst, Massachusetts, and colleagues modelled a five-strong wolf pack in pursuit of prey. They programmed each wolf to move towards the prey until it reached a certain safe distance. It then moved away from any other wolves that had reached that distance.`

The two suggest rules are:
 * Move towards the prey until it reached a certain safe distance.
 * Then moved away from any other wolves that had reached that distance.
 
 I decided to try the idéa.
 
 I previosly made an JavaScript lib making a guy being able to run around. The guy will represent a wolf. The lib works with a few functions like world.addGuy("Wolf 1", x, y), worl.guys[0].run(x, y).
 
I produced toe following JavaScript to represent the two rules.
 
```javascript
for (const guy of world.guys) {
    let distance = Helper.distance(guy.x, guy.y, x, y);
    if (distance > 80 && distance < 60) { // Achive safe distance - Between 60 and 80 pixels from mousepointer (x,y)
        let point = Helper.closetOutsideRadius(guy.x, guy.y, x, y, 60);
        guy.run(point.x, point.y);
    } else {                              // Move away from closest friend
        let neighbor = Helper.closestNeighbor(guy, world.guys);
        let p = Helper.addDistance(neighbor.x, neighbor.y, guy.x, guy.y, 10); // move away 10px parallel away from neighbor
        let point = Helper.closetOutsideRadius(p.x, p.y, x, y, 60); // move the point to radius 60px from mousepointer (x,y)
        guy.run(point.x, point.y);
    }
}
```
