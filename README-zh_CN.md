# FuseNet
## [PyTorch](https://github.com/MehmetAygun/fusenet-pytorch)

请参考PyTorch实现以获取最新版本。

FuseNet是用于训练包含RGB-D图像数据集的深度卷积神经网络（CNN）的一般架构。它可用于语义分割、场景分类和其他应用。此仓库是[this paper](#paper)的官方发布，基于[BVLC/caffe](https://github.com/BVLC/caffe)框架实现。

* [使用FuseNet](#usage)
* [发布Caffemodel](#release)
* [出版物](#paper)
* [许可和联系方式](#other)

## 使用
### 安装
代码与2016年6月早期的Caffe版本兼容。在Ubuntu 16.04下开发，使用CUDA 7.5和cuDNN v5.0。如果在其他Ubuntu发行版下使用程序，可能需要在root CMakeLists.txt文件的第72-73行添加注释。如果在其他操作系统下编译，请善用Google。我们主要在Nvidia Titan X GPU上测试程序。请注意，支持多GPU训练。
```
git clone https://github.com/tum-vision/fusenet.git
mkdir build && cd build
cmake ..
make -j10
make runtest -j10
```

### 训练和测试
我们在`./fusenet/`下提供所有必要的python脚本和prototxt文件，以重现我们发表的结果。以下是简要指南。有关更详细的说明，请查看[这里](fusenet/readme.md)。

#### 初始化
我们的网络架构基于VGGNet-16layer。然而，由于我们有额外的深度输入通道，我们提供了相应的计算。

#### 数据准备
为了存储数据集，我们将配对的RGB-D图像保存到LMDB中。我们还将原始深度图像缩放到[0, 255]的范围。可选择将缩放后的深度值转换为unsigned char（灰度图），以节省内存。如果不想损失精度，可以将缩放后的深度存储为float。为了准备LMDB，我们提供了以下python脚本供参考。然而，您也可以编写自己的图像输入层来抓取配对的RGB-D图像。
```
demo ./fusenet/scripts/save_lmdb.py
```

#### LMDB随机打乱
我们支持LMDB随机打乱，并建议在每次训练周期后进行随机打乱。要启用此选项，请在prototxt中将DataLayer的shuffle标志设置为true。请注意，我们不支持与LevelDB的随机打乱。
```
demo ./fusenet/segmentation/nyuv2_sf1/train.prototxt
```
#### 加权交叉熵损失
处理类别不平衡的一种常见技术是为每个类别的损失赋予不同的权重，通常对较少见的类别赋予较高权重，对较常见的类别赋予较低权重。对于语义分割，我们通过允许用户为每个标签指定权重，支持使用SoftmaxWithLossLayer进行这种损失加权。设置权重的一种方法是根据逆类别频率（详见我们的论文）。我们在`./fusenet/data/`中提供了论文中使用的权重。

#### 批归一化
我们在每次卷积后使用批归一化，这由Caffe的BatchNormLayer支持。请注意，我们在每个BatchNormLayer后添加ScaleLayer。


#### 测试
为了测试语义分割性能，我们提供python脚本来计算全局准确率、平均类别准确率和平均交并比分数。实现基于混淆矩阵。
```
demo ./fusenet/scripts/test_segmentation.py
```

## <b name="release">发布Caffemodel</b>
### 语义图像分割
标有勾号的项目已可下载，否则将很快发布。除非另有说明，所有模型均从预训练的VGGNet-16Layer模型微调。敬请期待 :fire:

#### NYUv2 40类语义分割
有关数据集的更多信息，请查看[这里](http://cs.nyu.edu/~silberman/datasets/nyu_depth_v2.html)。
* FuseNet-SF1:

  此模型使用FuseNet Sparse-Fusion1架构在320x240分辨率下训练。要获得640x480的全分辨率，可以使用双线性上采样或更好的CRF细化。
 - [x] [deploy.prototxt](fusenet/segmentation/nyu40-sf1/deploy.prototxt)
 - [ ] caffemodel

* FuseNet-SF5:

  此模型使用FuseNet SparseFusion5架构在320x240分辨率下训练。达到66.0%全局像素准确率、43.4%平均类别准确率和32.7%平均类别IoU。
 - [x] [deploy.prototxt](fusenet/segmentation/nyu40-sf5/deploy.prototxt)
 - [ ] caffemodel

#### SUN-RGBD 37类语义分割
有关数据集的更多信息，请查看[这里](http://rgbd.cs.princeton.edu/)。
* FuseNet-SF5:

  此模型在224x224分辨率下训练。达到76.3%全局像素准确率、48.30%平均类别准确率和37.3%平均类别IoU。
 - [x] [deploy.prototxt](fusenet/segmentation/sunrgbd-sf5/deploy.prototxt)
 - [ ] caffemodel

## <c name="paper">出版物</c>
如果您在工作中使用此代码或我们的训练模型，请考虑引用以下论文。

Caner Hazirbas, Lingni Ma, Csaba Domokos and Daniel Cremers, _"FuseNet: Incorporating Depth into Semantic Segmentation via Fusion-based CNN Architecture"_, in proceedings of the 13th Asian Conference on Computer Vision, 2016. ([pdf](https://vision.in.tum.de/_media/spezial/bib/hazirbasma2016fusenet.pdf))

    @inproceedings{fusenet2016accv,
     author    = "C. Hazirbas and L. Ma and C. Domokos and D. Cremers",
     title     = "FuseNet: incorporating depth into semantic segmentation via fusion-based CNN architecture",
     booktitle = "Asian Conference on Computer Vision",
     year      = "2016",
     month     = "November",
    }

## <d name="others"> 许可和联系方式</d>
[BVLC/caffe](https://github.com/BVLC/caffe)根据[BSD 2-Clause license](https://github.com/BVLC/caffe/blob/master/LICENSE)发布。对原始代码的修改根据[GNU General Public License Version 3 (GPLv3)](http://www.gnu.org/licenses/gpl.html)发布。

如有问题、评论或报告错误，请联系**Lingni Ma** [:envelope:](mailto:lingni@in.tum.de)。
