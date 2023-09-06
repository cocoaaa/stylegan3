1. set up conda environment:
  - make sure condarc is symlinked to my Dotfile/.condarc
      - `ln -s $HOME/Dotfiles/.condarc $HOME/.condarc`
  - update conda in base env
      - `conda update conda` or `conda update -n base conda`
  - `conda env create -f environment.yml`
  - `conda activate stylegan3`

  - #imp if torch._six import error: try installing these versions:
    ```shell
    torch==1.8.1  
    torchvision=0.9.1
    ```

2. sample using their coda and released ckpt (accessible from the url, which is nice 

## Generate a single image at given random state/seed 
```shell

conda activate taming

#here
model_name=stylegan2-ffhq256
run_id=test #20230904-190201
ckpt_fp=https://api.ngc.nvidia.com/v2/models/nvidia/research/stylegan2/versions/1/files/stylegan2-ffhq-256x256.pkl
# outdir=./samples-$model_name-$run_id
outdir=./Generated-Images-$model_name-$run_id
echo $outdir

CUDA_VISIBLE_DEVICES=0 python gen_images.py --outdir=$outdir --trunc=1 --seeds=2  --network=$ckpt_fp

```

## To generate multiple images, pass seeds in range (e.g., `--seeds=600-605`) like below:
```shell
export nsamples=1
export seed_start=0
export seed_last=$((seed_start + nsamples-1))
ckpt_fp=https://api.ngc.nvidia.com/v2/models/nvidia/research/stylegan2/versions/1/files/stylegan2-ffhq-256x256.pkl
echo "seed from: $seed_start to $seed_last"

python gen_images.py --outdir=$outdir --trunc=1 "--seeds=$seed_start-$seed_last" \
    --network=$ckpt_fp 
```

## Run: stylegan2 trained on ffhq256; sample total 50k images 
```shell
#loging to a compute node
# jid=410924
# srun --jobid=$jid  --pty /usr/bin/bash
# conda activate taming
#sampling
export nsamples=50000 
export run_id=20230904-201921
export seed_start=0
export seed_last=$((seed_start + nsamples-1))
ckpt_fp=https://api.ngc.nvidia.com/v2/models/nvidia/research/stylegan2/versions/1/files/stylegan2-ffhq-256x256.pkl
outdir=./Generated-Images-$model_name-$run_id
echo $outdir


CUDA_VISIBLE_DEVICES=0 
nohup python gen_images.py --outdir=$outdir --trunc=1 "--seeds=$seed_start-$seed_last" \
    --network=$ckpt_fp &

```

### Progress:
- status: #running
- started: 20230904-202055
- pid: 8781
- gpu: 0
- outdir: /data/hayley-old/Github/GANs/stylegan3/Generated-Images-stylegan2-ffhq256-test



## Run: stylegan3, trained on ffhq256; sample total 50k
```shell
#loging to a compute node
# jid=410924
# srun --jobid=$jid  --pty /usr/bin/bash
# conda activate taming

#sampling
model_name=stylegan3-r-ffhqu-256
ckpt_fp=https://api.ngc.nvidia.com/v2/models/nvidia/research/stylegan3/versions/1/files/stylegan3-r-ffhqu-256x256.pkl
nsamples=50000 
run_id=20230905-175443
seed_start=0
seed_last=$((seed_start + nsamples-1))
outdir=./Generated-Images-$model_name-$run_id
echo $outdir


CUDA_VISIBLE_DEVICES=0
nohup python gen_images.py --outdir=$outdir --trunc=1 "--seeds=$seed_start-$seed_last"  --network=$ckpt_fp &

```

### Progress:
- status: #running
- started: 
- pid: 24580
- gpu: 0
- outdir: /data/hayley-old/Github/GANs/stylegan3/Generated-Images-stylegan3-r-ffhqu-256-????