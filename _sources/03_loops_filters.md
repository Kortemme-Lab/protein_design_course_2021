Loop Modeling & Filters Exercise
===

### Loop modeling

In this exercise, we will take a look at some results from a few different loop modeling simulations of the adenine-recognition loop in guanylate kinase (residues 167-186).  

Download the prepared RosEasy workspace from [here](https://ucsf.box.com/s/1k4ik9ldq4gllmtsmdmp55svbtjf8hii) to your class folder and unzip its contents:

```bash
tar -xzvf loop_modeling.tar.gz
cd loop_modeling
```

In this workspace are 5 folders with the outputs of various simulations.
- `01_designs/` contains outputs for a FastRelax simulation, which was used to prepare the input files for the rest of the simulations. You can ignore this folder.  
- `02_validated_designs/` contains outputs from a next-generation KIC simulation (NGK). This LoopModeler protocol samples torsions from a Ramachandran distribution and closes chain breaks with KIC.  
- `03_validated_designs/` contains outputs from a loophash-KIC simulation (LHKIC). Here, torsions are sampled from fragments which roughly bridge a random subsegment of the loop.  
- `04_validated_designs/` contains outputs from a fragment-KIC simulation (FKIC). Torsions here are sampled from fragments which were picked, prior to running the simulation, based on their similarity to the target sequence.  
- `05_validated_designs/` contains outputs from a fragment-CCD simulation (CCD). Here, torsions were also sampled from fragments picked based on sequence similarity to the target sequence, but the chain breaks were closed using closed cyclic descent.  


Plot the outputs of the loop modeling simulations:

```bash
roseasy plot */outputs/*
```

(This command will not plot the FastRelax outputs due to the way RosEasy's output subdirectories are organized.)  
On the X-axis, select "loop_rmsd". This view plots each model's total Rosetta score versus the model's RMSD to the crystal structure. In structure prediction runs, 
it is desirable to see a "funneling" of points towards the low-REU, low-RMSD region of the graph; this means that, as far as Rosetta can tell within the limits of its sampling and scoring, there is an energetic 
drive towards the correct structure, with few or no viable alternative conformations.  

Using the pane on the left, scroll through the different protocols. **Which method best predicted the loop structure?**

You can right-click each of the points and select `compare_to_input` to view the model in PyMOL aligned with the input structure, with the remodeled portion of the loop colored in orange.  
This "loop" is part beta sheet, part alpha helix, and part coil. **Look through the different methods and use the `compare_to_input` feature to examine a few models from each protocol 
that scored well but had >1A loop RMSDs. 
What type of secondary structure did each of the methods tend to get wrong?**

Now change the X-axis to display `avg_9_residue_fragment_crmsd`. This is the Fragment quality metric that we discussed in class. 

**Which method(s) produce the best fragment quality? Why?**

Change the Y-axis to display `loop_rmsd`, so that you are plotting fragment quality vs. RMSD.

**Is there a correlation between fragment quality and loop RMSD? Does this correlation depend on which method you look at? Which methods have this correlation and why?**


### Filtering

The Rosetta scorefunction has limitations, both in its ability to accurately predict stability and in its applicability to specific problems. A good example is that, because all Rosetta score terms must be pairwise decomposable (summable 2-body energies) to keep simulation times fast, Rosetta can not know via a Rosetta score whether there are any buried unsatisfied hydrogen bonds, which can substantially destabilize a structure. Filters and SimpleMetrics help us to account for these limitations. Contrary to their name, filters are often used as metrics at the end of a simulation, rather than as filters that throw out designs.  

In this exercise, we will examine part of the dataset from the de novo protein stability paper by Gabe Rocklin et. al. The dataset we will examine consists of four rounds of design, each containing around 3,000 proteins. Designs were created using FastDesign, with each round having increasingly stringent filters and resfiles. The detailed design protocol can be found in the supplemental material: https://science.sciencemag.org/content/sci/suppl/2017/07/12/357.6347.168.DC1/aan0693_Rocklin_SM.pdf

First, download the dataset (another RosEasy workspace) from https://ucsf.box.com/s/269cgkn7wg4gmw3og93vj4ffd90f7so4 to your class folder.
Unzip the file and `cd` into the folder. 

```bash
tar -xzvf filter_exercise.tar.gz 
cd filter_exercise
```

This workspace contains 4 folders, one from each round of design from the Rocklin paper. Plot all four rounds:
```bash
roseasy plot */outputs/
```

Note that at any time, you can right-click on a point and select ‘open in pymol’ to view the structure in more detail. This may be helpful in explaining what filters are picking up on. 

Set the y-axis to display the `stability score`. **This score represents the experimental results for that design sequence; higher is better.**
Examine the relationship between stability and a variety of metrics by cycling through different x-axis metrics, making sure to look at the following:
- `Total score`
- `normalized_score` (Rosetta score divided by design length) 
- `buried_np` (buried nonpolar surface area)
- `unsat_hbond` (# of buried unsatisfied hydrogen bonds)
- `exposed_hydrophobics` (surface area of exposed hydrophobic residues)
- `pack`/`cavity_volume`/`holes` (different ways of measuring the volume of internal cavities in the protein)
- `average_fragment` (averaged backbone RMSDs of protein fragments with similar sequence to design)
- `worst_fragment` (worst backbone RMSD of fragments with similar sequence to design)

**Which metrics seem to correlate with the stability score in earlier rounds? Is there a difference between which metrics are effective in each round?**

**Does the number of buried unsatisfied hydrogen bonds correlate with stability score in this dataset? Given what we know about the energetic 
cost of buried unsatisfied hydrogen bonds, why might that be?**

**Select a metric that either seems to correlate with stability or has at least 2 distinct clusters, and examine a few structures from either extreme/both clusters in PyMOL. Can you spot any trends in what the metric is picking up on? (For some metrics it may help to enter `show sticks, all` in PyMOL.)**


### Pareto fronts (if time)

Now that we have some idea of how well each metric correlates with stability, it's time to try and find some combination of metrics that gives us the greatest chance of selecting stable designs. Pareto fronts are one such way of doing this. A design is "pareto efficient", or part of a Pareto front, if there are no other designs which have better scores in all of the metrics being evaluated. You can have as many metrics be a part of a Pareto front as you wish, but as you do so, you increase the number of designs which will be considered Pareto efficient.  

![Pareto fronts](images/pareto.png)

We will use Pareto fronts to see if we can use the metrics that seem to correlate with stability score to further increase our chances of picking stable designs. 

Pick 3-5 metrics that you think are predictive of stable designs. You are going to create a "picks" file, which tells RosEasy how to pick designs for computational validation. Picks files have the following format and should have a `.yml` extension:

```
threshold:
- restraint_dist_e38 < 0.8
- h_bond_to_e38_sidechain == 0
- oversaturated_h_bonds == 0

pareto:
- total_score
- restraint_dist_e38
- max_9_residue_fragment_rmsd_c

depth: 1
epsilon: 0.5
```

Metrics under "threshold" tell RosEasy to throw out any designs that do not meet the threshold. We will not include any thresholds for now. 

Metrics under `pareto` are included in a pareto optimization. These metric names are the same as the name on the *left* in the plotting GUI axes menus (circled in the image below). 

You should set `epsilon` to 0; this value is a way of tuning how many designs make it through the Pareto optimization (higher numbers means fewer designs). 

You may want to play around with the `depth` setting; this integer value tells RosEasy how many times to run the Pareto optimization. So if `depth` is set to 2, RosEasy will run a Pareto optimization, remove those designs from consideration, and then run a second Pareto optimization. A depth of 4 or 5 seems to be good for this exercise, depending on the number of metrics (for higher numbers of metrics, you may want to use a lower `depth` parameter).  

![Metric names](images/metric_names.png)

Make a file with the above formatting (do not include thresholds) and name it `picks.yml`. 

Note that in order for a metric to be included in a Pareto front, RosEasy must know whether higher scores or lower scores are better. Normally it detects this via naming conventions, but because these designs were created using another workflow, we have to specify these directions ourselves. For each of your metrics, determine whether higher scores (`+`) or lower scores (`-`) are "better". 


Then, open  `04_designs/outputs/metrics.yml` in a text editor and locate your metric name. The line above the metric name should say  `- dir: '-'`, indicating that RosEasy thinks lower values are better for this metric. If higher values are better for any of your metrics, change that to  `- dir: '+'` and save the file.


Now we are ready to run the Pareto optimization:
```
roseasy pick . 5 picks.yml
```

This command will make a new folder, `05_validated_designs`, and place the selected designs in the `inputs` sub-folder (`05_validated_designs/inputs`), as normally the next step would be to run some sort of structure prediction script on the selected designs. Depending on the `depth` setting, the command may take a few minutes. Once it's finished, see how well your optimization worked by plotting all of the designs as well as the Pareto optimal designs:

```
roseasy plot 04_designs/outputs/ 05_validated_designs/inputs/
```

You can overlay the Pareto optimal designs (`05_validated_designs/inputs/`) on top of the complete set of designs (`04_designs/outputs/`) by shift-clicking in the left-hand menu (may take a couple clicks). Plot the stability score on the X-axis.  

![Pareto optimized](images/pareto_picks.png)

**If at any point you need to start over, you can delete or move the `05_validated_designs` folder (`rm -r 05_validated_designs/`) and re-run the `pick` command.**

**Did your chosen metrics select for stable designs? Are you missing out on a significant number of stable designs?** If you have time, try a few different combinations of metrics. **What metrics best predict design success when used in a Pareto front?**
 
