NOM : RASOLOARIVONY Elysé
Prénom : Elysé
Groupe : M1 apprentissage Big data
Id : 20211003
LAB YARN 1



# Running the wordcount example 

#### List of samples 
>  yarn jar/usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

```
An example program must be given as the first argument.
Valid program names are:
  aggregatewordcount: An Aggregate based map/reduce program that counts the words in the input files.
  aggregatewordhist: An Aggregate based map/reduce program that computes the histogram of the words in the input files.
  bbp: A map/reduce program that uses Bailey-Borwein-Plouffe to compute exact digits of Pi.
  dbcount: An example job that count the pageview counts from a database.
  distbbp: A map/reduce program that uses a BBP-type formula to compute exact bits of Pi.
  grep: A map/reduce program that counts the matches of a regex in the input.
  join: A job that effects a join over sorted, equally partitioned datasets
  multifilewc: A job that counts words from several files.
  pentomino: A map/reduce tile laying program to find solutions to pentomino problems.
  pi: A map/reduce program that estimates Pi using a quasi-Monte Carlo method.
  randomtextwriter: A map/reduce program that writes 10GB of random textual data per node.
  randomwriter: A map/reduce program that writes 10GB of random data per node.
  secondarysort: An example defining a secondary sort to the reduce.
  sort: A map/reduce program that sorts the data written by the random writer.
  sudoku: A sudoku solver.
  teragen: Generate data for the terasort
  terasort: Run the terasort
  teravalidate: Checking results of terasort
  wordcount: A map/reduce program that counts the words in the input files.
  wordmean: A map/reduce program that counts the average length of the words in the input files.
  wordmedian: A map/reduce program that counts the median length of the words in the input files.
  wordstandarddeviation: A map/reduce program that counts the standard deviation of the length of the words in the input files.
```

#### help on specific sample
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \wordcount
```
Usage: wordcount <in> [<in>...] <out>
```

* count all words in downloaded e-book from Project Gutenberg (pg66464.txt) and store the output in a specific place : *elyse.rasoloarivony/raw/wordcount* (the same place as the input)

> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \wordcount /user/elyse.rasoloarivony/raw/pg66464.txt /user/elyse.rasoloarivony/wordcount

Remark : the file is named part-r-00000 in the folder wordcount
 
> hdfs dfs -cat /user/elyse.rasoloarivony/raw/wordcount/part-r-00000
```
...
receive 6
received        19
receiving       3
recently        3
recommend       1
recommended     1
recovered,      1
recovery,       1
red     1
redistribute    1
redistributing  1
redistribution. 1
reduced 2
references      2
refreshment     1
...
```

# The Sudoku example 

## create sudoku.dta in local (edge)

> cat > sudoku.dta

* sudoku.dta contents
> cat sudoku.dta
```
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

* put it in the hdfs 
> hdfs dfs -put sodoku.dta


* solve this soduku problem
$ yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \sudoku sudoku.dta
```
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

# Pi example

* estimate pi
Using several maps : here 16 maps, with 10 000 000 samples each to estimate pi!

> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \pi 16 10000000

**Estimated value of Pi is 3.14159155000000000000**

# 10 GB GraySort example 
benchmarck (in TB/min) : minimum 100 TB (~173 min in 2009, 0.573TB/min) 

Here, this sample uses just 10 GB of data (MapReduce)

MapReduce used :
* Teragen : generates raw of data to sort
* TeraSort: samples (=> N-1 samples ordered) the imput data and uses MapReduce to sort the data into a total order 
* TeraValidate : validates if output is globally sorted


* Generate 10 GB of data
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \teragen -Dmapred.map.tasks=50 100000000 /user/elyse.rasoloarivony/data/10GB-sort-input

*generated file directory : data/10GB-sort-validate/part-r-00000*


* sample and sort the data  
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /user/elyse.rasoloarivony/data/10GB-sort-input /user/elyse.rasoloarivony/data/10GB-sort-output
The partitions are located in /user.elyse.rasoloarivony/data/10GB-sort-output

* Validate the data generate by the sort
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /user/elyse.rasoloarivony/data/10GB-sort-input /user/elyse.rasoloarivony/data/10GB-sort-validate



# MapReduce2 
* We can translate Python code using Jython into a Java jar file
* using Hadoop Streaming API 

# Python MapReduce Code 

# Map step: mapper.py
```
> cat > mapper.py

[elyse.rasoloarivony@hadoop-edge01 ~]$ cat mapper.py
#!/ usr/bin/env python

""" mapper .py """

import sys
""" input comes from STDIN ( standard input ) """
for line in sys . stdin :
    # remove leading and trailing whitespace
    line = line.strip()
    # split the line into words
    words = line.split()
    # increase counters
    for word in words :
        # write the results to STDOUT ( standard output );
        # what we output here will be the input for the
        # Reduce step , i.e. the input for reducer .py
        #
        # tab - delimited ; the trivial word count is 1
        print '%s\t%s' % (word,1)
  
```
# reduce step : reducer.py

> cat > reducer.py
```
[elyse.rasoloarivony@hadoop-edge01 ~]$ cat reducer.py
"""reducer.py"""

from operator import itemgetter
import sys

current_word = None
current_count = 0
word = None

""" input comes from STDIN """
for line in sys.stdin:
    # remove leading and trailing whitespace
    line = line.strip()

    # parse the input we got from mapper.py
    word, count = line.split('\t', 1)

    # convert count (currently a string) to int
    try:
        count = int(count)
    except ValueError:
        # count was not a number, so silently
        # ignore/discard this line
        continue

    # this IF-switch only works because Hadoop sorts map output
    # by key (here: word) before it is passed to the reducer
    if current_word == word:
        current_count += count
    else:
        if current_word:
            # write result to STDOUT
            print '%s\t%s' % (current_word, current_count)
        current_count = count
        current_word = word

""" do not forget to output the last word if needed! """
if current_word == word:
```

# Testing code
> echo "foo foo quux labs foo bar quux" | /home/elyse.rasoloarivony/mapper.py | sort -k1,1 | /home/elyse.rasoloarivony/reducer.py
```
bar     1
foo     3
labs    1
quux    2
```

# Running on Hadoop

* download 3 files txt
> wget https://www.gutenberg.org/cache/epub/20417/pg20417.txt
> wget https://www.gutenberg.org/files/34300/34300-0.txt
> wget https://www.gutenberg.org/files/4300/4300-0.txt

j'ai renommé les fichiers .txt via la commande mv, en :

davinci.txt

outline_of_science.txt

ulysses.txt

* exemples :
> mv 34300-0.txt davinci.txt

* copy davinci.txt to HDFS :
> hdfs dfs -put davinci.txt outline_of_science.txt ulysses.txt gutenberg

# Running MapReduce job
>  yarn jar /usr/odp/1.0.3.0-223/hadoop-mapreduce/hadoop-streaming.jar -file /home/elyse.rasoloarivony/mapper.py -mapper /home/elyse.rasoloarivony/mapper.py -file /home/elyse.rasoloarivony/reducer.py -reducer /home/elyse.rasoloarivony/reducer.py -input /user/elyse.rasoloarivony/gutenberg/* -output /user/elyse.rasoloarivony/gutenberg-output

```
21/10/29 11:50:14 INFO mapreduce.Job:  map 0% reduce 0%
21/10/29 11:50:21 INFO mapreduce.Job:  map 67% reduce 0%
21/10/29 11:50:22 INFO mapreduce.Job:  map 100% reduce 0%
21/10/29 11:50:27 INFO mapreduce.Job:  map 100% reduce 100%
21/10/29 11:50:27 INFO mapreduce.Job: Job job_1630864376208_4282 completed successfully

......
              WRONG_REDUCE=0
        File Input Format Counters
                Bytes Read=3689749
        File Output Format Counters
                Bytes Written=75
```


* checking if it was successful

> hdfs dfs -ls gutenbberg-output

```
hdfs dfs -ls gutenberg-output
Found 2 items
-rw-r--r--   3 elyse.rasoloarivony elyse.rasoloarivony          0 2021-10-29 11:50 gutenberg-output/_SUCCESS
-rw-r--r--   3 elyse.rasoloarivony elyse.rasoloarivony         75 2021-10-29 11:50 gutenberg-output/part-00000
```



# Advanced mapper 

> [elyse.rasoloarivony@hadoop-edge01 ~]$ nano mapper.py
```
#!/usr/bin/env python

"""A more advanced Mapper, using Python iterators and generators."""

import sys

def read_input(file):
    for line in file:
        # split the line into words
        yield line.split()

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_input(sys.stdin)
    for words in data:
        # write the results to STDOUT (standard output);
        # what we output here will be the input for the
        # Reduce step, i.e. the input for reducer.py
        #
        # tab-delimited; the trivial word count is 1
        for word in words:
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```


# Advanced reducer 

> [elyse.rasoloarivony@hadoop-edge01 ~]$ nano reducer.py
```
#!/usr/bin/env python

"""A more advanced Reducer, using Python iterators and generators."""

from itertools import groupby
from operator import itemgetter
import sys

def read_mapper_output(file, separator='\t'):
    for line in file:
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_mapper_output(sys.stdin, separator=separator)
    # groupby groups multiple word-count pairs by word,
    # and creates an iterator that returns consecutive keys and their group:
    #   current_word - string containing a word (the key)
    #   group - iterator yielding all ["&lt;current_word&gt;", "&lt;count&gt;"] items
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            total_count = sum(int(count) for current_word, count in group)
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # count was not a number, so silently discard this item
            pass

if __name__ == "__main__":
    main()
```