
<style>
p.caption {
  font-size: 0.8em;
}
</style>

# scCustomize <img src="man/figures/scCustomize_Logo.svg" align="right" width="150"/>

[![CRAN
Version](https://img.shields.io/cran/v/scCustomize?color=green&label=CRAN)](https://cran.r-project.org/package=scCustomize)
[![CRAN
Downloads](https://cranlogs.r-pkg.org/badges/scCustomize)](https://cran.r-project.org/package=scCustomize)
[![license](https://img.shields.io/github/license/samuel-marsh/scCustomize)](https://github.com/samuel-marsh/scCustomize/blob/master/LICENSE.md)
[![R-CMD-check](https://github.com/samuel-marsh/scCustomize/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/samuel-marsh/scCustomize/actions/workflows/R-CMD-check.yaml)
[![issues](https://img.shields.io/github/issues/samuel-marsh/scCustomize)](https://github.com/samuel-marsh/scCustomize/issues)
[![DOI](https://img.shields.io/badge/DOI-10.5281/zenodo.5706430-blue)](https://doi.org/10.5281/zenodo.5706430)

scCustomize is a collection of functions created and/or curated to aid
in the visualization and analysis of single-cell data using R.

## Installing scCustomize

Please see
[Installation](https://samuel-marsh.github.io/scCustomize/articles/Installation.html)
page for full installation instructions.

## Vignettes/Tutorials

See Vignettes for detailed tutorials of all aspects of scCustomize
functionality.

## Goals/About scCustomize

***The goals of scCustomize are to:***

***1. Customize visualizations for aid in ease of use and create more
aesthetic visuals.***  
***2. Improve speed/reproducibility of common tasks/pieces of code in
scRNA-seq analysis with a single or group of functions.***

scCustomize aims to achieve these goals through:

- **Customized versions of many commonly used plotting functions (and
  some custom ones).**  
  To create greater flexibility in visualization and more aesthetic
  visuals by:
  - Altering default parameters for more intuitive plots (or at least I
    believe more intuitive). For instance:
    `FeaturePlot(..., order = TRUE)`.  
  - Wrapping commonly used ggplot2 post-plot themeing into function
    call. No more copy/paste of the same theme elements for every plot
    over and over (e.g.,
    `plot + scale_color_continuous(...) + ggtitle(...) + theme(plot.title = element_text(...), legend.position = ...) + guides(...)`  
  - Creating new plotting functions either: 1. as wrapper around Seurat
    function with parameters already specified (e.g., `QC_Plot_Genes()`)
    or 2. create new plots (e.g., `Seq_QC_Plot_Reads_per_Cell()` or
    `Plot_Median_Genes()`) or 3. both (e.g.,
    `QC_Plot_UMIvsGene(..., combination = TRUE)`).  
  - Adding additional parameters to existing plots inside new function
    (e.g., high and low cutoff parameters in `QC_Plot_UMIvsGene()`)
- **Easy iterative plotting functionality.**  
  Many plotting functions can be easily automated with loops, apply,
  purrr etc. However, these can be intimidating to novice user and often
  can be made easier through wrapping into a function.
  - scCustomize contains a number of [iterative plotting
    functions](https://samuel-marsh.github.io/scCustomize/articles/Iterative_Plotting.html)
    which contain extra parameters to specify file type, path, name and
    then render progress bar in console to track progress.
  - Returns either single PDF document or multiple plots of any valid
    file type (e.g., png, tiff, jpeg, pdf, etc).
- **Helper functions easily import multiple raw data types**  
  [Import data
  functions](https://samuel-marsh.github.io/scCustomize/articles/Read_and_Write_Functions.html)
  are aimed at streamlining importing multiple files/samples with single
  function and/or importing files with “non-standard” file names.
  - Iterate import functions to simplify the import process across
    groups of files/samples.
  - Parallelize functions where possible to allow for dramatic speed
    improvements when import large number of samples simultaneously.
  - Provide easy wrapper functions to import files with output formats
    (e.g., CellBender) not supported by Seurat or other common R
    package.
- **Helper functions to simplify analysis with addition of new default
  parameters or wrapping multiple lines of code into single
  function.**  
  Goal is to both speed up and simplify coding and reduce the use of
  copy/paste of the same lines of code which is more likely to lead to
  errors in code reproducibility.
  - *Example of adding new parameters:* Adding the percentage of counts
    aligning to mitochondrial (and/or ribosomal) genes is common early
    step in analysis. scCustomize provides `Add_Mito_Ribo_Seurat()` (and
    LIGER version) to simplify this. Basic use requires only one line of
    code and two parameters.

        Add_Mito_Ribo_Seurat(seurat_object = obj_name, species = "Human") 

    - Function already knows the defaults for Human, Mouse, Rat,
      Zebrafish, Drosophila, Marmoset, and Rhesus Macaque (submit a PR
      if you would like more species added!).  

  - *Example of wrapping many lines to one:* Extracting the top 10 (or
    15, 20, 25, etc) genes per identity after running
    `Seurat::FindAllMarkers()` is very common and scCustomize provides
    `Extract_Top_Markers()` function to simplify process.  
    *Using scCustomize function:*

        markers_df <- FindAllMarkers(object = obj_name)

        # Get vector/string
        top10_list <- Extract_Top_Markers(marker_dataframe = markers_df)

        # or for data.frame
        top10_df <- Extract_Top_Markers(marker_dataframe = markers_df, dataframe = TRUE)

    *Instead of tidyverse/base R:*

        markers_df <- FindAllMarkers(object = obj_name)

        # Get vector/string
        top10_list <- markers_df %>%
          rownames_to_column("rownames") %>%
          group_by(cluster) %>%
          slice_max(n = 10, order_by = avg_log2FC) %>%
          pull("gene")

        # or for data.frame
        top10_df <- markers_df %>%
          rownames_to_column("rownames") %>%
          group_by(cluster) %>%
          slice_max(n = 10, order_by = avg_log2FC) %>%
          column_to_rownames("rownames")
- **Provide more informative error messages for many common issues**  
  Base R error messages resulting from error deep inside Seurat (or
  other package) function can sometimes be difficult to interpret,
  especially for users new to R.
  - scCustomize provides checks/warnings, using the cli/rlang packages,
    wrapped inside its functions to help and provide more informative
    error/warning messages. Two examples include:  
  - `Add_Mito_Ribo_Seurat()` will warn you if no mitochondrial or
    ribosomal features are found and won’t create new metadata column.  
  - `Rename_Clusters()` will check and make sure the right number of
    unique new names are provided and provide one of two error messages
    if not before attempting to rename the object idents.

## Support for Other scRNA-seq Object Formats (LIGER, SCE, etc)

Currently the package is primarily centered around interactivity with
Seurat Objects with some functionality with LIGER objects and support
for CellBender outputs.  
If users are interested in adapting functions (or creating separate
functions) to provide comparable functionality with SCE or other object
formats I would be happy to add them. See below for more info on PRs.

## Bug Reports/New Features

#### If you run into any issues or bugs please submit a [GitHub issue](https://github.com/samuel-marsh/scCustomize/issues) with details of the issue.

- If possible please include a reproducible example (suggest using
  [SeuratData package](https://github.com/satijalab/seurat-data) pbmc
  dataset for lightweight examples.)

#### Any requests for new features or enhancements can also be submitted as [GitHub issues](https://github.com/samuel-marsh/scCustomize/issues).

- Even if you don’t know how to implement/incorporate with current
  package go ahead a submit!

#### [Pull Requests](https://github.com/samuel-marsh/scCustomize/pulls) are welcome for bug fixes, new features, or enhancements.

- Please set PR to merge with “develop” branch and provide description
  of what the PR contains (referencing existing issue(s) if
  appropriate).
