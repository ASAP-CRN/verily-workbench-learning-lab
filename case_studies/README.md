# README 

Runnable notebook that extracts substantia nigra neurons from the full cohort runs MapMyCells with the Human-Mammalian Brain Basal Ganglia taxonomy, curates high‑confidence dopaminergic cells, fine‑tunes scANVI, validates annotations with multiple orthogonal checks, and exports a gene by cell type matrix for downstream analysis.

## Prerequisites
- Environment: Conda env `scvi-cells` with `scanpy`, `scvi-tools`, `cell_type_mapper`, `mygene`,and `decoupler`.

    - How to set up conda environment

      ```bash
        #in a terminal
        conda env create -f environment.yml
        conda init 
        ## at this point restart your terminal
        conda activate scvi-cells
        # ensure jupyter can recognize the new conda environemnt
        python -m ipykernel install --user --name=scvi-cells --display -name "Python (scvi-cells)"
      ```

- Paths: All file paths defined as variables at the top of the notebook.

## Pipeline Steps
1. Workflow orientation: setting paths and directories
2. Data Access & Integration: copying data to personal bucket, loading and integrating data and metadata
3. Evaluate SN samples in cohort
4. Subset cohort to SN region and re-introduce raw counts.
    - In addition subset and save separate dataframe for SN neuronal cells, in case increasing granularity later. 
5. Run MapMyCells on SN subset using Human-Mammalian Brain Basal Ganglia taxonomy. [more info](https://alleninstitute.github.io/abc_atlas_access/descriptions/HMBA-BG_dataset.html)
   - Filter for high-confidence labels > 0.7 
7.  Recompute HVGs and embeddings
    - Normalize and log1p .X for downstream analysis
    - Compute HVGs using layer="counts" and flavor="pearson-residuals". (using technique from [sc-rnaseq-wf](https://github.com/ASAP-CRN/sc-rnaseq-wf/blob/main/docker/sc_tools/scripts/main/process)
    - Run PCA and neighbors using global scVI embedding and compute leiden clusters at 1.0 resolution
8. Perform targeted manual validation of high-confidence DA cells from MapMyCells results (Manual QC for scANVI)
    - Remove low-confidence assignments
    - Confirm canonical DA markers are expressed in DA cluster & overlap with MapMyCells labels
--> OPTIONAL ADDITION Downsample labeled populations to equalize class sizes and retain highest confidence samples.
10. Train & Predict with scANVI
    - Initialize scANVI and train with curated labels.
    - Flag or remove low confidence or ambiguous cells.
12. Validate Labels
    - Validate using canonical marker expression, DA score distributions, and decoupler
    - Compute scIB to quantify performance 
13. Export gene by cell type matrix
    - Aggregate expression by final labels and save a gene x cell type matrix. 