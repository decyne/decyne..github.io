---
layout: post
title: You're up and running!
---

Next you can update your site name, avatar and other options using the _config.yml file in the root of your repository (shown below).

![_config.yml]({{ site.baseurl }}/images/config.png)

The easiest way to make your first post is to edit this one. Go into /_posts/ and update the Hello World markdown file. For more instructions head over to the [Jekyll Now repository](https://github.com/barryclark/jekyll-now) on GitHub.

The goals I aim to achieve with this are:
* Easily layer a hex grid over any map
* Adjustable hex sizes
* Able to make hex grid transparent
* Automatically key hexes
* Clicking on hex/hex key links you to the section describing the pdf

First I started by drawing a hex grid using the code from this stack overflow question:
https://tex.stackexchange.com/questions/61429/how-to-draw-a-hexagonal-grid-with-numbers-in-the-cells

```
\documentclass{standalone}
\usepackage{tikz}
\usetikzlibrary{shapes}

\begin{document}
\begin{tikzpicture} [hexa/.style= {shape=regular polygon,
                                   regular polygon sides=6,
                                   minimum size=1cm, draw,
                                   inner sep=0,anchor=south,
                                   fill=lightgray!85!blue}]

\foreach \j in {0,...,9}{% 
     \ifodd\j 
         \foreach \i in {0,...,9}{\node[hexa] (h\j;\i) at ({\j/2+\j/4},{(\i+1/2)*sin(60)}) {\j;\i};}        
    \else
         \foreach \i in {0,...,9}{\node[hexa] (h\j;\i) at ({\j/2+\j/4},{\i*sin(60)}) {\j;\i};}
    \fi}
\node [circle,draw,red,minimum size=1cm] at (h3;4){};
\end{tikzpicture}
\end{document}     
```

Then, I tried to overlay the hex grid over an image, like in:
https://tex.stackexchange.com/questions/9559/drawing-on-an-image-with-tikz

Then I had the problem of scaling hexes. I wanted to be able to specify the size of the hexes and have latex automatically calculate how many exes were needed to fill the image. I already knew the images width (textwidth), however I needed to calculate the height.
https://tex.stackexchange.com/questions/185161/how-to-get-the-size-width-height-of-an-image
```
\newdimen\imageheight % goes into the preamble

\settoheight{\imageheight}{%
  \includegraphics[width=\paperwidth,keepaspectratio]{myimage}%
}
```

Was overlapping the edge of the image occassionally when I was changing the size. This was because the division I was using was rounding to the nearest number, which was sometimes over. I wanted it to always round down.

```
\yhexes=\numexpr \imageheight / \hexdim \relax
```
Had to add hexdim to location points to scale with hex sizes
```
\foreach \j in {0,...,2}{% 
           \ifodd\j 
               \foreach \i in {0,...,9}{\node[hexa] (h\j;\i) at ({\j*\hexdim/2+\j*\hexdim/4},{(\i*\hexdim+\hexdim/2)*sin(60)}) {\j;\i};}
          \else
               \foreach \i in {0,...,9}{\node[hexa] (h\j;\i) at ({\j*\hexdim/2+\j*\hexdim/4},{\i*\hexdim*sin(60)}) {\j;\i};}
          \fi}
```

Needed to offset all hexagons on x axis so it didn't hang off the edge of the graph by adding +\hexdim/2 to the X co-ord
