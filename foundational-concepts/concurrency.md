```mermaid
classDiagram
direction TB
class Executor {
<<interface>>
+execute(Runnable command)
}

    class ExecutorService {
        <<interface>>
        +submit(Callable<T> task) : Future<T>
        +invokeAll(Collection<Callable<T>> tasks) : List<Future<T>>
        +shutdown()
    }

    class ScheduledExecutorService {
        <<interface>>
        +schedule(Runnable command, long delay, TimeUnit unit)
        +scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit)
    }

    class ForkJoinPool {
        +invoke(ForkJoinTask<T> task) : T
        +submit(ForkJoinTask<T> task) : ForkJoinTask<T>
        +execute(ForkJoinTask<?> task)
    }

    class ForkJoinTask {
        <<abstract>>
        +fork()
        +join()
        +compute()
    }

    class RecursiveTask {
        <<abstract>>
        +compute() : T
    }

    class RecursiveAction {
        <<abstract>>
        +compute()
    }

    class ThreadPoolExecutor {
        +execute(Runnable command)
        +shutdown()
    }

    class ScheduledThreadPoolExecutor {
        +schedule(Runnable command, long delay, TimeUnit unit)
        +scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit)
    }

    class Semaphore {
        +acquire()
        +release()
    }

    class ReentrantLock {
        +lock()
        +unlock()
        +tryLock()
    }

    class ReadWriteLock {
        <<interface>>
        +readLock() : Lock
        +writeLock() : Lock
    }

    class CountDownLatch {
        +await()
        +countDown()
    }

    class CyclicBarrier {
        +await()
    }

    class AtomicInteger {
        +incrementAndGet() : int
        +decrementAndGet() : int
        +compareAndSet(int expected, int newValue) : boolean
    }

    class ConcurrentHashMap {
        +putIfAbsent(K key, V value) : V
        +computeIfAbsent(K key, Function<K,V> mappingFunction) : V
    }

    class BlockingQueue {
        <<interface>>
        +put(E e)
        +take() : E
    }

    class ArrayBlockingQueue {
        +put(E e)
        +take() : E
    }

    class LinkedBlockingQueue {
        +put(E e)
        +take() : E
    }

    class PriorityBlockingQueue {
        +put(E e)
        +take() : E
    }

    Executor <|-- ExecutorService
    ExecutorService <|-- ThreadPoolExecutor
    ExecutorService <|-- ScheduledExecutorService
    ScheduledExecutorService <|-- ScheduledThreadPoolExecutor

    ForkJoinPool --> ForkJoinTask
    ForkJoinTask <|-- RecursiveTask
    ForkJoinTask <|-- RecursiveAction

    ReadWriteLock --> ReentrantLock
    BlockingQueue <|-- ArrayBlockingQueue
    BlockingQueue <|-- LinkedBlockingQueue
    BlockingQueue <|-- PriorityBlockingQueue
