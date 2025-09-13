# mapper_reducer_sort

Perfect — we can turn your full terminal session into a clean Markdown file for documentation. Here’s a ready-to-use .md version:

# Hadoop Streaming: Sorting Text and Numbers

This document records the terminal commands and output for running a Hadoop Streaming job to sort a list of numbers and strings.

---

## 1. Setup

List the files in the working directory:

```bash
ls
# Output:
# mapper.py  number.txt  reducer.py  text.txt

Make the Python scripts executable:

chmod +x mapper.py reducer.py

Set the Hadoop streaming JAR path:

export STREAMING_JAR=/Users/adityaray/hadoop/share/hadoop/tools/lib/hadoop-streaming-3.4.1.jar

Remove any previous output directory to avoid conflicts:

rm -rf output_dir


⸻

2. Running the Hadoop Streaming Job

hadoop jar "$STREAMING_JAR" \
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

Notes:
	•	mapreduce.framework.name=local runs Hadoop in local mode.
	•	-files uploads the mapper and reducer scripts to the job.
	•	Multiple input files are added using separate -input flags.
	•	The output directory is specified using -output.

⸻

3. Job Execution Output

The job runs successfully with logs showing:

...
map 100% reduce 100%
Job job_local1262296836_0001 completed successfully
Output directory: file:///Users/adityaray/Desktop/sem_7/Big_Data/mapper_reducer_sort/output_dir


⸻

4. Viewing the Output

cat output_dir/part-00000

Output:

1
2
4
9
apple
banana
cherry
date
