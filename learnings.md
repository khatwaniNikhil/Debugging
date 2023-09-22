# Learnings

## HIGH CPU USAGE
1. check GC logs
       1. grep "HealthMonitor" catalina.log (crosscheck minor.gc.count,  major.gc.count over last 15-30 mins)
2. check runnable threads in thread dumps
3.  top -H -p $PID --- displays what threads are consuming the OS resources within that particular process
4. Too high cpu usage on mysql - fetch and analyse pt-query-digest report over slow query log
   1. pt-query-digest (pt-query-digest --since 2020-06-19T19:00:00 --until 2020-06-19T20:15:00 ~/Desktop/slow-slow.log)

## Thread dump 
1. take multiple in quick sucession to analyse thread state changes)
   1. **Commands**   
      1. JVM Process Status (jps) command to discover the PID process of our application
      2. jstack -l $pid > $thread-dump.txt
      3. online reports
         1. https://fastthread.io/
         2. http://spotify.github.io/threaddump-analyzer/
   2. **Common issues**
       1. **Synchronization Issues**
           1. deadlock situation in which several threads running hold a synchronized block on a shared object
           2. thread contention, when a thread is blocked waiting for others to finish.        
       2. **real world **
          1. Most of the threads are in Timed Waiting state.
          2. 80 queue message consumers out of 97 are in Timed Waiting state and all of them are stuck in logging with lmax
          3. 919kb long string (third party api http response) were logged by integrations module
          4. lines logged were candidate of log masking which is a cpu intensive task, therefore contention among reader and writer threads around log buffer
          5. references
             1. https://github.com/LMAX-Exchange/disruptor/issues/209 
             2. https://github.com/LMAX-Exchange/disruptor/issues/219

## Heap dump
   1. **Concepts**
      1. OOM Types
      2. Shallow vs. Retained Heap
      3. Dominator Tree
      4. Garbage Collection Roots
      5. Inward and Outward References
   2. **what to check**
      1. GC Algo currently in use
      2. minor gc and major gc frequency
      3. heap retained via old gen
      4. histogram - to know memory usage by java types
   3. **Heap Dump Analyser Tooling**:
      1. Heap Hero
      2. Eclipse MAT 
         1. Solve common memory anti-patterns via Leak Suspects Report
         2. Analyzing Threads
         3. Analyzing Dominator Tree
         4. Analyzing Histogram
         5. Analyzing Java Collection Usage
         6. Querying Heap Objects (OQL)
         7. Comparing 2 Heap Dumps
         8. Exploring Paths to the GC Roots

## GC related debugging
https://github.com/chewiebug/GCViewer

## Async profiler 
https://github.com/apangin/java-profiling-presentation

## Debug Maven built time slowness
https://github.com/timgifford/maven-buildtime-extension
 
   
