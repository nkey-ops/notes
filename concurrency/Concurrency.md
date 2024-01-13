# Java Concurrency
src: https://jenkov.com/tutorials/java-concurrency/java-happens-before-guarantee.html

# Volatile | Visibility Guarantee | 
## Write
1. Writes directly into the main memory volatile var.
2. All writes that happen before "volatile  write" 
    are guaranteed to happen before "volatile write".
3. All variables visible to the thread 
    will be written back to the main memory.

## Read 
1. Reads directly from the main memory volatile var.
2. All reads that happen after "volatile read" 
    are guaranteed to happen after "volatile read".
3. All variables visible to the thread 
    will be re-read from the main memory.

## Synchronized Block 
1. When a thread enters a synchronized block, 
    all variables visible to the thread are refreshed from main memory.
2. When a thread exits a synchronized block, 
    all variables visible to the thread are written back to main memory. 

#### WRITE
All writes that happen before "Synchronized Block" 
    are guaranteed to happen before "Synchronized Block".

#### READ
All reads that happen after "Synchronized Block" 
    are guaranteed to happen after "Synchronized Block".


# Race Condition | Thread Safety
## Read Write Modify
   Whenever 2 or more threads read shared data then try to make a modification 
   based on that value and then update it, the last thread that updates the value with 
   its own modification will overwrite a modification made by the other thread 
   that updated the value prior to this thread.

## Check then act or Slipped Conditions
Occurs whenever one thread changes the result of the condition  
based on which other thread haven't acted upon yet 
but already have passed the condition. 

    Thread 1 aquires monitor A
    Thread 1 checks condition B
    Thread 1 passes condition B
    Thread 1 releases monitor A

    Thread 2 aquires monitor A
    Thread 2 checks condition B 
    Thread 2 passes condition B 
    Thread 2 releases monitor A

    Thread 1 aquires monitor A
    Thread 1 changes condition B to condition C 
    Thread 1 acts based on a condition B
    Thread 1 releases monitor A

    Thread 2 aquires monitor A
    // Slipped Condition 
    Thread 2 changes condition B to condition C 
    Thread 2 acts based on a condition B // even though it's already C

### Solution 
Keep together condition and acts based on the condition 
in the same synchronized block


# Deadlock
Occurs in cases when two or more threads acquire the lock of two or more 
separate monitors and then each thread requires each other monitors.

    Thread 1 aquires monitor A;
    Thread 2 aquires monitor B;
    // Dead Lock  
    Thread 1 requires monitor B to proceed;    
    Thread 2 requires monitor A to proceed;

## Solution
1. Lock Ordering

2. Lock Timeout
    Thread tries to get the monitor of the desired object 
    for some period of time, if it doesn't succeed it releases all the locks acquired. 
    Returns to the point where it has started acquiring all the locks. 
    Waits for some period of time to repeat the process.

3. Deadlock detection
    Using graphs or trees to store monitors and threads that own them.
    Whenever thread tries to acquire the monitor but the monitor is occupied
    it will try to find out whether it's a deadlock or the other thread still
    hasn't finished working. 
    It's done by using a graph to find which thread owns the desired monitor 
    than check what kind state that thread is in: 

    If it's working our thread will wait and try again acquire the monitor.
    If it's waiting for other monitor use the graph to find the owner of that 
    monitor, repeat the process until the first case is true or one of the 
    monitors is occupied by the current thread (Deadlock), release all the 
    acquired monitors, start doing then task again.

# Nested Monitor Lockout
Thread 1 is holding a lock A, and waits
for a signal from Thread 2. Thread 2 needs the lock A to send the
signal to Thread 1.

    Thread 1 aquires monitor A
    Thread 1 aquires monitor B
    Thread 1 releases monitor B and 
    waits for a notification 
    //Nested Monitor Lockout
    Thread 2 waits for a realase of monitor A
    to notify

```java
//lock implementation with nested monitor lockout problem

public class Lock{
  protected MonitorObject monitorObject = new MonitorObject();
  protected boolean isLocked = false;

  public void lock() throws InterruptedException{
    synchronized(this){
      while(isLocked){
        synchronized(this.monitorObject){
            this.monitorObject.wait();
        }
      }
      isLocked = true;
    }
  }

  // Can't be accessed by the second thread because the first occupies (this) monitor
  // inside of the lock() method. 
  public void unlock(){     
    synchronized(this){
      this.isLocked = false;
      synchronized(this.monitorObject){
        this.monitorObject.notify();
      }
    }
  }

```


### Solution
Properly order lock acquirements.

# False Sharing | Performance
Occurs when two or more threads work with data that 
shares same CPU cache line. If it the case the cache line becomes invalidated
in all other CPU cache levels which then will be refreshed. The refresh process 
takes time and resources which can lead to performance penalty.

### Solution 
Place date that potentially can be used by different threads
in different cache lines by adding padding bytes to the data so 
it can fill all the cache line or group date that will be used by single thread
in one cache line or both together.

### Cons: 
    additional memory will be used.
### Pros: 
    execution time is decreased. 

![](https://jenkov.com/images/java-concurrency/false-sharing-in-java-1.png) 

# Thread Congestion
Occurs where there are many threads that what to access
synchronized data. This will lead to a queue of threads that are just waiting 
for an access to the data.

### Solution
1) Reduction of the amount of threads that are trying to access the data.
2) Roles separation: Some threads responsible for data productions others for its consumption.
   Split the data and allow to access to only certain amount of threads.
3) Non-blocking Concurrency Algorithms


# Spurious Wake Up

## Note: 
1. Calling "notify" or "notifyAll()" DOESN'T mean that the thread
that was NOTIFIED FIRST will be THE FIRST that occupies the just released monitor
but it might be the one that wasn't even in a "WAITING" state but "BLOCKED".  

2. Log such guarding loops for spurious wake-up's to check if the
are any problems with code logic like (accidental call to notifyAll() instead of notify()).
Because if there are many spurious wake-up's it might be difficult to spot them 
and a lot of computing resources to make them will be wasted.


```java
public class Lock{
  private boolean isLocked = false;

  public synchronized void lock() throws InterruptedException {
    while(isLocked){
        wait();
        /* 
           Spurious wake up may occur because 
           a newly woken-up thread wasn't the first one
           that accessed this monitor after unlock() method call.

           Because isLocked is set to false after unlock() method call,
           the thread that was trying get inside of this method (lock()) 
           successfully skips while loop (isLocked == false)
           and locks the lock (isLocked = true)
           after leaving the method the notified thread 
           might have a chance to occupy the monitor 
           and "realize" that isLocked is set to true
           which might look as a spurious wake-up.
         */

        if(isLocked)
            System.out.println("Log: Lock#lock() has a spurious wake-up");
    }

    isLocked = true;
  }

  public synchronized void unlock(){
    isLocked = false;
    notify();
  }
}
```

# Re-entrance Lockout 
Occurs when the lock is not re-entrant.\
Re-entrance means the ability of the locking or synchronisation mechanism 
to allow one or more threads to access locked / synchronized block of code
when its monitor was locked by the same thread or threads.

### Solution
1) Avoid writing code that reenters locks
2) Use re-entrant locks


# Compare and Swap operation
## Problematic
When thread/threads try to enter occupied synchronized block
it's blocked and put in a waiting state until the mutex of a synchronized block 
is released.
But there is an indefinite delay between the time when the mutex is released 
and time when the thread is unblocked.

### Solution
The Thread keeps trying to execute compare and swap operation until it succeed.
Because time between execution's tries is short thread enters synchronized block 
earlier compare to default mechanism.

#### Cons: 
If the mutex is occupied for a long time compare
and swap operations will make lots of cpu cycles.


# Process
Each process provides the resources needed to execute a program.
A process has a virtual address space, executable code, open handles to system objects,
a security context, a unique process identifier, environment variables, 
a priority class, minimum and maximum working set sizes, and at least one thread of execution. 
Each process is started with a single thread, often called the primary thread,
but can create additional threads from any of its threads.

# Thread
A thread is an entity within a process that can be scheduled for execution. 
All threads of a process share its virtual address space and system resources. 
In addition, each thread maintains exception handlers, a scheduling priority, 
thread local storage, a unique thread identifier, and a set of structures the system will 
use to save the thread context until it is scheduled.
The thread context includes the thread's set of machine registers, the kernel stack,
a thread environment block, and a user stack in the address space of the thread's process.
Threads can also have their own security context, which can be used for impersonating clients.



