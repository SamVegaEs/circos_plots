Adapt for all the different combinations of plots againts the reference assembly.

```
 OutDir=analysis/genome_alignment/circos/cov_plot/vs_A22/ONT/ulp2
  mkdir -p $OutDir
```
```
  Ref_genome=$(ls assembly/misc_publications/c.albicans/A22_Chromosomes_29.fasta | grep 'A22')
  ProgDir=~/git_repos/tools/seq_tools/circos
  python $ProgDir/fasta2circos.py --genome $Ref_genome --contig_prefix "A22_" > $OutDir/query_genome.txt
```
```
OutDir=analysis/genome_alignment/circos/cov_plot/vs_A22/ONT/ulp2
for File in $(ls analysis/genome_alignment/minimap/*/vs_ab*/*_depth_10kb.tsv); do
  Strain=$(echo $File | cut -f5 -d '/')
  echo $Strain 
  cat $File | awk '{print $1,$2-1000,$2,$3,$4}' OFS='\t' | cut -f1,2,3,4 | sed 's/contig_/A22_contig_/g' > $OutDir/${Strain}_vs_ref_hardmasked_scatterplot.tsv
done
```
