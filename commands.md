
⸻

Hadoop Streaming Commands (Local Mode)

1. Export Hadoop Streaming JAR path

export STREAMING_JAR=/Users/adityaray/hadoop/share/hadoop/tools/lib/hadoop-streaming-3.4.1.jar

2. Remove old output directory

rm -rf output_dir

3. Run Hadoop Streaming job (local mode)

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

4. View output

cat output_dir/part-00000


⸻

