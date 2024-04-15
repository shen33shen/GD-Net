
## Usage
### Environment
```python
pip install -r requirements.txt
```
```python
# Download the pretrained model and edit [sr]_[sr3]_[resolution option].json about "resume_state":
"resume_state": [your pretrained model's path]
```

### Data Prepare

#### New Start

```python
python data/prepare_data.py  --path [dataset root]  --out [output root] --size 64,128 -l
```

then you need to change the datasets config to your data path and image resolution: 

```json
"datasets": {
    "train": {
        "dataroot": "dataset/train_64_128", // [output root] in prepare.py script
        "l_resolution": 64, // low resolution need to super_resolution
        "r_resolution": 128, // high resolution
        "datatype": "lmdb", //lmdb or img, path of img files
    },
    "val": {
        "dataroot": "dataset/val_64_128", // [output root] in prepare.py script
    }
},
```

#### Own Data

You also can use your image data by following steps, and we have some examples in dataset folder.

At first, you should organize the images layout like this, this step can be finished by `data/prepare_data.py` automatically:

```shell
# set the high/low resolution images, bicubic interpolation images path 
dataset
├── hr_128 # it's same with sr_64_128 directory if you don't have ground-truth images.
├── lr_64 # vinilla low resolution images
└── sr_64_128 # images ready to super resolution
```

```python
# super resolution from 64 to 128
python data/prepare_data.py  --path [dataset root]  --out train --size 64,128 -l
```

*Note: Above script can be used whether you have the vanilla high-resolution images or not.*

then you need to change the dataset config to your data path and image resolution: 

```json
"datasets": {
    "train|val": { // train and validation part
        "dataroot": "dataset",
        "l_resolution": 64, // low resolution need to super_resolution
        "r_resolution": 128, // high resolution
        "datatype": "img", //lmdb or img, path of img files
    }
},
```

### Training/Resume Training

```python
# Use sr.py and sample.py to train the super resolution task and unconditional generation task, respectively.
# Edit json files to adjust network structure and hyperparameters
python sr.py -p train -c config/sr_64_128.json
```

### Test/Evaluation

```python
# Edit json to add pretrain model path and run the evaluation 
python sr.py -p val -c config/sr_64_128.json

# Quantitative evaluation alone using SSIM/PSNR metrics on given result root
python eval.py -p [result root]
```

### Inference Alone

Set the  image path like steps in `Own Data`, then run the script:

```python
# run the script
python infer.py -c [config file]
```


## Acknowledgements

Our work is based on the following theoretical works:

- [Denoising Diffusion Probabilistic Models](https://arxiv.org/pdf/2006.11239.pdf)
- [Image Super-Resolution via Iterative Refinement](https://arxiv.org/pdf/2104.07636.pdf)
- [WaveGrad: Estimating Gradients for Waveform Generation](https://arxiv.org/abs/2009.00713)
- [Large Scale GAN Training for High Fidelity Natural Image Synthesis](https://arxiv.org/abs/1809.11096)

Furthermore, we are benefitting a lot from the following projects:

- https://github.com/bhushan23/BIG-GAN
- https://github.com/lmnt-com/wavegrad
- https://github.com/rosinality/denoising-diffusion-pytorch
- https://github.com/lucidrains/denoising-diffusion-pytorch
- https://github.com/hejingwenhejingwen/AdaFM
