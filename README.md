#bowtie1_unalign_from_multi 

#faulty output and work arounds when using bowtie1 with multiple reportable alignments (option `-M`) and writing unaligned reads to file (option `--un`) at the same time

#bioinformatics #upstream #sequencing #alignment #mapping #bowtie #bowtie1 #multiple #option #unaligned

#用“bowtie1”时，同时采用参数`-M`设定一条测序序列最多展示的比对结果数，以及参数`--un`输出比对不上参考基因组/数据库的测序序列，输出有错误，以及解决方法

#生信 #上游 #测序 #比对 #bowtie #bowtie1 #参数 #未比对 #未对齐

affect:

影响：

if using only one reference genome/database:

not affecting analysis using only aligned reads;

affects those analyzing unaligned reads.

if using multiple reference genomes and/or databases:

not affecting if all reference files are mutually exclusive;

affects if any database has overlaps with another, especially studies mapping to different databases using unmapped reads sequencially.

对于用单一参考基因组/数据库：

如果只用比对完的结果则没有影响；

会影响未对齐序列的分析。

对于用了多个参考基因组和数据库的情况：

如果各个基因组和数据库完全不重复则没有影响；

如果数据库有重合就会有影响，特别是按顺序使用未必对上的序列来一步步比对到一系列参考数据库的研究。

---
TLDR:

太长不看:

Do NOT use options `-M` and `--un` together, like 
不要同时使用参数`-M`和`--un`，比如

`bowtie -M <num_aligned_reads_wanted_reporting> --un <filename_for_export_unaligned_reads>` #faulty 会有问题

or

或者

`bowtie -M <num_aligned_reads_wanted_reporting> --no-unal --un <filename_for_export_unaligned_reads>` #also faulty 也会有问题

but allow bowtie1 to include unaligned reads in the output (those with field $2 flag equal to 4 in `.sam` or `.bam` files) without using option `--no-unal` to suppress unaligned/unmapped reads；

and use `samtools` in alternative to do the separation.

但让“bowtie1”的输出包含未比对/未对齐的测序序列（在`.sam`或`.bam`文件中第二列的“flag”等于4的那些）而不要用参数`--no-unal`来排除未比对/未对齐的测序序列；

可以用`samtools`代替筛选。

---
Detailed explain and work around

详细解释以及替代的应对方法

create some test files in working directory to repeat, using `nano`, `vim`, whatever editors, or directly use the ".txt" from windows then move to the wsl2:

在工作路径文件夹里，用`nano`, `vim`,或者随便什么文本编辑器，或者直接用windows的txt编辑之后再移到wsl2虚拟机，来新建一些测试文件用来复现：

"testref.fa" #the reference genome/database fasta file for test 
#测试用的参考基因组/数据库的“fasta”文件
```
>testref1_for_single
AAATTTGGGCCCAAATTTGGGCCC
>testref2_for_multi
AGCTAGCTAAGGCCTTTCGATCGAA
AGCTAGCTAAGGCCTTTCGATCGAA
AGCTAGCTAAGGCCTTTCGATCGAA
AGCTAGCTAAGGCCTTTCGATCGAA
```

"testread.fq" #the sample read fastq file for test
#测试用的样本测序数据fastq文件
```
@testread1_should_singlemap
AAATTTGGGCCCAAATTTGGGCCC
+
AAAAAAAAAAAAAAAAAAAAAAAA
@testread2a_should_multimap
AGCTAGCTAAGGCCTTTCGATCGAA
+
GGGGGGGGGGGGGGGGGGGGGGGGG
@testread2b_should_multimap
AGCTAGCTAAGGCCTTTCGATCGAA
+
GGGGGGGGGGGGGGGGGGGGGGGGG
@testread3_should_unmap
TCGATCGATTCCGGAAAGCTAGCT
+
BBBBBBBBBBBBBBBBBBBBBBBB
```

build the index with bowtie1 (creating 6 .ebwt files)

用“bowtie1”生成索引 （生成6个“.ebwt”文件）
```
bowtie-build --threads 1 testref.fa  testi
# add the ! symbol right in front of bowtie-build without space if running in jupyter notebook cells, like `!bowtie-build`
# 在jupyter notebook的代码单元框里运行时要在“bowtie-build”前面加上一个半角叹号（!），没有空格，像这样`!bowtie-build`
```

When using unique reporting option `-k 1

editing...
正在编辑


