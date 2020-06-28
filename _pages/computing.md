---
title: Statistical Computing
permalink: /computing/ 
---

I am often tempted to program quickly but poorly. Ironically, this only slows me down in the long term. I've been working to develop good coding practices, and it has helped me become much more efficient.

Developing a good computational workflow is a multifaceted and individual endeavor. The endeavor is multi-faceted because you want your code to be organized, version-controlled, reproducible, scalable, portable, etc. There isn't a single tool out there that will single-handedly facilitate your computational workflow. Instead, you need to bring together a set of tools and programming practices. This endeavor is individual because we have different computational needs. Some may work with real data a lot while others only do simulations. Some may program in MATLAB while others program in R. Some face computational obstacles in their work while others don't. Therefore, there isn't a one-size-fits all solution, and we all need to find a workflow that works for us.

Despite the fact that our individual computational needs vary, I have found a number of guiding principles that I think are general enough to be useful for others. I've put together a list of these computing tips, many illustrated by a project I recently completed that is now on [Github](https://github.com/ekatsevi/Focused-BH). 


Programming in general
------

- *Modularity*. If you have a chunk of code that you use often, wrap it into a function. This makes your code much cleaner. For instance, check out [utils_DAG.R](https://github.com/ekatsevi/Focused-BH/blob/master/src/methodology/utils_DAG.R), where I've coded lots of DAG operations (finding ancestors, descendants, etc) into separate functions. Also, I've modularized the [methodology](https://github.com/ekatsevi/Focused-BH/tree/master/src/methodology) so that I can call these functions for both my simulations and my real data analysis.
- *Writing readable code*. To make your code more readable, use descriptive variable names and leave lots of comments. Take a look at the variable names I've used in utils_DAG; most of them are full words.
- *Folder structure*. Your source code files will naturally fall into various categories, like code for plotting, code for simulations, code for methodology, etc. Look at the folder structure of my [source code](https://github.com/ekatsevi/Focused-BH/tree/master/src).
- *Packaging dependencies*. Your code will depend on various R packages and on functions you have written. Packaging these dependencies in one file makes it easier to load them all at once and to keep track of them; check out setup.R. Structuring your code as an [R package](https://www.youtube.com/playlist?list=PLk3B5c8iCV-T4LM0mwEyWIunIunLyEjqM) is an even cleaner way to handle this issue.
- *Version control*. Version control tools like Git are very helpful for several reasons: (1) they help you keep track of various versions of your code, (2) they allow easy synchronization of code between multiple machines (like your computer and the cluster), and (3) they allow you to share your code with others via Github. The last item is increasingly expected to supplement statistics papers with a computational component.
- *Keeping track of provenance*. Whether it's for simulations or data analysis, it's very useful to know which output files came from which program, run with which parameters, on which input file. This is called provenance, and is an important component of reproducibility. One method of provenance tracking I have been experimenting with is to add "commented" lines to the end of my output files with (1) the function call that produced that file, (2) the commit hash that specifies the version of the code at the time, and (3) the R session information, including versions of R and all packages loaded. For example, see the end of the output file [metrics_PheWAS_clustered_1.tsv](https://github.com/ekatsevi/Focused-BH/blob/master/results/PheWAS_clustered/metrics_PheWAS_clustered_1.tsv). The code that adds this provenance information to an output file is in [utils_provenance.R](https://github.com/ekatsevi/Focused-BH/blob/master/src/utils/utils_provenance.R).

Programming in R
------
- *Tidyverse for input/output*. For reading data and writing data, read_tsv() and write_tsv(), respectively, are fast and convenient (see for example lines 28 and 105 of [parse_UKBB_data.R](https://github.com/ekatsevi/Focused-BH/blob/master/src/data_analysis/parse_UKBB_data.R)). These are from the readr package, a part of the Tidyverse.
- *Tidyverse for data wrangling*. For wrangling data frames, functions like filter(), group_by(), select(), mutate(), gather(), spread() are extremely useful (see for example lines 31-55 of [plot_PheWAS_experiment.R](https://github.com/ekatsevi/Focused-BH/blob/master/src/plotting/plot_PheWAS_experiment.R)). These are from the tidyr and dplyr packages, which are also parts of the Tidyverse.
- *Tidyverse for plotting*. For plotting, ggplot2 is amazing (see for examples lines 58-70 of [plot_PheWAS_experiment.R](https://github.com/ekatsevi/Focused-BH/blob/master/src/plotting/plot_PheWAS_experiment.R)). You can see how naturally I've integrated a filtering command from tidyr with the plotting commands from ggplot2. This is facilitated by the Tidyverse philosophy of having a collection of packages organized to interact nicely with each other.
- Hadley Wickham's [R for Data Science](https://r4ds.had.co.nz/) book is a great way to learn more about the Tidyverse.

Numerical simulations
------
- *Give simulations interpretable names*. This allows you to refer to these simulations easily in your code.
- *Write executable input files for each simulation*. Check out the input files in [/src/simulations/input_files](https://github.com/ekatsevi/Focused-BH/tree/master/src/simulations/input_files). Note that each is named input_file_[simulation name].R.
- *Avoid repeating costly computations*. If there are computations that remain fixed across multiple repetitions or simulation settings, precompute them once and save the results to disk. I used this principle in [run_one_precomputation.R](https://github.com/ekatsevi/Focused-BH/blob/master/src/simulations/run_one_precomputation.R).
- *Use a cluster for parallelization*. Numerical simulations are by nature easily parallelizable and require lots of computationally power. This makes them ideally suited to run on a cluster. Also see below.

Data analysis
------

- *Keep track of the raw data, and where it came from*. To do the former, have a separate folder the contains only the raw data, with original filenames. All data that you processed in any way should be in another folder. Check out the folder structure in [/data](https://github.com/ekatsevi/Focused-BH/tree/master/data). To do the latter, e.g. if your data were downloaded from the internet, save the download links or even write a script that downloads the data. See for example [download_GO_data.sh](https://github.com/ekatsevi/Focused-BH/blob/master/src/data_analysis/download_GO_data.sh).
- *Save intermediate results that are computationally costly*. For example, often there are labor-intensive preprocessing steps to get the data into a form that's easy for you to manipulate. Save this processed form of the data so you don't need to repeat the costly preprocessing. See [parse_GO_data.R](https://github.com/ekatsevi/Focused-BH/blob/master/src/data_analysis/parse_GO_data.R) and [parse_UKBB_data.R](https://github.com/ekatsevi/Focused-BH/blob/master/src/data_analysis/parse_UKBB_data.R). This is similar to the precomputation principle under "numerical simulations" above.

Cluster computing
------

- *Learn about the available resources*. At Wharton, a good starting point is the [HPC Cluster](https://research-it.wharton.upenn.edu/documentation/).
- *Synchronizing between cluster and local computer*. I recommend using Github to transfer code between the cluster and your local computer (just point both to the same Github repository), and scp for transferring data. If you need to transfer lots of data, compress/archive it before transferring.
- *Writing code for the cluster*. I recommend developing and debugging code on small examples on your local machine; once you're convinced your code is working, move to the cluster to do large-scale computations. The less your code needs to be modified to work on the cluster, the more likely there won't be issues arising on the cluster that didn't arise locally.

