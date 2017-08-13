---
layout: "post"
title: "Fish Move By The Magic Mathematics"
date: "2017-07-31 00:12"
---

# Interesting
Have you play a game named FishLord? It's a very popular mobile game in 2009, even now, a lot of people love with it. there are many fishes are moving, and you can shoot them with several guns, i love the game and especial interesting with the moving fishes.<!--more-->like this:

![](./media/img/fish.jpeg)

# Mathematics is magic
What should we do to make the fishes move along many kinds of paths. Mathematics is magic, let's start with some simple paths.

### Straight Lines
The easiest path is the fishes move straight left and right. In math it is y = a(x=a is moving up and down), more difficult will be y = ax + b, they will be moving straight with same director, so if you just need to set the a and b, the fish will move with straight line.

```cpp
Point StraightLineMove::next(float delta) {
    _calcPos = _curPos;

    _calcPos.x += _step.x*delta;
    _calcPos.y += _step.y*delta;

    return BaseMove::next(delta);
}
```

![](./media/img/fish_straight.png)

### Smooth Curve
as you know, straight line is too rigid, i want the fish more beautiful, so we will need create path with smooth curve. In the university, i have learned about curve fitting or polynomial interpolation which can make smooth curve. At this story i will use Lagrange interpolation polynomial, give me several points, i will give you a smooth curve.

![](./media/img/Lagrang1.svg)   ![](./media/img/Lagrang2.svg)

```cpp
Point LagrangeCurveMove::next(float delta) {
    _calcPos.x = _curPos.x;
    _calcPos.x += step*delta;

    if ((_points[0].x-_points[_points.size()-1].x)*(_calcPos.x-_points[_points.size()-1].x)<=0) {
        _prePos = _curPos;
        _curPos = _points[_points.size()-1];

        return _curPos;
    }

    _calcPos.y = 0;
    float i_total_numerator = 1;
    float k = _points.size();
    for (int j=0; j<k; ++j) {
        i_total_numerator *= (_calcPos.x-_points[j].x);
    }

    float i_total_denominator = 1;
    for (int j=0; j<_points.size(); ++j) {
        i_total_denominator = 1;
        for (int i=0; i<_points.size(); ++i) {
            if (i == j) continue;
            i_total_denominator *= (_points[j].x-_points[i].x);
        }
        _calcPos.y += _points[j].y*i_total_numerator/(_calcPos.x-_points[j].x)/i_total_denominator;
    }

    return BaseMove::next(delta);
}
```

![](./media/img/fish_lagrange.png)

### Bézier Curve
there is another way to make smooth curve called Bézier curve, the different with Lagrange interpolation polynomial is the curve will not pass the points except the first and the last one. What's the matter, it's also very beautiful.

![](./media/img/bezier.svg)

```cpp
float BezierCurveMove::getCombination(int i) {
    if (_points.size()<2 || i>=_points.size() || i<0) return -1;

    if (_combinations.size() <= 0) {
        int n = (int)_points.size()-1;
        float i_combination = 1;
        _combinations.push_back(i_combination);
        for (int k=0; k<=n; ++k)
        {
            i_combination *= 1.0f*(n-k)/(k+1);
            _combinations.push_back(i_combination);
        }
    }

    return _combinations[i];
}

Point BezierCurveMove::next(float delta) {
    _curTime += step*delta;

    int n = (int)_points.size()-1;
    if (_curTime >= 1) {
        _prePos = _curPos;
        _curPos = _points[n];
        return _curPos;
    }

    float k1 = powf(1-_curTime, n);    //(1-t)^(n-i)
    float k2 = 1;                            //t^i

    _calcPos = Point::ZERO;
    _calcPos.x = 0;
    _calcPos.y = 0;
    for (int i=0; i<=n; ++i) {
        float k = getCombination(i)*k1*k2;
        _calcPos.x += k*_points[i].x;
        _calcPos.y += k*_points[i].y;

        k1 /= (1-_curTime);
        k2 *= _curTime;
    }

    return BaseMove::next(delta);
}
```

![](./media/img/fish_bezier.png)

### Parametric Equation
if you know more about math, you will find polar coordinate equation will make the fish move with a lot of magical paths. Here is some example:

### Common Polar

```cpp
bool PolarMove::setTotalTime(float time) {
    if (!BaseMove::setTotalTime(time)) return false;

    _step = 2*M_PI/_totalTime;
    return true;
}

Point PolarMove::getPosByTheta(float theta) {
    float r = getRadius(theta);
    return Point(_origin.x+r*cosf(theta), _origin.y+r*sinf(theta));
}

Point PolarMove::next(float delta, bool fix) {
    _theta += _step*delta;

    _calcPos = getPosByTheta(_theta);

    return BaseMove::next(delta, fix);
}


bool PolarMove::isEnd() {
    return _theta >= 2*M_PI;
}

void PolarMove::setOrigin(cocos2d::Vec2 origin) {
    _origin = origin;

    _curPos = getPosByTheta(_theta);
    _prePos = _curPos;
}
```

### Archimedean Spiral

r = 12*θ (0=<θ<10π)

```cpp
const float spiral_size = 12;

bool SpiralMove::isEnd() {
    return _theta >= 10*M_PI;
}

float SpiralMove::getRadius(float theta) {
    return spiral_size*_theta;
}
```

![](./media/img/fish_spiral.png)

### Descartes's Love

r=200*(1-sinθ) (0=<θ<2π)

```cpp
const float heart_size = 200;

float HeartMove::getRadius(float theta) {
    return heart_size*(1-sinf(_theta));
}
```

![](./media/img/fish_heart.png)

### Rose

r = 350*sin2θ (0=<θ<2π)

```cpp
const float rose_size = 350;

float RoseMove::getRadius(float theta) {
    return rose_size*sinf(theta*2);
}
```

![](./media/img/fish_rose.png)

### Lemniscate of Bernoulli

r^2 = 2500*cos(2θ) (0=<θ<2π)

```cpp
const float lemniscate_size = 500;

float LemniscateMove::getRadius(float theta) {
    return lemniscate_size*sqrtf(cosf(theta*2));
}

Point LemniscateMove::next(float delta, bool fix) {
    _theta += _step*delta;

    if (_theta >= 2*M_PI) {
        _prePos = _curPos;
        _curPos = _origin;

        return _curPos;
    }


    if (_theta>=M_PI/4 && _theta<=M_PI*3/4) {
        _calcPos = _origin;
    } else {
        _calcPos = getPosByTheta(_theta);
    }

    _curPos = BaseMove::next(delta, fix);

    if (!fix) {
        if (_theta>=M_PI/4 && _theta<=M_PI*3/4) {
            if (_step > 0) {
                _theta = M_PI*5/4;
            } else {
                _theta = M_PI*7/4;
            }
            _step *= -1;
        }
    }

    return _curPos;
}
```

![](./media/img/fish_lemniscate.png)

# Angle

Now we have create the paths, but the angle with fish which is not clear. One solution is to calculate derivative of the function which describes the director and speed of a position. Unfortunately sometimes the derivative is very hard to find out, it's the most accurate but not the best. So we need to know the real means with derivative is when you divide the path into several small pieces which you can't saw it with your eyes, connect the start position and end position will be the value of derivative, we don't need so accurate because fishes is moving you won't find out the defect, we get the position along the path step by step, so the angle will be calculate by each point and the next point.
```cpp
float BaseMove::getAngle() {
    Point dir = Vec2(_curPos.x-_prePos.x, _curPos.y-_prePos.y);

    // Translate to rotation of the fish
    if (fabs(dir.x) < 0.0001) {
        return dir.y>0 ? 90 : 270;
    } else if (fabs(dir.y) < 0.0001) {
        return dir.x>0 ? 0 : 180;
    } else {
        return (float)(atan2f(dir.y, dir.x)*180.0f/M_PI);
    }
}
```

# Uniform Motion

If you make the fish move along the path, you will find the distance which fish move is not always the same step by step. you will get the answer like this: 10, 20, 80, 5, 21, it looks really bad. How can we make the fish move with uniform motion? Make the fish more slower if you found fish move too quick and otherwise more faster, what a simple answer it is! but how to do it.

> Do you remember binary search? Yes the answer you know is if you find fish move too quick, make the fish go back half of the step, otherwise make the fish go forward, and then do again with the new step, several times you will make the fish move with uniform motion.

```cpp
const float move_dis_min = 2;
const float move_dis_max = 5;

Point BaseMove::next(float delta) {
    float dis = (_calcPos.x-_curPos.x)*(_calcPos.x-_curPos.x) + (_calcPos.y-_curPos.y)*(_calcPos.y-_curPos.y);
    if (fabsf(delta) > 0.0001) {
        if (dis < move_dis_min) {
            return next(0.5f * fabsf(delta), true);
        } else if (dis > move_dis_max) {
            return next(-0.5f * fabsf(delta), true);
        }
    }

    _prePos = _curPos;
    _curPos = _calcPos;
    return _curPos;
}
```

# Mathematics is a magic

She can create more beautiful and magical path, i just implement above at [Github FishMove](https://github.com/freeblank/fish_move), you can fork it and create more path
