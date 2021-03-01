---
layout: post
title:  "ExecutorService"
date:   2021-03-01 00:01:02 +0900
category: java
---
## thread pool management
- we can running some task asynchronously using java thread.
- jvm execute main method using called by "main thread". when main thread hits new Thread(), it created another new thread, and new thread finish job, jvm will stop.

```java
package me.byoungju94;

import java.util.logging.Logger;

public class Main {

    private static final Logger logger = Logger.getGlobal();

    private static void printThreadId() {
        logger.info("Thread Unique Id: " + Thread.currentThread().getId());
    }

    public static class Task implements Runnable {
        @Override
        public void run() {
            Main.printThreadId();
        }
    }

    public static void main(String[] args) {
        printThreadId();
        
        new Thread(new Task()).start();
    }
}
```

<br />

- we can create multiple thread for disposed multiple task.

```java
package me.byoungju94;

import java.util.logging.Logger;
import java.util.stream.IntStream;

public class Main {

    private static final Logger logger = Logger.getGlobal();

    private static void printThreadId() {
        logger.info("Thread Unique Id: " + Thread.currentThread().getId());
    }

    public static class Task implements Runnable {
        @Override
        public void run() {
            Main.printThreadId();
        }
    }

    public static void main(String[] args) {
        printThreadId();

        IntStream.range(0, 10).forEach(i -> {
            new Thread(new Task()).start();
        });
    }
}
```

<br />

- one java thread per one os thread.
- it's not effective using multiple thread. creating thread is very expensive operation.
- if we settings fixed number of thread bigger than number of core in machine's cpu, it will do time-sharing operating system. so having too many thread is not effective. 
- so we create fixed number of thread in thread pool when application bootstrap, and pick thread from thread pool whenever it needs.
- java.util.concurrent.ExecutorService internally using blocking queue for store submitted multiple task.
- fixed number of thead fetch task from queue, and disposed finish then get new a task from queue.
- it's satisfied thread-safe and concurrent operations

```java
package me.byoungju94;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.logging.Logger;
import java.util.stream.IntStream;

public class Main {

    private static final Logger logger = Logger.getGlobal();

    private static final ExecutorService executorService = Executors.newFixedThreadPool(Runtime.getRuntime().availableProcessors());

    private static void printThreadId() {
        logger.info("Thread Unique Id: " + Thread.currentThread().getId());
    }

    public static class Task implements Runnable {
        @Override
        public void run() {
            Main.printThreadId();
        }
    }

    public static void main(String[] args) {
        printThreadId();    

        IntStream.range(0, 50).forEach(i -> {
            executorService.execute(new Task());
        });
    }
}
```
