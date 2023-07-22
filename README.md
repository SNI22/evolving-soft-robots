# Soft Robots Learn to Crawl: Jointly Optimizing Design and Control with Sim-to-Real Transfer

![](frames.png)

This repository contains code for the paper [Sim-to-Real Transfer of Co-Optimized Soft Robot Crawlers]().

It provides the code for model order reduction, co-optimiztion of design and control, and testing the optimized design-control pairs in simulation.


## Installation

Our code relies on [Docker](https://www.docker.com/get-started) and the docker wrapper [cpk](https://github.com/afdaniele/cpk) to manage its dependencies.

To use this codebase, follow these installation steps:

1. Intstall Docker.
2. Install cpk: `python -m pip install cpk`
3. Clone this repository.
4. From the top level directory, run `cpk build` to build the docker container.

Additionally, this codebase uses [Weights and Biases](https://wandb.ai/site) for logging and visualization, which is free to use for academics.
Running a training job will prompt you to login, create an account, or not visualize the results.
If you wish to avoid manual logins, place your wandb `.netrc` file in the `assets/wandb_info` directory.

If you only want to visiualize the pretrained model, then creating a wandb account is not required.

## Testing the Learned and Baseline Models

We provide code to visualize a pretrained model and our baseline.

- To visualize the pretrained model, run: `cpk run -M -f -X -L viz_pretrained`
- To visualize the baseline, run: `cpk run -M -f -X -L viz_baseline`

## Running a Co-optimization Experiment

The training code is run in two pieces: a training script and an evaluation script which runs concurrently.
Training is CPU intensive. We ran this experiment with 96 paralell SOFA environments for training on a 32-core [AMD EPYC 7502](https://www.amd.com/en/products/cpu/amd-epyc-7502).
You can adjust the cpu load by editing the config file `configs/coopt.yaml`.

- To launch a training job, run: `cpk run -M -f -n train -L train`
- To launch the eval job, run: `cpk run -M -f -n eval -L eval`

The eval job will search for checkpoints in the `exps` directory and generate videos and evaluation performance.
All logs and videos will be visible on wandb in a newly created evolving-soft-robots project.

### Running Experiments with Domain Randomization

- To launch the training job with friction randomization, edit the python2 file `evolving-soft-robots/packages/python2/scene/emptyscene.py`, uncomment line 31,32 to enable domain randomization on the range of friction you specified on line 32.
- To save the actual friction trained in the complete run. First edit line 11 of `evolving-soft-robots/packages/python2/scene/emptyscene.py` to save the friction to the file name you wanted. Uncomment line 33 to enable the feature.
- Although it is hard to specify a seed when using such method to enable friction randomization in python2. By using python2 built in I/O features and saved friction data, repeatability of the friction randomization can be ensured.
## Running a non co-optimized experiment (Training the contoller only using)

-  This framework now provides capability to train controllers on the L1 or the baseline robot. Simply change the `sofa_make_env.design_space` entry in `/configs/coopt.yaml` to `DiskWithLegsL1DiscreteOpenLoopDesignSpace` `DiskWithLegsBaselineDiscreteOpenLoopDesignSpace` respectively. 
-  To return to the full co-optimization scheme, set `sofa_make_env.design_space` entry in `/configs/coopt.yaml` to `DiskWithLegsSixLegsDiscreteOpenLoopDesignSpace`
## Performing Model Order Reduction

This repository contains the reduced order models used in this paper, as well as code to create your own reductions.

- To launch a reduction, run: `python scripts/launch_reduction_block.py`

This will launch a grid search over the two tolerances for the reduction.
The reduction takes several hours to complete and requires large amount of RAM (~200GB).
