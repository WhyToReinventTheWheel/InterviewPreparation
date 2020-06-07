### Thread 

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

### Executors
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

### NamingExecutorThreads
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

### ReturningValues
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
- 
