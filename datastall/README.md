# Introduction
Once you get here, means you have finished the setup from the first step. I will now give specifications on how to reproduce the graphs. 
The first thing to layout is which graphs are we reproducing from the paper:
* Figure 3
* Figure 4(a) + Figure 4(b)
* Figure 5
* Figure 6

In other words, you will have to reproduce at least 5 figures according to the paper, but you are definitely welcome to try to reproduce more figures using different types of GPUs combined with different types of models. 

Now, the first thing we want to do is to make all the scripts (.sh) executable. TO do that we do the following:
```
chmod +x clear-cache.sh
chmod +x figure3_100.sh
chmod +x figure3.sh
chmod +x figure4.sh
chmod +x figure5.sh
chmod +x figure6.sh
```
Now you can execute the scripts by running:
```
./clear-cache.sh
```
as an example. Now you know how to run the scripts, so I can introduce you to how to run experiments and collect data for each graph. 
## Figure 3
### Run Experiments
We notice that figure 3 shows us the fetch stall percentage compared to the whole computation time. To get the fetch stall, wel will have to run figure3.sh and figure3_100.sh. figure3.sh is just regular run, which contains fetch time. figure3_100.sh runs the experiments by making the cache full first, so that no fetch time is needed. Only from Time_with_fetch_time-Time_without_fetch_time will we be able to get the actual fetch time. We also noticed that the cache percentage is about 35%, so we must be able to limit the memory limit. First run this:
```
sudo apt install -y cgroup-tools
```
Other commands related to limit memory are all in the scripts. To check out the specifications, go here: [limit-memory-usage-for-a-single-linux-process](https://unix.stackexchange.com/questions/44985/limit-memory-usage-for-a-single-linux-process). 
Now we are ready to run the experiment. 
First run:
```
./figure3.sh
```
After the first one is finished, then run the second one: 
```
./figure3_100.sh
```
You will see that files are produced and you just need to wait for the data at this point. 
After both scripts are finished:
```
cd outputsfig3
python parser.py
python parser100.py
```
You will get the information needed inside parsed_output.txt and parsed_output_100.txt. 

There are a couple of things to notice: 
* To change the type of GPU, go inside the scripts and modify:
gpu_type="p100" to gpu_type="v100" and so on. 

* Notice that the models and the memory size are correspondent, changing them might result in too small of a memory that the program will be killed, or too large such that the dataset cached is much larger than 35%. To add more models, you can test out the corresponding memory limit separately.
* Notice that the number of workers can also be changed. However, to get the best result for figure 3, we used 8 workers. 

Now you will need to organize the data you get in excel to get the final percentage. Please use this link as a reference: [Reproduce Graphs of DNN Stall Paper](https://docs.google.com/spreadsheets/d/1bNqTgfoViRjSRZdvgYbvBx_hniwbwkMDYc0U-O--C8Y/edit?usp=sharing)

For figure 3, head to file 3_Gus_version_HDD or 3_Gus_version_SSD depending on which disk you are using. I would say do SSD first because it is faster to finish running. After viewing the sheet and have a sense of the format, do the following: 
* Copy the result from parsed_output.txt into the Column C,D,E. 
* Copy the result from parsed_output_100.txt into the Column F,G,H. 
* Check out the functions embedded in the Google sheet and take the result of 2 set of results:
  * percentages of the fetch stall
  * percentages of the cache size

The above steps need you to read the google sheet carefully and click on some of the results to understand what is going on. But to explain it the easy way, all we did was to use the data time of 100% cache to minus the data time of 35% cached to get the fetch stall time. 

Now you have the results, let me show you how to make the figures. 
### Reproduce Figure 3
In general, we just copy the results into corresponding .dat file and then run:
```
python grapher.py
```
To reproduce figure 3, do the following: 
* Copy the "percentages of the fetch stall" into time3.dat inside "OSRE_DELIVERABLE/make_figures/make_graph_fig3/time3.dat". I used 8 models so there should be 8 numbers. 
* Next, copy the "percentages of the cache size" into cache.dat inside "OSRE_DELIVERABLE/make_figures/make_graph_fig3/cache.dat". 
* Finally, run python grapher.py, you will get a graph of figure 3 reproduced. 

Please still look inside the grapher.py and see how the naming works. For example, when I use p100_8workers_SSD, I would specify the setup that I'm using, which should also be corresponding to the experiments you ran. 

That sums up a general workflow of reproducing one of the figures. Now let me show you how to reproduce Figure4(a)+(b)
## Figure 4 (a)+(b)
### Run Experiments


