## Process

*Input folder*: contains mark and control files in the bigWig file format (.bigWig or .bw). Mark
files contain chromatin mark enrichment data across an organism's genome, generally ChIP-Seq,
and control files contain baseline data for the same experiment. For a given mark name, mark 
files must be named as [mark name]\_mark.bigWig or [mark name]\_mark.bw, and that mark's 
corresponding control file must be named [mark name]\_control.bigWig or [mark name]\_control.bw.

*Output folder*: contains two tables for normalized scores and percentile ranks of predicted or reference genes. Scores are normalized to either the length of the gene or the baseline enrichment data, if available, and percentile ranks are of the normalized scores. 

### Example command line usage

*Minimum*:
    Rscript process.R -i input\_dir -r "mm9"
    
*Human genome*:
    Rscript process.R -i input\_dir -o output -r "hg19" -t "human"
    
*Mouse genome with many options*:
    Rscript process.R -i input\_dir -o output -r "mm9" -f -p 2500 -d 0.01 -c -l
    
### Arguments

All arguments are also described via "Rscript process.R --help"

*-i, --input_folder*:
    Path to input folder, described earlier
    
*-o, --output_folder*:
    Path to output folder, described earlier [default output]
    
*-r, --refseq_file*: 
    either a name of a default refseq file ("mm9" or "hg19") *or* a path to a refseq file downloaded from the [UCSC table browser](https://genome.ucsc.edu/cgi-bin/hgTables). IMPORTANT: must *not* contain exonStarts and exonEnds columns
    
*-f, --no_filter*: 
    disable sex, extra chrom and olfactory gene filtering
    
*-p, --promoter_length*: 
    length of promoter region, disables promoter region separation if set to 0 [default 5000]
    
*-d, --drop_percent*: 
    bottom baseline enrichment percentile of genes to drop, as a decimal [default 0.01]
    
*-c, --no_clean_intermediate*: 
    disable automatic removal of intermediate output files

*-l, --no_overlap*: 
    disables default option of summing enrichment for *promoter_length* base pairs on either side of the TSS for a promoter region
    
*-t, --training_genes_file*: 
    if generating classifiers, specify "mouse", "human" or the name of a training genes file in the reference folder; otherwise, specify "none"
    
*-q, --quiet*: 
    disables console output, do not flag if required packages missing [default FALSE]

## Analyze

*Input file*: file output by process.R

*Output folder*: contains predictions on the input file using specified classifiers in a single dataframe

### Example command line usage

*Minimum*:
    Rscript analyze.R -i "input_file.tsv" -m models 
    
*With many options*:
    Rscript analyze.R -i "input_file.tsv" -o output -m models -po "BAE"
    
### Arguments

All arguments are also described via "Rscript generate.R --help"

*-i, --input_file*:
    Path to input file, described in general usage
    
*-o, --output_folder*:
    Path to output folder, described in general usage [default output]

*-m, --models_folder*:
    contains models output from generate.R

*-ex, --excluded_models*: 
    list of models to exclude from folder, separated by commas

*-p, --positive_class*:
    name of target feature's positive clas [default "MAE"]
    
*-q, --quiet*: 
    disables console output, do not flag if required packages missing [default FALSE]

## Generate

*Input file*: file output by process.R

*Output folder*: contains classifiers trained on 80% of the data as well comparisons based on as their predictive performance on the remaining 20% of the data.

### Example command line usage

*Minimum*:
    Rscript generate.R -i "input_file.tsv"
    
*With many options*:
    Rscript generate.R -i "input_file.tsv" -o output -ta "status" -sa "down" -se "oneSE"
    
### Arguments

All arguments are also described via "Rscript generate.R --help"

*-i, --input_folder*:
    Path to input folder, described in general usage
    
*-o, --output_folder*:
    Path to output folder, described in general usage [default output]

*-t, --target_feature*:
    name of column in dataset with feature to classify by [default "status"]

*-m, --sampling_method*: 
    resampling method to use when training classifiers, one of "none", "down", or "up"
[default "none"]

*-r, --selection_rule*:
    caret rule used to select the best model [default "best"]
    
*-q, --quiet*: 
    disables console output, do not flag if required packages missing [default FALSE]