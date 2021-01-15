# seek-and-read-HDD
This project provides tools for testing seek and scanning consecutive blocks time on HDD. This document introduces how these tools are used. FileGenerator, TestScan, TestSeek and AnalyzeSeek should be transformed to executable jar file at the beginning. You can modify the pom.xml to specify a Main Class and then use `mvn package` command. Please put FileGenerator.jar, TestScan.jar, TestSeek.jar, AnalyzeSeek.jar and test_scan.sh in the same directory.

## Generate a File

This tool generates a file of a specified size. The file name is given by the user. The file will be filled with zeroes. It takes 2 arguments:

1. name of the output file, such as "test"
2. size of the output file, such as "256KB", "2MB", "16MB", etc.

Here's an example:
```
java -jar FileGenerator.jar test 256KB
```

## Test Seek Time
This tool tests average seek time and seek speed. It performs seek on a file from a position to another with the specified span. When it hit the end of the file or the position exceeds the length of the file, it actually returns to the begin of the file by modular arithmetic. However, in that case, the result will not be taken into account since the seek is not sequential. The tool takes 3 arguments:

1. name of the input file, such as "test"
2. span size, such as "256K"
3. iteration number, such as "5000"

Here's an example:
```
java -jar TestSeek.jar test 256K 5000
```

## Test Scan Time
Put the test_scan.sh and TestScan.jar to a same directory, and then run test_scan.sh. The reason to use shell script is to execute Linux command to clear the system cache. 

### TestScan 

* NOTE: This tool is executed in test_scan.sh, so you don't have to executed it explicitly.

This tool test scan time with specified span(block) size and file. It records the duration of reading data of the specified size from a position near begin of the file, e.g., 128. It takes 2 argument:

1. name of the input file, such as "test"
2. span, such as "256KB"

Here's an example:
```
java -jar TestScan.jar test 256KB
```

### AnalyzeSeek
* NOTE: This tool is executed in test_scan.sh, so you don't have to executed it explicitly.

This tool is for analyzing temporary intermediate document during scan testing. It read all files whose name starts with "temp_", then sum up and average the value in it. It takes 2 arguments:

1. name of the test file
2. director holding the temporary files

Here's an example:
```
java -jar AnalyzeSeek.jar test /data/test_seek_scan/
```

### test_scan.sh

* NOTE: This is the tool you actually need to execute to test scan time.

It will generate several intermediate files recording scan durations. The number of files is consistent with your input, number of different spans. The file name is something like "temp_\<span>". It will then execute AnalyzeSeek to summarize the records. If you prefer to removing those intermediate files, you can uncomment some line in AnalyzeSeek.java, which delete those files. 

The number of arguments of test_scan.sh is not fixed, which are as follows:

1. name of the input file, which is typically generated by FileGenerator, such as "test"
2. number of iterations, such as "10", so that the test will be repeated for 10 times.
3. different span, such as "1M 2M 3M" indicating 3 kinds of spans.

Here's an example:
```
./test_scan.sh test 20 1M 2M
```

