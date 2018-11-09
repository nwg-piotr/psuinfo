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

### Command:
```
psuinfo [-C{components}] [-F] [-N] [-T] [-ALL] [-h] [--help]
```

### Arguments:

```
-C defines Components. If none given, -CgStfM will be used by default

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
  d - (d)rives as names usage in %
  D - (D)rives as names used/total
  n - drives as mou(n)tpoints usage in %
  N - drives as mou(N)tpoints used/total
  u - (u)ptime HH:MM
  U - (U)ptime HH:MM:SS

-F - use Fahrenheit instead of ℃
-N - display field names (except for (g)raphical CPU load bar)
-T - test execution time
-ALL - display all possible data (for use in terminal)
```

Sample usage:

```
No arguments, default display:

[piotr@asuspro ~]$ psuinfo
___▁_▁__ 2.4/3.3GHz 47.0℃ 2500/m 1.9/15.6GB

Custom display, CPU average load, speed and memory w/o max values + names:

[piotr@asuspro ~]$ psuinfo -Castfm -N
avCPU:~3.5% SPD:2.1GHz CORE:49.0℃ FAN:2500/m MEM:2.5GB

Custom display graphical bar, average CPU speed, core temperature, fan speed, memory used/total, drives usage:

[piotr@asuspro ~]$ psuinfo -CgStfMD -N
_▁___▁▁_ avSPD:2.1/3.3GHz CORE:47.0℃ FAN:2500/m MEM:1.9/15.6GB SDB3:101.0/210.6GB SDA1:154.1/915.9GB

All available information (use in terminal e.g. to check which data will be available on a certain machine). Time of execution on the very end:

[piotr@asuspro ~]$ psuinfo -ALL
_____▁__ CPU:~3% ~2% ~3% ~2% ~2% 14% ~3% ~0% avCPU:~5.5% CPU:1.2|1.2|1.2|1.2|1.2|1.2|1.2|1.2/3.3GHz avSPD:1.2/3.3GHz CORE:48.0℃ FAN:2500/m MEM:1.9/15.6GB SWAP:0.0/8.0GB SDB3:101.0/210.6GB SDA1:154.1/915.9GB UP:2:55:36 [2.151s]
```

### Execution time:

Since executing of the `psutil.cpu_percent()` function takes a certain time (set to 1 seond here), it makes sense to learn how long it takes your customized command to produce output. To do so, use the `-T` argument:

```
[piotr@asuspro ~]$ python ~/tint2-executors/cpu-fan-mem.py -CgStfM -T
________ 1.6/3.3GHz 49.0℃ 2500/m 3.5/15.6GB [1.067s]
```

Execution of the command above takes 1.067 second. To use it in a Tint2 executor, it makes sense to set at least 2 seconds long interval:
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
