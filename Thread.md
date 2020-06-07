# Topic 
- Basic Thread Concept
	- Volatile, Join, Synchronization, Lock, wait and notify,Latch, Thread Local, Cyclic barrier, Semaphores, Mutex, Atomic Variable
- Executor 
	- thread pool(Fix,Cached,Signle), completion service, Thread Factory, Future and callable
- Fork
- Concurrent Collection 
	- Blocking Queue, Delay Queue, Priority Queue, Concurrent Maps
- Deadlock
- Thread dump analyzer

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

