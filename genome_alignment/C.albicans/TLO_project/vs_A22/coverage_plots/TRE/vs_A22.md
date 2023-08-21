To generate the coverage plots with circos we need to generate the .tsv files from the Nanopore raw reads alignment done with minimap.

```bash
for Sam in $(ls analysis/genome_alignment/minimap/*/vs_r*/*_aligned_sorted.bam); do
  Target=$(echo $Sam | rev | cut -f3 -d '/' | rev)
  Strain=$(echo $Sam | rev | cut -f2 -d '/' | rev)
  echo "$Strain-$Target"
  OutDir=$(dirname $Sam)
  samtools depth -aa $Sam > $OutDir/${Strain}_${Target}_depth.tsv
done

for Strain in rC3 rC4 rC5 rC6 rC7 rF2 rH9; do
  for Cov in $(ls analysis/genome_alignment/minimap/*/vs_r*/*_depth.tsv); do
    echo ${Cov} | cut -f4,5,6 -d '/' --output-delimiter " - "
    cat $Cov | cut -f3 | sort -n | awk ' { a[i++]=$1; } END { print a[int(i/2)]; }'
  done
done > analysis/genome_alignment/minimap/read_coverage.txt
```
```bash
for Cov in $(ls analysis/genome_alignment/minimap/*/vs_r*/*_depth.tsv); do
    Strain=$(echo $Cov | rev | cut -f2 -d '/' | rev)
    Organism=$(echo $Cov | rev | cut -f3 -d '/' | rev)
    echo "$Organism - $Strain"
    OutDir=$(dirname $Cov)
    ProgDir=git_repos/tools/seq_tools/genome_alignment/coverage_analysis
    $ProgDir/cov_by_window.py --cov $OutDir/${Strain}_${Organism}_depth.tsv > $OutDir/${Organism}_${Strain}_depth_10kb.tsv
    sed -i "s/$/\t$Strain/g" $OutDir/${Organism}_${Strain}_depth_10kb.tsv
done
```

```
OutDir=analysis/genome_alignment/circos/cov_plot/vs_A22/ONT/TRE
mkdir -p $OutDir

  Ref_genome=$(ls assembly/misc_publications/c.albicans/A22_Chromosomes_29.fasta | grep 'A22')
  ProgDir=~/git_repos/tools/seq_tools/circos
  python $ProgDir/fasta2circos.py --genome $Ref_genome --contig_prefix "A22_" > $OutDir/query_genome.txt


OutDir=analysis/genome_alignment/circos/cov_plot/vs_A22/ONT/TRE
for File in $(ls analysis/genome_alignment/minimap/*/vs_r*/*_depth_10kb.tsv); do
  Strain=$(echo $File | cut -f5 -d '/')
  echo $Strain 
  cat $File | awk '{print $1,$2-1000,$2,$3,$4}' OFS='\t' | cut -f1,2,3,4 | sed 's/contig_/A22_contig_/g' > $OutDir/${Strain}_vs_ref_scatterplot.tsv
done
```

To run circos:

```
OutDir=/home/vegasa/circos/cov_plot/vs_A22/ONT
ProgDir=~/git_repos/scripts/circos_plots/genome_alignment/C.albicans/TLO_project/vs_A22/coverage_plots/TRE
circos -conf $ProgDir/vs_A22_circos.conf -outputdir $OutDir
mv $OutDir/circos.png $OutDir/ab791_vs_SC5314_circos.png
mv $OutDir/circos.svg $OutDir/ab791_vs_SC5314_circos.svg
ls $PWD/$OutDir/589_vs_Y-11545_v2_circos.png
