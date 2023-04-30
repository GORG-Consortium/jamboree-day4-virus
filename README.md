## Consruction of this tutorial is in progress.

#### Identification of viruses and other pangenomic elements within SAGs

In this tutorial we are focusing on viruses from plates AM-388 and AM-390 from GORG-Dark.  

SAGs were run through several steps and are ready for manual assessment for the presence of viruses or other virus-like elements. 

SAGs have run through the following workflows. These SAGs were processed on Bigelow's server, but below I am providing example commands as they would be run on the JupyterHub.

---
#### Virus Finding
[VirSorter2](https://github.com/jiarong/VirSorter2):
```
source activate /mnt/storage/envs/vs2
virsorter run -i ${contigs_fasta} --include-groups "dsDNAphage,ssDNA,RNA"
```

[VIBRANT](https://github.com/AnantharamanLab/VIBRANT):
```
source activate /mnt/storage/envs/vibrant
VIBRANT_run.py -i ${contigs_fasta} -t ${cpus} -folder ${outdir}
```

[DeepVirFinder](https://github.com/jessieren/DeepVirFinder):
```
source activate /mnt/storage/envs/dvf
python /mnt/storage/envs/opt/DeepVirFinder/dvf.py -i ${contig} -o ${plateout} -c ${cpus}
```
---
### Identification of initial candidates

Results from the above workflows were merged, contigs were maintained as initial viral candidates based on the following criteria:
* VirSorter2: max_score = 0.9, hallmark genes > 0  
* VIBRANT: all hits
* DeepVirFinder: dvf_score_min = 0.9, dvf_pvalue_max = 0.05  

Merged results for all contigs from plates AM-388 and AM-390 that had hits to at least one of these finders can be found in this table:
``` table here```

---
### Candidate initial QC
Initial virus candidate contigs were then extracted and written to a new fasta file and examined by CheckV:

[CheckV](https://bitbucket.org/berkeleylab/checkv/src/master/)
```
source activate /mnt/storage/envs/checkv
checkv end_to_end $infa $outdir -t 20 -d /mnt/storage/reference_dbs/checkv/checkv-db-v1.5
```


---
### Annotation

[DRAM](https://github.com/WrightonLabCSU/DRAM)

```
source activate /mnt/storage/envs/dram
DRAM-v.py annotate -i ${contig} -o $plateout --min_contig_size 2000
```

---
### Summary Tables
#### *sag_vcontig_summary.csv*

Columns:  
sag: sag ID 
total_vcontig_bp: total length of viral candidates sequences in SAG
'Complete', 'High-quality', 'Low-quality', 'Medium-quality', 'Not-determined’: contig count for checkV quality categories
'Total vContigs’: total number of viral candidate contigs in SAG 
'dramv_top_vir_genome_match’: viral genome from NCBI with the most hits to vCandidate open reading frames (orfs)
'dramv_top_vir_pct_matched’: percent orfs matching the top hit virus
'total_vc_orfs’: total number of orfs on virus candidate contigs 
'total_annotated_vc_orfs’: total vCandidate orfs that were annotated by DRAMv
'pct_annotated’: percent annotated orfs on virus candidates 
'phage structurals’: list of identified viral structural genes on vCandidates 
'plate_id’: SAG plate ID 
'integrase_count’: total number of integrases on vCandidates within SAG

#### *vcandidate_contig_summary_stats.csv*

Columns:
'contig’: contig ID 
'sag’: SAG id 
'plate’: plate id
'type_vs2’: virsorter2 type (integrated or individual) 
'vs2_pos’: 1 if virsorter identified
'type_vib’: VIBRANT type (X or Y) 
'vib_pos’: 1 if VIBRANT identified 
'dvf_pos’: 1 if DeepVirFinder identified 
'consensus_score’: count of how many virus finders identified as viral (max 3)
'contig_length’: contig length in bp
'provirus’: X if checkv identified as integrated
'gene_count’: total genes on contig
'viral_genes’: total viral genes on contig (checkV determined) 
'host_genes’: total host genes on contig (checkV determined) 
'pct_host_genes’: percent genes on contig identified as ‘host’ by checkV
'checkv_quality’: checkV determined viral genome quality
'miuvig_quality’: another genome quality metric
'completeness': checkv estimated viral contig completeness
'completeness_method': method checkv used to estimate completeness

#### *per-SAG ORF annotation tables*

