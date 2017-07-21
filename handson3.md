# Exercises for Hands-on 3: Valgrind

1. Study `helgrind`'s output and the `ph.c` program. Clearly, something is wrong with `put()` on line 61. Identify a sequence of events that can lead to missing keys for 2 threads. Using that sequence, explain why there are missing keys with 2 or more threads, but not with 1 thread. Your explanation should refer to the given code and make use of theory to justify your answer.

*Write or type your answers to both parts of questions 1 below. Your answers should not extend onto a second page.*

1. In the `for` loop between lines 60 and 65, the function is looking for a place in the table to store the value. However, there’s no lock, and thus, another thread or program could’ve modified the value at some point. Because of this collision, both threads lose out on their values. With one thread, there are no race conditions and the thread is free to put its value into the hash table without risk of another thread coming in and modifying the data concurrently.

# Fixing the Error
To avoid this sequence of events, insert lock and unlock statements in put so that the number keys missing is always 0. The relevant `pthread` calls are (for more see the manual pages, `man pthread`):
```c
pthread_mutex_t lock; // declare a lock
pthread_mutex_init(&lock, NULL); // initialize the lock
pthread_mutex_lock(&lock); // acquire lock
pthread_mutex_unlock(&lock); // release lock
```
The function `get()` has an example use of locks, and `main` initializes it.
Modify `ph.c` to make it correct and recompile with `gcc`. Test your code first with 1 thread, then test it with 2 threads. Is it correct (i.e. have you eliminated missing keys)? Check the correctness using `helgrind`. (Note that `valgrind` slows down ph a lot, so you may want to modify `NKEYS` to be some small number.)

2. Describe your changes and argue why these changes ensure correctness.
3. Is the two-threaded version faster than the single-threaded version in the `put` phase? Report the running times for the two-threaded version and the single-threaded version. Make sure to undo your `NKEYS` change.
4. Most likely (depending on your exact changes) `ph` won't achieve any speedup. Why?

*Write or type your answers to question 2-4 below. Your answers should not extend onto a second page.*

2. I did exactly what the `get` function does: I locked the `put` function during its execution so that it would not encounter any race conditions. This is obviously a very hacky and simple solution, but it works.
3. Reverting back to 100,000 keys, I found that the two-threaded version was actually slower than the
 single-threaded version.
 ```
 SINGLE-THREADED: 2.637628s put, 2.663865 get.
 TWO-THREADED: 3.011790s put, 2.932921 get.
 ```
4. This is because we’ve now locked the `put` function without optimizing for parallelism. Essentially, when
a thread performs its `put` operation, all the other threads are blocked and wait for the lock to be released.

# Living Dangerously
Remove the locks from `get()` in `ph.c`, recompile, and rerun the program.

5. You should observe speedup on several cores for the get phase of `ph`. Why?
6. Why does `valgrind` report no errors for `get()`?
7. How long did this assignment take you?

*Write or type your answers to questions 5-7 below. Your answers should not extend onto a second page.*

5. Because we parallelized `get` by removing the lock, it runs faster.
6. Because `get` is a read-only operation, there are no risks to memory and concurrent access.
Reading in parallel is fine.
7. The assignment took about an hour or two.
