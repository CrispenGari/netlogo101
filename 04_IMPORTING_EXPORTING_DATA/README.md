### Saving and Loading data

In this tutorial we are going to demonstrate how we can save and load data in csv format in netlogo.

### Saving

First of all we are going to create a simple model that moves turtles around and record their position in a csv file at each time stamp. The `setup` method will look as follows:

```shell
globals [file-name cwd]
;; patches-own [occupancy]
turtles-own [energy]

to setup
  ca
  set cwd user-directory
  set file-name (word cwd "\\simulation_data.csv")
  print word "The data will be saved: " file-name

  file-open file-name
  file-print "tick,id,x,y,energy"
  crt 10 [
    setxy random-xcor random-ycor
    set energy random 100
    set shape "flower"
  ]
  reset-ticks
end
```

> Note that the `user-directory` reporter allows us to get the current working directory of the user.

The `go` method will look as follows.

```shell
to go
  ask turtles [
    move
    set energy energy - 1
  ]
  record-data
  tick
  if ticks > 50 [
    file-close
    stop
  ] ;; we stop when we collected 50 rows of data
end
```

Here are the procedures that are being used in the `go` method.

```shell
to move
  rt random 360
  lt random 360
  fd 1
end

to record-data
  ask turtles [
    file-print (word ticks "," who "," xcor "," ycor "," energy)
  ]
end

to stop-simulation
  stop
  file-close
end
```

The above can be done using the [`csv`](https://ccl.northwestern.edu/netlogo/docs/csv.html) extension that makes it easy to do. Let's consider the following modifications in the `code` tab.

```shell
extensions [ csv ]
globals [file-name cwd df]
turtles-own [energy]

to setup
  ca
  set cwd user-directory
  set file-name (word cwd "\\simulation_data.csv")
  set df csv:from-string "tick,id,x,y,energy\n"
  crt 10 [
    setxy random-xcor random-ycor
    set energy random 100
    set shape "flower"
  ]
  reset-ticks
  print(df)
end

to go

  ask turtles [
    move
    set energy energy - 1
  ]
  record-data
  tick
  if ticks > 50 [
    csv:to-file file-name df
    stop
  ] ;; we stop when we collected 50 rows of data
end

to move
  rt random 360
  lt random 360
  fd 1
end

to record-data
  ask turtles [
    let row csv:from-string (word ticks "," who "," xcor "," ycor "," energy)
    set df lput (first row) df
  ]
end


to load-data
  let data csv:from-file file-name
  foreach  but-first data [ row ->
    ;; The but-first will allow us to skip the headers
    ;; each row has  [tick,id,x,y,energy]
    let tick-count item 0 row
    let turtle-id item 1 row
    let x item 2 row
    let y item 3 row
    let energy-level item 4 row

    ;; Find or create the turtle and set its properties
    if not any? turtles with [who = turtle-id] [
      create-turtles 1 [
        set who turtle-id
        set shape "flower"
      ]
    ]
    ask turtle turtle-id [
      setxy x y
      set energy energy-level
    ]
  ]
end


to stop-simulation
  csv:to-file file-name df
  stop
end

```

Next we are going to learn how we can load the data from `csv` file in netlogo.

### Loading

We are going to create a procedure called `load-data` data which allows us to load the data that we have saved previously.

```shell
to load-data
  let data csv:from-file file-name
  foreach  but-first data [ row ->
    ;; The but-first will allow us to skip the headers
    ;; each row has  [tick,id,x,y,energy]
    let tick-count item 0 row
    let turtle-id item 1 row
    let x item 2 row
    let y item 3 row
    let energy-level item 4 row

    ;; Find or create the turtle and set its properties
    if not any? turtles with [who = turtle-id] [
      create-turtles 1 [
        set who turtle-id
        set shape "flower"
      ]
    ]
    ask turtle turtle-id [
      setxy x y
      set energy energy-level
    ]
  ]
end
```

### Ref

1. [Docs](https://ccl.northwestern.edu/netlogo/docs/csv.html)
