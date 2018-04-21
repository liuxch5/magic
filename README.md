# MaGIC

This software generates predictions or classifiers for monoallelic gene expression (MAE) from
chromatin mark enrichment data. It is primarily based on the work of the [Gimelbrant lab](https://gimelbrantlab.dfci.harvard.edu/).

## Requirements

Linux/Mac machine with updated R (~3.4.x) and git. Please see the [instructions](https://github.com/gimelbrantlab/magic/blob/master/Installation.md) if you don't have git or R installed. There is a number of R packages that need to be installed, please see [dependencies](https://github.com/gimelbrantlab/magic/blob/master/Dependencies.md). During Magic installation, install.R script will try to download and install missing packages. However, if installation some of them fails, you will have to install them [manually](https://github.com/gimelbrantlab/magic/blob/master/Installation.md). 

## MaGIC installation

Installing MaGIC takes only a few commands. Required R packages and [bwtool](https://github.com/CRG-Barcelona/bwtool/wiki) are installed automatically.

To install MaGIC system-wide (i.e. with root/sudo access): 
```
git clone https://github.com/gimelbrantlab/magic.git
cd magic
Rscript src/install.R
```

To install MaGIC locally (i.e. on a server without root access to your R installation): 
```
git clone https://github.com/gimelbrantlab/magic.git
cd magic
Rscript src/install.R libraries
```
These commands will enable MaGIC to run from the command line. We also developed a Shiny app that can be run with the following command: 

```
R -e "shiny::runApp('shiny/MagicWeb/', launch.browser = T)"
```
If you change your mind and want to uninstall MaGIC, you can just delete the whole folder:

```
cd ..
rm -r magic
```


## Quick start

The easiest way to test MaGIC is to run the Shiny app (see [Tutorial](https://github.com/gimelbrantlab/magic/blob/master/shiny/MagicWeb/Tutorial.md) for help).

The following commands show you how to run MaGIC from the command line.

*Process.R*

Process.R script takes ChIP-seq files in bigWig format and converts them into normalized enrichment over genomic intervals (gene body or promoter), formatted as tables usable by analyze.R or generate.R. To run process.R using test data (human GM12787, chr15, H3K27me3 and H3K36me3 marks), run this command:

```
Rscript src/process.R -i data/input.txt -o data/output -r "hg19" -p 0 -s 3

```
You will get joint_scores_percentile.txt in your output folder, and this file will be used for further analysis.

*Generate.R*

Generate.R script trains models using output from process.R saved in the joined_scores_percentile.txt file. We also provide a pre-generated joined_scores_percentile_full_dataset.txt file. Run the following command to train ada, SVM, and random forest model using 80% percent of the data for training and 20% for testing:

```
Rscript src/generate.R -i data/joined_scores_percentile_full_dataset.txt -o data/classifiers -a "human" -p 80 -l ada,svmPoly,rf -c 5
```
The output folder will contain a model_output folder containing a summary_models.tsv file describing key metrics of the models' performance. 

*Analyze.R*

Finally, call analyze.R, using the joined_scores_percentile.txt (output from process.R) and the default classifiers that are packaged with MaGIC as inputs.
```
Rscript src/analyze.R -i data/joined_scores_percentile_full_dataset.txt -o data/output -m models
```
You can also run analyze.R with your own trained classifers, but be careful to avoid overfitting. 

## Contact us

Questions, comments and concerns can be directed to [Alexander Gimelbrant](alexander_gimelbrant@dfci.harvard.edu), [Sébastien Vigneau](Sebastien_Vigneau@dfci.harvard.edu), and [Svetlana Vinogradova](Svetlana_Vinogradova@dfci.harvard.edu). Additional inquiries about how the program works can be directed to [Henry Ward](wardx596@umn.edu) and [Sachit Saksena](sachitdsaksena@utexas.edu).
