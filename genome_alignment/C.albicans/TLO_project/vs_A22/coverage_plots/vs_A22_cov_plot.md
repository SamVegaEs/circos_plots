```bash
 OutDir=analysis/genome_alignment/circos/cov_plot/vs_A22/ONT
  mkdir -p $OutDir

  Ref_genome=$(ls assembly/misc_publications/c.albicans/A22_Chromosomes_29.fasta | grep 'A22')
  ProgDir=~/git_repos/tools/seq_tools/circos
  python $ProgDir/fasta2circos.py --genome $Ref_genome --contig_prefix "A22_" > $OutDir/query_genome.txt
```


Calculate coverage over 10kb windows for unmasked files. Parse read depth files:

```bash
 OutDir=analysis/genome_alignment/circos/cov_plot/vs_A22/ONT
for File in $(ls analysis/genome_alignment/minimap/*/vs_*/*_depth_10kb.tsv); do
  Strain=$(echo $File | cut -f5 -d '/')
  echo $Strain 
  cat $File | awk '{print $1,$2-1000,$2,$3,$4}' OFS='\t' | cut -f1,2,3,4 | sed 's/contig_/A22_contig_/g' > $OutDir/${Strain}_vs_ref_hardmasked_scatterplot.tsv
done
```
```bash
OutDir=/projects/oldhome/groups/harrisonlab/project_files/Pichia/cov_chromosome_level/ONT
ProgDir=/projects/oldhome/vegasa/git_repos/scripts/pichia/genome_alignment/Chromosome_level_assembly/cov_plot/ONT/vs_589
circos -conf $ProgDir/vs_589_circos.conf -outputdir $OutDir
mv $OutDir/circos.png $OutDir/vs_589_cov_unmasked_circos.png
mv $OutDir/circos.svg $OutDir/vs_589_cov_unmasked_circos.svg
ls $PWD/$OutDir/vs_589*_circos.png
```
