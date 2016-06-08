This R package helps [`remake`](https://github.com/richfitz/remake) by adding parallelism. The function `write_makefile` creates a master [`Makefile`](https://www.gnu.org/software/make/) from an existing [`remake`](https://github.com/richfitz/remake)/[`YAML`](http://yaml.org/) file so that [`remake`](https://github.com/richfitz/remake) targets can be built in parallel with `make -j <n>`, where `<n>` is the number of parallel processes.

# Acknowledgements

This package stands on the shoulders of [Rich Fitzjohn](https://richfitz.github.io/)'s [`remake`](https://github.com/richfitz/remake) package, an understanding of which is a prerequisite for this one. Also thanks to [Daniel Falster](http://danielfalster.com/) for [the idea](https://github.com/richfitz/remake/issues/84) that cleaned everything up.

# Installation

Ensure that [R](https://www.r-project.org/) and [GNU make](https://www.gnu.org/software/make/) are installed, as well as the dependencies in the [`DESCRIPTION`](https://github.com/wlandau/parallelRemake/blob/master/DESCRIPTION). Open an R session and run 

```
library(devtools)
install_github("wlandau/parallelRemake")
```

Alternatively, you can build the package from the source and install it by hand. First, ensure that [git](https://git-scm.com/) is installed. Next, open a [command line program](http://linuxcommand.org/) such as [Terminal](https://en.wikipedia.org/wiki/Terminal_%28OS_X%29) and enter the following commands.

```
git clone git@github.com:wlandau/parallelRemake.git
R CMD build parallelRemake
R CMD INSTALL ...
```

where `...` is replaced by the name of the tarball produced by `R CMD build`.

## Windows users need [`Rtools`](https://github.com/stan-dev/rstan/wiki/Install-Rtools-for-Windows).

The example and tests sometimes use `system("make")` and similar commands. So if you're using the Windows operating system, you will need to install the [`Rtools`](https://github.com/stan-dev/rstan/wiki/Install-Rtools-for-Windows) package.

# Example

Use the `run_example` function to run the example workflow from start to finish. The steps are as follows.

1. Use the `example_source_file` function to create `code.R` with example user-defined R code.
2. Use the `example_remake_file` function to create the example `remake.yml` file, which contains [`remake`](https://github.com/richfitz/remake) instructions to direct the workflow. The `write_example` function does steps 1 and 2.
3. Use `write_makefile` to create the master [`Makefile`](https://www.gnu.org/software/make/) for running [`remake`](https://github.com/richfitz/remake) targets. The user will typically write `code.R` and `remake.yml` by hand and begin with this step. The `setup_example` function does steps 1 through 3.
4. Use [`Makefile`](https://www.gnu.org/software/make/) to run the workflow. The `run_example` function does steps 1 through 4. Some example options are as follows.
    - `make` just runs the workflow in 1 process.
    - `make -j 4` distributes the workflow over at most 4 parallel processes.
    - `make clean` removes the files created by [`remake`](https://github.com/richfitz/remake).
5. Optionally, use the `clean_example` function to remove all the files generated in steps 1 through 4.
    
# More on `write_makefile`

`write_makefile` has additional arguments. You can control the names of the [`Makefile`](https://www.gnu.org/software/make/) and the [`remake`](https://github.com/richfitz/remake)/[`YAML`](http://yaml.org/) file with the `makefile` and `remakefile` arguments, respectively. You can add lines to the beginning of the [`Makefile`](https://www.gnu.org/software/make/) with the `begin` argument, which could be useful for setting up the workflow for execution on a cluster, for example. You can append commands to `make clean` with the `clean` argument. In addition, the `remake_args` argument passes additional arguments to `remake::make`. For example, `write_makefile(remake_args = list(verbose = FALSE))` is equivalent to `remake::make(..., verbose = F)` for each target. You cannot set `target_names` or `remake_file` this way.

# Accessing the [`remake`](https://github.com/richfitz/remake) cache

Intermediate [`remake`](https://github.com/richfitz/remake) objects are maintained in [`remake`](https://github.com/richfitz/remake)'s hidden [`storr`](https://github.com/richfitz/storr) cache. At any point in the workflow, you can reload them using `recall(obj)`, where `obj` is a character string, and you can see the available values of `obj` with the `recallable` function.

# Multiple [`remake`](https://github.com/richfitz/remake) files

[`remake`](https://github.com/richfitz/remake) has the option to split the workflow over multiple [`YAML`](http://yaml.org/) files and collate them with the "include:" field. If that's the case, just specify all the root nodes in the `remakefiles` argument to `write_makefile`. (You could also specify every single [`YAML`](http://yaml.org/) file, but that's tedious.) If needed, `write_makefile` will recursively combine the targets, sources, etc. in the constituent `remakefiles` and output a new collated [`YAML`](http://yaml.org/) file that the master [`Makefile`](https://www.gnu.org/software/make/) will then use.

# Distributed computing

If you're running `make -j` over multiple nodes of a cluster or cloud resource, read this. [`remake`](https://github.com/richfitz/remake) uses a hidden folder called `.remake` in the current working directory for storing intermediate objects. You need to make sure that all the nodes share the same copy of `.remake` instead of creating their own local copies. That way, unnecessarily redundant rebuilds will be avoided. You could achieve this by using symbolic links, changing all nodes to the same working directory (`write_makefile(..., begin = "cd $PBS_O_WORKDIR")` for [PBS](https://en.wikipedia.org/wiki/Portable_Batch_System)), or some other method.
