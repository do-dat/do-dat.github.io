---
title: "Non-minimum phase case"
date: 2020-07-20T15:34:30-04:00
categories:
  - Examples
---

<img src="https://github.com/do-dat/do-dat.github.io/blob/master/assets/images/DOB.png?raw=true" alt="Sublime's custom image"/>

It is assumed that in the figure,

- ![equation](https://latex.codecogs.com/gif.latex?C(s)&space;=&space;1/(s&plus;1)),
- ![equation](https://latex.codecogs.com/gif.latex?P_n(s)&space;=&space;(s^2&plus;s&plus;5)/(s^3&plus;3s^2&plus;3s&plus;1)),
- ![equation](https://latex.codecogs.com/gif.latex?P(s)&space;=&space;(s^2&plus;\beta_1&space;s&space;&plus;&space;5)/(s^3&plus;3s^2&plus;3s&plus;1)), 
where ![equation](https://latex.codecogs.com/gif.latex?-0.2\leq&space;\beta_1&space;\leq&space;2),
- ![equation](https://latex.codecogs.com/gif.latex?Q(s;1)&space;=&space;1/(\tau&space;s&space;&plus;&space;1)).

### Setup

---

The following code returns a structure variable `sysEnv` that contains the information about the system environment that will be used later in designing the DOB.

```
C = tf(1, [1, 1]);
P_n = tf([1, 1, 5], [1, 3, 3, 1]);

N = {1, [-0.2, 2], 5};
D = {1, 3, 3, 1};

sysEnv = setup_sys(N, D, P_n, C)
```

The field `nominalStab` is 1 since the nominal closed-loop system is stable, but the field `minPhase` is 0 since a given set of uncertain plants contains some non-minimum phase systems.
Therefore, there exists a constant ![equation](https://latex.codecogs.com/gif.latex?\tau^*>0) such that, for all ![equation](https://latex.codecogs.com/gif.latex?0<\tau<\tau^*), 
the closed-loop system is not robustly stable.

### Design of Q-filter

---

Then let's see if the closed-loop system is robustly stable for a particular (sufficiently large) ![equation](https://latex.codecogs.com/gif.latex?\tau).
First of all, the following code informs you that the fast dynamics of the closed-loop system is robustly stable.

```
Qcanon = tf(1, [1, 1]);
isFastDynamicsStable(sysEnv, Qcanon)
```

After several trials and errors like the following code (Unfortunately, systematic ways do not yet exist.), you can find out that at least ![equation](https://latex.codecogs.com/gif.latex?\tau&space;=&space;0.21) robustly stabilizes the closed-loop system.

```
isValidTau(sysEnv, Qcanon, 0.18)
isValidTau(sysEnv, Qcanon, 0.19)
isValidTau(sysEnv, Qcanon, 0.20)
isValidTau(sysEnv, Qcanon, 0.21)
```

In conclusion, DO-DAT suggests you to use the Q-filter for the robust stability of the closed-loop system as follows.
Although this result does not guarantee any level of performance in terms of disturbance rejection, you can notice that DOB is still available for non-minimum phase plant.

![equation](https://latex.codecogs.com/gif.latex?Q(s;1)&space;=&space;1/(\tau&space;s&space;&plus;&space;1)), for ![equation](https://latex.codecogs.com/gif.latex?\tau&space;=&space;0.21).
