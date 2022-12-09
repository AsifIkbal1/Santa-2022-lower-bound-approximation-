# Santa-2022-lower-bound-approximation-
Santa 2022 lower bound approximation 
Santa 2022 lower bound approximation 

# Welcome to *The Christmas Card Conundrum*! #

Each year Kaggle hosts a special Santa-themed optimization challenge. This year you're challenged to optimize the path of a robotic arm over the points of an image, minimizing both the movement of the arm and the change in color from step to step.

In this notebook we will learn how to break down submission cost into individual components to see where most of the potential optimization is possible. To do so we will approximate optimal solution from below as good as we can.

# So what the step cost consists of?

It may appear from the competition description that cost of individual movement of an arm consists of 2 parts: cost of color change and cost of arm legs rotation.

However it can be useful to further split cost of arm movement into 2 components: geometrical and configurational. Lets talk about what it means, why and how it is possible first.

# Geometrical vs configurational cost

First important consideration is that rotation of one leg always translates into movement of arm tip to one of 4 adjacent cells of the image. It means that combination of rotations of 2 legs moves arm tip by 0 or 2 units if we add up x and y distance between source and destination cells. Similarly when rotating 3 legs we move arm tip by 1 or 3 units. 4 legs - by 0, 2 or 4 units, etc.

It makes sense to define distance between cells of an image as sum of differences in their x and y coordinates. In math this measure divided by number of dimentions is called $L_1 norm$. Just like regular Eucledian distance is called $L_2 norm$ because it uses squares of distances by each coordinate.

As we remember that usually there are multiple changes to arm configuration that move arm tip to the same cell of the image, any configuration change that moves arm tip to lower distance then number of legs that were rotated is suboptimal and can be optimized by removing 2 or more leg rotations that move arm tip in the opposite directions. Thus the is a natural cost of the step induced by geometrical distance between starting and ending points, along with possible additional cost from rotating spare arm legs.

To reiterate, geometrical cost comes from the movement of arm tip and in some sense is inevitable.

Configurational cost comes from spare leg rotations that are not necessary for arm tip movement.

# So how do we split arm movement cost of entire solution?

There is a tremendous number of ways to draw single color picture of size 257x257 (e.g. "black square") in exactly 257x257=66049 moves each involving single leg rotation, thus not inducing any additional score. So any step where arm tip is moved by more then one unit can be considered geometrically not optimal. And any time we paint over a point which was already painted, we introduce whole spare step, which is one of the worst types of waste of score possible.

Now we can split total cost of arm movement into 4 components:

cost of optimal colorless solution (66049) + number of spare steps + additional cost of movements by more then one unit + cost of spare leg rotations.

# What about color cost?

Costs of color changes also do not come for free. Completely or partially ignoring geometrical limitations of the problem, we can find lower bound on sum of all color transitions, thus finding closer lower bound of optimal solution, then just from colorless score.

E.g. we know that changing configuration of 8 legs cannot move arm tip more then by 8 units in our $L_1$ distance. For each cell in our image we can find 2 closest colors withing 8 unit distance by color transition score. We can also take into account that choosing further point comes with a price of $sqrt(distance)-1$. Adding up scores of all these transitions divided by 2 will give some lower bound on color-induced score.

Let's get to coding.
Below is some useful code from [Getting Started](https://www.kaggle.com/code/ryanholbrook/getting-started-with-santa-2022) Notebook, which I forked from.
