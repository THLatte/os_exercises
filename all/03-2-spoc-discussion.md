# lec6 SPOC思考题


NOTICE
- 有"w3l2"标记的题是助教要提交到学堂在线上的。
- 有"w3l2"和"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的git repo上。
- 有"hard"标记的题有一定难度，鼓励实现。
- 有"easy"标记的题很容易实现，鼓励实现。
- 有"midd"标记的题是一般水平，鼓励实现。


## 个人思考题
---

（1） (w3l2) 请简要分析64bit CPU体系结构下的分页机制是如何实现的
```
  + 采分点：说明64bit CPU架构的分页机制的大致特点和页表执行过程
  - 答案没有涉及如下3点；（0分）
  - 正确描述了64bit CPU支持的物理内存大小限制（1分）
  - 正确描述了64bit CPU下的多级页表的级数和多级页表的结构或反置页表的结构（2分）
  - 除上述两点外，进一步描述了在多级页表或反置页表下的虚拟地址-->物理地址的映射过程（3分）
 ```
- [x]  

> 64bit CPU支持的物理内存不能超过2^64B，即16EB。

> 但由于需要实现分页机制，64位地址不能全部用来表示物理内存，所以实际能够表示的内存数量会大大减少。

> 64bit CPU下将页表分为5级，虚拟地址将被分为6段，前5段每一段的长度决定了相应级页表的页表项数目，其值就是页表项编号。前4级页表中，每一个页表项存储的是下一级页表的基址和标志位（如存在位、修改位等）。最后一级页表存储的内容就是物理地址页帧号。虚拟地址最后一段即页帧内偏移量。

> 得到虚拟地址后，首先读取其第一段，在第一级页表中找到对应的页表项，查看其标志位，如果标志位表明表项有效，则获取表项内容，也就是第二级页表的基址，然后读取虚拟地址第二段，重复上述工作，直到找到最后一级页表。最后一级页表中获取的内容即物理地址页帧号，将其左移相应位数（由物理页帧大小决定），加上虚拟地址最后一段即帧内偏移量，就能得到虚拟地址对应的物理地址了。

## 小组思考题
---

（1）（1）(spoc) 某系统使用请求分页存储管理，若页在内存中，满足一个内存请求需要150ns (10^-9s)。若缺页率是10%，为使有效访问时间达到0.5us(10^-6s),求不在内存的页面的平均访问时间。请给出计算步骤。

[x]
500=0.9*150+0.1*x

> 由题意500=0.9\*150+0.1\*x

> 得x=3650ns=3.65us,则不在内存的页面的平均访问时间为3.65us

（2）(spoc) 有一台假想的计算机，页大小（page size）为32 Bytes，支持32KB的虚拟地址空间（virtual address space）,有4KB的物理内存空间（physical memory），采用二级页表，一个页目录项（page directory entry ，PDE）大小为1 Byte,一个页表项（page-table entries
PTEs）大小为1 Byte，1个页目录表大小为32 Bytes，1个页表大小为32 Bytes。页目录基址寄存器（page directory base register，PDBR）保存了页目录表的物理地址（按页对齐）。

PTE格式（8 bit） :
```
  VALID | PFN6 ... PFN0
```
PDE格式（8 bit） :
```
  VALID | PT6 ... PT0
```
其
```
VALID==1表示，表示映射存在；VALID==0表示，表示映射不存在。
PFN6..0:页帧号
PT6..0:页表的物理基址>>5
```
在[物理内存模拟数据文件](./03-2-spoc-testdata.md)中，给出了4KB物理内存空间的值，请回答下列虚地址是否有合法对应的物理内存，请给出对应的pde index, pde contents, pte index, pte contents。
```
Virtual Address 6c74
Virtual Address 6b22
Virtual Address 03df
Virtual Address 69dc
Virtual Address 317a
Virtual Address 4546
Virtual Address 2c03
Virtual Address 7fd7
Virtual Address 390e
Virtual Address 748b
```

比如答案可以如下表示：
```
Virtual Address 7570:
  --> pde index:0x1d  pde contents:(valid 1, pfn 0x33)
    --> pte index:0xb  pte contents:(valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)
      
Virtual Address 21e1:
  --> pde index:0x8  pde contents:(valid 0, pfn 0x7f)
      --> Fault (page directory entry not valid)

Virtual Address 7268:
  --> pde index:0x1c  pde contents:(valid 1, pfn 0x5e)
    --> pte index:0x13  pte contents:(valid 1, pfn 0x65)
      --> Translates to Physical Address 0xca8 --> Value: 16
```

> 答案如下：

```
Virtual Address 6c74:
  --> pde index:0x1b  pde contents:(valid 1, pt 0x20)
    --> pte index:0x3  pte contents:(valid 1, pfn 0x61)
      --> Translates to Physical Address 0xc34 --> Value: 06

Virtual Address 6b22:
  --> pde index:0x1a  pde contents:(valid 1, pt 0x52)
    --> pte index:0x19  pte contents:(valid 1, pfn 0x47)
      --> Translates to Physical Address 0x8e0 --> Value: 1a

Virtual Address 03df:
  --> pde index:0x0  pde contents:(valid 1, pt 0x5a)
    --> pte index:0x1e  pte contents:(valid 1, pfn 0x05)
      --> Translates to Physical Address 0x0bf --> Value: 0f

Virtual Address 69dc:
  --> pde index:0x1a  pde contents:(valid 1, pt 0x52)
    --> pte index:0xe  pte contents:(valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)

Virtual Address 317a:
  --> pde index:0xc  pde contents:(valid 1, pt 0x18)
    --> pte index:0xb  pte contents:(valid 1, pfn 0x35)
      --> Translates to Physical Address 0x6ba --> Value: 1e

Virtual Address 4546:
  --> pde index:0x11  pde contents:(valid 1, pt 0x21)
    --> pte index:0xa  pte contents:(valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)
      
Virtual Address 2c03:
  --> pde index:0xb  pde contents:(valid 1, pt 0x44)
    --> pte index:0x0  pte contents:(valid 1, pfn 0x57)
      --> Translates to Physical Address 0xae3 --> Value: 16

Virtual Address 7fd7:
  --> pde index:0x1f  pde contents:(valid 1, pt 0x12)
    --> pte index:0x1e  pte contents:(valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)

Virtual Address 390e:
  --> pde index:0xe  pde contents:(valid 0, pt 0x7f)
    --> Fault (page directory entry not valid)

Virtual Address 748b:
  --> pde index:0x1d  pde contents:(valid 1, pt 0x00)
    --> pte index:0x4  pte contents:(valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)
```

（3）请基于你对原理课二级页表的理解，并参考Lab2建页表的过程，设计一个应用程序（可基于python, ruby, C, C++，LISP等）可模拟实现(2)题中描述的抽象OS，可正确完成二级页表转换。


（4）假设你有一台支持[反置页表](http://en.wikipedia.org/wiki/Page_table#Inverted_page_table)的机器，请问你如何设计操作系统支持这种类型计算机？请给出设计方案。

 (5)[X86的页面结构](http://os.cs.tsinghua.edu.cn/oscourse/OS2015/lecture06#head-1f58ea81c046bd27b196ea2c366d0a2063b304ab)
--- 

## 扩展思考题

阅读64bit IBM Powerpc CPU架构是如何实现[反置页表](http://en.wikipedia.org/wiki/Page_table#Inverted_page_table)，给出分析报告。

--- 
