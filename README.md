# caffe-SSDH

By [Zexi Han](http://liamhancs.weebly.com).

National Laboratory of Pattern Recognition (NLPR) at the Chinese Academy of Sciences (CAS).

## Introduction

caffe-SSDH updates the caffe version of [Caffe-DeepBinaryCode](https://github.com/kevinlin311tw/Caffe-DeepBinaryCode). Now it supports the experiments with the latest batch normalization and [Deep Residual Networks](https://github.com/KaimingHe/deep-residual-networks). 

We made tests on the dataset from Alibaba Large-scale Image Search Challenge with SSDH approach, which contains 5 million product images, but the performance was not as good as Kevin's experiment on CIFAR-10 dataset. It should have worked better theoretically. In the future work, I will make improvements on the original SSDH and update it here.

## Alibaba Large-scale Image Search Challenge

0. Introduction of Alibaba Large-scale Image Search Challenge dataset:

     Train set  |Evaluation set|  Query set   
  :------------:|:------------:|:------------:
     1,950,998  |  3,195,334   | 1,417+3,567

0. The dataset is classified into 10 high-level concepts and 676 sub concepts:
    
      Tops     | Skirts | Trousers and shorts |  Bags  |   Shoes   | Accessories | Snacks |       Makeup        | Drinks | Furnishing 

## Contact

Please feel free to leave suggestions or comments to Zexi Han (zexihan@me.com).

# Caffe-DeepBinaryCode

Supervised Learning of Semantics-Preserving Deep Hashing (SSDH)

Created by Kevin Lin, Huei-Fang Yang, and Chu-Song Chen at Academia Sinica, Taipei, Taiwan.


## Introduction

We present a simple yet effective supervised deep hash approach that constructs binary hash codes from labeled data for large-scale image search. SSDH constructs hash functions as a latent layer in a deep network and the binary codes are learned by minimizing an objective function defined over classification error and other desirable hash codes properties. Compared to state-of-the-art results, SSDH achieves 26.30% (89.68% vs. 63.38%), 17.11% (89.00% vs. 71.89%) and 19.56% (31.28% vs. 11.72%) higher precisions averaged over a different number of top returned images for the CIFAR-10, NUS-WIDE, and SUN397 datasets, respectively.

<img src="https://www.csie.ntu.edu.tw/~r01944012/ssdh_intro.png" width="800">

The details can be found in the following [arXiv preprint.](http://arxiv.org/abs/1507.00101)
Presentation slide can be found [here](http://www.csie.ntu.edu.tw/~r01944012/deepworkshop-slide.pdf)


## Citing the deep hashing work

If you find our work useful in your research, please consider citing:

    Supervised Learning of Semantics-Preserving Hashing via Deep Neural Networks for Large-Scale Image Search
    Huei-Fang Yang, Kevin Lin, Chu-Song Chen
    arXiv preprint arXiv:1507.00101



## Prerequisites

  0. MATLAB (tested with 2012b on 64-bit Linux)
  0. Caffe's [prerequisites](http://caffe.berkeleyvision.org/installation.html#prequequisites)


## Install Caffe-DeepBinaryCode

Adjust Makefile.config and simply run the following commands:

    $ make all -j8
    $ make test -j8
    $ make matcaffe
    $ ./prepare.sh

For a faster build, compile in parallel by doing `make all -j8` where 8 is the number of parallel threads for compilation (a good choice for the number of threads is the number of cores in your machine).

## Demo
 
Launch matlab and run `demo.m`. This demo will generate 48-bits binary codes for each image using the proposed SSDH.
    
    >> demo


<img src="https://www.csie.ntu.edu.tw/~r01944012/ssdh_demo.png" width="350">


## Retrieval evaluation on CIFAR10

Launch matalb and run `run_cifar10.m` to perform the evaluation of `precision at k` and `mean average precision at k`. We set `k=1000` in the experiments. The bit length of binary codes is `48`. This process takes around 12 minutes.
    
    >> run_cifar10


Then, you will get the `mAP` result as follows. 

    >> MAP = 0.899731

Moreover, simply run the following commands to generate the `precision at k` curves:

    $ cd analysis
    $ gnuplot plot-p-at-k.gnuplot 

You will reproduce the precision curves with respect to different number of top retrieved samples when the 48-bit hash codes are
used in the evaluation.
 
## Train SSDH on CIFAR10

Simply run the following command to train SSDH:


    $ ./examples/SSDH/train.sh


After 50,000 iterations, the top-1 error is 9.7% on the test set of CIFAR10 dataset:
```
I0107 19:24:32.258903 23945 solver.cpp:326] Iteration 50000, loss = 0.0274982
I0107 19:24:32.259012 23945 solver.cpp:346] Iteration 50000, Testing net (#0)
I0107 19:24:36.696506 23945 solver.cpp:414]     Test net output #0: accuracy = 0.903125
I0107 19:24:36.696543 23945 solver.cpp:414]     Test net output #1: loss: 50%-fire-rate = 1.47562e-06 (* 1 = 1.47562e-06 loss)
I0107 19:24:36.696552 23945 solver.cpp:414]     Test net output #2: loss: classfication-error = 0.332657 (* 1 = 0.332657 loss)
I0107 19:24:36.696559 23945 solver.cpp:414]     Test net output #3: loss: forcing-binary = -0.00317774 (* 1 = -0.00317774 loss)
I0107 19:24:36.696565 23945 solver.cpp:331] Optimization Done.
I0107 19:24:36.696570 23945 caffe.cpp:214] Optimization Done.
```

The training process takes roughly 2~3 hours on a desktop with Titian X GPU. You will finally get your model named `SSDH48_iter_xxxxxx.caffemodel` under folder `/examples/SSDH/`

To use the model, modify the `model_file` in `demo.m` to link to your model:

```
    model_file = './YOUR/MODEL/PATH/filename.caffemodel';
```

Launch matlab, run `demo.m` and enjoy!
    
    >> demo

## Train SSDH on another dataset

It should be easy to train the model using another dataset as long as that dataset has label annotations.
 
  0. Convert your training/test set into leveldb/lmdb format using `create_imagenet.sh`.
  0. Modify the `source` in `/example/SSDH/train_val.prototxt` to link to your training/test set.
  0. Run `./examples/SSDH/train.sh`, and start training on your dataset.


## Resources

**Note**: This documentation may contain links to third party websites, which are provided for your convenience only. Third party websites may be subject to the third party’s terms, conditions, and privacy statements.

If `./prepare.sh` fails to download data, you may manually download the resouces from:

0. 48-bit SSDH model: [MEGA](https://mega.nz/#!kJ1jwDpJ!X4dVUeWJ7Eqg9L8bhJaGbr9l5-HS3ccudbjIjIbYNpk), [DropBox](https://www.dropbox.com/s/6iqyz1mdhadhzbu/SSDH48_iter_50000.caffemodel?dl=0), [BaiduYun](http://pan.baidu.com/s/1nurCaJR)

0. CIFAR10 dataset (jpg format): [MEGA](https://mega.nz/#!RENV1bhZ!x0uFnAkqUSTJzKr6HzeeNV9mtDjlgQ0x6ZaXfpxbJkw), [DropBox](https://www.dropbox.com/s/f7q3bbgvat2q1u2/cifar10-dataset.zip?dl=0), [BaiduYun](http://pan.baidu.com/s/1pKsSK7h)

0. AlexNet pretrained networks: [MEGA](https://mega.nz/#!UZ0VGIYB!y2crhbo89S9hYLv5TyHLXXB5Sus8ZkpUzTNkeUPkfU4), [DropBox](https://www.dropbox.com/s/nlggnj47xxdmwkb/bvlc_reference_caffenet.caffemodel?dl=0), [BaiduYun](http://pan.baidu.com/s/1qWRMy4G)


## Contact

Please feel free to leave suggestions or comments to Kevin Lin (kevinlin311.tw@iis.sinica.edu.tw), Huei-Fang Yang (hfyang@citi.sinica.edu.tw) or Chu-Song Chen (song@iis.sinica.edu.tw)



