---
title: Statistical Computing
permalink: /computing/ 
---


- Programming poorly is tempting, because it allows faster progress in the short term. However, in the longer term it is actually much slower and much more frustrating. Conversely, programming well requires some conscious effort and takes more time in the short term, but pays off very nicely in the longer term.
- Developing a good computational workflow is a multifaceted and individual endeavor. The endeavor is multi-faceted because you want your code to be organized, version-controlled, reproducible, scalable, portable, etc. There isn't a single tool out there that will single-handedly facilitate your computational workflow. Instead, you need to bring together a set of tools and programming practices. This endeavor is individual because we have different computational needs. Some may work with real data a lot while others only do simulations. Some may program in MATLAB while others program in R. Some face computational obstacles in their work while others don't. Therefore, there isn't a one-size-fits all solution, and we all need to find a workflow that works for us.

Despite the fact that our individual computational needs vary, I have found a number of guiding principles that I think are general enough to be useful for others. At the end of this email, I've put together a list of these computing tips, many illustrated by a project I recently completed that is now on Github. I hope you will find these useful, and feel free to share this email with anyone else who you think may benefit from it.


Programming, in general
------

- Modularity. If you have a chunk of code that you use often, wrap it into a function. This makes your code much cleaner. For instance, check out utils_DAG.R, where I've coded lots of DAG operations (finding ancestors, descendants, etc) into separate functions. Also, I've modularized the methodology so that I can call these functions for both my simulations and my real data analysis.
- Writing readable code. To make your code more readable, use descriptive variable names and leave lots of comments. Take a look at the variable names I've used in utils_DAG; most of them are full words.
- Folder structure. Your source code files will naturally fall into various categories, like code for plotting, code for simulations, code for methodology, etc. Look at the folder structure of my source code.
- Packaging dependencies. Your code will depend on various R packages and on functions you have written. Packaging these dependencies in one file makes it easier to load them all at once and to keep track of them; check out setup.R.
- Version control. Version control tools like Git are very helpful for several reasons: (1) they help you keep track of various versions of your code, (2) they allow easy synchronization of code between multiple machines (like your computer and the cluster), and (3) they allow you to share your code with others via Github.
- Keeping track of paths. Depending what machine you are using, there may be different paths to folders where you store your code, your data, your figures, etc. A neat way of taking care of this is to have a script that takes as input the name of the machine you are using and automatically sets various paths. See set_paths.R, which you might have noticed is called by setup.R. I posted a simplified version of set_paths on Github because in general people don't need to know what paths I used; attached is the actual set_paths.R file I used for this project.
- Keeping track of provenance. Whether it's for simulations or data analysis, it's very useful to know which output files came from which program, run with which parameters, on which input file. This is called provenance, and is an important component of reproducibility. One method of provenance tracking I have been experimenting with is to add "commented" lines to the end of my output files with (1) the function call that produced that file, (2) the commit hash that specifies the version of the code at the time, and (3) the R session information, including versions of R and all packages loaded. For example, see the end of the output file metrics_PheWAS_clustered_1.tsv. The code that adds this provenance information to an output file is in utils_provenance.R.

Programming, in R
------
- Tidyverse for input/output. For reading data and writing data, read_tsv() and write_tsv(), respectively, are fast and convenient (see for example lines 28 and 105 of parse_UKBB_data.R). These are from the readr package, a pat of the Tidyverse.
- Tidyverse for data wrangling. For wrangling data frames, functions like filter(), group_by(), select(), mutate(), gather(), spread() are extremely useful (see for example lines 31-55 of plot_PheWAS_experiment.R). These are from the tidyr and dplyr packages, which are also parts of the Tidyverse.
- Tidyverse for plotting. For plotting, ggplot2 is amazing (see for examples lines 58-70 of plot_PheWAS_experiment.R). You can see how naturally I've integrated a filtering command from tidyr with the plotting commands from ggplot2. This is facilitated by the Tidyverse philosophy of having a collection of packages organized to interact nicely with each other.
- Hadley Wickham's R for Data Science book is a great way to learn more about the Tidyverse.

Numerical simulations
------
- Give simulations interpretable names. This allows you to refer to these simulations easily in your code.
- Write executable input files for each simulation. Check out the input files in /src/simulations/input_files. Note that each is named input_file_[simulation name].R.
- Avoid repeating costly computations. If there are computations that remain fixed across multiple repetitions or simulation settings, precompute them once and save the results to disk. I used this principle in run_one_precomputation.R.
- Use a cluster for parallelization. Numerical simulations are by nature easily parallelizable and require lots of computationally power. This makes them ideally suited to run on a cluster. We have access to the cluster at Pittsburgh Supercomputing Center, which is an amazing resource (see below).

Data analysis
------

- Keep track of the raw data, and where it came from. To do the former, have a separate folder the contains only the raw data, with original filenames. All data that you processed in any way should be in another folder. Check out the folder structure in /data. To do the latter, e.g. if your data were downloaded from the internet, save the download links or even write a script that downloads the data. See for example download_GO_data.sh.
- Save intermediate results that are computationally costly. For example, often there are labor-intensive preprocessing steps to get the data into a form that's easy for you to manipulate. Save this processed form of the data so you don't need to repeat the costly preprocessing. See parse_GO_data.R and parse_UKBB_data.R. This is similar to the precomputation principle under "numerical simulations" above.

Cluster computing
------

- Learn about the available resources. At Wharton, a good starting point is the [HPC Cluster](https://research-it.wharton.upenn.edu/documentation/).
- Synchronizing between cluster and local computer. I recommend using Github to transfer code between the cluster and your local computer (just point both to the same Github repository), and scp for transferring data. If you need to transfer lots of data, compress/archive it before transferring.
- Writing code for the cluster. I recommend developing and debugging code on small examples on your local machine; once you're convinced your code is working, move to the cluster to do large-scale computations. The less your code needs to be modified to work on the cluster, the more likely there won't be issues arising on the cluster that didn't arise locally. Note, for example, how submit_jobs.sh sets the base directory (line 13) and executes the commands (lines 34-58) depending on the machine. Therefore, the same script can be run locally or on the cluster; you just need to modify the "machine" parameter in line 8.

