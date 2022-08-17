Run circos from NIAB cluster

```bash
OutDir=analysis/genome_alignment/circos/cov_plot/vs_A22/Matt_MRS
mkdir -p $OutDir

Ref_genome=$(ls assembly/misc_publications/c.albicans/*.fasta)
ProgDir=~/git_repos/tools/seq_tools/circos
python $ProgDir/fasta2circos.py --genome $Ref_genome --contig_prefix "A22_" > $OutDir/query_genome.txt
```

Calculate coverage over 10kb windows for unmasked files. Parse read depth files:

```bash
OutDir=analysis/genome_alignment/circos/cov_plot/vs_A22/Matt_MRS
for File in $(ls analysis/genome_alignment/minimap/c.albicans/vs_ab109*/*_depth_10kb.tsv); do
  Strain=$(echo $File | cut -f5 -d '/')
  echo $Strain
  cat $File | awk '{print $1,$2-1000,$2,$3,$4}' OFS='\t' | cut -f1,2,3,4 | sed 's/contig_/A22_contig_/g' > $OutDir/${Strain}_vs_ref_scatterplot.tsv
done
```

```
OutDir=/home/vegasa/circos/cov_plot/vs_A22
ProgDir=~/git_repos/scripts/circos_plots/genome_alignment/C.albicans/TLO_project/vs_A22/coverage_plots/ulp2
circos -conf $ProgDir/vs_A22_circos_4.conf -outputdir $OutDir
mv $OutDir/circos.png $OutDir/ab863_ONT_vs_SC5314_circos.png
mv $OutDir/circos.svg $OutDir/ab863_ONT_vs_SC5314_circos.svg
ls $PWD/$OutDir/ab791_vs_SC5314_circos.png
```
