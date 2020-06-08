# Topic
- http://tutorials.jenkov.com/java-concurrency/index.html
- Basic Thread Concept
	- Volatile, Join, Synchronization, Lock, wait and notify,Latch, Thread Local, Cyclic barrier, Semaphores, Mutex, Atomic Variable
- Executor 
	- thread pool(Fix,Cached,Signle), completion service, Thread Factory, Future and callable
- Fork
- Concurrent Collection 
	- Blocking Queue, Delay Queue, Priority Queue, Concurrent Maps
- Deadlock
- Thread dump analyzer

## Thread Life Cycle

New -> Runnable -> Running -> Terminated           
			|-> Waiting -> Terminated  
- New = new Thread()
- Runnable = start()
- waiting = sleep() , wait()
- Terminated = completes its task or otherwise terminates.

## Thread Creation

- Anonymous
```
Thread t = new Thread(new Runnable() {
	@Override
	public void run() {
	}
});
```
- Java lambda
```
Runnable runnable = () -> {}
```
## volatile vs synchronized:
- Changes to a volatile variable are always visible to other threads. 
```
class SharedObj
{
   // volatile keyword here makes sure that
   // the changes made in one thread are 
   // immediately reflect in other thread
   static volatile int sharedVar = 6;
}
```
Before we move on let’s take a look at two important features of locks and synchronization.

Mutual Exclusion: It means that only one thread or process can execute a block of code (critical section) at a time.
Visibility: It means that changes made by one thread to shared data are visible to other threads.
Java’s synchronized keyword guarantees both mutual exclusion and visibility. If we make the blocks of threads that modifies the value of shared variable synchronized only one thread can enter the block and changes made by it will be reflected in the main memory. All other thread trying to enter the block at the same time will be blocked and put to sleep.

In some cases we may only desire the visibility and not atomicity. Use of synchronized in such situation is an overkill and may cause scalability problems. Here volatile comes to the rescue. Volatile variables have the visibility features of synchronized but not the atomicity features. The values of volatile variable will never be cached and all writes and reads will be done to and from the main memory. However, use of volatile is limited to very restricted set of cases as most of the times atomicity is desired. For example a simple increment statement such as x = x + 1; or x++ seems to be a single operation but is s really a compound read-modify-write sequence of operations that must execute atomically.

## synchronized 
```
public void addName(String name) {
    synchronized(this) {}
}
```
```
public class MsLunch {
    private long c1 = 0;
    private Object lock1 = new Object();
    public void inc1() {
        synchronized(lock1) {
            c1++;
        }
    }
} 
```

## Locks in Java
```
private Lock lock = new Lock();
lock.lock();
try{
  //do critical section code, which may throw exception
} finally {
 lock.unlock();
}
```

## Read / Write Locks in Java
A read / write lock is more sophisticated lock than the Lock implementations shown in the text Locks in Java. Imagine you have an application that reads and writes some resource, but writing it is not done as much as reading it is. Two threads reading the same resource does not cause problems for each other, so multiple threads that want to read the resource are granted access at the same time, overlapping. But, if a single thread wants to write to the resource, no other reads nor writes must be in progress at the same time. To solve this problem of allowing multiple readers but only one writer, you will need a read / write lock.
- Read / Write Lock Java Implementation
`Read Access`   	If no threads are writing, and no threads have requested write access.
`Write Access`   	If no threads are reading or writing.

If a thread wants to read the resource, it is okay as long as no threads are writing to it, and no threads have requested write access to the resource. By up-prioritizing write-access requests we assume that write requests are more important than read-requests. Besides, if reads are what happens most often, and we did not up-prioritize writes, starvation could occur. Threads requesting write access would be blocked until all readers had unlocked the ReadWriteLock. If new threads were constantly granted read access the thread waiting for write access would remain blocked indefinately, resulting in starvation. Therefore a thread can only be granted read access if no thread has currently locked the ReadWriteLock for writing, or requested it locked for writing.

A thread that wants write access to the resource can be granted so when no threads are reading nor writing to the resource. It doesn't matter how many threads have requested write access or in what sequence, unless you want to guarantee fairness between threads requesting write access.


```
ReadWriteLock rwLock = new ReentrantReadWriteLock();
Lock readLock = rwLock.readLock();
Lock writeLock = rwLock.writeLock();
readLock.lock();
try {
    // reading data
} finally {
    readLock.unlock();
}

writeLock.lock();
 
try {
    // update data
} finally {
 
    writeLock.unlock();
}
```
## Blocking Queues
A blocking queue is a queue that blocks when you try to dequeue from it and the queue is empty, or if you try to `enqueue` items to it and the queue is already full. A thread trying to `dequeue` from an empty queue is blocked until some other thread inserts an item into the queue. A thread trying to enqueue an item in a full queue is blocked until some other thread makes space in the queue, either by dequeuing one or more items or clearing the queue completely.
- BlockingQueue Implementations
	- ArrayBlockingQueue
	- DelayQueue
	- LinkedBlockingQueue
	- PriorityBlockingQueue
	- SynchronousQueue
 ```
BlockingQueue queue = new ArrayBlockingQueue(1024);
Producer producer = new Producer(queue);
Consumer consumer = new Consumer(queue);
new Thread(producer).start();
new Thread(consumer).start();
 ```
```
public class Producer implements Runnable{
    private BlockingQueue queue = null;
    public Producer(BlockingQueue queue) {
        this.queue = queue;
    }
    public void run() {
        try {
		while(true)
            		queue.put("1");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```
 
```
public class Consumer implements Runnable{
    private BlockingQueue queue = null;
    public Consumer(BlockingQueue queue) {
        this.queue = queue;
    }
    public void run() {
        try {
       		while(true)
       			System.out.println(queue.take());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
```


 
## ThreadLocal
The Java ThreadLocal class enables you to create variables that can only be read and written by the same thread. Thus, even if two threads are executing the same code, and the code has a reference to the same ThreadLocal variable, the two threads cannot see each other's ThreadLocal variables. Thus, the Java ThreadLocal class provides a simple way to make code thread safe that would not otherwise be so.
```
public class MyRunnable implements Runnable {
    private ThreadLocal<Integer> threadLocal = new ThreadLocal<Integer>();
    @Override
    public void run() {
        threadLocal.set( (int) (Math.random() * 100D) );
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
        }
        System.out.println(threadLocal.get());
    }
}
```
- threadLocal.remove();

## Atomic Variables
The java.util.concurrent.atomic package defines classes that support atomic operations on single variables. All classes have get and set methods that work like reads and writes on volatile variables. That is, a set has a happens-before relationship with any subsequent get on the same variable. The atomic compareAndSet method also has these memory consistency features, as do the simple atomic arithmetic methods that apply to integer atomic variables.

## Daemon Threads
User threads are high-priority threads. The JVM will wait for any user thread to complete its task before terminating it.
On the other hand, daemon threads are low-priority threads whose only role is to provide services to user threads.

Since daemon threads are meant to serve user threads and are only needed while user threads are running, they won't prevent the JVM from exiting once all user threads have finished their execution.

That's why infinite loops, which typically exist in daemon threads, will not cause problems, because any code, including the finally blocks, won't be executed once all user threads have finished their execution. For this reason, daemon threads are not recommended for I/O tasks.

However, there're exceptions to this rule. Poorly designed code in daemon threads can prevent the JVM from exiting. For example, calling Thread.join() on a running daemon thread can block the shutdown of the application.
```
NewThread daemonThread = new NewThread();
daemonThread.setDaemon(true);
daemonThread.start();
```

## Thread Deadlock
A deadlock is when two or more threads are blocked waiting to obtain locks that some of the other threads in the deadlock are holding. Deadlock can occur when multiple threads need the same locks, at the same time, but obtain them in different order.

For instance, if thread 1 locks A, and tries to lock B, and thread 2 has already locked B, and tries to lock A, a deadlock arises. Thread 1 can never get B, and thread 2 can never get A. In addition, neither of them will ever know. They will remain blocked on each their object, A and B, forever. This situation is a deadlock.

The situation is illustrated below:

Thread 1  locks A, waits for B
Thread 2  locks B, waits for A

- Deadlock Prevention
 - Lock Ordering
 - Lock Timeout
 - Deadlock Detection
 	Deadlock detection is a heavier deadlock prevention mechanism aimed at cases in which lock ordering isn't possible, and lock timeout isn't feasible. Every time a thread takes a lock it is noted in a data structure (map, graph etc.) of threads and locks. Additionally, whenever a thread requests a lock this is also noted in this data structure.
	
## Interrupts
- public void interrupt() Interrupts this thread.  // thread.interrupt() 
- Thread.currentThread().interrupt();
- Many methods that throw InterruptedException, such as sleep, are designed to cancel their current operation and return immediately when an interrupt is received.
```
try {
        Thread.sleep(4000);
} catch (InterruptedException e) {
// We've been interrupted: no execution
return;
}
```
- 2nd way , Thread.interrupted, which returns true if an interrupt has been received
```
for (int i = 0; i < inputs.length; i++) {
    heavyCrunch(inputs[i]);
    if (Thread.interrupted()) {
        // We've been interrupted: no more crunching.
        return;
    }
}

OR 
Throw error 
if (Thread.interrupted()) {
    throw new InterruptedException();
}
```

## Executors
- newFixedThreadPool
```
ExecutorService execService = Executors.newFixedThreadPool(2);
execService.execute(new LoopTaskA());
execService.execute(new LoopTaskA());
execService.execute(new LoopTaskA());
execService.shutdown();
execService.execute(new LoopTaskA()); // java.util.concurrent.RejectedExecutionException
```
- ExecutorService execService = Executors.newSingleThreadExecutor();
- ExecutorService execService = Executors.newCachedThreadPool();

## NamingExecutorThreads
- ExecutorService execService = Executors.newCachedThreadPool(new NamedThreadsFactory());
```
public class NamedThreadsFactory implements ThreadFactory {
	private static int count = 0;
	private static String NAME = "MyThread-";

	@Override
	public Thread newThread(Runnable r) {
		Thread t = new Thread(r, NAME + ++count);
		return t;
	}
}
```

## ReturningValues
- Callable
```
public class CalculationTaskA implements Callable<Integer> {
	@Override
	public Integer call() throws Exception {
		return a + b;
	}
}
```
- Runnable
```
public class LoopTaskA implements Runnable {
	@Override
	public void run() {
	}
}
```
- ExecutorService
```
ExecutorService execService = Executors.newCachedThreadPool();
Future<Integer> result1 = execService.submit(new CalculationTaskA(2, 3, 2000));
Future<?> result4       = execService.submit(new LoopTaskA());
Future<Double> result5  = execService.submit(new LoopTaskA(), 999.888);

execService.shutdown();
try {
	System.out.println("Result-1 = " + result1.get());
	System.out.println("Result-4 = " + result4.get());
	System.out.println("Result-5 = " + result5.get());
} catch (InterruptedException | ExecutionException e) {
	e.printStackTrace();
}
```
- Result 
```
--------------------
Result-1 = 5
Result-4 = null
Result-5 = 999.888
--------------------
```

## CompletionService

```
ExecutorService execService = Executors.newCachedThreadPool();
CompletionService<Integer> tasks = new ExecutorCompletionService<Integer>(execService);
tasks.submit(new CalculationTaskA(2, 3, 2000));
tasks.submit(new CalculationTaskA(3, 4, 1000));
tasks.submit(new CalculationTaskA(4, 5, 500));
tasks.submit(new LoopTaskA(), 10);
execService.shutdown();
for (int i = 0; i < 4; i++) {
	try {
		System.out.println(tasks.take().get());
	} catch (InterruptedException | ExecutionException e) {
		e.printStackTrace();
	}
}
```

## Thread Returning Value
- Runnable
```
public class ValueReturningTaskA implements Runnable {
	private volatile boolean done = false;
	@Override
	public void run() {
		TimeUnit.MILLISECONDS.sleep(sleepTime);
		done = true;
		synchronized(this) {
			this.notifyAll();
		}
	}
	public int getSum() {
		if (!done) {
			synchronized(this) {
				try {
					this.wait();
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
		}
		return sum;
	}
}
```

- Setup 
```
ValueReturningTaskA task1 = new ValueReturningTaskA(2, 3, 2000);
Thread t1 = new Thread(task1, "Thread-1");
t1.start();
System.out.println(task1.getSum());

```

- Another way to add listener 

```
public interface ResultListener<T> {
	void notifyResult(T result);
}

public class ValueReturningTaskB implements Runnable {
	private ResultListener<Integer> listener;
	public ValueReturningTaskB(ResultListener<Integer> listener) {
		this.listener = listener;
	}
	@Override
	public void run() {
		listener.notifyResult(sum);
	}
}
```

## JoiningExecutorThreads

```
ExecutorService execService = Executors.newCachedThreadPool();
CountDownLatch doneSignal = new CountDownLatch(2);
execService.execute(new LoopTaskI(doneSignal));
execService.execute(new LoopTaskI(doneSignal));
execService.shutdown();
try {
	doneSignal.await();
} catch (InterruptedException e) {
	e.printStackTrace();
}
```

```
public class LoopTaskI implements Runnable {
	private CountDownLatch doneCountLatch;
	@Override
	public void run() {
		doneCountLatch.countDown();
	}
	public LoopTaskI(CountDownLatch doneCountLatch) {
		this.doneCountLatch = doneCountLatch;
	}
}

```

