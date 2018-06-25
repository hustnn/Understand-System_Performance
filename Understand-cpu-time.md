
Wall-clock time is the time that a clock on the wall (or a stopwatch in hand) would measure as having elapsed between the start of the process and 'now'.

The user-cpu time and system-cpu time are pretty much as you said - the amount of time spent in user code and the amount of time spent in kernel code.

The units are seconds (and subseconds, which might be microseconds or nanoseconds).

The wall-clock time is not the number of seconds that the process has spent on the CPU; it is the elapsed time, including time spent waiting for its turn on the CPU (while other processes get to run).

https://www.xing.com/communities/posts/cpu-times-system-user-wall-time-cpu-own-time-1003946160
