---
layout: "post"
title: "Fish Move"
date: "2017-07-31 00:12"
---

# Interesting
Have you play a game named FishLord? It's a very popular mobile game in 2009, even now, a lot of people love with it. there are many fishes are moving, and you can shoot them with several guns, i love the game and especial interesting with the moving fishes.<!--more-->like this:

![](./media/img/fish.jpeg)

# On the way
What should we do to make the fishes move along many kinds of paths. Mathematics is a magic, let's start with some simple paths.

### Straight Lines
The easiest path is the fishes move straight left and right. In math it is y = a(x=a is moving up and down), more difficult will be y = ax + b, they will be moving straight with same director, so if you just need to set the a and b, the fish will move with straight line.

![](./media/img/fish_straight.png)

### Smooth Curve
as you know, straight line is too rigid, i want the fish more beautiful, so we will need create path with smooth curve. In the university, i have learned about curve fitting or polynomial interpolation which can make smooth curve. At this story i will use Lagrange interpolation polynomial, give me several points, i will give you a smooth curve.

![](./media/img/Lagrang1.svg)   ![](./media/img/Lagrang2.svg)

### Bézier Curve
there is another way to make smooth curve called Bézier curve, the difficult with Lagrange interpolation polynomial is the curve will not pass the points except the first and the last one. What's the matter, it's also very beautiful.

![](./media/img/bezier.svg)

![](./media/img/fish_bezier.png)

### Parametric Equation
if you know more about math, you will find parametric equation will make the fish move with a lot of magical paths.

### Polar Coordinate Equation
Another magical path can be create by polar coordinate equation.

Mathematics is a magic, it can create more beautiful and magical path, you can do more, i just implement above at [Github FishMove](https://github.com/freeblank/fish_move), you can fork it and create more path
