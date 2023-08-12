# Introduction
Once you get here, means you have finished the setup from the first step. I will now give specifications on how to reproduce the graphs. 
The first thing to layout is which graphs are we reproducing from the paper:
* Figure 3
* Figure 4(a) + Figure 4(b)
* Figure 5
* Figure 6

However, for 4(a) and 4(b), they are ran on two different disk (SSD & HDD), meaning that you probably have to experiment them on 2 difference machines. In other words, you will have to reproduce at least 4 figures according to the paper, but you are definitely welcome to try to reproduce more figures using different types of GPUs combined with different types of models and number of workers. 

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

## GPU Profile
You notice that **profile.csv** contains only V100 and P100 using 64 batches. To explore more, you can replace these by the GPU profile collected in **GPU Model Profiles - p100 (Meng).csv**. 

# General Workflow

Despite all the information provided, you will generally follow this workflow:
1. Run the script so that it can produce raw data in those .txt files. 
2. Run the parser to parse out the data we need. 
3. Go to the google sheet that I created to understand how the data are then modified to produce the format that can be read by the scripts used to graph the figures. 
4. Copy the organized result into .dat file. 
5. Run the grapher.py to make the figure. 

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
As I noted, 4(a) is done on SSD and 4(b) is done on HDD, so we can only reproduce one of two depending on which disk you are using. But it doesn't really matter because there is no essential difference in the workflow except changing the name. 

Let me explain figure 4 briefly first. We change the percentage of cache to see that the time of fetch stall would change accordingly. Therefore, we mainly collect the result fetch time by changing the percentage of memory limit. 
* First run: 
```
./figure4.sh
```
Now go inside outputsfig4:
```
cd outputsfig4
```
You will see two types of file, one if cache_result, the other is output. You want to check the cache_result and make sure that each one has a percentage close to 100%. Like this: 
```
  Files: 87473
     Directories: 92
  Resident Pages: 2620079/2620080  9G/9G  100%
         Elapsed: 1.0465 seconds
```
This is because this time, we need to use the data wait time of all other percentage time result to minus the 100% time result. 
The next you do is to run:
```
python parser.py
```
The results are inside parsed_output.txt. 
Similarly, you will need to go to the [Reproduce Graphs of DNN Stall Paper](https://docs.google.com/spreadsheets/d/1bNqTgfoViRjSRZdvgYbvBx_hniwbwkMDYc0U-O--C8Y/edit?usp=sharing)
and find the sheet 4(a)_Gus_version and 4(b)_Gus_version. Read the sheet and click on some cells to see how are they calculated. Next, you want copy the first 6 rows of the result into column C, D, E, and the next 6 rows of the result into column F, G, H. Next, you want to do the calculation as shown on the google sheet to reproduce the results. 
You will get a set of data like this:
```
0.22	9.060285568	1.006896734	34.32368517
0.4	7.811053276	1.047990561	34.40868378
0.589	5.951296329	0.9371159077	34.36483169
0.768	3.629170179	0.9525830746	34.23939323
0.961	1.219727993	1.097440004	34.48386073
1	0	1.03643775	34.44117975
```
Each column corresponds to: 
* cache percentage
* fetch time
* transfer time
* computation time

Now you have gathred the data needed. 
### Reproduce Figures
Copy the result above into time4(b).dat and run grapher_a.py to get the figure reproduced. (change b to a if you are running on SSD). 

## Figure 5
With the experience from 3 and 4, you are now more familiar with the workflow. Similarly, you run ./figure5 and gather the result. Next, you go to the google sheet and look at how I categorized the models inside 5_Gus_version. After understanding that google sheet. You will get a result like this:
```
20.4343	20.4086	20.4354	11.8241
40.3184	25.6369	32.0695	11.8219
58.5369	25.6848	31.9801	11.8256
73.2554	25.6631	32.0404	11.8152
73.1109	25.6009	31.9794	11.8134
72.7043	25.6401	31.9135	11.8145
```
for 4 different models: 
* alexnet	
* renet18	
* mobilenet_v2	
* resnet50

Now you copy these result inside time5.dat in make_graph_fig5. Run the grapher, then you will get the figure of figure 5. 

## Figure 6
For figure 6, you will just get percentage as a result that looks like this:
```
76.60
33.69
46.59
2.01
```
Corresponding to 4 models:
* alexnet
* resnet18
* mobilenet_v2
* resnet50
Copy the result into the time6.dat and run the grapher.py. 

# Conclusion
Now you have gotten the figures you need and see how the GPU Emulator can be used to reproduce the figures without using a GPU. 