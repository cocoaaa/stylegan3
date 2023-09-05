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
outdir=Generated-Images-$model_name-$run_id
echo $outdir

python gen_images.py --outdir=$outdir --trunc=1 --seeds=2 \
    --network=$ckpt_fp

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

## Run0: start=0, end=20k-1
```shell
#loging to a compute node
jid=410924
srun --jobid=$jid  --pty /usr/bin/bash
conda activate taming

#sampling
export batch_size=20000 
export run_id=0
export seed_start=0
export seed_last=$((seed_start + batch_size-1))

nohup python gen_images.py --outdir=$outdir --trunc=1 "--seeds=$seed_start-$seed_last" \
    --network=$ckpt_fp &

```

### Progress:
- started: 
- pid: