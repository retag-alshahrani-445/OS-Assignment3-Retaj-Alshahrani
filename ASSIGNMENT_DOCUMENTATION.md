# Assignment 3 - Complete Documentation

**Student Name**: [Retaj Talal Al-Shahrani]  
**Student ID**: [445052009]  
**Date Submitted**: [6 May 2026]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: https://drive.google.com/file/d/1heMG0lAwFdjKgPJ6oAvRXO6qLqqZXQeT/view?usp=sharing

**Video filename**: `445052009_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [29 April 2026, 5:30 PM]
**What I implemented**: Fork Repository and set  my Id 
- Fork Repository on GitHub
- Rename the repository 
- Git clon https://github.com/retag-alshahrani-445/OS-Assignment3-Retaj-Alshahrani.git in VS code
- Change the Id to my id
- save and commit as "Set my student ID: 445052009"
**Challenges encountered**: No challenges its esay becuse I have knowledge from the previous assignment.

**How I solved it**: No 

**Testing approach**: I have knowledge from the previous assignment

**Time spent**: 30 min

---

### Entry 2 - [29 April 2026, 11:30 PM]
**What I implemented**: do task 1 Added fine-grained ReentrantLock for three counters 

**Challenges encountered**: 
it  was clear in REAd me file all  instructions was clear

**How I solved it**: by read the instructions it was easy

**Testing approach**: 
in VS I ran  the java SchedulerSimulationSync meny times before locks, totalWaitingTime varied but after locks, still small variation.

**Time spent**: 90 min

---

### Entry 3 - [30 April 2026, 2:00 AM]
**What I implemented**: Do task2: Added ReentrantLock for execution log

**Challenges encountered**: 
no problem in this said becuse it is like task 1 methode  but work in deffrent 
**How I solved it**: by read the instructions it was easy

**Testing approach**: 
I added a separate lock to protect the shared ArrayList executionLog inside logExecution() method

**Time spent**: 70 min

---

### Entry 4 - [30 April 2026, 4: 00 PM]
**What I implemented**: Do task 3: added binary Semaphore cpuSemaphore in SharedResources 

**Challenges encountered**: it was the hardest part was when I accidentally changed the code order and encountered several errors. then I forgot to add SharedResources. before cpuSemaphore 

**How I solved it**: By trying to read the previously saved code from task 2 and added SharedResources. before cpuSemaphore, I finally succeeded.

**Testing approach**: 
Added binary Semaphore cpuSemaphore in SharedResources and used it in run() and runToCompletion() methods to allow only one process at a time. then change value cpuSemaphor (1) to (2)

**Time spent**: 240 min

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:
---
First, the shared counter contextSwitchCount is updated using contextSwitchCount++, a non‑atomic read‑modify‑write operation. When two threads execute this simultaneously, they may both read the same old value, increment it, and write back the same new value, causing one increment to be lost and leading to an undercount of context switches. Second, the executionLog ArrayList is modified via executionLog.add(message), but ArrayList is not thread‑safe. Concurrent calls to add() can corrupt the list’s internal structure or throw ConcurrentModificationException

```java
public static int contextSwitchCount = 0;      // Shared counter - NEEDS PROTECTION! 
public static List<String> executionLog = new ArrayList<>();  // Shared list - NEEDS PROTECTION!
```
### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

ReentrantLock allows only one thread at a time "mutual exclusion", used for protecting shared data like counters and the log. Semaphore controls how many threads can access a resource, I used a binary semaphore "count=1" to limit CPU access to one process at a time, ensuring deterministic execution.

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

Deadlock is a situation where two or more threads are blocked forever, each waiting for a resource held by another. to prevent deadlocks in my code, I used two techniques, the first one always release locks in finally blocks – when calling lock() or acquire(), I placed unlock() or release() inside finally to ensure resources are freed even if an exception occurs; the second one avoid holding multiple locks at once – in my code, each critical section uses only one lock at a time "e.g., counters use separate locks, but no method locks more than one". This eliminates circular wait conditions.

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:
-  Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?             
I used three different locks, one for each counter, I did not use one lock for all counters.
- Explain WHY you made this choice
Because the three counters are independent. Each counter is updated by different threads at different times, if I use one lock for all, threads will wait for no reason, for example, when thread p1 updates contextSwitchCount, thread p2 cannot update completedProcessCount even though they are separate variables, that is not efficient.
- What are the trade-offs between the two approaches?         
One lock is easier to write but slower because it blocks everything. Three locks need more code but allow threads to work in parallel, the problem with one lock is that it creates a bottleneck.  

- Given that the three counters are independent, which approach provides better concurrency and why?        
Fine grained locking gives better concurrency because threads can update different counters at the same time, since the counters do not affect each other, there is no need to make them wait, this makes the simulation faster and more realistic.    


---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
Context switch counter
Completed operations counter
Total waiting times

**Why they need protection**: 
These variables are shared by all threads (operations). In the original code, they were updated without any protection. If two threads execute the `incrementContextSwitch()` function simultaneously, the value might be read as 5, then the first thread increments it to 6 and writes it, and then the second thread (which also read 5) increments it to 6 and writes it, resulting in a result of 6 instead of 7. This is a race condition that leads to lost statistics and incorrect results.      

**Synchronization mechanism used**: 
I used ReentrantLock with fine-grained locking, meaning a separate lock for each counter.     
**Code snippet**:
```java
// Paste your implementation here
//Task 1
import java.util.concurrent.locks.ReentrantLock; 
//in sharedclass
// Task 1: i choese Fine-grained locks for each counter 
    public static final ReentrantLock contextSwitchLock = new ReentrantLock();
    public static final ReentrantLock completedProcessLock = new ReentrantLock();
    public static final ReentrantLock waitingTimeLock = new ReentrantLock();
    // Method to increment context switch counter 
    public static void incrementContextSwitch() { 
        // TODO: Protect this critical section with a lock 
        contextSwitchLock.lock();
        try {
            // RACE CONDITION: Multiple threads might read and write simultaneously!
            contextSwitchCount++;
        } finally {
            contextSwitchLock.unlock();}

    }
    // Method to increment completed process counter==ReentrantLock
    public static void incrementCompletedProcess(){
        //TODO: Protect this critical section with a lock
        completedProcessLock.lock();
        try{
            completedProcessCount++;
            
        } finally{ 
            completedProcessLock.unlock();
            }
        
    }
    
    // Method to add waiting time  
    public static void addWaitingTime(long time){
        // TODO: Protect this critical section with a lock
        waitingTimeLock.lock();
    try {
        totalWaitingTime+= time;
    } finally {
        waitingTimeLock.unlock();
    }
     
```

**Justification**: 

---

### Critical Section #2: Execution Log

**What resource**: 
executionLog      
**Why it needs protection**:     
ArrayList is not thread-safe when two threads call add() at the same time, the list can become corrupted or throw ConcurrentModificationException.    
**Synchronization mechanism used**:     ReentrantLock 

**Code snippet**:
```java
// Paste your implementation here
//TASK 2
public static final ReentrantLock logLock = new ReentrantLock();
public static void logExecution(String message) {
        // TODO: Protect this critical section with a lock
        logLock.lock();
        try{
            executionLog.add(message);
        // RACE CONDITION: ArrayList is not thread-safe!
        } finally{
            logLock.unlock();
        }
    }
```

**Justification**: I used  separate lock to log so it does not interfere with the counter locks, because when added try-finally block ensures the lock is always released, preventing deadlock and this makes log thread-safe without slowing down other operations.

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: Limit number of processes that can execute at the same time, with the semaphore, only one process can use the CPU at any moment, without it, processes would run in an unpredictable interleaved way, causing waiting time to vary significantly. 

**Number of permits and why**: 
Number of permit = 1.
Becuse it ensures processes run one after another, not overlapping.

**Where implemented**: Inside the run() method and the runToCompletion() method of the Process class.

**Code snippet**:
```java
//in sharedclass
 // Binary semaphore - only 1 process can execute" at a time
// Task 3
public static final Semaphore cpuSemaphore = new Semaphore(1);

// In Process.run()
try {
    SharedResources.cpuSemaphore.acquire();
    try {
        // ... process execution code ...
    } finally {
        SharedResources.cpuSemaphore.release();
    }
} catch (InterruptedException e) {
    e.printStackTrace();
}

//  in runToCompletion(
try {
    SharedResources.cpuSemaphore.acquire();
    try {
        // ... process execution code ...
    } finally {
        SharedResources.cpuSemaphore.release();
    }
} catch (InterruptedException e) {
    e.printStackTrace();
}
```

**Effect on program behavior**:  
With Semaphore(1), processes execute sequentially. The waiting times are still slightly different, but the differences are small approximate 0.5% . Without the semaphore, variations were much larger.

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: I running the program 6 times with the same settings to verify consistent results.

**Testing procedure**: 
```bash
javac SchedulerSimulationSync.java # 1 time 
java SchedulerSimulationSync   # repeated 6 times
```

**Results**: 

Run- Waiting Time 
1- 493,166
2- 492,989
3- 493,076
4- 495,913
5- 493,031
6- 492,929
the other [context switches = 21, completed processes = 10, and log entrie= 42] the same at all times.

**Why synchronization is necessary**: 
Without synchronization, Race Condition can access shared resources such as Counters or the Execution Log, leading to incorrect results such as lost operations or incorrect event logging.

**Conclusion**: The added locks and semaphore prevent race conditions. Results are consistent and reliable.

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: Ran the program 10 times after adding logLock.

**Results**: No ConcurrentModificationException occurred in any run. All log entries were recorded correctly


**What this proves**: logLock properly protects the ArrayList. Only one thread can add to the log at a time.

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: Completed processes = number of processes 10
Context switches = 21
Log entries = all quantum starts + yields + completions

**Actual values**: Completed processes = 10
Context switches = 21 
Log entries = 42

**Analysis**: 
- All counters are correct.
- Small waiting time variations.
- Synchronization ensures no data corruption.
---

### Test 4: Different Scenarios
**Scenario tested**: Changed Semaphore(1) to Semaphore(2)

**Purpose**: To show that how waiting time changes when two processes can run at the same time.

**Results**: Waiting times decreased slightly and varied more.

**What I learned**: The semaphore (1) makes execution sequential and deterministic but take more waiting time,  however semaphore (2) allows less waiting time but varied more for each process.

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

- Synchronization prevents race conditions and maintains the integrity of shared data.
- Using locks and semaphore was initially challenging to understand when they were needed.
- I learned that synchronization isn't just about preventing errors, but also about ensuring consistent results.
- I discovered that interference between threads can cause unexpected problems, even if the code appears correct.
- I understood that choosing the appropriate synchronization type (lock vs. semaphore) depends on the nature of the shared resource.
- I learned that testing the code multiple times is essential for establishing consistency and stability.
- I realized that synchronization increases code complexity but is necessary in multithreaded environments.
- I became more aware of the importance of documentation and testing to validate the solution.

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: Banking System   
When two people withdraw from the same account simultaneously, the balance must be updated concurrently. Without a locking mechanism, one withdrawal could overwrite the other's, resulting in an incorrect balance.


**Example 2**: Online Flight Booking   
When two users attempt to book the last seat on a flight at the same time, a single-authorization traffic signal ensures that only one transaction is successful, preventing overbooking.

---

### How I would explain synchronization to others:

Synchronization is like a traffic light on the street: if all the cars enter the intersection without order, there will be an accident. But if there's a traffic light, each car waits its turn and crosses safely. The same applies to code; synchronization makes each thread wait its turn before modifying a shared resource.

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/retag-alshahrani-445/OS-Assignment3-Retaj-Alshahrani.git

**Number of commits**: 6

**Commit messages**: 
1. Set my student ID: 445052009
2. Entry 1: Fork Repository and set my Id [Iam sorry]
3. Task 1 (445052009): Added ReentrantLock for counter protection
4. Task 2 (445052009): Added Reentrant Lock for execution log
5. Task 3 (445052009): Implemented semaphore for CPU control
6. Task 4 (445052009): Completed comprehensive documentation

---

## Summary

**Total time spent on assignment**: Approximately 14 hours (coding, testing, documentation, video).

**Key takeaways**: 
1. Race conditions are real and can corrupt data.
2. Fine‑grained locking improves performance when resources are independent.
3. A binary semaphore controls access to a physical resource like a single CPU.

**Most challenging aspect**: Understanding why totalWaitingTime still varied slightly after adding all locks, and learning that OS scheduler timing is the cause, not a bug.

**What I'm most proud of**: 
Before I mention what I'm most proud of, I want to say that this assignment is fantastic; I truly enjoyed it and benefited greatly from it. It wasn't long or difficult compared to the previous assignment (1). I'm proud to have completed the assignment and understood the meaning of control the syncruonunce by closures and their theoretical and practical benefits.

---

**End of Documentation**
