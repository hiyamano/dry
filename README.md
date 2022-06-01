# linuxの基本コマンド
rmの-fはforceで、強制削除です。使用する際は注意しましょう。
一度は消してはいけないものを消すのが初心者あるあるですが、fastqファイル大本を消したりすると.....まずいですよね？
以下のようなコマンドは実際に使って覚えましょう。知らないものがあれば調べてみましょう。

```
ls
ls -l
ls -al

cd
cd ../
cd ../../
cd -
cd ~

pwd

mkdir test
touch test.txt
echo "test dayo" > test.txt
echo "test dayo" >> test.txt

cat test.txt
head test.txt
tail test.txt

more test.txt
less test.txt

rm test.txt
rm -f test
rm -rf test

cp
cp -f
cp -rf
```

# miniconda3 or anaconda3
https://docs.conda.io/en/latest/miniconda.html
該当するパッケージをインストール(基本的にlinuxを想定)

もしconda導入後、condaコマンド等が使えなければ、bashと打ち込みbashシェルを起動しましょう。
condaはbashrc等に、起動するように内容を書き込むので、bashシェルを起動すると、デフォルトではconda環境のbaseに入るはずです。
```
bash
```

baseという環境は、conda環境内の大本となるところで、ここにいろいろツールを入れることもできますが、
いろんなツールを入れたことによって環境が汚くなってきた際に、大本故に切り捨てることがめんどうです。
最初から切り捨てることも視野に入れ、別途子供となる環境を作成して、その中で作業しましょう。


conda環境を作成 (testという名の環境を作成)　一応pythonを3.8か3.9指定でインストールしましょう。
```
conda create -n test python=3.9
```

testという環境のactivateします。
```
conda activate test
```

testという環境にいろいろツールを追加しましょう。
NGS関連のツールはbiocondaというリポジトリにほとんど登録されています。
(最新のツールが登録されているわけではなく、一部ツールはとても古かったりもしばしば)

```
conda install bwa -c bioconda
conda install bowtie2 -c bioconda
conda install cutadapt -c bioconda
conda install trimmomatic -c bioconda
conda install seqkit -c bioconda
conda install samtools -c bioconda
```

上ではbiocondaを直接指定しましたが、インストールするツールを探す先として、biocondaを追加もできます。(つまりデフォルトではbiocondaは探す先にはいっていない。)
リポジトリの追加
```
conda config --add channnels conda-forge
conda config --add channnels bioconda
```

# fastq (fastq.gz)
注)以下、実際に走らせるコマンドは、調べてください。
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
〇トリミング
```
trimmomatic
cutadapt
```

〇マッピング
bwa、bowtie2は予めindexを作っておく必要があります。  
-t: threads数を指定。スレッドがよくわからない人は、この数の並列で進むと思いましょう。スレッド指定した数だけcpuが必要。  
-k: kmer, referenceとreadの配列で少なくともこの指定した長さが一致しないとマップしたことにならない。


bwa index  
reference.fasta.amb reference.fasta.ann、reference.fasta.bwt、reference.fasta.pac、reference.fasta.sa  
等が生成される。db名をつけられるが特に指定する必要はない。  
以後、reference.fastaと指定すれば、reference.fastaをindex化した上記ファイル等が使用される。  
```
bwa index reference.fasta
```

bwa paired-end
```
bwa mem -t 22 -k 25 reference.fasta read1.fastq.gz read2.fastq.gz > output.sam
```
bwa single-end
```
bwa mem -t 22 -k 25 reference.fasta read1.fastq.gz > output.sam
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



