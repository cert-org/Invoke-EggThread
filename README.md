BENCHMARK: https://github.com/eggstoastbacon/Invoke-EggThread/wiki/Benchmark:-EggThread-vs.-EggJob-vs.-Parallel

# Invoke-EggThread
Based on PSThreadJobs,  Launches the specified amounts of jobs, divides tasks evenly between them and runs them concurrently, in threads.

.SYNOPSIS
  Launches the specified amounts of jobs, divides tasks evenly between them and runs them concurrently, in threads.
 
.DESCRIPTION
  Specify the amount of jobs, a variable or command to gather records and a scriptblock to run.
  Additional features include error log path, and reordering of records.
  If your scriptblock will output a file append $x to the file name. Optional Throttling of threads per job
 
.PARAMETER jobs
  Mandatory. Specify the amount of jobs that will be started. The $x variable refers to the job number while running.
 
.PARAMETER int_records
  Mandatory. Provide an array of records, such as $items, or $items[0..2500]
 
.PARAMETER exp_records
  Optional. Provide an expression that results in an array of records. Use this instead on Int_Records.
 
.PARAMETER scriptBlock
  Mandatory. Provide a scriptblock to run against the provided array. Scriptblocks are closed in single quotes or parenthesis.
  Use $myJobVar as the reference.
 
  Ex: $myScriptBlock = {$math = $myjobvar + 6 | out-file c:\temp\math.txt -append}
 
.PARAMETER errorLog
  Optional. Provide an error logging path such as "c:\temp"
 
.PARAMETER combinePath
  Required for Combine.
  Together with CombineSrcName, this will combine all output files into one file.
  ex: "c:\temp"
  Specify the path of your output, this must be referenced in your scriptblock as your output destination.
 
.PARAMETER combineSrcName
  Required for Combine, you must tell the script what your unique output file name is, ex: "exportdata".
  This must be the part of the file name you are using to to output in your scriptblock. Function Invoke-EggJob {
  <#
.SYNOPSIS
  Launches the specified amounts of jobs, divides tasks evenly between them and runs them concurrently.
 
.DESCRIPTION
  Specify the amount of jobs, a variable or command to gather records and a scriptblock to run.
  Additional features include error log path, and reordering of records.
  If your scriptblock will output a file append $x to the file name.
 
.PARAMETER jobs
  Mandatory. Specify the amount of jobs that will be started. The $x variable refers to the job number while running.
 
.PARAMETER int_records
  Mandatory. Provide an array of records, such as $items, or $items[0..2500]
 
.PARAMETER exp_records
  Optional. Provide an expression that results in an array of records. Use this instead on Int_Records.
 
.PARAMETER scriptBlock
  Mandatory. Provide a scriptblock to run against the provided array. Scriptblocks are closed in single quotes or parenthesis.
  Use $myJobVar as the reference.
 
  Ex: $myScriptBlock = {$math = $myjobvar + 6 | out-file c:\temp\math.txt -append}
 
.PARAMETER errorLog
  Optional. Provide an error logging path such as "c:\windows\temp"
 
.PARAMETER combinePath
  Required for Combine.
  Together with CombineSrcName, this will combine all output files into one file.
  ex: "c:\temp"
  Specify the path of your output, this must be referenced in your scriptblock as your output destination.
 
.PARAMETER combineSrcName
  Required for Combine, you must tell the script what your unique output file name is, ex: "exportdata".
  This must be the part of the file name you are using to to output in your scriptblock.
  ex: out-file c:\temp\exportdata_$x
 
.PARAMETER combineDestName
  Optional. Set the name of the combined file, default is "combined"

.PARAMETER Throttle
  Optional. Throttle the amount of threads, default value is: 32
 
.PARAMETER skipNth
  Optional. Divides files among jobs by assigning array objects to jobs in a sequential order.
  divides job records by skipping instead of assigning in order (can speed up some jobs) _
 
  Example: If you choose -jobs 4 and -skipnth 4
 
  job1 assigned records 0,3,7,11,15
 
  job2 assigned records 1,4,8,12,16
 
  job3 assigned records 2,5,9,13,17
 
  job4 assigned records 3,6,10,14,18
 
.INPUTS
  Parameters above
 
.OUTPUTS
  Records or items processed in parallel with a scriptblock you provide.
 
.NOTES
  Version: 1.0.0
  Author: Eggs Toast Bacon
  Creation Date: 02/19/2020
  Purpose/Change: Initial function development.
 
.EXAMPLE
   
  Ex A:
  $items = (1..2500)
 
  $myScriptBlock = {$math = $myjobvar + 6 | out-file c:\temp\math_$x.txt -append}
 
  Invoke-EggThread -jobs 8 -int_records $items -scriptBlock $myscriptblock -errorlog C:\windows\temp
 
  Result is the number of items is divided by the number of jobs specified and each job is assigned an even workload.
  8 jobs run concurrently in parallel until their assigned workload is complete.
  Each $item in $items is added by 6 and the result output is appended to c:\temp\math_[job#].txt
 
  #############
 
  Ex B:
   
  $items = (1..2500)
 
  $myScriptBlock = {$math = $myjobvar + 6 | out-file c:\temp\mydata_$x.txt -append}
 
  Invoke-EggThread -jobs 8 -int_records $items -scriptBlock $myscriptblock -errorLog "C:\temp" -combinePath "c:\temp" -combineSrcName "mydata" -combineDestName "combo"
 
  Result is the number of items is divided by the number of jobs specified and each job is assigned an even workload.
  8 jobs run concurrently in parallel until their assigned workload is complete.
  Each $item in $items is added by 6 and the result output is appended to c:\temp\math_[job#].txt
  All files are combined into a file named [random number]combo.txt
