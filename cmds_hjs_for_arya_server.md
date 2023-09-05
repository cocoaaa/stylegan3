in this repo:
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
bc i don't need to download the file and move to this repo manually.)

#here!!!
Run below: will generate a **single** number of images to outdir:



## Generate a single image at given random state/seed 
```shell
export outdir=samples_stylegan2_celebhq25

python gen_images.py --outdir=$outdir --trunc=1 --seeds=2 \
    --network=https://api.ngc.nvidia.com/v2/models/nvidia/research/stylegan2/versions/1/files/stylegan2-celebahq-256x256.pkl

```

## To generate multiple images, pass seeds in range (e.g., `--seeds=600-605`) like below:
```shell
export nsamples=1
export seed_start=0
export seed_last=$((seed_start + nsamples-1))
echo "seed from: $seed_start to $seed_last"

python gen_images.py --outdir=$outdir --trunc=1 "--seeds=$seed_start-$seed_last" \
    --network=https://api.ngc.nvidia.com/v2/models/nvidia/research/stylegan2/versions/1/files/stylegan2-celebahq-256x256.pkl
``````
Thankfully, I can run the code using my `test` conda env. 

## Log of commands I ran:
export batch_size=20000 
export outdir=samples_stylegan2_celebhq25


### run0: start=0, end=20k-1
export run_id=0
export seed_start=0
export seed_last=$((seed_start + batch_size-1))

nohup python gen_images.py --outdir=$outdir --trunc=1 "--seeds=$seed_start-$seed_last" \
    --network=https://api.ngc.nvidia.com/v2/models/nvidia/research/stylegan2/versions/1/files/stylegan2-celebahq-256x256.pkl &


#### Progress:
- started: 20230227-191811
- pid: 32392

### run1: seed_start=20k ~ end=40k-1
export run_id=1
export seed_start=$((run_id*batch_size))
export seed_last=$((seed_start + batch_size-1))
export outdir=samples_stylegan2_celebhq25
nohup python gen_images.py --outdir=$outdir --trunc=1 "--seeds=$seed_start-$seed_last" \
    --network=https://api.ngc.nvidia.com/v2/models/nvidia/research/stylegan2/versions/1/files/stylegan2-celebahq-256x256.pkl \
    > "log$run_id.out" 2>&1 &

#### Progress:
- started: 20230227-192050 
- pid: 3174

### run2: seed start: 40k ~ end=60k-1
export run_id=2
export seed_start=$((run_id*batch_size))
export seed_last=$((seed_start + batch_size-1))
export outdir=samples_stylegan2_celebhq25
nohup python gen_images.py --outdir=$outdir --trunc=1 "--seeds=$seed_start-$seed_last" \
    --network=https://api.ngc.nvidia.com/v2/models/nvidia/research/stylegan2/versions/1/files/stylegan2-celebahq-256x256.pkl \
   > "log$run_id.out" 2>&1 &

#### Progress:
- started: 20230227-192050 
-[1] 6148 
 

### run3: start=60k ~ end=80k-1
export run_id=3
export seed_start=$((run_id*batch_size))
export seed_last=$((seed_start + batch_size-1))
export outdir=samples_stylegan2_celebhq25

nohup python gen_images.py --outdir=$outdir --trunc=1 "--seeds=$seed_start-$seed_last" \
    --network=https://api.ngc.nvidia.com/v2/models/nvidia/research/stylegan2/versions/1/files/stylegan2-celebahq-256x256.pkl \
    > "log$run_id.out" 2>&1 &

#### Progress:
- started: 20230227-192418
-[1] 7003 


### run4


export run_id=4
export seed_start=$((run_id*batch_size))
export seed_last=$((seed_start + batch_size-1))
export outdir=samples_stylegan2_celebhq25

nohup python gen_images.py --outdir$outdir --trunc=1 "--seeds=$seed_start-$seed_last" \
    --network=https://api.ngc.nvidia.com/v2/models/nvidia/research/stylegan2/versions/1/files/stylegan2-celebahq-256x256.pkl \
    > "log$run_id.out" 2>&1 &
#### Progress:
- started: 20230227-192613
-[1] 7969 



## todo: 
- [ ] change the image save filename to be of totdal 7 digits (currently 4, e.g., img0001.png) 
      b.c. i'm going to sample > 100k

- [ ] add two more arguments to `gen_images.py` to set `seed_start` and `n_samples`

# Generat sampes fomffhq256 pretrained network (stylegan2, treaind on ffhq256)

## Log of cmmands I ran:
jid=407645  
gpu=4
srun --jobid=$jid  --pty /usr/bin/bash
conda activate torch-cuda118
WORKDIR=/nas/vista-ssd01/users/haejinso/Github/GANs/stylegan3
cd $WORKDIR


export batch_size=5000 
export outdir=./samples_stylegan2_ffhq256


### run0: start=0, end=20k-1
export run_id=stylegan2-ffhq256-run0
export seed_start=0
export seed_last=$((seed_start + batch_size-1))


nohup python gen_images.py --outdir $outdir --trunc 1 --seeds 0-5000 \
--network=https://api.ngc.nvidia.com/v2/models/nvidia/research/stylegan2/versions/1/files/stylegan2-ffhq-256x256.pkl \
> "log-$run_id.out" 2>&1  &


#### Progress:
- started: 20230828-231445
- status: running
- pid: [1] 18861
























# alt: NEVER DO THIS!!! THIS messes up the loaded kernel module version vs. installed nvidia driver mismatch... 
--- if it happens, to resolve: and I need to unload the modules and call nvidia-smi: See this https://stackoverflow.com/a/45319156/20405542
- Build the stylegan3:latest image
docker build --tag stylegan3 .

- Run the gen_images.py script using Docker:
sudo docker run --gpus all -it --rm --user $(id -u):$(id -g) \
    -v `pwd`:/scratch --workdir /scratch -e HOME=/scratch \
    stylegan3 \
    python gen_images.py --outdir=out --trunc=1 --seeds=2 \
    --network=https://api.ngc.nvidia.com/v2/models/nvidia/research/stylegan2/versions/1/files/stylegan2-celebahq-256x256.pkl


sudo nvidia-docker run -it --rm --user $(id -u):$(id -g) \
    -v `pwd`:/scratch --workdir /scratch -e HOME=/scratch \
    stylegan3 \
    python gen_images.py --outdir=out --trunc=1 --seeds=2 \
    --network=https://api.ngc.nvidia.com/v2/models/nvidia/research/stylegan2/versions/1/files/stylegan2-celebahq-256x256.pkl