### Message Communication

In this tutorial we are going to implement the a simple simulation of message communication or rumour spread, which we can use as a base understanding of how the disease spread works. Here is what we are going to build.

1. We are going to create turtles
2. One of them will have a message
3. Then as the turtles moves randomly and meet each other they spread the message.
4. We then plot how the spread of messages population.

### Setting up turtles and make them randomly move.

- First we are going to create the setup method and we set

```shell
turtles-own [message?]

to setup
  ca
  reset-ticks
  crt population [
    setxy random-xcor random-ycor
    set size 1
    set shape "person"
    set color white
    set message? false
  ]

  ask one-of turtles[
    set message? true
    set color red
    ifelse message = "" [set label "hi"][set label message]
  ]
```

- We added a boolean property `message?` to turtles and make one turtle to have a message.
- We took the message from the input box it it is available.
- We set the color of the argent with a message to red.

### Message Spreading.

In the `go` method we have the following.

```shell
to go
  ask turtles [
    ifelse flip? [right random 60 ][left random 60]
    forward random 4
    if any? other turtles-here with [message?] [
      ;; if any of the turtles in the same patch has a message then
      set message? true
      set color red
      ifelse message = "" [set label "hi"][set label message]
    ]
  ]
end

to-report flip?
  report random 2 = 0
end
```

1. We created a reporter `flip?` that flips a coin and returns true or false.
2. We randomly set the heading of the turtle based on the flip value to either left or right
3. We move our turtle forward with a random units between 0 and 3.
4. If the turtles are in the same patch with the one that has the message, then we spread the message to the other turtles.
5.

### Plotting the message spread

To make some plots we update our `go` procedure to look as follows:

```shell

to go
  ask turtles [
    ifelse flip? [right random 60 ][left random 60]
    forward random 4
    if any? other turtles-here with [message?] [
      ;; if any of the turtles in the same patch has a message then
      set message? true
      set color red
      ifelse message = "" [set label "hi"][set label message]
    ]
  ]
  tick ;; add this to update the plot
end

```

1. We first need to plot the turtles count for the ones that has the messages and the one's that doesn't have the messages's population. Here are the commands that are being passed to the plot in the interface:

```shell
plot count turtles with [color = white]
plot count turtles with [message?]
```

### Let's add a monitor

Next we are going to add two monitors that will helps us visualize and see the turtle counts with some messages. We can add a widget called `monitor` and for the reporters we are going to have the following for each monitors.

```shell
count turtles with [color = white]
count turtles with [message?]
```

- With this we will be able to see the population count of turtles that has messages and the ones that doesn't have messages.
