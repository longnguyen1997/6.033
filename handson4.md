# Exercises for Hands-on 4: Map-Reduce

# Studying mapreduce.py

1. The parameters to `WordCount`'s `__init__` method are `maptask` and `reducetask`. What does `maptask` control? What does `reducetask` control?
2. Briefly explain how calling run triggers calls to both the `map` and `reduce` methods of the `WordCount` instance.
3. What do the parameters `keyvalue` and `value` of the `map` method in WordCount represent?
4. What do the parameters `key` and `keyvalues` of the `reduce` method in WordCount represent?

*Write or type your answers to questions 1-4 below. Your answers should not extend onto a second page.*

1. `maptask` controls the amount of Map tasks to be assigned. `reducetask` controls how many Reduce tasks should be performed on the intermediate data.
2. If we look at the `run()` method, we see that in it, the `pool` multiprocessing unit is calling `regions` as the variable whose result depends on a call to the `map` method—likewise with the `partitions` variable for `reduce`.
3. `keyvalue` represents the key to be analyzed (current byte offset in the file), and `value` is the value corresponding to the key.
4. `key` represents the intermediate key output, and `keyvalues` are the values mapped to it.

# Modifying mapreduce.py
Modify the program so that you can answer the following questions.

5. How many invocations are there to `doMap` and how many to `doReduce`? Why?
6. Which invocations run in parallel (assuming there are enough cores)?
7. How much input (in number of bytes) does a single `doMap` process?
8. How much input (in number of keys) does a single `doReduce` process?
9. For which parameters of `maptask` and `reducetask` do you see speedup? Why do you observe no speedup for some parameters? (You might see no speedup at all on a busy machine or a machine with a single core.)
10. What is the bottleneck of this process? If you wanted to decrease the amount of time it took to run, what would you do?

*Write or type your answers to questions 5-10 below. Your answers should not extend onto a second page.*

5. There are `m` invocations to `doMap` and `r` invocations to `doReduce`. This is because that is the specification of MapReduce itself, where we specify how many Map operations and how many Reduce operations we want to call.
6. Every invocation runs in parallel, since the whole point of MapReduce is to simplify large computations through a distributed system.
7. A single `doMap` process handles the total number of bytes divided by `m`.
8. Given the amount of intermediate keys generated from doMap, we process this number divided by `r` (roughly) keys for a single `doReduce` process.
9. There was no speedup at all, regardless of how I changed the parameters. Strangely enough, increasing the number of Reduce tasks to 4 worsened performance. There is no speedup because my Macbook only has 4 cores. If I was running on an i7-6700K, there might have been a difference.
10. The bottleneck here is how many threads we can truly utilize from our hardware. To decrease the amount of time, we can beef up our computer and run on real, dedicated MapReduce servers, or, locally, run on a processor with at least 8 cores.

# Reverse Word Index
Extend the program with a `ReverseIndex` class. The `Map` function should produce for each word in the input a pair `(word, offset)`, where offset is the byte offset in the input file. The `Reduce` function should output `(word, [offset, offset, ...])`, sorted by ascending offset.

This should require few lines of code (~25); if you find yourself writing much more code, you might be on the wrong track; ask for help to double check that you have the right plan. Fill in the `ReverseIndex` skeleton code in [reverseindex.py](https://web.mit.edu/6.033/www/assignments/reverseindex.py).

While developing `ReverseIndex`, you want to use a small input file for which you know what the right answer is, so that you can quickly iterate to the correct solution.

Once you have a correct implementation, run it on the bible and look at the top 20 results. Your output should be as follows (without the top two entries `a` and `aaron`).

```bash
aaronites [1817624, 1875693] abaddon [4789761]
abagtha [2165842]
abana [1643159]
abarim [732687, 767117, 767187, 944076]
abase [2304339, 2823950, 3322367, 3494485]
abased [3830632, 4056754, 4075715, 4589626]
abasing [4530190]
abated [28468, 29098, 29656, 573980, 950776, 1106814]
abba [3947502, 4412428, 4550068]
abda [1495474, 2149589]
abdeel [3121198]
abdi [1785594, 2005637, 2096175]
abdiel [1779568]
abdon [1048236, 1135107, 1135327, 1789463, 1797454, 1797941, 1804405, 2036728]
abednego [3468578, 3480066, 3482484, 3482731, 3482889, 3483421, 3483989, 3484242, 3484673, 3484736, 3485390, 3485499, 3485959, 3486358, 3486570]
abel [14223, 14233, 14448, 14563, 15039, 15141, 15228, 17635, 1222829, 1448386, 1448550, 1449216, 3834153, 4040933, 4686214, 4695492]
abelbethmaachah [1570895, 1700412]
```

11. Once you are finished, submit your code in `reverseindex.py` on Gradescope as a separate assignment. How long did it take you to complete this assignment?

*Write or type your answers to question 11 (timing only) below. Submit your code to Gradescope under the appropriate assignment.*

11. This assignment took about 2-3 hours combined across a few days of the week. Because I self-studied this, code is provided below as well.

    ```python
    class ReverseIndex(MapReduce):
        """
        Extends the Map Reduce class to produce ReverseIndex.
        """
        def __init__(self, maptask, reducetask, path):
            MapReduce.__init__(self,  maptask, reducetask, path)

        # Produce a (key, value) pair for each title word in value
        def Map(self, keyvalue, value):
            """
            Map function for ReverseIndex.
            The Map function should produce for each word in the input a pair (word, offset),
            where offset is the byte offset in the input file.
            See WordCount Map() in mapreduce.py for ideas on how to implement.
            """
            results = []
            i = 0
            n = len(value)
            while i < n:
                # skip non-ascii letters in C/C++ style a la MapReduce paper:
                while i < n and value[i] not in string.ascii_letters:
                    i += 1
                start = i
                while i < n and value[i] in string.ascii_letters:
                    i += 1
                w = value[start:i]
                if start < i and w.istitle():
                    results.append ((w.lower(), int(keyvalue) + start))
            return results

        # Reduce [(key,value), ...])
        def Reduce(self, key, keyvalues):
            """
            The Reducefunction should output (word, [offset, offset, ...]), sorted by ascending offset.
            Reduce function for ReverseIndex.
            Note: this should be for all words, not just the Title words
            See WordCount Map() in mapreduce.py for reference

            """
            return key, sorted(tuple[1] for tuple in keyvalues)

    def run_reverse_index(file):
        """
        Runs the map and reduce function of ReverseIndex
        See main function of mapreduce.py for reference
        :param file: path to king james bible
        :type file: string
        :return: list of tuples( names , list( byte offsets))
        :rtype: [(string, [int,]]
        """
        ri = ReverseIndex(4, 2, sys.argv[1])
        ri.run()
        return ri.Merge()
    ```
