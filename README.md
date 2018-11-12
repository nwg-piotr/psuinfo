# psuinfo
A Python psutil-based command to display customizable system usage info in a single line. Intended for [Tint2 panel](https://gitlab.com/o9000/tint2) executors, may also be used in other panels or executed in terminal. This project derives from my [collection of Tint2 executors](https://github.com/nwg-piotr/tint2-executors) and replaces the [cpu-fan-mem.py](https://github.com/nwg-piotr/tint2-executors/blob/master/cpu-fan-mem.py) script.

Inspired by @tknomanzr 's [cpu.py](https://github.com/tknomanzr/scripts/blob/master/tint2/executors/cpu.py).

___
New in 0.0.3-2 version:

```text
-S<string> argument for custom separators
```
___

Called with no argument, the script will display following data (if appropriate sensors available):

[![cpu-fan-mem](http://nwg.pl/wiki-tint2-executors/psuinfo-def.png)](http://nwg.pl/wiki-tint2-executors/psuinfo-def.png)

- graphical CPU load bar
- CPU speed current/max
- core temperature
- fan speed
- memory available/total

You may customize display, using arguments described below:

[![cpu-fan-mem](http://nwg.pl/wiki-tint2-executors/psuinfo-customized.png)](http://nwg.pl/wiki-tint2-executors/psuinfo-customized.png)

## Command:
```
psuinfo [-C{components}] [-F] [-N] [-S<number>] [-T] [-all] [-h] [--help]
```

## Arguments:

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
  c - used memory per(c)entage
  C - free memory per(C)entage
  w - s(w)ap memory in use
  W - s(W)ap memory in use/total
  x - swap usage in %
  d - (d)rives as names usage in %
  D - (D)rives as names used/total
  n - drives as mou(n)tpoints usage in %
  N - drives as mou(N)tpoints used/total
  u - (u)ptime HH:MM
  U - (U)ptime HH:MM:SS

-F - use Fahrenheit instead of ℃
-N - display field names (except for (g)raphical CPU load bar)
-S<number> - number of spaces between components (-S2 by default)
-S<string> - a custom separator (use ' | ' to include spaces)
-T - test execution time
-all - display all possible data (for use in terminal)
```

## Sample usage:

```
No arguments, default display:

[piotr@asuspro ~]$ psuinfo
_▁______ 1.2/3.3GHz 48.0℃ 2500/m 1.3/15.6GB

Custom display, CPU average load, speed and memory w/o max values + names:

[piotr@asuspro ~]$ psuinfo -Castfm -N
avCPU: 3.4%  SPD:1.5GHz  CORE:48.0℃  FAN:2500/m   MEM:1.9GB

Custom display with graphical bar, average CPU speed, core temperature, fan speed, 
memory used/total, drives usage:

[piotr@asuspro ~]$ psuinfo -CgStfMD -N
_▁___▁__  avSPD:1.4/3.3GHz  CORE:49.0℃  FAN:2500/m  MEM:1.3/15.6GB  SDB3:101.1/210.6GB  SDA1:154.1/915.9GB

All available information (use in terminal e.g. to check which data will be available on a certain machine). 
Time of execution on the very end:

[piotr@asuspro ~]$ psuinfo -all
___▁____  CPU: 2%  3%  3%  8%  3%  2%  3%  3%   avCPU: 2.4%  CPU:1.3|1.3|1.3|1.3|1.3|1.3|1.3|1.3/3.3GHz  avSPD:1.3/3.3GHz  CORE:47.0℃  FAN:2500/m  MEM:1.3/15.6GB  MEM:12.3%  SWAP:0.0/8.0GB  SDB3:101.1/210.6GB  SDA1:154.1/915.9GB  UP:1:07:03  [2.148s]
```

### Execution time:

Since execution of the `psutil.cpu_percent()` function takes a certain time (set to 1 second here), it makes sense to learn how long it takes your customized command to produce output. To do so, use the `-T` argument:

```
[piotr@asuspro ~]$ psuinfo -CgStfM -T
▁▁__▁___  2.0/3.3GHz  48.0℃  2500/m  1.3/15.6GB  [1.012s]
```

Executing the command above took 1.113 second. To use it in a Tint2 executor, it makes sense to set at least 2 seconds long interval:
```
execp_interval = 2
```

**Why does it take so long?**

1. Data collection time for `g` and `p` component in the script is set to 1 second
2. Data collection time for `a` component is also set to 1 second

If you'd like to use (`g` **and**/**or** `p`) **and** `a`, the executor interval should be > 2 seconds.
```
piotr@asuspro ~]$ psuinfo -Cga -T -N
_____▁__  avCPU: 3.0%  [2.139s]
``` 
Since data are being collected only once, multiple use of components doesn't change much:
```
___▁▁▁▁_ avCPU:~4.4% [2.003s]
[piotr@asuspro ~]$ psuinfo -Cggppaa -T -N
___▁____  ___▁____  CPU: 4%  2%  2%  7%  2%  5%  1%  3%   CPU: 4%  2%  2%  7%  2%  5%  1%  3%   avCPU: 3.9%  avCPU: 3.9%  [2.105s]
```

## Sample Tint2 executor:

(fixed width font recommended)

```
#-------------------------------------
# Executor 1
execp = new
execp_command = psuinfo -N
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

## Installation:

### Arch Linux

Install the [psuinfo](https://aur.archlinux.org/packages/psuinfo) AUR package.

### Other distributions

Just place the [psuinfo](https://github.com/nwg-piotr/psuinfo/blob/master/psuinfo) file in your /usr/bin folder. You may need to replace the line:
```
#!/usr/bin/python
```
with what reflects location of your Python3 interpreter.

**Dependencies:** `python` (`python3`), `python-psutil` (`python3-psutil`)

The script was not tested with Python versions below 3.5.
