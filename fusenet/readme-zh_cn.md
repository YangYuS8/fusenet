# FuseNet 使用指南

-   [数据集](#data)
-   [保存 LMDB](#lmdb)
-   [训练 FuseNet 用于语义分割](#train)
-   [测试训练好的 FuseNet 语义分割](#test)

### <a name="data">数据集</a>

-   NYUv2 RGB-D dataset，可在此[下载](http://cs.nyu.edu/~silberman/datasets/nyu_depth_v2.html)。该数据集包含 1449 对 RGB-D 图像。Gupta 等提供的标准 train-val-test 分割，可见此[论文](https://people.eecs.berkeley.edu/~sgupta/pdf/rcnn-depth.pdf)。要将原始标签映射到 40 个类别，我们使用 Gupta 等提供的映射，可见此[论文](https://people.eecs.berkeley.edu/~sgupta/pdf/GuptaArbelaezMalikCVPR13.pdf)。要将原始标签映射到 13 个类别，我们使用 Couprie 等提供的映射，可见此[论文](https://arxiv.org/pdf/1301.3572.pdf)。为方便您，我们将数据分割和标签映射放在子文件夹`./data/`中。

    ```
    nyuv2_13class_mapping.mat      maps to 13 classes, 0 is invalid
    nyuv2_40class_mapping.mat      maps to 40 classes, 0 is invalid
    nyuv2_splits.mat               standard train-val-test split, index starts from 5001
    ```

-   SUN-RGBD dataset，可在此[下载](http://rgbd.cs.princeton.edu/)。该数据集包含 10335 个 RGB-D 图像，前 5050 个用于测试，其余用于训练和验证，默认使用 37 个类别。

### <a name="lmdb">保存 LMDB</a>

### <a name="train">训练 FuseNet 用于语义分割</a>

#### weighted cross-entropy

我们通过标准 trainval 子集图像的反类频率计算权重。您可以在以下文件中找到这些权重，并在训练期间选择性地使用它们。

```
check    ./data/nyuv2_13class_weight.txt
check    ./data/nyuv2_40class_weight.txt
check    ./data/sunrgb_37class_weight.txt
```

### <a name="test">测试训练好的 FuseNet 语义分割</a>
