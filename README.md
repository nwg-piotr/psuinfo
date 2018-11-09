# psuinfo
A psutil-based command to display customizable system usage info in a single line, for Tint2 executors or CLI

Inspired by @tknomanzr 's [cpu.py](https://github.com/tknomanzr/scripts/blob/master/tint2/executors/cpu.py).

Called with no argument, the script will display following data (if appropriate sensors available):

- graphical CPU load bar
- CPU speed current/max
- core temperature
- fan speed
- memory available/total

[![cpu-fan-mem](http://nwg.pl/wiki-tint2-executors/cpu-fan-mem.png)](http://nwg.pl/wiki-tint2-executors/cpu-fan-mem.png)

### Dependencies:

`python` (`python3`), `python-psutil` (`python3-psutil`)

### Command
```
python ~/tint2-executors/cpu-fan-mem.py [-C{components}] [-F] [-N] [-T]
```

### Arguments:

```
-C stands for Components:

    g - (g)raphical CPU load bar
    p - (p)ercentage for each core (text)
    a - (a)verage CPU load (text)
    q - fre(q)ency for each thread
    Q - fre(Q)ency for each thread/max frequency
    s - current CPU (s)peed
    S - current/max CPU (S)peed
    t - CPU (t)emperature
    f - (f)an speed
    m - (m)emory in use
    M - (M)emory in use/total
    w - s(w)ap memory in use
    W - s(W)ap memory in use/total
    d - (d)rives usage in %
    D - (D)rives used/total
    u - (u)ptime HH:MM
    U - (U)ptime HH:MM:SS

-F - use Fahrenheit instead of ℃
-N - display field names (except for (g)raphical CPU load bar)
-T - show execution time (for setting executor interval, see below)
```

If no `-C` argument given, `-CgStfMu` will be used by default.

Sample usage:

```
No arguments, default display:

[piotr@asuspro ~]$ python ~/tint2-executors/cpu-fan-mem.py
_▁__▁___ 1.2/3.3GHz 47.0℃ 2500/m 3.5/15.6GB

Custom display, CPU average load, speed and memory w/o max values:

[piotr@asuspro ~]$ python ~/tint2-executors/cpu-fan-mem.py -Castfm
CPU: 3.6% 1.9GHz 47.0℃ 2500/m 3.5GB

Percentage for each core:

[piotr@asuspro ~]$ python ~/tint2-executors/cpu-fan-mem.py -CpStfM
07% 02% 05% 02% 02% 05% 04% 03% 1.2/3.3GHz 49.0℃ 2500/m 3.5/15.6GB

Fahrenheit temperature:

[piotr@asuspro ~]$ python ~/tint2-executors/cpu-fan-mem.py -F
________ 1.8/3.3GHz 118.4℉ 2500/m 3.5/15.6GB
```

### Execution time:

Since executing of the `psutil.cpu_percent()` function takes a certain time, it makes sense to learn how long it takes your customized command to produce output. To do so, use the `-T` argument:

```
[piotr@asuspro ~]$ python ~/tint2-executors/cpu-fan-mem.py -CgStfM -T
________ 1.6/3.3GHz 49.0℃ 2500/m 3.5/15.6GB [1.067s]
```

Execution of the command above takes 1.067 second. If so, it makes sense to set at least 2 seconds long interval:
```
execp_interval = 2
```

**Why does it take so long?**

1. Data collection for `g` and `p` component takes 1 second
2. Data collection for `a` component takes another 1 second

If you'd like to use (`g` **and**/**or** `p`) **and** `a`, the executor interval should be > 2 seconds.
```
[piotr@asuspro ~]$ python ~/tint2-executors/cpu-fan-mem.py -Cga -T
▁_______ CPU: 3.2% [2.003s]
``` 
Since data are being collected only once, multiple use of components doesn't change much:
```
piotr@ideapad ~ % python ~/tint2-executors/cpu-fan-mem.py -Cga -T   
▁▂ 17.8% [2.003s]
piotr@ideapad ~ % python ~/tint2-executors/cpu-fan-mem.py -Cggppaa -T
▂▃ ▂▃ 25% 30% 25% 30% 25.1% 25.1% [2.004s]
```

### Sample executor:

```
#-------------------------------------
# Executor 1
execp = new
execp_command = psuinfo
execp_interval = 2
execp_has_icon = 0
execp_cache_icon = 1
execp_continuous = 1
execp_markup = 1
execp_tooltip = 
execp_lclick_command = 
execp_rclick_command = 
execp_mclick_command = 
execp_uwheel_command = 
execp_dwheel_command = 
execp_font = Monospace 10
execp_font_color = #aaaaff 100
execp_padding = 0 0
execp_background_id = 0
execp_centered = 0
execp_icon_w = 0
execp_icon_h = 0
```
