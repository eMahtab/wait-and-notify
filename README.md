# Thread Synchronization using wait() and notify()

```java
import java.util.LinkedList;
import java.util.Queue;

class SharedBuffer {
    private Queue<Integer> buffer = new LinkedList<>();
    private int capacity;
    public SharedBuffer(int capacity) {
        this.capacity = capacity;
    }

    public synchronized void produce(int value) throws InterruptedException {
        while (buffer.size() == capacity) {
            System.out.println(Thread.currentThread().getName() + " Buffer is full. Producer is waiting...");
            wait(); // Wait until there’s space in the buffer
        }
        buffer.add(value);
        System.out.println(Thread.currentThread().getName() + " Produced: " + value);
        notify(); // Notify the consumer that there’s data available
    }

    public synchronized int consume() throws InterruptedException {
        while (buffer.isEmpty()) {
            System.out.println(Thread.currentThread().getName() + " Buffer is empty. Consumer is waiting...");
            wait(); // Wait until there’s data in the buffer
        }
        int value = buffer.poll();
        System.out.println(Thread.currentThread().getName() + " Consumed: " + value);
        notify(); // Notify the producer that there’s space available
        return value;
    }
}

class Producer extends Thread {
    private SharedBuffer buffer;
    public Producer(SharedBuffer buffer) {
        this.buffer = buffer;
    }

    @Override
    public void run() {
        int value = 0;
        while (true) {
            try {
                buffer.produce(value++);
                Thread.sleep(1000); // Simulate time taken to produce an item
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

class Consumer extends Thread {
    private SharedBuffer buffer;
    public Consumer(SharedBuffer buffer) {
        this.buffer = buffer;
    }

    @Override
    public void run() {
        while (true) {
            try {
                buffer.consume();
                Thread.sleep(1500); // Simulate time taken to consume an item
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

public class Main {
    public static void main(String[] args) {
        SharedBuffer buffer = new SharedBuffer(5); // Shared buffer with capacity 5
        Producer producer = new Producer(buffer);
        Consumer consumer = new Consumer(buffer);
        consumer.start(); // Start the consumer thread
        producer.start(); // Start the producer thread
    }
}
```

#### Execution Output :
```
Thread-1 Buffer is empty. Consumer is waiting...
Thread-0 Produced: 0
Thread-1 Consumed: 0
Thread-0 Produced: 1
Thread-1 Consumed: 1
Thread-0 Produced: 2
Thread-0 Produced: 3
Thread-1 Consumed: 2
Thread-0 Produced: 4
Thread-1 Consumed: 3
Thread-0 Produced: 5
Thread-0 Produced: 6
Thread-1 Consumed: 4
Thread-0 Produced: 7
Thread-1 Consumed: 5
Thread-0 Produced: 8
Thread-0 Produced: 9
Thread-1 Consumed: 6
Thread-0 Produced: 10
Thread-1 Consumed: 7
Thread-0 Produced: 11
Thread-0 Produced: 12
Thread-1 Consumed: 8
Thread-0 Produced: 13
Thread-1 Consumed: 9
Thread-0 Produced: 14
Thread-0 Buffer is full. Producer is waiting...
Thread-1 Consumed: 10
Thread-0 Produced: 15
Thread-0 Buffer is full. Producer is waiting...
Thread-1 Consumed: 11
Thread-0 Produced: 16
Thread-0 Buffer is full. Producer is waiting...
Thread-1 Consumed: 12
Thread-0 Produced: 17
```
