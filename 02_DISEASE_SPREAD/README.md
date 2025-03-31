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

In this section we are going to take into consideration some other variables and add more plots to our simulation environment. Here are some of the variables that we will take into considerations, some of them are.

1. `Incubation period`: Time between infection and symptom onset.
2. `Infectious period`: The infectious period is the time during which an infected person can spread a disease to others.
3. `Immunity duration`: Duration of immunity post-infection or vaccination.
4. `Social distancing measures`: Effectiveness of lockdowns, quarantine, and isolation.
5. `Vaccination rate`: Percentage of population vaccinated over time.

In the `setup` function we are going to have the following code in it.

```shell
globals [%infected %health %incubated]

breed [humans human]

humans-own [inc-p ims vaccinated?]
;; incubation period, imune system strength

to setup
  ca
  reset-ticks
  create-humans population [
    setxy random-xcor random-ycor
    set shape "person"
    set color green
    set ims ((random 9) + 1) / 10
    set inc-p 0
    set vaccinated? false
  ]
  ask one-of humans [set color red]

  ;; let's update the % of the health and sick human beings
  let n-infected count humans with [color = red]
  let n-health  count humans with [color = green]
  let n-incubated count humans with [color = yellow]
  set %infected calculate-percentage n-infected
  set %health calculate-percentage n-health
  set %incubated calculate-percentage n-incubated
end

```

- We start by creating a `breed` human. And we added 2 more properties to this breed which are `inc-p` and `vaccinated?`
- Upon initialization of the simulation we also set default values.

Next is our `go` procedure which looks as follows:

```shell
to go
  tick
  ;; if social distance mesures are being practiced then movements will be reduces

   ask turtles [
      rt random 100
      lt random 100
      fd random 5
  ]
  ifelse social-distance = false [ask humans [fd random 5]][ask humans [fd random 2]]

  ;; lets infect others
  ask humans with [color = red or inc-p >= infectious-period][
    ;; if a human get in contact with another human instead of getting infected their incubation period will start
    ;; depending on various factors like (their immune sytems, mask-m)
    ;; if their ims is less than 0.5 then they will get infected easily there for incubation period start
    ask other humans-here with [color != red or color != yellow ][
      let %rate-of-infection %infectious

      ifelse mask-on [ set %rate-of-infection (%infectious * .8)][ set %rate-of-infection (%rate-of-infection * 1)]
      ;; if you are vaccinated that boosts your immune system with a certain fraction (0. - .2)

      if vaccinated? [set ims ims + ((random 2) + 1) / 10]

      if (random 100) / 100 < %rate-of-infection[
        if ims < 0.95 [
          ;; the incubation period starts
          set color yellow
        ]
      ]
    ]
  ]
  ;; if a human is incubated for days and haven't recieved a vaccine then they will
  ;; will get infected

  ask humans with [color = yellow][
    if inc-p != incubation-period [set inc-p  inc-p + 1]
    if inc-p = incubation-period [
      set color red
    ] ;; you have been infected
  ]

  ;; humans can get vaccinated if they haven't
  ask n-of ((count humans with [not vaccinated?]) * vaccination-rate) humans with [not vaccinated?] [
    set vaccinated? true
  ]

  ;; then we update our health and infected % values
  let n-infected count humans with [color = red]
  let n-health  count humans with [color = green]
  let n-incubated count humans with [color = yellow]

  set %infected calculate-percentage n-infected
  set %health calculate-percentage n-health
  set %incubated calculate-percentage n-incubated

  ;; we want to stop the program if all of the humans are infected.
  if %infected = 100 [stop]
end

to-report calculate-percentage[n]
  report ( n / population) * 100
end
```

The whole code of this implementation will be found at [02_DISEASE_SPREAD.nlogo](/02_DISEASE_SPREAD/02_DISEASE_SPREAD.nlogo) file.

### PART 3 - COVID Spread and Death of People and Recovery

The above implementation is not realistic, infected people can recover, and can also die. In this section we are going to implement recovery of people and dying of those people in netlogo.

### PART 4 - COVID Spread in Cells
