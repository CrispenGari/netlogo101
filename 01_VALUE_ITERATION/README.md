### Value Iteration Implementation using NetLogo

Value iteration is an algorithm that uses dynamic programming to find the best policy for an agent to follow in a given environment. It's used to solve reinforcement learning (RL) problems. We are going to implement the following algorithm.

<p align="center"><img src="/images/via.png" alt="via" width="80%"/></p>

We are going to go through implementing this step by step.

> We want to give reward to the agent in our case a `turtle` a positive reward when it hit the green patch when it is moving around the world/environment and give it a negative reward when it hits the red patch.

Let's start by first defining the `setup` and the `go` procedures.

Our `setup` procedure will be as follows, this just resets the environment and create the new enviroment:

```shell
to setup
  clear-all
  reset-ticks
  ask patches [
    ;; generate a random number between 0 and 10 (10 so thet we can
    ;; accomodate non-rewarding patches.
    let rand random 10
    set pcolor white
    if rand = 0 [ set pcolor red ]
    if rand = 1 [ set pcolor green ]
  ]
end
```

Now let's talk about state and actions.

1. `state` - we want the state of the turtle to be `x`, `y` and `heading`.
2. `actions` - we want the action of the urgent in the invironment to be `fd 1` (forward 1 step), `rt 90` (right turn 90deg) `lt 90` (left turn 90deg)

> Let's let's create the utility function. These function will set and get utility value that will be stored in the hash table. Here is the code for doing that.

```shell
extensions [table] ;; importing the hashtable

globals [utility] ;; the global variable that holds the utility

to put-utility[x y dir u]
  let state (list x y dir) ; the key of the utility
  table:put utility state u ; stores the key state and value utility in a hash table
end

to-report get-utility[x y dir]
  let state (list x y dir)
  if (table:has-key? utility state) [
    report table:get utility state
  ]
  put-utility x y dir 0
  report 0
end

to setup
# ....
  # note that we have added we are creating a new hash table at setup

  set utility table:make ; create a new hash table during setup
end

;; turtle functions
to-report get-reward
  if pcolor = green [report 10]
  if pcolor = red [report -10]
  report 0
end
```

1. First we import the table utility package so that we can use hashtables
2. We created a global variable `utility` that will be updated.
3. We have the `put-utility` and `get-utility` procedure and reporters responsible for setting and getting the utility value.
4. We are creating another reporter `get-reward` that returns the reward based on the position of the urgent in the environment.

> Before we create the value-iteration function, we need to first set the `gamma` and `epsilon` values and these values must be between 0 and 1 and we are going to get them from the GUI (from the slider).

We are going to create a procedure called `value-iteration` and it will have the following code in it.

```shell
extensions [table] ;; importing the hashtable

globals [utility headings actions] ;; the global variable that holds the utility,
;; headings and actions

# ....


to value-iteration
  let delta 100000 ; set a delta value to 100000
  let agent 0 ;; create a new temporary urgent
  create-turtles 1 [
    set agent self ; set the agent to be the created turtle
    set hidden? true
  ]
  ask patches [
    foreach headings [ _dir ->
      ; lets get the state
      let x pxcor
      let y pycor
      let dir _dir ; set the heading to the current heading
      let best-action 0
      ask agent [
        setxy x y
        set heading dir
        let best-utility item 1 get-best-action ;; the second item of the list is the best action
        ;; we can get the reward of the current state
        let reward get-reward
        let current-utility get-utility x y dir
        let new-utility (reward + gamma * best-utility)
        ;; we update our utility
        put-utility x y dir new-utility

        if (abs (current-utility - new-utility) > delta)[
          set delta abs (current-utility - new-utility)
        ]
      ]

    ]
  ]
  ;; kill the agent
  ask agent [die]
end

```

The `get-best-action` reporter will look as follows:

```shell

to-report get-best-action
  ; report the agent's best action based on it's x, y, heading and current utility
  ;; & report [best-action, utility]
  let x xcor
  let y ycor
  let dir heading
  let best-action 0
  let best-utility -1000000
  foreach actions [ action ->
    run action ; runs the current action command e.g "rt 90"
    let utility-of-action get-utility xcor ycor heading
    if (utility-of-action > best-utility) [
      set best-action action
      set best-utility utility-of-action
    ]
    ; after checking the best actions we need to reset the agent's original state because we are not taking any action rather we are trying to get the best action
    setxy x y
    set heading dir
  ]
  report (list best-action best-utility)
end
```

> Now with this we can test our `value-iteration` procedure by typing the following command in the command center and see actions that are being taken.

```shell
value-iteration
# to check the utility we say
show utility
```

Now we can run the while loop until `delta` value is less than `epsilon * (1 - gamma)/gamma`. So we are going to change the `value-iteration` procedure to:

```shell
to value-iteration
  let delta 100 ; set a delta value to 10000
  let agent 0 ;; create a new temporary urgent
  create-turtles 1 [
    set agent self ; set the agent to be the created turtle
    set hidden? true
  ]
  while [delta > epsilon * (1 - gamma) / gamma][
    set delta 0
    ask patches [
      foreach headings [ _dir ->
        ; lets get the state
        let x pxcor
        let y pycor
        let dir _dir ; set the heading to the current heading
        let best-action 0
        ask agent [
          setxy x y
          set heading dir
          let best-utility item 1 get-best-action ;; the second item of the list is the best action
                                                  ;; we can get the reward of the current state
          let reward get-reward
          let current-utility get-utility x y dir
          let new-utility (reward + gamma * best-utility)
          ;; we update our utility
          put-utility x y dir new-utility
          if (abs (current-utility - new-utility) > delta)[
            set delta abs (current-utility - new-utility)
          ]
        ]
      ]
    ]
    ;; let's display the gamma
    show delta
    ;; and we can plot the values
    plot delta
  ]
  ;; kill the agent
  ask agent [die]
end
```

1. Note that we have added some plots based on the value of the delta that we are also printing in the value-iteration function

Next we are going to create a slider that allows us to select number of turtles that we can create during setup. This is how the setup will look like:

```shell
to setup
  clear-all
  reset-ticks
  ask patches [
    ;; generate a random number between 0 and 10 (10 so thet we can
    ;; accomodate non-rewarding patches.
    let rand random 10
    set pcolor white
    if rand = 0 [ set pcolor red ]
    if rand = 1 [ set pcolor green ]
  ]
  set actions ["fd 1" "lt 90" "rt 90"]
  set headings [0 90 180 270]
  set utility table:make ; create a new hash table during setup

  create-turtles num-turtles [
    set color blue
    setxy random-pxcor random-pycor
    set heading one-of headings
    set shape "person"
  ]
end
```

Next we are going to create a procedure `take-best-action` that will report on the best action a turtle can take.

```shell
to take-best-action
  let best-action first get-best-action ; you can say: let best-action item 0 get-best-action
  run best-action
end
```

Then our `go` procedure will do the following:

```shell
to go
  tick
  ask turtles [
    take-best-action
  ]
end
```
