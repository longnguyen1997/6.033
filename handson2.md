# Exercises for Hands-on 2: UNIX

# Pipe Questions
For each question, give a series of UNIX commands that will produce the result.
1. A listing of all processes that you are currently running on the Athena machine you are using, sorted by the command name in alphabetical order (i.e. a process running `acroread` should be listed before a process running `zwgc`). The output should consist only of the processes you are running, and nothing else (i.e. if you are running 6 processes, the output should only have 6 lines).
2. The number of words in the file `/usr/share/dict/words (*)` which do not
 contain any of the letters a, e, i, o, or u (upper and lower case).
3. A 5x6 matrix of entries of alternating 1's and 0's. It should look like this:
```
1 0 1 0 1
0 1 0 1 0
1 0 1 0 1
0 1 0 1 0
1 0 1 0 1
0 1 0 1 0
```

4. A "long" listing of the smallest 5 files in the /etc directory whose name contains the string `.conf`, sorted by *increasing* file size.

*Write or type your answers to questions 1-4 below. Your answers should not extend onto a second page.*

1. I used the following sequence: `ps -ce --sort=comm | tail -n +2`.
2. Permission was denied for the file.
3. I used the following sequence: `yes “1 0” | fmt -10 | head -n 6`.
4. I used the following sequence:
```bash
cd /etc
ls -rS | grep ‘.conf’ | head -5; cd ..
```

# File Redirection
Now we'd like to explore something slightly different, having to do with file redirection as discussed in Sections 6.2 - 6.4 of the paper. The authors explain that the following two commands are functionally equivalent (except that you have to remove the `temp` file afterwards in the second case). We’ll use `athena%` to indicate the command-line prompt.

```bash
athena% ls | head -1
athena% ls > temp; head -1 < temp
```

5. Try the above commands in a few different directories. What happens if you try both of the commands in the `/etc` directory on `athena`? How else can the second command not produce the same output as the first? Can you think of any negative side effects that the second construction might cause for the user? You might want to think about the commands you used to solve the first four questions and consider their behavior.

*Write or type your answers to question 5 below. Your answers should not extend onto a second page.*

5. The first command works just fine, as it doesn’t need to write to memory. When we access the `/etc` folder, we run into read-only problems, and trying to write `temp` to memory fails, with a “Permission denied” message. An additional negative side effect of the second construction is its potential screwup of the file hierarchy, as calling any commands such as those used in the previous four questions will result in a different output. `temp` will be treated like a real file, even though we only want it to be what it is, a temporary placeholder.

The UNIX paper authors explain in Section 6.3 that a user can type two commands together in parentheses separated by a semicolon, and redirect the output to a file. The file will then contain the concatenation of the two commands. The example from the paper is roughly `athena% (date ; ls) > temp1 &`.

Note that this example uses the & operator to run the process asynchronously. That is, the shell will accept and run another command without waiting for the first to finish. The authors also mention that one can use the & operator multiple times on one line. For example, we can do almost the same thing with `athena% (date & ls) > temp2 &`.

Let's explore the difference between using `;` and `&` in the examples above. First, we will write a very simple variation on the "yes" program that you encountered earlier on in this assignment. To do so, we will use the "command file" functionality described in Section 6.4 of the paper. Most people call these command files "shell scripts", since they are essentially simple scripts that are executed by the shell.

First, start up a copy of emacs editing a new file called `myyes`.

```
athena% emacs myyes
```

Now, enter the following lines into your file.

```bash
#!/bin/sh
echo y
sleep 1
echo n
```

Save the file (`Ctrl-x Ctrl-s`) and exit `emacs` (`Ctrl-x Ctrl-c`). If you don't already know what the `echo` and `sleep` commands do, look them up in the `man` pages. Lastly, make the file executable by running the following command:

```bash
athena% chmod a+rx myyes
```

Now let's try running the following two commands:

```bash
athena% (./myyes ; ./myyes) > temp3
athena% (./myyes & ./myyes) > temp4
```

*Note: Some fast multicore machines may occasionally give unexpected answers, where some output is lost - originally unexpected even to the 6.033 staff. If this occurs for you on your home machine, you may want to think briefly about why this occurs.*

6. Compare the two `temp` files. Based on your understanding of file I/O in UNIX, what is going on here, and why? Is this different from what you would expect? (If there is more than one difference between the two files, it is the ordering of the letters `y` and `n` that we are interested in).
7. The paper describes the Unix system call interface in some detail. In particular, the read and write system calls do not take the offset as an argument. Why did the Unix designers not include the offset as an argument to read and write? How would an application write to a specific offset in a file?

*Write or type your answers to questions 6 and 7 below. Your answers should not extend onto a second page.*

6. Concurrency and asynchronous execution are the key features here. Because we are running the `&` command, the second `echo` script will have no regard for the timing of the first. This is what I expected.
7. The Unix designers chose not to include the offset as a parameter because of its potentially destructive behavior. If you specified an offset, the command would write or read somewhere straight from the hard disk / memory, and you risk corrupting data that’s already there, or worse, destroy the filesystem altogether. For applications, the pointer merely moves to the offset of the file.

# Looking Around
When you log in, the system sets your current working directory to your home directory, your personal name space where you can create your own files, directories and links. You can view the contents of your current working directory with the `ls` command (see above). Use the `pwd` command to learn the absolute path of your current working directory. This will tell you where you are in the directory name space even if you move around in the directories.

The output of `pwd` reveals where the Athena administrators store your home directory. For example, `/afs/athena.mit.edu/user` tells us that the your home directory is stored as a user in the Athena namespace.

The `stat` program reports detailed information about a file including its inode number, link count, file type and other metadata. To use it, type `stat` followed by a file name at the command prompt. Run `stat` on your home directory: `stat .`.

# Creating Directories and Files
Now create a directory named `6.033-handson2` in your home directory using the `mkdir`
command. You can learn more about the `mkdir` command with `man mkdir`.

Use `ls` to verify that the new directory exists. Now change your current working directory to your new `6.033-handson2` directory using the `cd` command and verify that your working directory has changed using `pwd`.

The `stat` program reports detailed information about a file including its inode number, link count, file type and other metadata. To use it, type `stat` followed by a file name at the command prompt. Run `stat` on your home directory: `stat .`.

View the contents of your new directory using `ls -a -l`. ls normally hides the directories `.`
and `..`, but the `-a` option forces it to show them.

8. Change to the `.` entry in your new directory. What happens to your working directory? Next, change to the `..` entry. What happens to your working directory?
9. Describe a scenario where you might need to use the `.` directory.

*Write or type your answers to questions 8, 9 below. Your answers should not extend onto a second page.*

8. The working directory doesn’t change with the `.` entry. The working directory goes up a level when changing to the `..` entry.
9.  The `.` directory is simply a self-reference. We’d use it for path resolution just as the textbook and the paper say. How else would we address the folder without having to type the entire current directory name?

Change your current directory back to your new `6.033-handson2` directory and `stat` the current directory; note the link count. Now create a couple files in your new directory using `touch` and `stat` the directory again.

```bash
stat .
touch foo bar ls
stat .
```

10. What has changed in the `stat` output and why has it changed?

Now create a subdirectory `baz` in `6.033-handson2` and `stat` the directory once more.

```bash
mkdir baz
stat .
```

11. What has changed in the `stat` output this time and why has it changed? Why does the link count only change when you create a new directory?

*Write or type your answers to questions 10, 11 below. Your answers should not extend onto a second page.*

10. Access, Modify, and Change fields have all changed, since we created new files and modified the system.
11. The number of links changed this time. The link count only changes when creating a new directory because links are defined as hierarchies of directories. There’s no need to have a link when dealing with new files, as they can be directly referenced straight from the current directory without having to `cd` or refer to another directory.

# Creating Links
The `ln` command can create both hard links and soft (symbolic) links. First `stat` your file `foo` and read the output information. Then create a hard-link named `foo-lnk` and `stat` both `foo` and `foo-lnk`.
```bash
stat foo
ln foo foo-lnk
stat foo
stat foo-lnk
```
Note that everything about `foo` and `foo-lnk` is identical except for their names. If you modify `foo` you will see the modifications in `foo-lnk`.
```bash
echo Hello >> foo cat foo-lnk
```
Now create a symbolic link to `foo` and note that the symbolic link differs from the original file in several ways. Creating the symbolic link does not increase the link count of `foo` and the symbolic link does not share an inode with `foo`.
```bash
stat foo
ln -s foo foo-slnk stat foo
stat foo-slnk
```
12. One reason for supporting symbolic links is to allow linking from one disk to another disk, but you can also create a symbolic link to a file on the same disk. Name one advantage and one disadvantage of using symbolic links on the same disk.

*Write or type your answers to question 12 below. Your answer should not extend onto a second page.*

12. One advantage of using a symbolic link is that we can alias our files and thus have a primitive form of shortcuts in our filesystem. A disadvantage is that if anything happens to the original alias to the inode, then we have locked ourselves out without knowing about it. Another client could have come in and modified the alias that our soft link points to. In this case, using a hard link would just take us straight to the raw data, and we wouldn’t be in any danger from mutation or external modification.

Now `cd` into the 6.033 locker: `cd /mit/6.033`. Your home directory is accessible by the path `/mit/YOUR_USERNAME` (replace `YOUR_USERNAME` with your username). Try to change to your home directory with the command:
```bash
cd ../YOUR_USERNAME
```

13. What happened? Why?

Like your home directory, the 6.033 locker's absolute path is much longer than `/mit/6.033` and `/mit/6.033` is only a symbolic link. You can learn the absolute path name by typing `pwd` or by typing `ls -l /mit`.

14. You can reach the 6.033 locker with the path `/afs/athena.mit.edu/course/6/6.033`. Why does Athena also provide the `/mit/6.033` symbolic link?
15. How would you change the file system to make this command (`cd /mit/6.033; cd ../YOUR_USERNAME`) actually change to your home directory?

*Write or type your answers to questions 13-15 below. Your answers should not extend onto a second page.*

13. The command has two actions: it takes us to the parent directory of /mit/6.033, then from that parent directory, enters our username directory.
14. The symbolic link is for ease of typing, as we don’t want to deal with increasingly complex inputs just to navigate around our filesystem.
15. I’m not sure about this question.

# The Search Path
The UNIX shell has a configuration variable named `PATH` that tells the shell where to look in the
file system for programs we type on the command line. You can see your `PATH` variable with `echo $PATH`.

You can configure your shell to search the current working directory by adding `.` to the `PATH` using these commands:
```bash
setenv OLDPATH $PATH
setenv PATH .:$PATH
```
Now, `cd` to `/mit/6.033` and run `demo`.  Oh no, something terrible just happened! Just kidding, the demo program did not actually do anything. Verify that nothing happened with `ls`.

16. What happened to `ls`? Why isn't it listing files like it did before? (Hint: set your
path back to its original state: `setenv PATH $OLDPATH`)

Usually, it is a bad idea to have `.` in your PATH, because it is easy to run the wrong programs by accident. Instead, you can use `.` explicitly to run programs in your working directory like this: `./demo`.

*Write or type your answers to question 16 below. Your answers should not extend onto a
second page.*

16. System access wasn’t granted or something. It wouldn’t run.

# System Calls
In this final question we take a quick peek the systems calls that a program issues to the operating systems using the program `strace`. Run the following command:
```bash
strace ls
```
`strace` shows all the system calls that ls makes. Look for one of the write systems calls, and
answer the following questions.

17. What does 1 represent in the first argument of `write`? You may have to consult Section 3.6 of the Unix paper to answer the question.
18. How long did this assignment take you to complete up to this point?

*Write or type your answers to questions 17, 18 below. Your answers should not extend onto a
second page.*

17. 1 represents the `filep` argument (descriptor) as presented in Section 3.6 of the Unix paper. We’re writing
to file 1 with our arguments.
18. This took just a few days, but not all questions were completed.

# Unix Paper Reference
[The UNIX Time-Sharing System](https://web.mit.edu/6.033/www/papers/protected/cacm.html) (MIT certificates required)
