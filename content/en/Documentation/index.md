---
title: "Documentation"
description: ""
#featured_image: '/images/ai_center_cut.png'
menu:
  main:
    weight: 1
---

## Citation

#### For citation purposes, please refer to the following source:
Willems, E., & Heylen, F. (2023). flempar: An R-package for analyzing data from the Flemish Parliament. https://doi.org/10.31235/osf.io/7qwvt

## Licenses

- MIT License
- Contains public sector information obtained under the [model license](https://www.vlaamsparlement.be/nl/parlementair-werk/dossiers/dossiers/open-data) for free reuse Flanders v1.0

## Developers
[Frederik Heylen](https://datamarinier.be/team.html). Author. Datamarinier.

[Evelien Willems](https://evelienwillems.be/). Analyst, Tester & Contributor. Department of Political Science, University of Antwerp.

[Wouter Van Dooren](https://www.woutervandooren.eu/). Contributor. Department of Political Science, University of Antwerp.

**Department of Political Science, University of Antwerp**. Copyright holder, funder.

## Troubleshooting

#### Installation 

This guide is designed to help users troubleshoot issues when installing `flempar`. Installing and updating `flempar` is generally straightforward, but occasionally you may need help with problems. Here are some suggestions for solving these issues:

```r 
"package 'package_name' is not available (for R version x.y.z)"
```

Solution: Update R to the latest version. A best practice is to keep R and all of its packages updated!

```r 
"Error in install.packages: cannot remove prior installation of package ‘some package’"
```

Solution: This typically indicates a permission issue or a locked file preventing the removal of the older package version, on which `flempar` depends. To resolve this issue, follow these steps:
- Close any running instances of R or RStudio that might be using the package.
- Restart R or RStudio with administrative privileges (right-click the R or RStudio icon and select "Run as administrator" on Windows, or use "sudo" on macOS and Linux).
- Run the command `install.packages("package_name")` to update the package, replacing "package_name" with the actual name of the package you are trying to update.

If the issue persists after following these steps, you can manually remove the older package version:
- Locate the package library folder, which can be found by running `.libPaths()` in R.
- In the library folder, find the package subfolder (with the same name as the package) and delete it.
- Restart R or RStudio without administrative privileges.
- Reinstall the package by running `install.packages("package_name")`.

```r 
"Installation of package 'package_name' had non-zero exit status"
```

R packages may depend on other packages or other pieces of software to run. Make sure that all dependencies are installed. Sometimes, it might not be entirely clear what exactly is missing. To troubleshoot this error, Google the error message to determine which dependencies are missing!


If problems persist, you can also completely side-step dependencies and system issues using our containerized version of `flempar` (Docker). When using Docker, you can use `flempar` with two easy commands. See this [blog](https://www.flempar.be/post/running-docker/) for more information.

#### Read me when using `flempar` to get written questions between 2005 and 2014

In the API of the Flemish parliament, the documents that contain the written questions may come in several formats (e.g. pdf, doc, docx). `flempar` is designed to deal with and parse all types of documents. 

Sometimes, a Word document in an older format (.doc) may be corrupted. `flempar` rescues these documents by first converting them to a PDF. To do so, we use the package [`doconv`](https://cran.r-project.org/web/packages/doconv/index.html). This package uses your local Word installation to read the document by saving it as a PDF. 

When you do not have Word installed, `doconv`, and so by extension `flempar`, uses LibreOffice. LibreOffice is a free, open-source suite that includes applications for word processing, serving as an alternative to commercial suites like Microsoft Office.

If LibreOffice is not installed, `flempar` can still retrieve the text from all uncorrupted files. In this case, the corrupted files will be indicated by `'Error: Libreofice not found'` in the variable text. To get all the texts, you can install LibreOffice from this [link](https://www.libreoffice.org/download/download-libreoffice/). 

In our [Dockerfile](https://hub.docker.com/r/datamarinier/flempar), LibreOffice is already installed and ready for use!


## Codebooks

You can find an overview of all possible data output  [here](https://docs.google.com/spreadsheets/d/1x4E3nK1ymuOqAB37y0n1XBFF5GbsJ58CJoZvdqmA70c/edit?usp=sharing). 

