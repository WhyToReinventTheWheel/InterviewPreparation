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

### 
