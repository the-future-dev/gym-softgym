# SoftGym
<a href="https://sites.google.com/view/softgym/home">SoftGym</a> is a set of benchmark environments for deformable object manipulation including tasks involving fluid, cloth and rope. It is built on top of the Nvidia FleX simulator and has standard Gym API for interaction with RL agents. A number of RL algorithms benchmarked on SoftGym can be found in <a href="https://github.com/Xingyu-Lin/softagent">SoftAgent</a>


## Installation

0. Prerequisites:
- Install Docker: [docker ce](https://docs.docker.com/engine/install/ubuntu/)
- Install [Anaconda](https://www.anaconda.com/distribution/)

1. Install dependencies:
```
sudo apt-get install build-essential libgl1-mesa-dev freeglut3-dev libglfw3 libgles2-mesa-dev
```

2. Conda environment
   Create a conda environment and activate it:
   ```
   conda env create -f environment.yml
   conda activate softgym
   ```

3. Compile PyFleX:
    Pyflex is a C++ library used in the repository to simulate deformable objects.

    First, generate the variables:    
    ```
    export PATH_TO_SOFTGYM=~/your_path_to_softgym/
    ```
    ```
    export PATH_TO_CONDA=~/your_path_to_conda/ # ex: /home/user/miniconda3/
    ```

    Then let us use docker to compile PyFleX:
    ```
    sudo docker pull xingyu/softgym:latest

    sudo docker run \
        -v $PATH_TO_SOFTGYM:/workspace/softgym \
        -v $PATH_TO_CONDA:$PATH_TO_CONDA \
        -v /tmp/.X11-unix:/tmp/.X11-unix \
        -e DISPLAY=$DISPLAY \
        -e QT_X11_NO_MITSHM=1 \
        -it xingyu/softgym:latest bash
    ```
    
    Now inside the docker container:
    ```
    cd /workspace/softgym
    . ./prepare_1.0.sh
    . ./compile_1.0.sh
    ```

    Finally you can exit the docker container by typing `exit`.

    Let us source pyflex and softgym:
    ```    
    export PYFLEXROOT=${PWD}/PyFlex
    export PYTHONPATH=${PYFLEXROOT}/bindings/build:$PYTHONPATH
    export LD_LIBRARY_PATH=${PYFLEXROOT}/external/SDL2-2.0.4/lib/x64:$LD_LIBRARY_PATH
    ```

    Optionally, you can add the above lines to your `~/.bashrc` file.

    Now you can test the installation by running:
    ```
    python examples/random_env.py --env_name ClothDrop
    ```

## SoftGym Environments
|Image|Name|Description|
|----------|:-------------|:-------------|
|![Gif](./examples/ClothDrop.gif)|[DropCloth](softgym/envs/cloth_drop.py) | Lay a piece of cloth in the air flat on the floor|
|![Gif](./examples/ClothFold.gif)|[FoldCloth](softgym/envs/cloth_fold.py) | Fold a piece of flattened cloth in half|
|![Gif](./examples/ClothFlatten.gif)|[SpreadCloth](softgym/envs/cloth_flatten.py)| Spread a crumpled cloth on the floor|
|![Gif](./examples/PourWater.gif)|[PourWater](softgym/envs/pour_water.py)| Pour a cup of water into a target cup |
|![Gif](./examples/PassWater.gif)|[TransportWater](softgym/envs/pass_water.py)| Move a cup of water to a target position as fast as possible without spilling out the water|
|![Gif](./examples/RopeFlatten.gif)|[StraightenRope](softgym/envs/rope_flatten.py)| Straighten a rope starting from a random configuration|
|![Gif](./examples/PourWaterAmount.gif)|[PourWaterAmount](softgym/envs/pour_water_amount.py)| This task is similar to PourWater but requires a specific amount of water poured into the target cup. The required water level is indicated by a red line.|
|![Gif](./examples/ClothFoldCrumpled.gif)|[FoldCrumpledCloth](softgym/envs/cloth_fold_crumpled.py)| This task is similar to FoldCloth but the cloth is initially crumpled| 
|![Gif](./examples/ClothFoldDrop.gif)|[DropFoldCloth](softgym/envs/cloth_fold_drop.py)| This task has the same initial state as DropCloth but requires the agent to fold the cloth instead of just laying it on the ground|
|![Gif](./examples/RopeConfiguration.gif)|[RopeConfiguration](softgym/envs/rope_configuration.py)| This task is similar to StraightenCloth but the agent needs to manipulate the rope into a specific configuration from different starting locations.|

To have a quick view of different tasks listed in the paper (with random actions), run the following commands:
For SoftGym-Medium:
- TransportWater: `python examples/random_env.py --env_name PassWater`
- PourWater: `python examples/random_env.py --env_name PourWater`
- StraightenRope: `python examples/random_env.py --env_name RopeFlatten`
- SpreadCloth: `python examples/random_env.py --env_name ClothFlatten`
- FoldCloth: `python examples/random_env.py --env_name ClothFold`
- DropCloth: `python examples/random_env.py --env_name ClothDrop`

For SoftGym-Hard:
- PourWaterAmount: `python examples/random_env.py --env_name PourWaterAmount`
- FoldCrumpledCloth: `python examples/random_env.py --env_name ClothFoldCrumpled`
- DropFoldCloth: `python examples/random_env.py --env_name ClothFoldDrop`
- RopeConfiguration:
  First download the rope configuration file using [this link](https://drive.google.com/file/d/1f3FK_7gwnJLVm3VaSacvYS7o-19-XPrr/view?usp=sharing) then run `python examples/random_env.py --env_name RopeConfiguration`

Turn on the `--headless` option if you are running on a cluster machine that does not have a display environment. Otherwise you will get segmentation issues. Please refer to `softgym/registered_env.py` for the default parameters and source code files for each of these environments.

## Cite
If you find this codebase useful in your research, please consider citing:
```
@inproceedings{corl2020softgym,
 title={SoftGym: Benchmarking Deep Reinforcement Learning for Deformable Object Manipulation},
 author={Lin, Xingyu and Wang, Yufei and Olkin, Jake and Held, David},
 booktitle={Conference on Robot Learning},
 year={2020}
}
```

## References
- NVIDIA FleX - 1.2.0: https://github.com/NVIDIAGameWorks/FleX
- Our python interface builds on top of PyFleX: https://github.com/YunzhuLi/PyFleX
- If you run into problems setting up SoftGym, Daniel Seita wrote a nice blog that may help you get started on SoftGym: https://danieltakeshi.github.io/2021/02/20/softgym/
