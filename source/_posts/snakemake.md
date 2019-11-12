---
title: snakemake
date: 2019-10-29 22:42:44
tags: snakemake
---

### 优点

- 适用于多样品分析流程，可以对多个样品的流程进行拆分，并对每一步的CPU，内存进行限制；
- 对任务个数，总CPU进行限制；
- 对中间文件的删除和保护；
- 根据输入，输出及时间戳来控制模块的先后顺序；
- 适用于PBS,LFS,qsub,slurm等资源管理系统。

相同功能的工具有qsub-pbs ,sjm等。



### 规则：

```
rule FastqToSam_cf:
    input:
        fwd= rawdir + "/" + cf_fwd,
        rev= rawdir + "/" + cf_rev
    output:
        temp(pre + "/" + pre + ".01.unmaped.bam")
    params:
        tmp_dir=TMP_DIR,
        prefix=pre
    log:
        pre + "/" + pre + "_log/log1." + pre + ".FastqToSam"
    benchmark:
        "benchmarks/" + pre + ".FastqToSam.benchmark.txt"
    threads:2
    shell:"""
        gatk FastqToSam \
        -F1 {input.fwd} \
        -F2 {input.rev} \
        -O {output} \
        --TMP_DIR {params.tmp_dir} \
        -SM {params.prefix} \
        -RG KAPA \
        -PL illumina &> {log}
        """

rule ExtractUmisFromBam_cf:
    input:
        pre + "/" + pre + ".01.unmaped.bam"
    output:
        temp(pre + "/" + pre + ".02.unmapped.withUMI.bam")
    params:
        tmp_dir=TMP_DIR,
        fgbio=FGBIO
    log:
        pre + "/" + pre + "_log/log2." + pre + ".ExtractUmisFromBam"
    benchmark:
        "benchmarks/" + pre + ".ExtractUmisFromBam.benchmark.txt"
    threads:2
    shell:"""
        java -Djava.io.tmpdir={params.tmp_dir} -Xmx4g -jar {params.fgbio} ExtractUmisFromBam \
        --input {input} \
        --output {output} \
        --read-structure 3M2S146T 3M2S146T \
        --molecular-index-tags ZA ZB \
        --single-tag RX \
        &> {log}
        """

rule SamToFastq_cf:
    input:
        pre + "/" + pre + ".02.unmapped.withUMI.bam"
    output:
        temp(pre + "/" + pre + ".03.unmapped.withUMI.fastq")
    params:
        tmp_dir=TMP_DIR
    log:
        pre + "/" + pre + "_log/log3." + pre + ".SamToFastq"
    benchmark:
        "benchmarks/" + pre + ".SamToFastq.benchmark.txt"
    threads:2
    shell:"""
        gatk SamToFastq \
    -I {input} \
    -F {output} \
    --INTERLEAVE true \
    --TMP_DIR {params.tmp_dir} &> {log}
    """

```

### 投递任务

```
snakemake -s Snakefile --cluster "sbatch -N 1 -n {threads} -p node2 " -j 5 -c 100 
```

### 流程图

![启健流程](http://pydi4imsi.bkt.clouddn.com/dag.svg)



### 对启健流程的优化

- 服务器CPU 利用率提高
- 单位CPU内分析样品量增加

### 测试

70个启健样品，c02（192核），CPU占用情况如下所示：

  <img src="http://pydi4imsi.bkt.clouddn.com/out.png" style="zoom: 150%;" />

**结果**：20h左右可以完成分析，16hCPU开始出现空余，即可对其他数据进行分析。