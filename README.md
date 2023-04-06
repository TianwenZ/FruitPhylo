# 云南省9370种植物果实类型的系统发育保守性分析
本项目是对发表在《ecology》上的文章「Phylogenetic conservatism explains why plants are more likely to produce fleshy fruits in the tropics」的结果的复现。

本文的问题主要是探讨植物的果实类型（干果或者鲜果）是什么因素决定的，或者说是各个都可能的因子的相对的贡献。目前有着多种因子可能对其影响，例如温度、气候、生活型等，但鲜有研究考虑系统发育在其中的贡献。

这里面有三个包在默认的CRAN中并没有，需要从GitHub上安装：
- `devtools::install_github("arives/rr2")`
- `devtools::install_github("daijiang/phyr")`
- `devtools::install_github("lamho86/phylolm")`

## 项目结构
- 根目录是主要的代码；
- `data`目录是来源数据，**本项目中使用的数据与原始数据略微不同，体现在原始数据中已经进行了名义数据的One-Hot编码，而本项目没有**；
- `result`目录下是生成的结果，以及部分中间文件。

## 结果解读
### 描述性分析
这篇文章首先对这9370种云南的植物物种进行了一个描述性的统计，2635种是鲜果（fleshy fruits），其具有较强的系统发育信号，**这里的果实类型是结果**，也就是文章想要讨论的对象，而其他的对象都是影响的因素，本文中主要有分布区（热带、亚热带、温带）、生长类型（草本、木本），以及少有考虑的系统发育关系。这种描述性分析通过绘制简单的百分比堆积柱形图展示。参考`summary_analysis.R`脚本。在这一部分中还有一个针对各变量之间相关性的分析，原文使用的是相关性表格的形式，这里修改为更友好地相关矩阵图来展示。

### 系统发育分析
第二块内容则是进行系统发育分析，在维管植物中，有一个非常方便的包`V.PhyloMaker2`，涵盖了绝大多数已知的维管植物，可以直接构建带有时间年份的系统树，我们用它构建自己的数据子集即可，该包的使用方法参见[https://github.com/jinyizju/V.PhyloMaker2](https://github.com/jinyizju/V.PhyloMaker2)。

这里构建树的时间随着物种的数量而延长，这里可以把生成的系统树保存到本地方便后续使用。**注意，原文中的附件直接附带了系统发育树的文件，但是我重新构建后的树和原本地树有非常大的差异，猜测是由于版本更新带来的影响，推荐还是使用原文附件中现成的系统树文件。**

对于有变种或者亚种等物种的情况，我们需要额外的添加一列`species.relative`，把这些变种对应的物种填写上才不会删除重复的物种名称。

直接生成的系统树并不会多美观，还需要结合性状等数据进行映射后才能看出一些现象。系统树的解释也是主要看聚集在一块的性状是否一致，是就是保守性状，否则就是不保守。绘制过程中要注意结合额外的性状数据时，可能会出现匹配的问题，这是因为**`V.PhyloMaker2`生成的树文件会默认把物种名转换为`A_B`的下划线格式**，因此原先的物种名用空格的情况可能就会存在无法匹配的情况，需要注意。

当然也可以用系统发育信号来衡量。再结合 $R^2_{lik}$ 值，可以看出各个因子在其中的贡献。这里面在使用`phyloglm`等函数进行分析的时候，需要把名义变量转换为`0/1`数据，记得数据格式的转换。**另外需要注意的是，原文中的 $\alpha$ 的值是把树文件进行了标准化后的值（即树的枝长缩放到0-1范围内），因此在值上有会一定程度的差异，但是整体上比较是没有问题的，就我自己的看法，做系统发育信号的时候不标准化枝长也完全没有问题**。

### 多性状的权重贡献分析（偏决定系数）
前面的系统发育分析中我们可以得知系统发育在果实性状上有很高的解释度，但是却并不能说明系统发育就是最重要的那个因子，仅仅是他解释力强罢了。我们可以用一种叫偏决定系数的方法通过全模型减去协变量模型来量化实际的贡献率。

对于本文，

## 警告信息
在构建系统树的过程中，可能会出现如下的警告，表示物种清单的科和实际用来构建树的科有一定冲突，但并不会影响结果。
```markdown
[1] "Taxonomic classification not consistent between sp.list and tree."
            genus family_in_sp.list family_in_tree
11       Alangium       Alangiaceae      Cornaceae
24        Aporosa     Euphorbiaceae Phyllanthaceae
28        Ardisia       Myrsinaceae    Primulaceae
38      Baccaurea     Euphorbiaceae Phyllanthaceae
45      Bischofia     Euphorbiaceae Phyllanthaceae
63       Carallia   Rhizophoraceae  Rhizophoraceae
65       Caryocar    Caryocareaceae  Caryocaraceae
75         Celtis          Ulmaceae    Cannabaceae
86   Cleistanthus     Euphorbiaceae Phyllanthaceae
87   Clerodendrum       Verbenaceae      Lamiaceae
90  Cochlospermum  Cochlospermaceae       Bixaceae
104  Cunninghamia     Euphorbiaceae   Cupressaceae
109      Drypetes     Euphorbiaceae Putranjivaceae
124      Garcinia         Rubiaceae     Clusiaceae
128    Gironniera          Ulmaceae    Cannabaceae
139        Idesia    Flacourtiaceae     Salicaceae
143          Itea     Saxifragaceae       Iteaceae
147    Kielmeyera        Clusiaceae Calophyllaceae
161   Liquidambar    Hamamelidaceae   Altingiaceae
188       Myrsine      Myrsinanceae    Primulaceae
221   Pteroceltis          Ulmaceae    Cannabaceae
239       Salsola    Chenopodiaceae  Amaranthaceae
251     Simarouba    Simaroubiaceae  Simaroubaceae
263         Taxus      Cupressaceae       Taxaceae
268         Trema          Ulmaceae    Cannabaceae
274         Vitex       Verbenaceae      Lamiaceae
```