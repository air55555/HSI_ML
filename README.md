
# HSI_ML 

A Python tool to perform deep learning experiments on various hyperspectral datasets. Based on [DeepHyperX](https://github.com/nshaud/DeepHyperX)  

What i did - implemented blood dataset as a custom dataset.   [Link](https://arxiv.org/abs/2008.10254). 
Then trained the model model/run_epoch500_1.00.pth using CUDA. Li 3D CNN model CNN was used as the most accurate 
as per initial paper .  ([Spectral–Spatial Classification of Hyperspectral Imagery with 3D Convolutional Neural Network, Li et al., Remote Sensing 2017](http://www.mdpi.com/2072-4292/9/1/67))
After that the model was saved and used to segment HSI images.
Result image - prediction with ground truth Datasets/Blood/predict_vs_GT.jpg  
Confusion matrix Datasets/Blood/Confusion_matrix.svg:

[    0     0     0     0     0     0     0     0     0]
 [    0 26638     0     0     0     0     0     0     0]
 [    0     0 12759     0     0     0     0     0     0]
 [    0     0     0  8643     0     0     0     0     0]
 [    0     0     0     0 12588     0     0     0     0]
 [   94     0     0     0     0  8267     0     0     0]
 [  143     0    29     0     0     0  7872     0     0]
 [   77     0     0     0     0     0     0  7367     0]
 [    0     0     0     0     0     0     0     0     0]
Accuracy : 99.594%
---
F1 scores :
	background: 0.000
	blood: 1.000
	ketchup: 0.999
	artificial blood: 1.000
	beetroot juice: 1.000
	poster paint: 0.994
	tomato concentrate: 0.989
	acrtylic paint: 0.995
	uncertain blood: nan
---
Kappa: 0.995
## Blood dataset
Unzip 7z archive file F_1 to the "Blood" and run --model li --dataset Blood --training_sample 0.3 --epoch 5

## CUDA install 
pip install torch===1.7.1+cu110 torchvision===0.8.2+cu110 torchaudio===0.7.2 -f https://download.pytorch.org/whl/torch_stable.html

## Pretrained 500 epoch model 
Used CUDA to generate it ,so need some tuning in main.py (line 306). Available here model/run_epoch500_1.00.pth.
Use it  with these params --model li   --dataset Blood --restore model/run_epoch500_1.00.pth --epoch 0



## Requirements

This tool is compatible with Python 2.7 and Python 3.5+.

It is based on the [PyTorch](http://pytorch.org/) deep learning and GPU computing framework and use the [Visdom](https://github.com/facebookresearch/visdom) visualization server.

## Setup

The easiest way to install this code is to create a [Python virtual environment](https://docs.python.org/3/tutorial/venv.html) and to install dependencies using:
`pip install -r requirements.txt`


## Models

Currently, this tool implements several SVM variants from the [scikit-learn](http://scikit-learn.org/stable/) library and many state-of-the-art deep networks implemented in PyTorch.
  * SVM (linear, RBF and poly kernels with grid search)
  * SGD (linear SVM using stochastic gradient descent for fast optimization)
  * baseline neural network (4 fully connected layers with dropout)
  * 1D CNN ([Deep Convolutional Neural Networks for Hyperspectral Image Classification, Hu et al., Journal of Sensors 2015](https://www.hindawi.com/journals/js/2015/258619/))
  * Semi-supervised 1D CNN ([Autoencodeurs pour la visualisation d'images hyperspectrales, Boulch et al., GRETSI 2017](https://delta-onera.github.io/publication/2017-GRETSI))
  * 2D CNN ([Hyperspectral CNN for Image Classification & Band Selection, with Application to Face Recognition, Sharma et al, technical report 2018](https://lirias.kuleuven.be/bitstream/123456789/566754/1/4166_final.pdf))
  * Semi-supervised 2D CNN ([A semi-supervised Convolutional Neural Network for Hyperspectral Image Classification, Liu et al, Remote Sensing Letters 2017](https://www.tandfonline.com/doi/abs/10.1080/2150704X.2017.1331053))
  * 3D CNN ([3-D Deep Learning Approach for Remote Sensing Image Classification, Hamida et al., TGRS 2018](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=8344565))
  * 3D FCN ([Contextual Deep CNN Based Hyperspectral Classification, Lee and Kwon, IGARSS 2016](https://arxiv.org/abs/1604.03519))
  * 3D CNN ([Deep Feature Extraction and Classification of Hyperspectral Images Based on Convolutional Neural Networks, Chen et al., TGRS 2016](http://elib.dlr.de/106352/2/CNN.pdf))
  * 3D CNN ([Spectral–Spatial Classification of Hyperspectral Imagery with 3D Convolutional Neural Network, Li et al., Remote Sensing 2017](http://www.mdpi.com/2072-4292/9/1/67))
  * 3D CNN ([HSI-CNN: A Novel Convolution Neural Network for Hyperspectral Image, Luo et al, ICPR 2018](https://arxiv.org/abs/1802.10478))
  * Multi-scale 3D CNN ([Multi-scale 3D Deep Convolutional Neural Network for Hyperspectral Image Classification, He et al, ICIP 2017](https://ieeexplore.ieee.org/document/8297014/))

## Usage

Start a Visdom server:
`python -m visdom.server`
and go to [`http://localhost:8097`](http://localhost:8097) to see the visualizations (or [`http://localhost:9999`](http://localhost:9999) if you use Docker).

Then, run the script `main.py`.

The most useful arguments are:
  * `--model` to specify the model (e.g. 'svm', 'nn', 'hamida', 'lee', 'chen', 'li'),
  * `--dataset` to specify which dataset to use (e.g. 'PaviaC', 'PaviaU', 'IndianPines', 'KSC', 'Botswana'),
  * the `--cuda` switch to run the neural nets on GPU. The tool fallbacks on CPU if this switch is not specified.

There are more parameters that can be used to control more finely the behaviour of the tool. See `python main.py -h` for more information.

Examples:
  * `python main.py --model SVM --dataset IndianPines --training_sample 0.3`
    This runs a grid search on SVM on the Indian Pines dataset, using 30% of the samples for training and the rest for testing. Results are displayed in the visdom panel.
  * `python main.py --model nn --dataset PaviaU --training_sample 0.1 --cuda 0`
    This runs on GPU a basic 4-layers fully connected neural network on the Pavia University dataset, using 10% of the samples for training.
  * `python main.py --model hamida --dataset PaviaU --training_sample 0.5 --patch_size 7 --epoch 50 --cuda 0` 
    This runs on GPU the 3D CNN from Hamida et al. on the Pavia University dataset with a patch size of 7, using 50% of the samples for training and optimizing for 50 epochs.



## License information

Code for the DeepHyperX toolbox is dual licensed depending on applications, research or commercial.

---


### RESEARCH AND NON COMMERCIAL PURPOSES

##### Code license

For research and non commercial purposes, all the code and documentation is released under the GPLv3 license:

Copyright (c) 2018 ONERA and IRISA, Nicolas Audebert, Bertrand Le Saux, Sébastien Lefèvre.

This program is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or any later version. This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, write to the Free Software Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.

PLEASE ACKNOWLEDGE THE ORIGINAL AUTHORS AND PUBLICATION ACCORDING TO THE REPOSITORY github.com/nshaud/DeepHyperx OR IF NOT AVAILABLE:
Nicolas Audebert, Bertrand Le Saux and Sébastien Lefèvre
"Deep Learning for Classification of Hyperspectral Data: A comparative review",
IEEE Geosciences and Remote Sensing Magazine, 2019.
