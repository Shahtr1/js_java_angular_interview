# Java, Angular, and JavaScript Interview Questions & Answers

## **Java Multithreading Questions**

### **1. What is multithreading in Java? How is it different from multiprocessing?**

**Multithreading** is the ability of a CPU to execute multiple threads simultaneously within a single process. It allows concurrent execution of multiple parts of a program to maximize CPU utilization.

**Differences:**

| Feature               | Multithreading                       | Multiprocessing                              |
| --------------------- | ------------------------------------ | -------------------------------------------- |
| **Definition**        | Multiple threads in a single process | Multiple processes, each with its own memory |
| **Memory Usage**      | Shared memory space                  | Separate memory space                        |
| **Context Switching** | Faster                               | Slower                                       |
| **Communication**     | Easier using shared variables        | More complex (IPC needed)                    |
| **Performance**       | More efficient for lightweight tasks | Better for heavy tasks                       |

---

### **2. What is the difference between extending the Thread class and implementing the Runnable interface?**

| Feature              | Extending `Thread` Class              | Implementing `Runnable` Interface |
| -------------------- | ------------------------------------- | --------------------------------- |
| **Inheritance**      | Cannot extend another class           | Can extend another class          |
| **Code Reusability** | Less flexible                         | More flexible                     |
| **Memory Usage**     | Each thread creates a separate object | Shares the same instance          |

---

### **3. Explain the different states of a thread in Java.**

Threads in Java can exist in the following states:

1. **New** – Created but not started.
2. **Runnable** – Ready to run but waiting for CPU.
3. **Running** – Actively executing.
4. **Blocked/Waiting** – Waiting for a resource or signal.
5. **Timed Waiting** – Sleeping for a specific duration.
6. **Terminated** – Execution finished.

---

### **4. How does a thread transition from Runnable to Running state?**

A thread moves from **Runnable to Running** when the **Thread Scheduler** selects it for execution based on factors like priority and CPU availability.

---

### **5. What is the difference between Blocked and Waiting states?**

| Feature              | Blocked State                              | Waiting State                                    |
| -------------------- | ------------------------------------------ | ------------------------------------------------ |
| **Cause**            | A thread tries to access a locked resource | A thread is waiting for notification             |
| **Example**          | Trying to enter a synchronized block       | `wait()` without timeout                         |
| **Resume Condition** | Lock becomes available                     | Another thread calls `notify()` or `notifyAll()` |

---

### **6. What happens if we call the `run()` method directly instead of `start()`?**

If `run()` is called directly, the method executes in the **same thread** rather than starting a new thread. This eliminates the benefits of multithreading.

---

### **7. Can we start a thread twice? Why or why not?**

No, once a thread is started, it **cannot be restarted**. If `start()` is called on an already started thread, an `IllegalThreadStateException` is thrown.

---

### **8. What happens if `start()` is called more than once on the same thread?**

The JVM throws an `IllegalThreadStateException` because a thread can only be started once.

---

### **9. How does the `synchronized` keyword work in Java?**

- Ensures that **only one thread** can execute a synchronized method/block at a time.
- Prevents **race conditions** by locking an object.
- Helps in achieving **thread safety**.

---

### **10. What are deadlocks in Java? How can they be avoided?**

A **deadlock** occurs when two or more threads hold resources and wait for each other indefinitely.

**Ways to avoid deadlocks:**

- Acquire locks in a **consistent order**.
- Use **timeout-based locking**.
- Minimize the use of locks where possible.

```java
public class Main {
    private static String firstName = "Shahrukh";
    private static String lastName = "Tramboo";

    private class InnerMain1 extends Thread {
        public void run() {
            synchronized (firstName) {
                System.out.println("Thread1 locked firstName");
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                System.out.println("Thread1 trying to lock lastName...");

                synchronized (lastName) {
                    Main.firstName = "Thread1_firstName";
                    Main.lastName = "Thread1_lastName";

                    System.out.println("firstName from Thread1 " + Main.firstName);
                    System.out.println("lastName from Thread1 " + Main.lastName);
                }
            }

        }
    }

    private class InnerMain2 extends Thread {
        public void run() {
            synchronized (lastName) {
                System.out.println("Thread2 locked lastName");
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                System.out.println("Thread2 trying to lock firstName...");
                synchronized (firstName) {
                    Main.firstName = "Thread2_firstName";
                    Main.lastName = "Thread2_lastName";

                    System.out.println("firstName from Thread2 " + Main.firstName);
                    System.out.println("lastName from Thread2 " + Main.lastName);
                }
            }

        }
    }

    public static void main(String[] args) {
        Main main = new Main();

        InnerMain1 thread1 = main.new InnerMain1();
        InnerMain2 thread2 = main.new InnerMain2();

        thread1.start();
        thread2.start();
    }

}
```

---

### **11. What is a volatile variable in Java? How is it different from synchronized?**

- The `volatile` keyword ensures that a variable is **always read from main memory**, avoiding thread-local caching.
- Unlike `synchronized`, it does **not block** other threads.

---

### **12. Given the following code snippet, what will be the output? Will the execution order be deterministic?**

```java
class TestThread extends Thread {
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + " - " + i);
        }
    }

    public static void main(String args[]) {
        TestThread t1 = new TestThread();
        TestThread t2 = new TestThread();
        t1.start();
        t2.start();
    }
}
```

The execution order **is not deterministic** because thread scheduling is managed by the JVM and CPU. Different runs may produce different sequences of output.

If `t1.run();` is used instead of `t1.start();`, the method will execute in the **main thread**, not in a separate thread.

---

### **13. Identify the issue in the following code:**

```java
class DemoThread extends Thread {
    public void run() {
        System.out.println("Thread is running...");
    }

    public static void main(String args[]) {
        DemoThread t1 = new DemoThread();
        t1.start();
        t1.start();  // What happens here?
    }
}
```

Calling `t1.start();` twice results in an `IllegalThreadStateException` because a thread cannot be restarted after it has already been started.

---

### **14. Modify the given program to ensure thread-safe execution.**

```java
class Counter {
    private int count = 0;

    public void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}

class ThreadExample extends Thread {
    Counter counter;

    ThreadExample(Counter counter) {
        this.counter = counter;
    }

    public void run() {
        for (int i = 0; i < 1000; i++) {
            counter.increment();
        }
    }

    public static void main(String args[]) throws InterruptedException {
        Counter counter = new Counter();
        ThreadExample t1 = new ThreadExample(counter);
        ThreadExample t2 = new ThreadExample(counter);
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println("Final Count: " + counter.getCount());
    }
}
```

The `increment()` method must be marked as `synchronized` to prevent **race conditions** when multiple threads modify the shared variable.

---

### **15. What are equals and hashcode contracts?**

- `equals()` must be **consistent** and define object equality.
- `hashCode()` must return the **same value** if `equals()` returns `true`.
- Objects that are **equal must have the same hashCode**.

---

### **16. What are Records?**

Java **Records** (introduced in Java 14) are immutable data classes that simplify the creation of DTOs by automatically generating **getters, equals, hashCode, and toString**.

---

### **17. What is the difference between ArrayList and LinkedList?**

| Feature                | ArrayList     | LinkedList         |
| ---------------------- | ------------- | ------------------ |
| **Data Storage**       | Dynamic array | Doubly linked list |
| **Access Speed**       | Fast (`O(1)`) | Slow (`O(n)`)      |
| **Insertion/Deletion** | Slow (`O(n)`) | Fast (`O(1)`)      |

---

### **18. How does HashSet ensure uniqueness?**

- Uses a **HashMap internally**.
- Stores values as **keys in a HashMap** to prevent duplicates.

---

### **19. What is the difference between fail-fast and fail-safe iterators?**

| Type         | Fail-Fast                                | Fail-Safe                                   |
| ------------ | ---------------------------------------- | ------------------------------------------- |
| **Behavior** | Throws `ConcurrentModificationException` | Allows modification while iterating         |
| **Example**  | `ArrayList`, `HashMap`                   | `ConcurrentHashMap`, `CopyOnWriteArrayList` |

---

### **20. How do you remove duplicates from an ArrayList?**

Using a `Set`, `distinct()` in streams, or `HashSet`.

---

### **21. How does TreeMap maintain sorting order?**

- Uses a **Red-Black Tree** internally.
- Sorts keys **in ascending order** by default.

---

### **22. Can we store `null` keys and values in HashMap?**

- **Null keys:** Allowed (`HashMap` allows one null key).
- **Null values:** Allowed (multiple null values can be stored).

---

## **Angular Interview Questions**

### **1. How is Angular different from AngularJS?**

| Feature          | AngularJS  | Angular         |
| ---------------- | ---------- | --------------- |
| **Architecture** | MVC        | Component-based |
| **Language**     | JavaScript | TypeScript      |
| **Performance**  | Slow       | Faster          |

---

### **2. What is the purpose of `NgModule`?**

It **organizes Angular applications** by defining:

- Components
- Services
- Imports & Exports

---

### **3. Explain the purpose of the `@Component` decorator.**

Defines:

- Selector (`<app-root>`)
- Template
- Styles

---

### **4. What is Two-way Binding in Angular?**

Synchronizes the **model** and **view** using `[()]` syntax.

---

### **5. How do you pass data between components?**

- **@Input()** (Parent to Child)
- **@Output()** (Child to Parent)
- **Services & BehaviorSubject** (Global communication)

---

### **6. What is the difference between `providedIn: 'root'` and providing services at the module level?**

- **`providedIn: 'root'`**: The service is available **globally** throughout the application and is **singleton**.
- **Providing in a module**: The service is available **only within the module** and is **not shared globally**.

---

### **7. What is Template-driven Forms, and how does it work?**

- Uses the **NgModel directive** for two-way data binding.
- Forms are defined in the HTML file.
- Simpler but **less scalable** than reactive forms.

---

### **8. What is Reactive Forms, and how is it different from Template-driven Forms?**

| Feature        | Template-driven Forms    | Reactive Forms                  |
| -------------- | ------------------------ | ------------------------------- |
| **Approach**   | Declarative (HTML-based) | Programmatic (TypeScript-based) |
| **Validation** | Uses directives          | Uses functions                  |
| **Best For**   | Simple forms             | Complex, dynamic forms          |

---

### **9. What is TrackBy, and how does it optimize performance?**

- `trackBy` is used in `*ngFor` to **identify elements uniquely**, preventing unnecessary re-renders.
- It improves performance in **large lists** by avoiding DOM manipulation.

---

### **10. How is BehaviorSubject used for state management?**

- **BehaviorSubject** holds the latest value and emits it to new subscribers.
- Used in **services** to share state across components.
- Ideal for handling **real-time updates**.

---

## **JavaScript Interview Questions**

### **1. What are `let`, `const`, and `var`? How do they differ?**

| Feature           | `var`    | `let` | `const` |
| ----------------- | -------- | ----- | ------- |
| **Scope**         | Function | Block | Block   |
| **Reassignable?** | Yes      | Yes   | No      |

---

### **2. What is hoisting in JavaScript?**

- **Variables & functions are moved to the top** before execution.
- `var` declarations are hoisted but **not initialized**.

---

### **3. What is closure, and how does it work?**

- A **closure** is a function that retains access to its outer scope even after the outer function has finished execution.
- Useful for **data encapsulation** and **creating private variables**.

```js
function myFunction() {
  let a = 4;
  return function () {
    return a * a;
  };
}

const closureFunc = myFunction(); // myFunction executes, but a remains accessible
console.log(closureFunc());
```

---

### **4. What are Immediately Invoked Function Expressions (IIFE)?**

- **IIFE** is a function that runs **immediately after definition**.
- Used to **avoid polluting the global scope**.

---

### **5. What is callback hell, and how can it be avoided?**

- **Callback hell** occurs when multiple nested callbacks make the code unreadable.
- Avoided using:
  - **Promises**
  - **Async/Await**
  - **Modular functions**

---

### **6. What are Promises in JavaScript?**

- **Promises** represent the eventual completion (or failure) of an asynchronous operation.
- **States**:
  - `pending` – Initial state
  - `fulfilled` – Completed successfully
  - `rejected` – Failed

---

### **7. What are async/await, and how do they work?**

- **Async functions** return a `Promise`.
- **Await** pauses execution until the `Promise` is resolved.

---

### **8. How do you handle errors in async/await functions?**

- Using **try...catch** block.

---

## LeetCode:

### 1. Problem: Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.

- You may assume that each input would have exactly one solution, and you may not use the same element twice.
- You can return the answer in any order.

```java
import java.util.*;

class Solution {
    Map<Integer, Integer> map = new HashMap<>();
    public int[] twoSum(int[] nums, int target) {
        for(int i = 0; i < nums.length; i++){
            int item = nums[i];
            if(map.get(item) != null){
                return new int[]{i, map.get(item)};
            }
            int difference = target - item;
            map.put(difference, i);
        }
        return new int[]{0, 0};
    }
}
```
