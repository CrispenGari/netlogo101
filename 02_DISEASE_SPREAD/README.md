### Disease Spread Simulation

In this tutorial we are going to do a step by step implementation of the disease spread agent based modelling. We are going to start with `Part 1` .

### PART 1

In this first part we are going to implement a simple simulation of `Covid19` disease spread model. Taking consideration the following variables.

1. `infectious rate` - a value between 0 and 1 which indicates how quickly the infection can be spread.
2. `population` - the amount of people in the environment
3. `masks` - are people wearing masks or not, this affects the infectious rate by reducing it by `50%`.

We are going to plot the following:

1. `infected people`
2. `health people`

- We are going to use line graphs to plot this out.

Together with the simulation going on we are going to display the following on the monitors.

1. `Days` - the number of days taken to infect the whole population.
2. `%infected` - the `%` of infected beings.
3. `%health` - the `%` of health beings.
4. `population` - the total people that are in the simulation.

### `Setup` Procedure

In the setup procedure we are going to have the following code in it:

```shell
globals [%infected %health]

to setup
  ca
  reset-ticks
  crt population [
    setxy random-xcor random-ycor
    set shape "person"
    set color green
  ]
  ask one-of turtles [set color red]

  ;; let's update the % of the health and sick human beings
  let n-infected count turtles with [color = red]
  let n-health  count turtles with [color = green]
  set %infected calculate-percentage n-infected
  set %health calculate-percentage n-health
end
```

The `calculate-percentage` reporter looks as follows:

```shell
to-report calculate-percentage[n]
  report ( n / population) * 100
end

```

### `Go` Procedure

```shell
to go
  tick
  ;; each and every days people will move random steps between 1 and 5
  ask turtles [
    rt random 100
    lt random 100
    fd random 5
  ]
  ;; lets infect others
  ask turtles with [color = red][
    ask other turtles-here [
      ;; if there are on the same patch we should infect
      ;; if people are wearing masks we want to reduce the %infectious by half.

      let %rate-of-infection %infectious

      ifelse mask-on [
        set %rate-of-infection (%infectious * .5)
      ][
        set %rate-of-infection (%rate-of-infection * 1)
      ]

      if (random 100) / 100 < %rate-of-infection[
        ;; if the infectious rate is higher then infect the turtles
        set color red
      ]
    ]
  ]
  ;; then we update our health and infected % values
  let n-infected count turtles with [color = red]
  let n-health  count turtles with [color = green]
  set %infected calculate-percentage n-infected
  set %health calculate-percentage n-health

  ;; we want to stop the program if all of the humans are infected.
  if (%infected - %health) = 100 [stop]
end
```

The whole code of the first part will be found in the [`00_DISEASE_SPREAD.nlogo`](/02_DISEASE_SPREAD/00_DISEASE_SPREAD.nlogo) file.

### PART 2 - Taking into considerations come variables.

In this section we are going to take into consideration some other variables and add more plots to our simulation environment. Here are some of the variables that we will take into considerations.

1. `Incubation period`: Time between infection and symptom onset.
2. `Infectious period`: Duration a person remains contagious.
3. `Immunity duration`: Duration of immunity post-infection or vaccination.
4. `Social distancing measures`: Effectiveness of lockdowns, quarantine, and isolation.
5. `Vaccination rate`: Percentage of population vaccinated over time.

### PART 3 - COVID Spread and Death of People

### PART 4 - COVID Spread in Cells
