# fastq (fastq.gz)
解析に用いるファイル。gz圧縮されたまま取り扱えるツールは多く、基本的に圧縮したまま扱います。
gz圧縮されていないファイルはテキストファイルなので中身を実際に見てみましょう。

〇fastqを取り扱うのに便利なツールseqkit
```
seqkit stat ~.fastq
```

〇QC　ほかにも色々あるが、fastqcは古くから存在し、ずっと使われている
```
fastqc
```

〇マッピング
bwa、bowtie2は予めindexを作っておく必要があります。

```
bwa index reference.fasta
bwa reference.fasta ~.fastq
```

```
bowtie2
```

# sam, bam
マッピングした後のファイルは、標準出力(コマンドラインにそのまま出力される)か、ファイルとして保存されることが一般的。
標準出力されるものは、以下のようにファイルに保存します。
```
bwa reference.fasta ~.fastq > outputfile.sam
```

マッピング後の出力ファイルはテキストで、samと呼ばれます。(拡張子は.samにしておきましょう。)
samファイルは通常、そのまま置いておかないで、バイナリー化したbamファイルとして置いておきます。
またbam化に際して、sort, indexの作成、を行います。

〇samtoolsを用いて、sortとbam化を行う。
```
samtools sort -O BAM -o output.bam input.sam 
```
indexの作成(~.bam.bai)
```
samtools index input.bam
```



