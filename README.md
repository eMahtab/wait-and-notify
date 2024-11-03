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
