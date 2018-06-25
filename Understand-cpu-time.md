
Wall-clock time is the time that a clock on the wall (or a stopwatch in hand) would measure as having elapsed between the start of the process and 'now'.

The user-cpu time and system-cpu time are pretty much as you said - the amount of time spent in user code and the amount of time spent in kernel code.

The units are seconds (and subseconds, which might be microseconds or nanoseconds).

The wall-clock time is not the number of seconds that the process has spent on the CPU; it is the elapsed time, including time spent waiting for its turn on the CPU (while other processes get to run).

The following content is from
https://www.xing.com/communities/posts/cpu-times-system-user-wall-time-cpu-own-time-1003946160


# CPU Times - System | User | Wall Time | CPU Own Time

I am sure a lot of people still look at CPU times like looking at a blackbox, so i thought i started the first article here by discussing this very important topic. 

One thing that i did learn is that CPU times are the entry point in any analysis you may be throwing yourself in. So it is really important first of all to understand the difference between user time and system time.
System time - This is the time that the CPU was used for executing system calls. It is literally the time the kernel is using the CPU for its operations. You can think of I/O operations, context switches, inter process communication, memory management, interrupt requests, etc.

User Time - This is the time the CPU spent running your code. It is called user time because the CPU is used by an operation in a program that a user has started.

It is the ratio between user time and system time that gives you the hints on possible performance bottlenecks.

Case 1 - High user time and low system time 

This is the sign that the problem lies within your application. In JAVA, this means the problem is inside your JVM. It could be the efficiency of your code, it could be memory leaks leading to garbage collection. This is already a good starting point on your path downwards to finding the source of the problem, since you know you are not delayed/restricted by some system limitations

Case 2 - Low user time and high system time 

High system time usually means that somewhere, somehow a queue is beeing built. Take for example logging. Suppose your application wants to access a log method that is synchronized. Let's go further, and say you have a bunch of users doing something in your application, and the log level is high enough for this method to become a bottleneck. Synchronized basically means that when one thread is executing a synchronized method for an object, all other threads invoking synchronized methods for the same object will suspend execution until the first thread is done with the object. This will eat a lot of the CPU time, that you would actually want spending time in your application, in user time.
Like Kirk was saying in his Java Performance Tuning Workshop, the system time should not grow bigger than 10 %. Any value bigger than that is an alarm that somebody is eating your valuable resources.
Now, you are well prepared and ready to drill down on that problem. You just made sure that the consumer is in your application, and hear all those words like " sampling ", "tracing", "profiling", "backtracing" etc. etc. Where to go, how to start? 

Once you make sure that your problem is in the code, and the consumer is indeed your application and not some other stuff in JVM (like poor configuration of Garbage Collection leading to excesive Garbage Collections), you need to see where is the CPU being spent. There are two indicators, often misleading if not prepared: 

Wall Time and CPU Own Time.

Wall Time - I think there is not better explanation than this one: 
"In the context of a task being performed on a computer, wall clock time or wall time is a measure of how much real time that elapses from start to end, including time that passes due to programmed (artificial) delays or waiting for resources to become available. In other words, it is the difference between the time at which a task finishes and the time at which the task started." (Wikipedia)

A good example for this is a thread dispatcher. Suppose you have a couple of threads, and one thread dispatcher waiting for tasks. Waiting for tasks means is actually actively listening to something...listening to a socket for example. This actively listening on the socket for incoming requests will be measured as CPU Time, and WILL APPEAR as the dominating CPU consumer in your CPU Consumer list. That does not mean that this is a problem, it is just the time reported by the CPU from starting that task, to finishing that task. It is a task and not a piece of your code, right?

CPU Own Time - This is the time actually spent by CPU executing method code, and this is the one you are most interested in. This is where you have to dig: for invocation count vs cpu own time. A low invocations count with a large cpu own time usually means your code is unefficient.

I hope this shades some light in the darkness of CPU time measuring when it comes to performance tuning and testing.
