-----

# Hadoop Streaming Sort Job Example

This document details the process of running a simple sorting job using **Hadoop Streaming**. The job takes two input files, `text.txt` and `number.txt`, processes them using Python scripts for the mapper and reducer, and outputs a single, lexicographically sorted file.

This example is run in Hadoop's **local standalone mode**, which is perfect for development and debugging without needing a full HDFS cluster.

-----

## Project Files

The project directory contains the following files:

```sh
$ ls
mapper.py	number.txt	reducer.py	text.txt
```

  * **`mapper.py`**: The Python script for the map phase.
  * **`reducer.py`**: The Python script for the reduce phase.
  * **`number.txt` & `text.txt`**: The input data files.

#### Input File Contents

Based on the final output, the contents of the input files are assumed to be:

**`text.txt`**

```
cherry
apple
date
banana
```

**`number.txt`**

```
4
9
1
2
```

-----

## Mapper and Reducer Scripts

For a simple sort, the mapper's role is to emit each line as a key, and the reducer's role is to write those sorted keys to the output.

### `mapper.py`

This script reads each line from the input, cleans it, and prints it as a key. The Hadoop framework will then sort these keys before sending them to the reducer.

```python
#!/usr/bin/env python
# mapper.py
import sys

# Read each line from standard input
for line in sys.stdin:
    # Strip leading/trailing whitespace
    line = line.strip()
    
    # Output the line as the key with a null value (a tab is the default separator)
    # The framework sorts data based on this key.
    if line:
        print(f"{line}\t")
```

### `reducer.py`

The reducer receives the keys (lines) in sorted order. Its only job is to print each key it receives, resulting in the final sorted output.

```python
#!/usr/bin/env python
# reducer.py
import sys

# The input from the mapper is already sorted by key
for line in sys.stdin:
    # The key is the entire line up to the first tab
    key = line.strip().split('\t')[0]
    
    # Print the sorted key to the final output
    print(key)

```

-----

## Executing the MapReduce Job

Here are the commands used to set up and run the Hadoop Streaming job.

### 1\. Set Permissions and Environment Variable

First, make the Python scripts executable. Then, set an environment variable to point to the `hadoop-streaming-*.jar` file for convenience.

```sh
# Make the Python scripts executable
$ chmod +x mapper.py reducer.py

# Set a variable for the path to the streaming JAR
$ export STREAMING_JAR=/Users/adityaray/hadoop/share/hadoop/tools/lib/hadoop-streaming-3.4.1.jar
```

### 2\. Run the Hadoop Streaming Command

Next, remove any previous output directory (Hadoop requires this) and execute the job.

```sh
# Clean up the previous output directory to prevent errors
$ rm -rf output_dir 

# Execute the Hadoop Streaming job
$ hadoop jar "$STREAMING_JAR" \
  -D mapreduce.framework.name=local \
  -D fs.defaultFS=file:/// \
  -D mapreduce.job.name="SortJob" \
  -D mapreduce.job.reduces=1 \
  -files mapper.py,reducer.py \
  -mapper mapper.py \
  -reducer reducer.py \
  -input file://$(pwd)/text.txt \
  -input file://$(pwd)/number.txt \
  -output file://$(pwd)/output_dir
```

#### Command Breakdown:

  * `-D mapreduce.framework.name=local`: Specifies running in local mode, not on a cluster.
  * `-D fs.defaultFS=file:///`: Uses the local file system instead of HDFS.
  * `-files mapper.py,reducer.py`: Packages the scripts to be distributed to the tasks.
  * `-mapper mapper.py`: Defines the mapper executable.
  * `-reducer reducer.py`: Defines the reducer executable.
  * `-input file://...`: Specifies an input path. We use it twice for two different files.
  * `-output file://...`: Specifies the output directory.

-----

## Final Output

The job combines the data from both input files, sorts it, and places the result in `part-00000` inside the `output_dir`.

We can view the result with the `cat` command.

```sh
$ cat output_dir/part-00000
```

The output shows the contents of both files, combined and sorted lexicographically.

```
1
2
4
9
apple
banana
cherry
date
```
