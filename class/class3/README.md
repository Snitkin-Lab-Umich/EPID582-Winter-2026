Class 3 – For loops and computing on multiple files
===============================

Goal
----

- In this module, We will apply for loops to perform same repetitive tasks on different files

Motivation from last class
--------------------------
If you recall from last class we learned how to count the number of sequences in a fasta file. For example, here is the command we used to count the number of sequences in an E. coli genome assembly.

```
grep ">" E_coli.fna | wc -l
```

We then were hoping to be able to apply this command to count the number of sequences in all the fasta files in the current directory by using wildcards as follows.

```
grep ">" *.fna | wc -l
```

Unfortunately, this didn't work as hoped. Instead, the grep command before the pipe is pulling the header lines from all the fasta files, and then send those to wc. So, we end up with the total number of sequences in all of our files, which is not as useful. To get the number of sequences in each individual file we will learn for loops, which are essential for automation of repetitive tasks.

Using for loops to perform same actions on different files
----------------------------------------------------------

So, using the wildcard in the command above did not have the desired action, as it told us the total number of lines in all files combined, instead of in each individual one. What we need is a way to execute our wc command on each file, but using a single command. You might say to yourself that it isn't a big deal to just run the command three times (once for each file), but what if you had 10 sequences? Or 100 sequences? It becomes inefficient and error prone, so we want to avoid that. What we are going to do instead is use a for loop, to loop over each file and run our command. For loops are not something unique to Unix, and are a fundamental tool for most programming languages for executing repetitive tasks in a streamlined way.


To get a sense for how for loops look in Unix and how they work, try the below example of for loop, that loops over a bunch of numbers and prints out each value until the list is exhausted.

```
for i in 1 2 3 4 5; do echo "Looping ... number $i"; done
```

The above for loop has the following key pieces:

1. for - All for loops start with the word 'for', to indicate the loop command is starting
2. Loop variable - In our case the loop variable is 'i'. We call it i here, but it could be called anything. The loop variable is what is going to change each time through the loop, and allow us to perform a single command in slightly different ways (e.g. run our grep/wc pipe on different files). 
3. Loop list - In our case the loop list is '1 2 3 4 5'. The loop list is the set of things you want to apply your command to. 
4. do - All for loops have the word 'do', which indicates that you are about to enter the command that will be repeated each time through the loop
5. Loop command(s) - After do comes the command that you want to run. A few things to note: i) this command should always include the loop variable, as that is what is going to change each time the loop statement is executed and ii) when you use the loop variable you need to preface it with a $ (e.g. $i)
6. done - All for loops have the word 'done', which indicates that the loop is ending. The commands between 'do' and 'done' get executed each time through the loop

So, what actually happens when we run the above for loop? Well, the following occurs:

1. The loop variable i is assigned 1 (the first value in our loop list)
2. echo "Looping ... number 1" is executed
3. The loop variable i is assigned 2 (the second value in our loop list)
4. echo "Looping ... number 2" is executed
5. The loop variable i is assigned 3 (the third value in our loop list)
6. echo "Looping ... number 3" is executed
7. The loop variable i is assigned 4 (the fourth value in our loop list)
8. echo "Looping ... number 4" is executed
9. The loop variable i is assigned 5 (the fifth value in our loop list)
10. echo "Looping ... number 5" is executed

OK - now let's create a for loop to count the number of sequences in each of our files!

```
for filename in E_coli.fna Kleb_pneu.fna Acinetobacter_baumannii.fna;
do
grep ">" $filename | wc -l
done
```
It works! Now let's make the code a little more streamlined and reusable by making it so it works on all fasta files in the current directory. To accomplish this we can use a wildcard in our loop list as follows:

```
for filename in *.fna;
do
grep ">" $filename | wc -l
done
```


One thing that would make it better is if the name of the file was printed out along with the number of sequences. Can you add a line to the for loop to accomplish this ?

Hint - look at how we printed something to the screen in our counting loop.

<details>
  <summary>Solution</summary>

```
for filename in *.fna;
do
echo $filename
grep ">" $filename | wc -l
done
```
</details>

Lastly, now that we have made this for loop more generalizable, we can apply it to fasta files in a new directory! How would you change the for loop to run on the fasta files in the more_fasta directory?

<details>
  <summary>Solution</summary>

```
for filename in more_fasta/*.fna;
do
echo $filename
grep ">" $filename | wc -l
done
```
</details>

Turning our fasta counter code into a shell script
--------------------------------------------------

Above we applied our for loop to count the number of sequences in a fasta file to two different directories by copying over the code and slightly modifying it. This worked, but is actually bad practice for a few reasons:
1) If you come up with a way to improve your loop (e.g. provide some more informative print outs), you'd have to change it in all the copies you made
2) Even worse, if you find a bug in your code, you have to fix the bug in all copies, which can be tedious and error prone

To avoid copying over our code, we can instead store the code in a file called a shell script, that we can then call everytime we want to run the code! 

Shell scripts are just the Unix version of a computer program, and are comprised of sets of Unix commands. 

We have gone ahead and put our for loop into a file called 'fasta_counter.sh'. Let's now open it in a text editor. There are many different programs that can be used to edit files in a Unix environment. Some of them are extremely complex and powerful, others are simple and there are even options that have a graphical interface for those of you craving that :). In class we will use the most basic text editor available, which is called nano. To edit the fasta_counter.sh file, type the follow command.

```
nano fasta_counter.sh
```

You can see that our for loop is there, now let's run it! First, you can exit nano as follows:

1. Save - type ctl-o and then enter
2. Exit - type ctl-x

Next, run the shell script as follows:

```
bash fasta_counter.sh
```

How would you run fasta_counter.sh on the more_fasta directory, without changing the for loop? Hint - fasta_counter.sh work on files in the current directory, so you need to run it from the more_fasta directory.

<details>
  <summary>Solution</summary>

```
#Go into the more_fasta directory
cd more_fasta

#Use relative paths to run fasta_counter.sh
bash ../fasta_counter.sh
```
</details>


Now, this is nice, but as it stands the shell script will only count the number of sequences in the directory in which it is stored. What would be even better is if we could apply it to count the number of sequences in any directory, without going there! To do this, we can give our script a command line argument. Command line arguments are additional information that you provide a shell script, which the shell script can then use to run in a slightly different manner. In bash, command line arguments go into special variables with numeric names (e.g. $1 for the first command line arugment, $2 for the second, etc.) 


Lets take an example to understand what those parameters stands for:


```
./some_program.sh Argument1 Argument2 Argument3
```

In the above command, we provide three command line arguments that acts like an input to some_program.sh 
These command line argument inputs can then be used to inside the scripts in the form of $0, $1, $2 and so on in different ways to run a command or a tool.

For this script $1 would contain "Argument1" , $2 would contain "Argument2" and so on...

Lets try to modify our for loop inside the fasta_counter.sh script to use the first command line argument to hold the directory we would like to apply it to. Our loop will be as follows:

```
for filename in $1/*.fna;
do
echo $filename
grep ">" $filename | wc -l
done
```

Now, we can run our updated shell script as follows:

```
./fasta_counter.sh more_fasta
```

OK - now that we know how to create shell scripts to house useful commands, let's create a shell script for our gff feature counter command from last class! Use nano to open up feature_counter.sh, where you will see the following:

```
#A BASH SCRIPT TO COUNT RRNA, TRNA, AND CDS FEATURES IN EACH GFF FILE                 
  
#USAGE - bash feature_counter.sh <directory containing gff files>


for ____ in ______ # REMOVE BLANKS AND FILL IN - HINT SIMILAR TO FASTA_COUNTER.SH BUT WITH GFF NOT FNA FILES 
do

        #PRINT THE NAME OF THE FILE


        #ENTER COMMAND TO GET THE NUMBER OF TRNA FEATURES

done
```

Challenge - fill in the missing pieces to enable counting the number of tRNA features in gff files in a directory provided in the first command line argument.

<details>
  <summary>Solution</summary>

```
for gff in $1/*.gff 
do

        #PRINT THE NAME OF THE FILE
        echo $gff

        #ENTER COMMAND TO GET THE NUMBER OF TRNA FEATURES
        cut -f 3 $gff | grep tRNA | wc -l

done
```
</details>

Now run feature_counter.sh on the gff file in the current directory.

```
bash feature_counter.sh ./
```

Challenge - modify feature_counter.sh to take a second command line argument that enables searching for any feature type!

<details>
  <summary>Solution</summary>

```
for gff in $1/*.gff 
do

        #PRINT THE NAME OF THE FILE
        echo $gff

        #ENTER COMMAND TO GET THE NUMBER OF TRNA FEATURES
        cut -f 3 $gff | grep $2 | wc -l

done
```
</details>
