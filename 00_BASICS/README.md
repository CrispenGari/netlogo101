### Practical 1 -NetLogo Basics.

### 1. Agents

- The NetLogo world is made up of agents. Agents are beings that can follow instructions.
- In NetLogo, there are four types of agents: turtles, patches, links, and the observer.

### 2. Procedures

User defined functions or commands are called procedures in netlogo and bellow is a example of 2 procedures in netlogo.

```shell
to setup
  clear-all
  create-turtles 10
  reset-ticks
end

to go
  ask turtles [
    fd 1            ;; forward 1 step
    rt random 10    ;; turn right
    lt random 10    ;; turn left
  ]
  tick
end
```

The above procedures will create two procedures `go` and `setup` each responsible for a different task. Procedures cal also take in some values bellow is an example of a procedure method that takes in the number of sides and length of the sides to draw a polygon.

```shell
to setup
  ca ;;same as: clear-all
  ask patches [set pcolor green] ;; changes the patch colors to be green
  crt 1 [ ;; same as: create-turtles
    ;;setxy random-xcor random-ycor ;; set's the random x and y coodinate of the created turle
    set color yellow
    set shape "butterfly"
  ]
  reset-ticks ;; reset ticks
end
to go
 ask turtles [ draw-polygon 8 5 ] ;; we are asking the created turtle to draw a polgon of 8 sides
end

to draw-polygon [num-sides len]  ;; turtle procedure
  pen-down
  repeat num-sides [
    fd len
    rt 360 / num-sides
  ]
end

# the method can be called as follows in the Command Center.
draw-polygon 6 40
```

> Let's create 10 turtles and we want them to be randomly placed. We also want the turtle that has id of `5` to draw a polygon. We can do it as follows:

```shell

to setup
  ca ;;same as: clear-all
  ask patches [set pcolor green] ;; changes the patch colors to be green
  crt 10 [ ;; same as: create-turtles
    setxy random-xcor random-ycor ;; set's the random x and y coodinate of the created turle
    set color yellow
    set shape "airplane"
  ]
  reset-ticks ;; reset ticks
end
to go
   ask turtles [
    ifelse who = 5 [
      draw-polygon 8 who
    ][
      set color red
    ]
  ]
end

to draw-polygon [num-sides len]  ;; turtle procedure
  pen-down
  repeat num-sides [
    fd len
    rt 360 / num-sides
  ]
end
```

Note that you can create [`breeds`](https://ccl.northwestern.edu/netlogo/docs/dictionary.html#breedvar) in netlogo using the breed keyword and must be defined at the before procedures. Here is an example that shows how we can create breeds of mice and frogs.

> The first input defines the name of the agentset associated with the breed. The second input defines the name of a single member of the breed.

```shell
breed [mice mouse]
breed [frogs frog]

mice-own [age name surname] ;; this create the properties of a turtle such age, name, surname

to setup
  clear-all
  create-mice 50
  ask mice [ set color white ]
  create-frogs 50
  ask frogs [ set color green ]
  show [breed] of one-of mice    ;; prints mice
  show [breed] of one-of frogs   ;; prints frogs
  ask mice [fd random 10]
  ask frogs [fd random 10]
end
```

You can check the mice that have colors green as follows using the `with` keyword which is a filter that allows you to get subsets of agents:

```shell
show mice with [color = red]
show frogs with [who < 4] ;; shows the frogs that have who property less than 4
```

Let's create a simple program that will draws a square. For that we are going to create a procedure `draw-square` as follows:

```shell
breed [mice mouse]
to setup
  clear-all
  create-mice 5 [
    setxy random-xcor random-ycor
    pen-down
  ]
end
to draw-square
  repeat 4 [
    forward 5
    right  90
  ]
end
```

Then we can run the following command from the Command Center.

```shell
ask mice [draw-square]
```

Note that we can't directly pass the `draw-square` method as the command of our buttons. In order for us to do that we need to create a procedure `go` you can name it whatever and then ask turtle to do the draw-square

```shell

breed [mice mouse]
to setup
  clear-all
  create-mice 5 [
    setxy random-xcor random-ycor
    pen-down
  ]
end

to draw-square
  repeat 4 [
    forward 5
    right  90
  ]
end

to go
  ;; you can also say ask turtles [draw-square]
  ask mice [
    draw-square
  ]
end
```

You can also specify which mice do you want to draw squares using the with filter for example:

```shell
to go
  ;; you can also say ask turtles [draw-square]
  ask mice with [color = green] [draw-square]
end
```

### 3. Reporters

Reporters are just like procedures however they return a value. Let's create a reporter that takes a list of numbers the mean of those numbers. We can do it as follows:

```shell
to go
  let l [2 3 4 6]
  print word "The mean of the list is: " calculate-mean l
  print word "A random number in a list: " one-of l ;; allows you to random select an element of a list.
end

to-report calculate-mean [l]
  report mean l
end

```

### 4. List Data type in NetLogo

To define a list in netlogo you use a `[]` bellow is an example of how you can define a list in netlogo.

```shell

to go
  let l [2 3 4 6 [8 16 24]]
  print word "The list created is: " l
  print word "The first element in the list is: " first l
  print word "The last element in the list is: " last l
  print  word "The rest of the elements in the list is without the first: " but-first l
  print word "Squred numbers: " map [i -> i * i] [1 2 3 4 5]
  print word "Summed numbers: " reduce [[a b] -> a + b] [1 2 3 4 5]
  print word "Filtered numbers: " filter [i -> i mod 2 = 0] [0 1 2 3 4 5]
  print word "Mean of the numbers: " mean [1 2 3 4 5]
  print word "4th item in a list is: " item 4 l
end

```

List comes with some different methods such as foreach, filter, map, reduce etc....

> REF: https://ccl.northwestern.edu/netlogo/docs/programming.html#lists

> We used the `word` keyword to concatenate strings with a a list and integers. The following code cell shows a program that reinforce the use of the `word` keyword to concatenate strings with a random generated integer.

```shell
to hi
  print (word "The random number: " (random 2) " was generated from range 0 and 1 inclusive.")
end
```

### The Code Tab

Let's start by writing our code, we are going to create `2` procedures which are `setup` and `go`

```shell
to setup
  ca ;;same as: clear-all
  crt 10 [ ;; same as: create-turtles
    setxy random-xcor random-ycor ;; set's the random x and y coodinate of the created turle
    set color blue
    set shape "person"
  ]
  reset-ticks ;; reset ticks
end
to go
  ask turtles [
    fd 5 ;; same as sayning forward move foward by 5 steps
    rt random 361 ;; randomly rotate the turtle to the right
  ]
  tick
end
```

> From the `setup` method first we are going to clear the environment, create 10 turtles of shape "person", give them a random `x-y` coo-dinates set the color to be blue and we reset the ticks.

> Note: Note that if you want to see the shapes that are supported in NetLogo you can click on the `Tools` > `Turtles and Shape Editor` there you will see different shapes that you can use to for your turtle rather than the default triangle.

- Now you can go to the Interface tab and on the `Command Center` under the `observer` you can execute these commands py typing them eg:

```shell
setup
go
```

You will notice that our turtles does not rotate by default, so in order to make them rotate we need to go to the `Tools` > `Turtles and Shape Editor` and then select the turtle you want to edit and click the `edit` button then check the `"Rotatable"` checkbox.

- Let's create two buttons that will execute these two commands from the Interface tab.
- Note that when creating a button you can check the "Forever" option to tell netlogo to execute the command forever till that button is pressed again.
- You can save your model when you are done coding by:
  - clicking on the `File` menu
  - Then click on Save As and then choose where you want to save your model.
- Note that while you are writing your model you can document them, to do that you need to go to the `Info` tab and then you click on the edit button and you can write some markdown code for the documentation.

### Setting Properties to Agents

You can set properties of a turtle. In this example we are going to create a simple model that track the wealth of our turtles and their happiness state based on their incomes.

```shell
globals [ wealth]
turtles-own [ income happy ]
to setup
  ca ;;same as: clear-all
  crt 100 [ ;; same as: create-turtles
    setxy random-xcor random-ycor ;; set's the random x and y coodinate of the created turle
    set color blue
    set shape "person"
    set income random 100
  ]
  reset-ticks ;; reset ticks
  set wealth 0
end
to go
  ask turtles [
    fd 5 ;; same as sayning forward move foward by 5 steps
    rt random 361 ;; randomly rotate the turtle to the right
    ;; let's say if the income of a person is greater than 40 we update the happiness of the turtle
    if income > 40 [
     set happy True
      set wealth wealth + income
      set color red
    ]
  ]
  tick
end
```

You can run commands like on the observer:

```shell
print wealth # to print the wealth i.e. that is our global variable
inspect one-of turtles # this will inspect a random turtle and open a popup window
```

### Flocking Butterflies 🦋 Example

In this example we are going to focus on some functions that can be used to on turtle, as well as creating variables, using sliders to get global variables.

```shell

to setup
  ca ;;same as: clear-all
  ask patches [set pcolor green] ;; changes the patch colors to be green
  crt num-turtles [ ;; same as: create-turtles
    setxy random-xcor random-ycor ;; set's the random x and y coodinate of the created turle
    set color yellow
    set shape "butterfly"
  ]
  reset-ticks ;; reset ticks
end
to go
  ask turtles [
    ;; looks for the closest title to the current turtle (myself)
    let closest-turtle min-one-of (other turtles) [distance myself]
    let difference subtract-headings heading (towards closest-turtle)
    set heading (heading + (attraction * difference))
    forward 1
  ]
  tick
end
```

### Simulating Conway's Game of Life

In this section we are going to implement the Conway's Game of life using netlogo. The rules of the game can be found on: https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life

Here are the basic Rules:

1. Any live cell with fewer than two live neighbors dies, as if by underpopulated.
2. Any live cell with two or three live neighbors lives on to the next generation.
3. Any live cell with more than three live neighbors dies, as if by overpopulation.
4. Any dead cell with exactly three live neighbors becomes a live cell, as if by reproduction.

From this game we are going to use patches in netlogo to build the game. Below is the procedure that does that.

```shell
patches-own [new-color]
to setup
  clear-all
  reset-ticks
  ;; we want to set the patches color to be a random number where
  ;; green patch - is alive
  ;; yello patch - represent dead
  ask patches [
    set pcolor one-of [green yellow]
  ]
end


to go
 ;; updating the tick
  tick
  ask patches [
    let alive-neighbors count (neighbors with [pcolor = green])
    set new-color pcolor
    ;; if and only if the patch is alive we can do this
    ifelse pcolor = green [
      if (alive-neighbors < 2 or alive-neighbors > 3) [
        ;; the patch is alive kill it
        set new-color yellow
      ]
    ][ ;; the else part: the patch is already dead
      if alive-neighbors = 3 [
        set new-color green
      ]
    ]
  ]
  ask patches [
    set pcolor new-color
  ]
end
```

### Simulating the Spread of Disease

In this example we are going to simulate the spread of an disease that spread via contact.

### References:

1. [Dictionary](https://ccl.northwestern.edu/netlogo/docs/dictionary.html)
2. [Programming Guide](https://ccl.northwestern.edu/netlogo/docs/programming.html)
3. [ccl.northwesten](https://ccl.northwestern.edu/netlogo/bind/primitive/mod.html)
