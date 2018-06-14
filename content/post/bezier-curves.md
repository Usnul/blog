---
date: 2016-12-29
published: true
status: publish
title: Bezier Curves from the Ground Up
type: post
image: /images/bezier/og_image.png
description: >
  Ever wondered how bezier curves work? It's surprisingly simple!
---

<link rel="stylesheet" 
href="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.6.0/katex.min.css">

How do you describe a straight line segment? We might think about a line segment 
in terms of its endpoints. Let's call those endpoints \\( P_0 \\) and \\( P_1 
\\).

<svg viewBox="0 0 600 200" preserveAspectRatio="xMinYMin meet" xmlns="http://www.w3.org/2000/svg">
    <path d="M50 50 L550 150" stroke="black" stroke-width="2"/>
    <g transform="translate(50, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">0</tspan>
        </text>
    </g>
    <g transform="translate(550, 150)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">1</tspan>
        </text>
    </g>
</svg>

To define the line segment rigorously, we might say "the set of all points along 
the line through \\( P_0 \\) and \\( P_1 \\) which lie between \\( P_0 \\) and 
\\( P_1 \\)", or perhaps this:

$$
L(t) = (1 - t) P_0 + t P_1, 0 \le t \le 1
$$

Conveniently, this definition let's us easily find the coordinate of the point 
any portion of the way along that line segment. The midpoint, for instance, lies 
at \\( L(0.5) \\).

<svg viewBox="0 0 600 200" preserveAspectRatio="xMinYMin meet" xmlns="http://www.w3.org/2000/svg">
    <path d="M50 50 L550 150" stroke="black" stroke-width="2"/>
    <g transform="translate(50, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">0</tspan>
        </text>
    </g>
    <g transform="translate(550, 150)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">1</tspan>
        </text>
    </g>
    <g transform="translate(300, 100)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="-20" y="30" font-family="KaTeX_Math">
            L(0.5)
        </text>
    </g>
</svg>

<div>$$
L(0.5) = (1 - 0.5) P_0 + 0.5 P_1 = \begin{bmatrix}
    0.5(P_{0_x} + P_{1_x}) \\
    0.5(P_{0_y} + P_{1_y})
\end{bmatrix}
$$</div>

We can, in fact, *linearly interpolate* to any value we want between the two 
points, with arbitrary precision. This allows us to do fancier things, like 
trace the line by having the \\( t \\) in \\( L(t) \\) be a function of time.

<svg viewBox="0 0 600 200" preserveAspectRatio="xMinYMin meet" xmlns="http://www.w3.org/2000/svg">
    <path d="M50 50 L550 150" stroke="black" stroke-width="2"/>
    <path d="M50 50 L50 50" stroke="#EB5757" stroke-width="2" id="p1" />
    <g transform="translate(50, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">0</tspan>
        </text>
    </g>
    <g transform="translate(550, 150)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">1</tspan>
        </text>
    </g>
    <g transform="translate(50, 50)" id="Lg1">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="-20" y="30" font-family="KaTeX_Math" id="Lt1">
            L(0.5)
        </text>
    </g>
</svg>

<script>
(function() {
    var t = 0;
    var dt = 0.004;

    var x0 = 50;
    var y0 = 50;
    var x1 = 550;
    var y1 = 150;

    var Lg1 = document.getElementById("Lg1");
    var Lt1 = document.getElementById("Lt1");
    var p1 = document.getElementById("p1");

    (function next() {
        t += dt;
        if (t < 0 || t > 1) {
            dt *= -1;
            t = Math.min(1, Math.max(0, t));
        }

        var x = x0 + t * (x1 - x0);
        var y = y0 + t * (y1 - y0);

        Lg1.setAttribute("transform",  "translate(" + x + "," + y + ")");
        Lt1.innerHTML = "L(" + t.toFixed(2) + ")";
        p1.setAttribute("d", "M50 50 L" + x.toFixed(2) + " " + y.toFixed(2));

        requestAnimationFrame(next);
    })();
})();
</script>

If you got this far, you might now be wondering, "What does this have to do with 
curves?". Well, it seems quite intuitive that you can precisely describe a line 
segment with only two points. How might you go about precisely describing this?

<svg viewBox="0 0 600 200" preserveAspectRatio="xMinYMin meet" xmlns="http://www.w3.org/2000/svg">
    <path d="M50 50 Q550 150 550 50" stroke="black" fill="none" stroke-width="2"/>
</svg>

It turns out that this *particular* kind of curve can be described by only 3 
points!

<svg viewBox="0 0 600 200" preserveAspectRatio="xMinYMin meet" xmlns="http://www.w3.org/2000/svg">
    <path d="M50 50 Q550 150 550 50" stroke="black" fill="none" stroke-width="2"/>
    <path d="M50 50 L550 150" stroke="black" fill="none" stroke-width="1" stroke-dasharray="1, 2" />
    <path d="M550 150 L 550 50" stroke="black" fill="none" stroke-width="1" stroke-dasharray="1, 2" />
    <g transform="translate(50, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">0</tspan>
        </text>
    </g>
    <g transform="translate(550, 150)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">1</tspan>
        </text>
    </g>
    <g transform="translate(550, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">2</tspan>
        </text>
    </g>
</svg>

This is called a *Quadratic Bezier Curve*. A line segment, donning a fancier 
hat, might be called a *Linear Bezier Curve*. Let's investigate why.

First, let's consider what it looks like when we interpolate between \\( P_0 \\) 
and \\( P_1 \\) while simultaneously interpolating between \\( P_1 \\) and \\( 
P_2 \\).

<svg viewBox="0 0 600 200" preserveAspectRatio="xMinYMin meet" xmlns="http://www.w3.org/2000/svg" id="quad1">
    <path d="M50 50 L550 150" stroke="black" fill="none" stroke-width="1" 
    stroke-dasharray="1, 2" />
    <path d="M550 150 L 550 50" stroke="black" fill="none" stroke-width="1" stroke-dasharray="1, 2" />
    <path d="" stroke="#EB5757" fill="none" stroke-width="1" class="p01" />
    <path d="" stroke="#EB5757" fill="none" stroke-width="1" class="p12" />
    <g transform="translate(50, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">0</tspan>
        </text>
    </g>
    <g transform="translate(550, 150)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">1</tspan>
        </text>
    </g>
    <g transform="translate(550, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">2</tspan>
        </text>
    </g>
    <g transform="translate(50, 50)" class="gB01">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="-20" y="30" font-family="KaTeX_Math">
            B<tspan baseline-shift="sub" font-size="70%">0,1</tspan>(<tspan class="t01">0.00</tspan>)
        </text>
    </g>
    <g transform="translate(550, 150)" class="gB12">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="-90" y="0" font-family="KaTeX_Math">
            B<tspan baseline-shift="sub" font-size="70%">1,2</tspan>(<tspan class="t12">0.00</tspan>)
        </text>
    </g>
</svg>

<script>
(function() {
    var t = 0;
    var dt = 0.004;

    var x0 = 50;
    var y0 = 50;
    var x1 = 550;
    var y1 = 150;
    var x2 = 550;
    var y2 = 50;

    var quad1 = document.getElementById("quad1");
    var gB01 = quad1.querySelector(".gB01");
    var gB12 = quad1.querySelector(".gB12");
    var t01 = quad1.querySelector(".t01");
    var t12 = quad1.querySelector(".t12");
    var p01 = quad1.querySelector(".p01");
    var p12 = quad1.querySelector(".p12");

    (function next() {
        t += dt;
        if (t < 0 || t > 1) {
            dt *= -1;
            t = Math.min(1, Math.max(0, t));
        }

        var x01 = x0 + t * (x1 - x0);
        var y01 = y0 + t * (y1 - y0);

        gB01.setAttribute("transform",  "translate(" + x01 + "," + y01 + ")");
        t01.innerHTML = t.toFixed(2);
        p01.setAttribute("d", "M" + x0 + " " + y0 + " L" + x01.toFixed(2) + " " + y01.toFixed(2));

        var x12 = x1 + t * (x2 - x1);
        var y12 = y1 + t * (y2 - y1);

        gB12.setAttribute("transform",  "translate(" + x12 + "," + y12 + ")");
        t12.innerHTML = t.toFixed(2);
        p12.setAttribute("d", "M" + x1 + " " + y1 + " L" + x12.toFixed(2) + " " + y12.toFixed(2));

        requestAnimationFrame(next);
    })();
})();
</script>

<div>$$
\begin{aligned}
B_{0,1}(t) = (1 - t) P_0 + t P_1, 0 \le t \le 1 \\
B_{1,2}(t) = (1 - t) P_1 + t P_2, 0 \le t \le 1 \\
\end{aligned}
$$</div>

Now let's linearly interpolate between \\( B\_{0, 1}(t) \\) and \\( B\_{1, 2}(t) 
\\)...

<svg viewBox="0 0 600 200" preserveAspectRatio="xMinYMin meet" xmlns="http://www.w3.org/2000/svg" id="quad2">
    <path d="M50 50 L550 150" stroke="black" fill="none" stroke-width="1" 
    stroke-dasharray="1, 2" />
    <path d="M550 150 L 550 50" stroke="black" fill="none" stroke-width="1" stroke-dasharray="1, 2" />
    <path d="" stroke="#EB5757" fill="none" stroke-width="1" class="p01" />
    <path d="" stroke="#EB5757" fill="none" stroke-width="1" class="p12" />
    <path d="" stroke="black" fill="none" stroke-width="0.5" class="p012" />
    <path d="" stroke="#27AE60" fill="none" stroke-width="1.5" class="pB012" />
    <g transform="translate(50, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">0</tspan>
        </text>
    </g>
    <g transform="translate(550, 150)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">1</tspan>
        </text>
    </g>
    <g transform="translate(550, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">2</tspan>
        </text>
    </g>
    <g transform="translate(50, 50)" class="gB01">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
    </g>
    <g transform="translate(550, 150)" class="gB12">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
    </g>
    <g transform="translate(550, 150)" class="gB012">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="-20" y="30" font-family="KaTeX_Math">
            B<tspan baseline-shift="sub" font-size="70%">0,1,2</tspan>(<tspan class="t012">0.00</tspan>)
        </text>
    </g>
</svg>

<div>$$
\begin{aligned}
B_{0,1,2}(t) = (1 - t) B_{0,1}(t) + t B_{1,2}(t), 0 \le t \le 1 \\
\end{aligned}
$$</div>

<script>
(function() {
    var t = 0;
    var dt = 0.004;

    var x0 = 50;
    var y0 = 50;
    var x1 = 550;
    var y1 = 150;
    var x2 = 550;
    var y2 = 50;

    var quad = document.getElementById("quad2");
    var gB01 = quad.querySelector(".gB01");
    var gB12 = quad.querySelector(".gB12");
    var gB012 = quad.querySelector(".gB012");
    var p01 = quad.querySelector(".p01");
    var p12 = quad.querySelector(".p12");
    var p012 = quad.querySelector(".p012");
    var pB012 = quad.querySelector(".pB012");
    var t012 = quad.querySelector(".t012");

    (function next() {
        t += dt;
        if (t < 0 || t > 1) {
            dt *= -1;
            t = Math.min(1, Math.max(0, t));
        }

        var x01 = x0 + t * (x1 - x0);
        var y01 = y0 + t * (y1 - y0);

        gB01.setAttribute("transform",  "translate(" + x01 + "," + y01 + ")");
        p01.setAttribute("d", "M" + x0 + " " + y0 + " L" + x01.toFixed(2) + " " + y01.toFixed(2));

        var x12 = x1 + t * (x2 - x1);
        var y12 = y1 + t * (y2 - y1);

        gB12.setAttribute("transform",  "translate(" + x12 + "," + y12 + ")");
        p12.setAttribute("d", "M" + x1 + " " + y1 + " L" + x12.toFixed(2) + " " + y12.toFixed(2));

        var x012 = x01 + t * (x12 - x01);
        var y012 = y01 + t * (y12 - y01);

        t012.innerHTML = t.toFixed(2);

        gB012.setAttribute("transform",  "translate(" + x012 + "," + y012 + ")");

        pB012.setAttribute("d", "M" + x01.toFixed(2) + " " + y01.toFixed(2) + " " +
                                "L" + x012.toFixed(2) + " " + y012.toFixed(2));

        p012.setAttribute("d", "M" + x01.toFixed(2) + " " + y01.toFixed(2) + " " +
                               "L" + x12.toFixed(2) + " " + y12.toFixed(2));

        requestAnimationFrame(next);
    })();
})();
</script>

Notice that the equation for \\( B\_{0, 1, 2}(t) \\) looks remarkably similar to 
the equations for \\( B\_{0, 1} \\) and \\( B\_{1, 2} \\). Let's see what 
happens when we trace the path of \\( B\_{0, 1, 2}(t) \\).

<svg viewBox="0 0 600 200" preserveAspectRatio="xMinYMin meet" 
    xmlns="http://www.w3.org/2000/svg" id="quad3">
    <path d="M50 50 Q550 150 550 50" stroke="#2F80ED" fill="none" 
    stroke-width="2" stroke-dasharray="100%" class="curve"/>
    <path d="M50 50 L550 150" stroke="black" fill="none" stroke-width="1" 
    stroke-dasharray="1, 2" />
    <path d="M550 150 L 550 50" stroke="black" fill="none" stroke-width="1" stroke-dasharray="1, 2" />
    <path d="" stroke="#EB5757" fill="none" stroke-width="1" class="p01" />
    <path d="" stroke="#EB5757" fill="none" stroke-width="1" class="p12" />
    <path d="" stroke="black" fill="none" stroke-width="0.5" class="p012" />
    <path d="" stroke="#27AE60" fill="none" stroke-width="1.5" class="pB012" />
    <g transform="translate(50, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">0</tspan>
        </text>
    </g>
    <g transform="translate(550, 150)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">1</tspan>
        </text>
    </g>
    <g transform="translate(550, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">2</tspan>
        </text>
    </g>
    <g transform="translate(50, 50)" class="gB01">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
    </g>
    <g transform="translate(550, 150)" class="gB12">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
    </g>
    <g transform="translate(550, 150)" class="gB012">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
    </g>
</svg>

<script>
(function() {
    var t = 0;
    var dt = 0.004;

    var x0 = 50;
    var y0 = 50;
    var x1 = 550;
    var y1 = 150;
    var x2 = 550;
    var y2 = 50;

    var quad = document.getElementById("quad3");
    var gB01 = quad.querySelector(".gB01");
    var gB12 = quad.querySelector(".gB12");
    var gB012 = quad.querySelector(".gB012");
    var p01 = quad.querySelector(".p01");
    var p12 = quad.querySelector(".p12");
    var p012 = quad.querySelector(".p012");
    var pB012 = quad.querySelector(".pB012");
    var curve = quad.querySelector(".curve");
    var curveLength = curve.getTotalLength();

    curve.setAttribute("stroke-dasharray", curveLength);

    (function next() {
        t += dt;
        if (t < 0 || t > 1) {
            dt *= -1;
            t = Math.min(1, Math.max(0, t));
        }

        var x01 = x0 + t * (x1 - x0);
        var y01 = y0 + t * (y1 - y0);

        gB01.setAttribute("transform",  "translate(" + x01 + "," + y01 + ")");
        p01.setAttribute("d", "M" + x0 + " " + y0 + " L" + x01.toFixed(2) + " " + y01.toFixed(2));

        var x12 = x1 + t * (x2 - x1);
        var y12 = y1 + t * (y2 - y1);

        gB12.setAttribute("transform",  "translate(" + x12 + "," + y12 + ")");
        p12.setAttribute("d", "M" + x1 + " " + y1 + " L" + x12.toFixed(2) + " " + y12.toFixed(2));

        var x012 = x01 + t * (x12 - x01);
        var y012 = y01 + t * (y12 - y01);

        gB012.setAttribute("transform",  "translate(" + x012 + "," + y012 + 
        ")");

        pB012.setAttribute("d", "M" + x01.toFixed(2) + " " + y01.toFixed(2) + " " +
                                "L" + x012.toFixed(2) + " " + y012.toFixed(2));

        p012.setAttribute("d", "M" + x01.toFixed(2) + " " + y01.toFixed(2) + " " +
                               "L" + x12.toFixed(2) + " " + y12.toFixed(2));

        curve.setAttribute("d", "M" + x0 + " " + y0 + " " +
                                "Q" + x01 + " " + y01 + " " + x012 + " " + y012);

        requestAnimationFrame(next);
    })();
})();
</script>

We get our curve!

<svg viewBox="0 0 600 200" preserveAspectRatio="xMinYMin meet" 
    xmlns="http://www.w3.org/2000/svg">
    <path d="M50 50 Q550 150 550 50" stroke="black" fill="none" stroke-width="2"/>
    <path d="M50 50 L550 150" stroke="black" fill="none" stroke-width="1" stroke-dasharray="1, 2" />
    <path d="M550 150 L 550 50" stroke="black" fill="none" stroke-width="1" stroke-dasharray="1, 2" />
    <g transform="translate(50, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">0</tspan>
        </text>
    </g>
    <g transform="translate(550, 150)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">1</tspan>
        </text>
    </g>
    <g transform="translate(550, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">2</tspan>
        </text>
    </g>
</svg>

# Higher Order Bezier Curves

Just as we get a quadratic bezier by interpolating between two linear bezier 
curves, we get a <span style="color:#9B51E0">cubic bezier curve</span> by 
interpolating between two <span style="color:#2F80ED">quadratic bezier 
curves</span>:

<svg viewBox="0 0 600 200" preserveAspectRatio="xMinYMin meet" 
    xmlns="http://www.w3.org/2000/svg" id="cubic1">
    <path d="M50 50" fill="none" stroke="black" stroke-width="1" class="line" />
    <path d="M50 50" fill="none" stroke="#27AE60" stroke-width="1" class="line-prog" />
    <path d="M50 50 Q50 150 550 50" stroke="#2F80ED" fill="none" 
    stroke-width="2" class="p012" />
    <path d="M50 150 Q550 50 550 150" stroke="#2F80ED" fill="none" 
    stroke-width="2" class="p123" />
    <path d="M50 50 C50 150 550 50 550 150" fill="none" stroke="#9B51E0" 
    stroke-width="3" class="p0123" />
    <g transform="translate(50, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">0</tspan>
        </text>
    </g>
    <g transform="translate(50, 150)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">1</tspan>
        </text>
    </g>
    <g transform="translate(550, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">2</tspan>
        </text>
    </g>
    <g transform="translate(550, 150)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">3</tspan>
        </text>
    </g>
    <g transform="translate(50, 50)" class="g012">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
    </g>
    <g transform="translate(50, 150)" class="g123">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
    </g>
    <g transform="translate(50, 50)" class="g0123">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
    </g>
</svg>
<script>
(function() {
    var t = 0;
    var dt = 0.004;

    function B(P) {
        function _B(P, k, n, t) {
            if (k == n) return P[n];
            var left = _B(P, k, n-1, t);
            var right = _B(P, k+1, n, t);
            return [
                (1-t) * left[0] + t * right[0],
                (1-t) * left[1] + t * right[1]
            ];
        }

        return function(t) {
            return _B(P, 0, P.length-1, t);
        }
    };

    var P = [
        [50, 50],
        [50, 150],
        [550, 50],
        [550, 150]
    ];

    // It's silly and pretty inefficient to this like this, but *shrug*!
    var B0123 = B(P);

    var B012 = B(P.slice(0, 3));
    var B123 = B(P.slice(1, 4));

    var B01 = B(P.slice(0, 2));
    var B12 = B(P.slice(1, 3));
    var B23 = B(P.slice(2, 4));

    var cubic = document.getElementById("cubic1");
    var p012 = cubic.querySelector(".p012");
    var p123 = cubic.querySelector(".p123");
    var p0123 = cubic.querySelector(".p0123");

    var g012 = cubic.querySelector(".g012");
    var g123 = cubic.querySelector(".g123");
    var g0123 = cubic.querySelector(".g0123");

    var line = cubic.querySelector(".line");
    var lineProg = cubic.querySelector(".line-prog");

    (function next() {
        t += dt;
        if (t < 0 || t > 1) {
            dt *= -1;
            t = Math.min(1, Math.max(0, t));
        }

        var P0123 = B0123(t);

        var P012 = B012(t);
        var P123 = B123(t);

        var P01 = B01(t);
        var P12 = B12(t);
        var P23 = B23(t);

        g0123.setAttribute("transform", "translate(" + P0123[0] + "," + P0123[1] + ")");
        g012.setAttribute("transform", "translate(" + P012[0] + "," + P012[1] + ")");
        g123.setAttribute("transform", "translate(" + P123[0] + "," + P123[1] + ")");

        p012.setAttribute("d", "M" + P[0][0] + " " + P[0][1] +
                              " Q" + P01[0] + " " + P01[1] +
                               " " + P012[0] + " " + P012[1]);
        p123.setAttribute("d", "M" + P[1][0] + " " + P[1][1] +
                              " Q" + P12[0] + " " + P12[1] +
                               " " + P123[0] + " " + P123[1]);
        p0123.setAttribute("d", "M" + P[0][0] + " " + P[0][1] +
                               " C" + P01[0] + " " + P01[1] +
                                " " + P012[0] + " " + P012[1] +
                                " " + P0123[0] + " " + P0123[1]);
        line.setAttribute("d", "M" + P012[0] + " " + P012[1] +
                               " " + P123[0] + " " + P123[1]);
        lineProg.setAttribute("d", "M" + P012[0] + " " + P012[1] +
                                   " " + P0123[0] + " " + P0123[1]);

        requestAnimationFrame(next);
    })();
})();
</script>

<div>$$
\begin{aligned}
B_{0,1,2,3}(t) = (1 - t) B_{0,1,2}(t) + t B_{1,2,3}(t), 0 \le t \le 1 \\
\end{aligned}
$$</div>

<svg viewBox="0 0 600 200" preserveAspectRatio="xMinYMin meet" 
    xmlns="http://www.w3.org/2000/svg" id="cubic1">
    <path d="M50 50 C50 150 550 50 550 150" fill="none" stroke="black" 
    stroke-width="3" class="p0123" />
    <path d="M50 50 L 50 150 M550 50 L 550 150" stroke="black" fill="none" 
    stroke-width="1" stroke-dasharray="1, 2" />
    <g transform="translate(50, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">0</tspan>
        </text>
    </g>
    <g transform="translate(50, 150)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">1</tspan>
        </text>
    </g>
    <g transform="translate(550, 50)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">2</tspan>
        </text>
    </g>
    <g transform="translate(550, 150)">
        <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <text x="0" y="-12" font-family="KaTeX_Math">
            P<tspan baseline-shift="sub" font-size="70%">3</tspan>
        </text>
    </g>
</svg>

You may have a sneaking suspicion at this point that there's a nice recursive 
definition lurking here. And indeed there is:

<div>$$
\begin{aligned}
B_{k,...,n}(t) &= (1 - t) B_{k,...,n-1}(t) + t B_{k+1,...,n}(t), 0 \le t \le 1 
\\
B_{i}(t) &= P_{i}
\end{aligned}
$$</div>

Or, expressed (concisely but inefficiently) in TypeScript, it might look like this:

```typescript
type Point = [number, number];
function B(P: Point[], t: number): Point {
    if (P.length === 1) return P[0];
    const left: Point = B(P.slice(0, P.length - 1), t);
    const right: Point = B(P.slice(1, P.length), t);
    return [(1 - t) * left[0] + t * right[0],
            (1 - t) * left[1] + t * right[1]];
}
// Evaluate a cubic spline at t=0.7
B([[0.0, 0.0], [0.0, 0.42], [0.58, 1.0], [1.0, 1.0]], 0.7)
```

# Cubic Bezier Curves in Vector Images

As it happens, cubic bezier curves seem to be the right balance between 
simplicity and accuracy for many purposes. These are the kind of curves you'll 
most often see in vector editing tools like [Figma][1].

<figure>
    <img src="/images/figmacubic.png" style="width: 500px" />
    <figcaption>A cubic bezier curve in <a href="https://www.figma.com">Figma</a></figcaption>
</figure>

You can think of the two filled in circles <span style="color: #2EC1FF">●
</span> as \\( P_0 \\) and \\( P_3 \\), and the two diamonds <span style="color: 
#2EC1FF">◇</span> as \\( P_1 \\) and \\( P_2 \\). These are the fundamental 
building blocks of more complex curved vector constructions.

Font glyphs are specified in terms of bezier curves in TrueType (.ttf) fonts.

<figure>
    <img src="/images/vectore.png" style="width: 400px"/>
    <figcaption>A lower-case "e" in <a 
    href="http://www.fonts2u.com/free-serif-italic.font">Free Serif Italic</a> 
    shown as a <a 
    href="https://medium.com/figma-design/introducing-vector-networks-3b877d2b864f#.95e6iz9he">vector 
    network</a> of cubic bezier curves</figcaption>
</figure>

The Scalable Vector Graphics (.svg) file format uses bezier curves as one of its 
two [curve primitives][0], which are used extensively in this:

<figure>
    <img src="/images/ghostscripttiger.svg" style="width: 400px" />
    <figcaption>The <a 
href="https://en.wikipedia.org/wiki/Talk%3AGhostscript#Origin_of_tiger.eps.3F_.28aka_.22cubic_spline_tiger.22.29">Cubic 
Spline Tiger</a> in SVG format.</figcaption>
</figure>

# Cubic Bezier Curves in Animation

While bezier curves have their most obvious uses in representing spacial curves, 
there's no reason why they can't be used to represented curved relationships 
between other quantities. For instance, rather than relating \\( x \\) and \\(y 
\\), [CSS transition timing functions][2] relate a time ratio with an output 
value ratio.

<style>
.linear {
    transition: all 1s linear;
}

.ease {
    /* This is ease reversed */
    transition: all 1s cubic-bezier(0.75, 0.0, 0.75, 0.9);
}

.ease.end {
    transition: all 1s ease;
}

.ease-in {
    /* This is ease-in reversed */
    transition: all 1s ease-out;
}

.ease-in.end {
    transition: all 1s ease-in;
}

.ease-in-out {
    transition: all 1s ease-in-out;
}

.ease-out {
    /* This is ease-out reversed */
    transition: all 1s ease-in;
}

.ease-out.end {
    transition: all 1s ease-out;
}

.custom-ease {
    transition: all 1s cubic-bezier(0.5, 1, 0.5, 0);
}

.ball.end {
    transform: translateY(-100px);
}

.timeline {
    transition: transform 1s linear;
}

.timeline.end {
    transform: translateX(100px);
}
</style>

<figure>
<svg viewBox="0 0 600 150" preserveAspectRatio="xMinYMin meet" xmlns="http://www.w3.org/2000/svg" id="anim0">
    <g transform="translate(50, 25)">
        <circle class="ball linear" cx="0" cy="100" r="5" fill="#F2994A" 
    stroke-width="0" />
    </g>
    <g transform="translate(150, 25)">
        <circle class="ball ease" cx="0" cy="100" r="5" fill="#F2994A" 
    stroke-width="0" />
    </g>
    <g transform="translate(250, 25)">
        <circle class="ball ease-in" cx="0" cy="100" r="5" fill="#F2994A" 
    stroke-width="0" />
    </g>
    <g transform="translate(350, 25)">
        <circle class="ball ease-in-out" cx="0" cy="100" r="5" fill="#F2994A" stroke-width="0" />
    </g>
    <g transform="translate(450, 25)">
        <circle class="ball ease-out" cx="0" cy="100" r="5" fill="#F2994A" stroke-width="0" />
    </g>
    <g transform="translate(550, 25)">
        <circle class="ball custom-ease" cx="0" cy="100" r="5" fill="#F2994A" stroke-width="0" />
    </g>
</svg>
<figcaption>Transition timing functions defined by bezier curves</figcaption>
</figure>

Cubic bezier curves are one of two ways of expressing timing functions in CSS 
([`steps()`][3] being the other).  The `cubic-bezier(x1, y1, x2, y2)` notation 
for CSS timing functions specifies the coordinates of \\( P_1 \\) and \\( P_2 
\\) of a cubic bezier curve.

<figure>
	<img src="/images/easing-function.svg" width="400">
	<figcaption>Diagram of <code>transition-timing-function: cubic-bezier(x1, y1, x2, y2)</code></figcaption>
</figure>

Let's pretend we're trying to animate an orange ball moving. In all of these 
diagrams, the <span style="color: #EB5757">red lines representing time</span> 
move at a constant speed.

<svg viewBox="0 0 600 400" preserveAspectRatio="xMinYMin meet" 
    xmlns="http://www.w3.org/2000/svg" id="anim1">
    <g transform="translate(25, 30)">
        <text x="50" y="-16" font-size="70%" text-anchor="middle">
            linear
        </text>
        <g class="timeline">
            <path d="M0 0 L0 100" fill="none" stroke-width="1" stroke-dasharray="1, 2" stroke="#EB5757" />
        </g>
        <path d="M0 100 L 100 0" fill="none" stroke-width="2" stroke="black" />
		<circle cx="0" cy="100" r="4" fill="white" stroke="black" stroke-width="2" />
        <g transform="translate(0, 100)">
            <text x="10" y="0" font-size="70%" alignment-baseline="middle">
                (0.00, 0.00)
            </text>
            <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        </g>
        <g transform="translate(100, 0)">
            <text x="-10" y="0" font-size="70%" text-anchor="end" 
                alignment-baseline="middle">
                (1.00, 1.00)
            </text>
            <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        </g>
        <circle cx="100" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <g class="ball linear">
            <path d="M130 100 L 0 100" stroke-width="1" stroke-dasharray="1, 2" stroke="#F2994A" />
            <circle cx="130" cy="100" r="5" fill="#F2994A" stroke-width="0" />
        </g>
    </g>
    <g transform="translate(225, 30)">
        <text x="50" y="-16" font-size="70%" text-anchor="middle">
            ease
        </text>
        <g class="timeline">
            <path d="M0 0 L0 100" fill="none" stroke-width="1" stroke-dasharray="1, 2" stroke="#EB5757" />
        </g>
        <path d="M0 100 C25  90 25 0 100 0" fill="none" stroke-width="2" stroke="black" />
        <path d="M0 100 L25  90 M25 0 L100 0" fill="none" stroke-dasharray="1, 2" stroke-width="1" stroke="black" />
		<circle cx="0" cy="100" r="4" fill="white" stroke="black" stroke-width="2" />
        <g transform="translate(25, 90)">
            <text x="10" y="0" font-size="70%" alignment-baseline="middle">
                (0.25, 0.10)
            </text>
            <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        </g>
        <g transform="translate(25, 0)">
            <text x="-10" y="0" font-size="70%" text-anchor="end" 
                alignment-baseline="middle">
                (0.25, 1.00)
            </text>
            <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        </g>
        <circle cx="100" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <g class="ball ease">
            <path d="M130 100 L 0 100" stroke-width="1" stroke-dasharray="1, 2" stroke="#F2994A" />
            <circle cx="130" cy="100" r="5" fill="#F2994A" stroke-width="0" />
        </g>
    </g>
    <g transform="translate(425, 30)">
        <text x="50" y="-16" font-size="70%" text-anchor="middle">
            ease-in
        </text>
        <g class="timeline">
            <path d="M0 0 L0 100" fill="none" stroke-width="1" stroke-dasharray="1, 2" stroke="#EB5757" />
        </g>
        <path d="M0 100 C42 100 100 0 100 0" fill="none" stroke-width="2" stroke="black" />
        <path d="M0 100 L42 100 M100 0 L100 0" fill="none" stroke-dasharray="1, 
        2" stroke-width="1" stroke="black" />
		<circle cx="0" cy="100" r="4" fill="white" stroke="black" stroke-width="2" />
        <g transform="translate(42, 100)">
            <text x="10" y="0" font-size="70%" alignment-baseline="middle">
                (0.42, 0.00)
            </text>
            <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        </g>
        <g transform="translate(100, 0)">
            <text x="-10" y="0" font-size="70%" text-anchor="end" 
                alignment-baseline="middle">
                (1.00, 1.00)
            </text>
            <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        </g>
        <circle cx="100" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <g class="ball ease-in">
            <path d="M130 100 L 0 100" stroke-width="1" stroke-dasharray="1, 2" stroke="#F2994A" />
            <circle cx="130" cy="100" r="5" fill="#F2994A" stroke-width="0" />
        </g>
    </g>
    <g transform="translate(25, 225)">
        <text x="50" y="-16" font-size="70%" text-anchor="middle">
            ease-out
        </text>
        <g class="timeline">
            <path d="M0 0 L0 100" fill="none" stroke-width="1" stroke-dasharray="1, 2" stroke="#EB5757" />
        </g>
        <path d="M0 100 C0 100 58 0 100 0" fill="none" stroke-width="2" stroke="black" /> <path d="M0 100 L0 100 M58 0 L100 0" fill="none" stroke-dasharray="1, 2" 
        stroke-width="1" stroke="black" />
		<circle cx="0" cy="100" r="4" fill="white" stroke="black" stroke-width="2" />
        <g transform="translate(0, 100)">
            <text x="10" y="0" font-size="70%" alignment-baseline="middle">
                (0.00, 0.00)
            </text>
            <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        </g>
        <g transform="translate(58, 0)">
            <text x="-10" y="0" font-size="70%" text-anchor="end" 
                alignment-baseline="middle">
                (0.58, 1.00)
            </text>
            <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        </g>
        <circle cx="100" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <g class="ball ease-out">
            <path d="M130 100 L 0 100" stroke-width="1" stroke-dasharray="1, 2" stroke="#F2994A" />
            <circle cx="130" cy="100" r="5" fill="#F2994A" stroke-width="0" />
        </g>
    </g>
    <g transform="translate(225, 225)">
        <text x="50" y="-16" font-size="70%" text-anchor="middle">
            ease-in-out
        </text>
        <g class="timeline">
            <path d="M0 0 L0 100" fill="none" stroke-width="1" stroke-dasharray="1, 2" stroke="#EB5757" />
        </g>
		<path d="M0 100 C42 100 58 0 100 0" fill="none" stroke-width="2" stroke="black" />
        <path d="M0 100 L42 100 M58 0 L100 0" fill="none" stroke-dasharray="1, 2" stroke-width="1" stroke="black" />
		<circle cx="0" cy="100" r="4" fill="white" stroke="black" stroke-width="2" />
        <g transform="translate(42, 100)">
            <text x="10" y="0" font-size="70%" alignment-baseline="middle">
                (0.42, 0.00)
            </text>
            <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        </g>
        <g transform="translate(58, 0)">
            <text x="-10" y="0" font-size="70%" text-anchor="end" 
                alignment-baseline="middle">
                (0.58, 1.00)
            </text>
            <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        </g>
        <circle cx="100" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <g class="ball ease-in-out">
            <path d="M130 100 L 0 100" stroke-width="1" stroke-dasharray="1, 2" stroke="#F2994A" />
            <circle cx="130" cy="100" r="5" fill="#F2994A" stroke-width="0" />
        </g>
    </g>
    <g transform="translate(425, 225)">
        <text x="50" y="-16" font-size="70%" text-anchor="middle">
            (custom)
        </text>
        <g class="timeline">
            <path d="M0 0 L0 100" fill="none" stroke-width="1" stroke-dasharray="1, 2" stroke="#EB5757" />
        </g>
        <path d="M0 100 C50 0 50 100 100 0" fill="none" stroke-width="2" stroke="black" />
        <path d="M0 100 L50 0 M50 100 L100 0" fill="none" stroke-dasharray="1, 2" stroke-width="1" stroke="black" />
		<circle cx="0" cy="100" r="4" fill="white" stroke="black" stroke-width="2" />
        <g transform="translate(50, 0)">
            <text x="-10" y="0" font-size="70%" alignment-baseline="middle" text-anchor="end">
                (0.50, 1.00)
            </text>
            <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        </g>
        <g transform="translate(50, 100)">
            <text x="10" y="0" font-size="70%"
                alignment-baseline="middle">
                (0.50, 0.00)
            </text>
            <circle cx="0" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        </g>
        <circle cx="100" cy="0" r="4" fill="white" stroke="black" stroke-width="2" />
        <g class="ball custom-ease">
            <path d="M130 100 L 0 100" stroke-width="1" stroke-dasharray="1, 2" stroke="#F2994A" />
            <circle cx="130" cy="100" r="5" fill="#F2994A" stroke-width="0" />
        </g>
    </g>
</svg>
<script>
(function() {
    var nextVal = 0;
    (function next() {
        Array.prototype.forEach.call(document.querySelectorAll(".ball"),
                                     function(ball) {
            if (nextVal == 0) {
                ball.classList.remove("end");
            } else {
                ball.classList.add("end");
            }
        });
        Array.prototype.forEach.call(document.querySelectorAll(".timeline"),
                                     function(timeline) {
            if (nextVal == 0) {
                timeline.classList.remove("end");
            } else {
                timeline.classList.add("end");
            }
        });
        nextVal = nextVal == 100 ? 0 : 100;
        setTimeout(next, 1000);
    })();
})();
</script>

# Why Bezier Curves?

Bezier curves are a beautiful abstraction for describing curves. The most 
commonly used form, cubic bezier curves, reduce the problem of describing and 
storing a curve down to storing 4 coordinates.

Beyond the efficiency benefits, the effect of moving the 4 control points on the 
curve shape is intuitive, making them suitable for direct manipulation editors.  

Since 2 of the points specify the endpoints of the curve, composing many bezier 
curves into more complex structures with precision becomes easy. The exact 
specification of endpoints is always what makes it so convenient in the 
animation case: the only sensible value of the easing function at \\( t = 0\% 
\\) is the initial value, and the only sensible value at \\( t = 100\% \\) is 
the final value.

A less obvious benefit is that the line from \\( P_0 \\) to \\( P_1 \\) 
specifies the tangent of the curve leaving \\( P_0 \\). This means if you have 
two joint curves with mirrored control points, the slope at the join point is 
guaranteed to be the same on either side of the join.

<figure>
    <img src="/images/jointbezier.png" style="width: 500px" />
    <figcaption>Left: Two joint cubic bezier curves with mirrored control 
    points.  Right: control points not mirrored.</figcaption>
</figure>

A major benefit of mathematical construct like bezier curves is the ability to 
leverage decades of mathematical research to solve most problems you might run 
into, completely agnostic to the rest of your problem domain.

For instance, to make this post, I had to learn how to split a bezier curve at a 
given value of \\( t \\) in order to animate the curves above. I was quickly 
able to find a well written article on the matter: [A Primer on Bézier Curves: 
Splitting Curves][4].

# Resources and Further Reading

- [A Primer on Bézier Curves][5] in addition to having a description of using 
deCasteljau's algorithm to draw and split curves, this free online book seems to 
be a pretty comprehensive intro.
- [Bézier curve on Wikipedia][6] shows many different mathematical formulas of 
bezier curves beyond the recursive definition shown here. It also contains the 
original animations that made bezier curves seem so evidently elegant to me.

Also a shoutout to Dudley Storey for his article [Make SVG Responsive][7], which 
allowed all of the inline SVG in this article to work nicely on mobile.

<script 
src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.6.0/katex.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.6.0/contrib/auto-render.min.js"></script>
<script>
renderMathInElement(document.body);
</script>

[0]: https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/Paths#Bezier_Curves
[1]: https://www.figma.com
[2]: https://developer.mozilla.org/en-US/docs/Web/CSS/single-transition-timing-function#Keywords_for_common_timing-functions
[3]: https://developer.mozilla.org/en-US/docs/Web/CSS/single-transition-timing-function#The_steps()_class_of_timing-functions
[4]: https://pomax.github.io/bezierinfo/#splitting
[5]: https://pomax.github.io/bezierinfo
[6]: https://en.wikipedia.org/wiki/B%C3%A9zier_curve
[7]: http://thenewcode.com/744/Make-SVG-Responsive
