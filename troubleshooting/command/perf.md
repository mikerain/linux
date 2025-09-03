

https://access.redhat.com/support/cases/#/case/04239217

如果是应用性能问题，可以先用strace看下，然后再用perf



可以比较一下strace, perf, collect三个工具的用途

```
  1. Install perf on system.  Perf can be installed by following steps in the below article.

                * How do I collect performance data with perf which is readable on another machine?
                  https://access.redhat.com/solutions/386343
	
	2. We will execute perf with the lscpu command, similar to how we did with strace.  Steps are provided below.

                a. make a directory for perf data collection
                        # mkdir -p /tmp/perf; cd $_
                b. collect perf data that traces the lscpu specifically
                        # perf record -gT -- lscpu
                c. once completed, archive perf data
                        # perf archive 
                d. generate perf script file
                        # perf script > perf_script.out
                e. gather all data into single archive file
                        # tar czf perf_collection_$(hostname)-$(date +"%Y-%m-%d-%H-%M-%S").tar.gz perf.data perf.data.tar.*z* perf_script.out

        3. Upload perf_collection archived data to the ticket
```