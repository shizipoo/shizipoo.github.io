---
title: bamdst使用方法
date: 2019-11-04 20:46:14
tags: NGS
---

Bamdst 是一个轻量级工具，用来统计bam文件的目标区域深度覆盖。Bam 文件需要进行排序，bed文件和输出路径必须进行指定。
Github地址：[https://github.com/shiquan/bamdst](https://github.com/shiquan/bamdst)

### 常规：  

    bamdst -p <probe.bed> -o ./ in1.bam
### 管道模式：

    samtools view in1.bam -u | bamdst -p x.bed -o ./ -
### 参数：

    -o / --outdir [dir] 
    设置输出路径 [强制]
    -p / --bed [file]
    探针或目标区域文件，这些区域需要合并 [强制]
### 可选参数：

    -f / --flank [num]
    如果你想计算侧翼区域的覆盖度，设置这个值，默认值200。
    --maxdepth [num]
    对于一些项目，特殊区域的深度非常高，如果你不想在分布文件中显示这些不正常的深度，设置阈值去过滤它们。默认值是0（不过滤）。
    --cutoffdepth [num]
    对于一些项目，人们关注特定深度的覆盖度，比如10000X。bamdst 仅仅计算0x, 4x, 10x, 30x, 100x的覆盖度 ，所以你可以设置这个值，在coverage.report 文件中会显示特定的覆盖度。默认值0。 
    --isize [num]
    对于比对率差的双端读长，推断的插入片段大小非常大。为了合理的可视化目的可以设置一个阈值。默认值2000。
    --uncover [num]
    为了计算覆盖度不好的区域，设置这个阈值。默认是<5。
    --use_rmdup (v1.0.0以后的一个可用参数 )
    使用rmdup depth代替cover depth来计算目标区域的覆盖度。
### 输出文件： 
输出文件夹中会有7个文件。它们是：

    -coverage.report
    这个文件包含目标区域和侧翼区域的所有覆盖度信息，输入文件的reads信息。
    -cumu.plot
    深度分布图
    -insert.plot
    推断的插入片段大小分布图
    -chromosome.report
    每一条染色体的深度和覆盖度信息
    -region.tsv.gz
    对于bed文件中的每一个区域，平均深度，中位深度和这个区域的覆盖度将显示在这个文件中。
    -depth.tsv.gz
    对于探针文件（bed）的每一个位置，三种深度值将被计算，包括raw depth , rmdup depth 和coverage depth 。raw depth 从bam文件中检索，没有任何限制。rmdup depth 计算去除重复reads，二次比对reads和低map质量reads(mapQ<20) ，这个值与samtools depth 的输出结果很相似。coverage depth 是考虑了缺失区域的raw depth ，所以它的值应该大于或等于raw depth 。我们用raw depth 来评估 coverage.report文件中的覆盖度信息。如果你想用rmdup depth 来计算覆盖度，请使用 "--use_rmdup"参数。
    -uncover.bed
    此bed文件中包含bam文件比对探针文件，不好的覆盖和没有覆盖的区域。通过“--uncover”来设置uncover的阈值。