<div align="center">

# COHORTNEY

<a href="https://www.python.org/"><img alt="Python" src="https://img.shields.io/badge/-Python 3.7+-blue?style=for-the-badge&logo=python&logoColor=white"></a>
<a href="https://pytorch.org/get-started/locally/"><img alt="PyTorch" src="https://img.shields.io/badge/-PyTorch 1.8+-ee4c2c?style=for-the-badge&logo=pytorch&logoColor=white"></a>
<a href="https://pytorchlightning.ai/"><img alt="Lightning" src="https://img.shields.io/badge/-Lightning 1.5+-792ee5?style=for-the-badge&logo=pytorchlightning&logoColor=white"></a>
<a href="https://hydra.cc/"><img alt="Config: hydra" src="https://img.shields.io/badge/config-hydra 1.1-89b8cd?style=for-the-badge&labelColor=gray"></a>
<a href="https://black.readthedocs.io/en/stable/"><img alt="Code style: black" src="https://img.shields.io/badge/code%20style-black-black.svg?style=for-the-badge&labelColor=gray"></a>
[![Paper](http://img.shields.io/badge/paper-arxiv.1001.2234-B31B1B.svg)](https://www.nature.com/articles/nature14539)
[![Conference](http://img.shields.io/badge/AnyConference-year-4b44ce.svg)](https://papers.nips.cc/paper/2020)
</div>
<br>

## Description  
In this project, the problem of return time prediction
and event time prediction is considered. To solve this problem, 
four models were considered: Continuous Time CNN, Transformer Hawkes, WaveNet and 
WaveNet with intensity interpolation. 
Here is a convenient pipeline for working with them and 
launching them. To get acquainted with the structure of 
the code and the features, you can look at the 
information provided below.
## The main logical parts of our project 

- **Predefined Structure**: clean and scalable so that work can easily be extended and replicated | [#Project Structure](#project-structure)
- **Rapid Experimentation**: thanks to automating pipeline with config files and hydra command line superpowers | [#Your Superpowers](#your-superpowers)
- **Little Boilerplate**: so pipeline can be easily modified | [#How It Works](#how-it-works)
- **Main Configuration**: main config file specifies default training configuration | [#Main Project Configuration](#main-project-configuration)
- **Experiment Configurations**: can be composed out of smaller configs and override chosen hyperparameters | [#Experiment Configuration](#experiment-configuration)
- **Workflow**: comes down to 4 simple steps | [#Workflow](#workflow)
- **Experiment Tracking**: many logging frameworks can be easily integrated, like Tensorboard, MLFlow or W&B | [#Experiment Tracking](#experiment-tracking)
- **Logs**: all logs (checkpoints, data from loggers, hparams, etc.) are stored in a convenient folder structure imposed by Hydra | [#Logs](#logs)
- **Hyperparameter Search**: made easier with Hydra built-in plugins like [Optuna Sweeper](https://hydra.cc/docs/next/plugins/optuna_sweeper) | [#Hyperparameter Search](#hyperparameter-search)
 
<br>

## Project Structure

The directory structure of new project looks like this:

```
├── configs                   <- Hydra configuration files
│   ├── callbacks                <- Callbacks configs
│   ├── datamodule               <- Datamodule configs
│   ├── debug                    <- Debugging configs
│   ├── experiment               <- Experiment configs
│   ├── hparams_search           <- Hyperparameter search configs
│   ├── local                    <- Local configs
│   ├── log_dir                  <- Logging directory configs
│   ├── logger                   <- Logger configs
│   ├── model                    <- Model configs
│   ├── trainer                  <- Trainer configs
│         │     
│         ├── test.yaml             <- Main config for testing
│         ├── train.yaml            <- Main config for training
│         ├── ...
├── data                   <- Project data
│
├── logs                   <- Logs generated by Hydra and PyTorch Lightning loggers
│
├── notebooks              <- Jupyter notebooks. Naming convention is a number (for ordering),
│                             the creator's initials, and a short `-` delimited description,
│                             e.g. `1.0-jqp-initial-data-exploration.ipynb`.
│
├── scripts                <- Shell scripts
│
├── src                    <- Source code
│   ├── datamodules              <- Lightning datamodules
│   ├── metrics                  <- Metrics for different model
│   ├── models                   <- Lightning models
│   ├── utils                    <- Utility scripts
│   ├── vendor                   <- Third party code that cannot be installed using PIP/Conda
│   │
│   ├── testing_pipeline.py
│   └── training_pipeline.py
│
├── tests                  <- Tests of any kind
│   ├── helpers                  <- A couple of testing utilities
│   ├── shell                    <- Shell/command based tests
│   └── unit                     <- Unit tests
│
├── test.py               <- Run testing
├── train.py              <- Run training
│
├── .env.example              <- Template of the file for storing private environment variables
├── .gitignore                <- List of files/folders ignored by git
├── .pre-commit-config.yaml   <- Configuration of pre-commit hooks for code formatting
├── requirements.txt          <- File for installing python dependencies
├── setup.cfg                 <- Configuration of linters and pytest
└── README.md
```

<br>

 
 

## Usage

Here is the code to run the code, 
the basic commands for this. 
Also, to understand the structure of the code, 
you should look at the tabs and notes below.

Install dependencies

```bash
# clone project
git clone https://github.com/YourGithubName/your-repo-name
cd your-repo-name

# [OPTIONAL] create conda environment
conda create -n myenv python=3.8
conda activate myenv

# install pytorch according to instructions
# https://pytorch.org/get-started/

# install requirements
pip install -r requirements.txt
```

Train model with default configuration

```bash
# train on CPU
python train.py trainer.gpus=0

# train on GPU
python train.py trainer.gpus=1
```

Train model with chosen experiment configuration from [configs/experiment/](configs/experiment/)

```bash
python train.py experiment=experiment_name.yaml
```

You can override any parameter from command line like this

```bash
python train.py trainer.max_epochs=20 datamodule.batch_size=64
``` 

## Metrics and comparison table
## Details
Here are the main points that may be needed when starting or developing code.

<details>
<summary><b>Override any config parameter from command line</b></summary>

> Hydra allows you to easily overwrite any parameter defined in your config.

```bash
python train.py trainer.max_epochs=20 model.lr=1e-4
```

> You can also add new parameters with `+` sign.

```bash
python train.py +model.new_param="uwu"
```

</details>

<details>
<summary><b>Train on CPU, GPU, multi-GPU and TPU</b></summary>

> PyTorch Lightning makes it easy to train your models on different hardware.

```bash
# train on CPU
python train.py trainer.gpus=0

# train on 1 GPU
python train.py trainer.gpus=1

# train on TPU
python train.py +trainer.tpu_cores=8

# train with DDP (Distributed Data Parallel) (4 GPUs)
python train.py trainer.gpus=4 +trainer.strategy=ddp

# train with DDP (Distributed Data Parallel) (8 GPUs, 2 nodes)
python train.py trainer.gpus=4 +trainer.num_nodes=2 +trainer.strategy=ddp
```

</details>

<details>
<summary><b>Train with mixed precision</b></summary>

```bash
# train with pytorch native automatic mixed precision (AMP)
python train.py trainer.gpus=1 +trainer.precision=16
```

</details>

<!-- deepspeed support still in beta
<details>
<summary><b>Optimize large scale models on multiple GPUs with Deepspeed</b></summary>

```bash
python train.py +trainer.
```

</details>
 -->

<details>
<summary><b>Train model with any logger available in PyTorch Lightning, like Weights&Biases or Tensorboard</b></summary>

> PyTorch Lightning provides convenient integrations with most popular logging frameworks, like Tensorboard, Neptune or simple csv files. Read more [here](#experiment-tracking). Using wandb requires you to [setup account](https://www.wandb.com/) first. After that just complete the config as below.<br> > **Click [here](https://wandb.ai/hobglob/template-dashboard/) to see example wandb dashboard generated with this template.**

```bash
# set project and entity names in `configs/logger/wandb`
wandb:
  project: "your_project_name"
  entity: "your_wandb_team_name"
```

```bash
# train model with Weights&Biases (link to wandb dashboard should appear in the terminal)
python train.py logger=wandb
```

</details>

<details>
<summary><b>Train model with chosen experiment config</b></summary>

> Experiment configurations are placed in [configs/experiment/](configs/experiment/).

```bash
python train.py experiment=example
```

</details>

<details>
<summary><b>Attach some callbacks to run</b></summary>

> Callbacks can be used for things such as as model checkpointing, early stopping and [many more](https://pytorch-lightning.readthedocs.io/en/latest/extensions/callbacks.html#built-in-callbacks).<br>
> Callbacks configurations are placed in [configs/callbacks/](configs/callbacks/).

```bash
python train.py callbacks=default
```

</details>

<details>
<summary><b>Use different tricks available in Pytorch Lightning</b></summary>

> PyTorch Lightning provides about [40+ useful trainer flags](https://pytorch-lightning.readthedocs.io/en/latest/common/trainer.html#trainer-flags).

```yaml
# gradient clipping may be enabled to avoid exploding gradients
python train.py +trainer.gradient_clip_val=0.5

# stochastic weight averaging can make your models generalize better
python train.py +trainer.stochastic_weight_avg=true

# run validation loop 4 times during a training epoch
python train.py +trainer.val_check_interval=0.25

# accumulate gradients
python train.py +trainer.accumulate_grad_batches=10

# terminate training after 12 hours
python train.py +trainer.max_time="00:12:00:00"
```

</details>

<details>
<summary><b>Easily debug</b></summary>

> Visit [configs/debug/](configs/debug/) for different debugging configs.

```bash
# runs 1 epoch in default debugging mode
# changes logging directory to `logs/debugs/...`
# sets level of all command line loggers to 'DEBUG'
# enables extra trainer flags like tracking gradient norm
# enforces debug-friendly configuration
python train.py debug=default

# runs test epoch without training
python train.py debug=test_only

# run 1 train, val and test loop, using only 1 batch
python train.py +trainer.fast_dev_run=true

# raise exception if there are any numerical anomalies in tensors, like NaN or +/-inf
python train.py +trainer.detect_anomaly=true

# print execution time profiling after training ends
python train.py +trainer.profiler="simple"

# try overfitting to 1 batch
python train.py +trainer.overfit_batches=1 trainer.max_epochs=20

# use only 20% of the data
python train.py +trainer.limit_train_batches=0.2 \
+trainer.limit_val_batches=0.2 +trainer.limit_test_batches=0.2

# log second gradient norm of the model
python train.py +trainer.track_grad_norm=2
```

</details>

<details>
<summary><b>Resume training from checkpoint</b></summary>

> Checkpoint can be either path or URL.

```yaml
python train.py trainer.resume_from_checkpoint="/path/to/ckpt/name.ckpt"
```

> ⚠️ Currently loading ckpt in Lightning doesn't resume logger experiment, but it will be supported in future Lightning release.

</details>

<details>
<summary><b>Execute evaluation for a given checkpoint</b></summary>

> Checkpoint can be either path or URL.

```yaml
python test.py ckpt_path="/path/to/ckpt/name.ckpt"
```

</details>

<details>
<summary><b>Create a sweep over hyperparameters</b></summary>

```bash
# this will run 6 experiments one after the other,
# each with different combination of batch_size and learning rate
python train.py -m datamodule.batch_size=32,64,128 model.lr=0.001,0.0005
```

> ⚠️ This sweep is not failure resistant (if one job crashes than the whole sweep crashes).

</details>

<details>
<summary><b>Create a sweep over hyperparameters with Optuna</b></summary>

> Using [Optuna Sweeper](https://hydra.cc/docs/next/plugins/optuna_sweeper) plugin doesn't require you to code any boilerplate into your pipeline, everything is defined in a [single config file](configs/hparams_search/mnist_optuna.yaml)!

```bash
# this will run hyperparameter search defined in `configs/hparams_search/mnist_optuna.yaml`
# over chosen experiment config
python train.py -m hparams_search=mnist_optuna experiment=example_simple
```

> ⚠️ Currently this sweep is not failure resistant (if one job crashes than the whole sweep crashes). Might be supported in future Hydra release.

</details>

<details>
<summary><b>Execute all experiments from folder</b></summary>

> Hydra provides special syntax for controlling behavior of multiruns. Learn more [here](https://hydra.cc/docs/next/tutorials/basic/running_your_app/multi-run). The command below executes all experiments from folder [configs/experiment/](configs/experiment/).

```bash
python train.py -m 'experiment=glob(*)'
```

</details>

<details>
<summary><b>Execute sweep on a remote AWS cluster</b></summary>

> This should be achievable with simple config using [Ray AWS launcher for Hydra](https://hydra.cc/docs/next/plugins/ray_launcher). Example is not yet implemented in this template.

</details>

<!-- <details>
<summary><b>Execute sweep on a SLURM cluster</b></summary>

> This should be achievable with either [the right lightning trainer flags](https://pytorch-lightning.readthedocs.io/en/latest/clouds/cluster.html?highlight=SLURM#slurm-managed-cluster) or simple config using [Submitit launcher for Hydra](https://hydra.cc/docs/plugins/submitit_launcher). Example is not yet implemented in this template.

</details> -->

<details>
<summary><b>Use Hydra tab completion</b></summary>

> Hydra allows you to autocomplete config argument overrides in shell as you write them, by pressing `tab` key. Learn more [here](https://hydra.cc/docs/tutorials/basic/running_your_app/tab_completion).

</details>

<details>
<summary><b>Apply pre-commit hooks</b></summary>

> Apply pre-commit hooks to automatically format your code and configs, perform code analysis and remove output from jupyter notebooks. See [# Best Practices](#best-practices) for more.

```bash
pre-commit run -a
```

</details>

<br>

 

## Guide

### How To Get Started

- First, you should probably get familiar with [PyTorch Lightning](https://www.pytorchlightning.ai)
- Next, go through [Hydra quick start guide](https://hydra.cc/docs/intro/) and [basic Hydra tutorial](https://hydra.cc/docs/tutorials/basic/your_first_app/simple_cli/)

<br>

 

### Main Project Configuration

Location: [configs/train.yaml](configs/train.yaml) <br>
Main project config contains default training configuration.<br>
It determines how config is composed when simply executing command `python train.py`.<br>

<details>
<summary><b>Show main project config</b></summary>

```yaml
# specify here default training configuration
defaults:
  - _self_
  - datamodule: mnist.yaml
  - model: mnist.yaml
  - callbacks: default.yaml
  - logger: null # set logger here or use command line (e.g. `python train.py logger=tensorboard`)
  - trainer: default.yaml
  - log_dir: default.yaml

  # experiment configs allow for version control of specific configurations
  # e.g. best hyperparameters for each combination of model and datamodule
  - experiment: null

  # debugging config (enable through command line, e.g. `python train.py debug=default)
  - debug: null

  # config for hyperparameter optimization
  - hparams_search: null

  # optional local config for machine/user specific settings
  # it's optional since it doesn't need to exist and is excluded from version control
  - optional local: default.yaml

  # enable color logging
  - override hydra/hydra_logging: colorlog
  - override hydra/job_logging: colorlog

# path to original working directory
# hydra hijacks working directory by changing it to the new log directory
# https://hydra.cc/docs/next/tutorials/basic/running_your_app/working_directory
original_work_dir: ${hydra:runtime.cwd}

# path to folder with data
data_dir: ${original_work_dir}/data/

# pretty print config at the start of the run using Rich library
print_config: True

# disable python warnings if they annoy you
ignore_warnings: True

# set False to skip model training
train: True

# evaluate on test set, using best model weights achieved during training
# lightning chooses best weights based on the metric specified in checkpoint callback
test: True

# seed for random number generators in pytorch, numpy and python.random
seed: null

# default name for the experiment, determines logging folder path
# (you can overwrite this name in experiment configs)
name: "default"
```

</details>

<br>

### Experiment Configuration

Location: [configs/experiment](configs/experiment)<br>
Experiment configs allow you to overwrite parameters from main project configuration.<br>
For example, you can use them to version control best hyperparameters for each combination of model and dataset.

<details>
<summary><b>Show example experiment config</b></summary>

```yaml
# to execute this experiment run:
# python train.py experiment=example

defaults:
  - override /datamodule: mnist.yaml
  - override /model: mnist.yaml
  - override /callbacks: default.yaml
  - override /logger: null
  - override /trainer: default.yaml

# all parameters below will be merged with parameters from default configurations set above
# this allows you to overwrite only specified parameters

# name of the run determines folder name in logs
name: "simple_dense_net"

seed: 12345

trainer:
  min_epochs: 10
  max_epochs: 10
  gradient_clip_val: 0.5

model:
  lr: 0.002
  net:
    lin1_size: 128
    lin2_size: 256
    lin3_size: 64

datamodule:
  batch_size: 64

logger:
  wandb:
    tags: ["mnist", "${name}"]
```

</details>

<br>

### Local Configuration

Location: [configs/local](configs/local) <br>
Some configurations are user/machine/installation specific (e.g. configuration of local cluster, or harddrive paths on a specific machine). For such scenarios, a file `configs/local/default.yaml` can be created which is automatically loaded but not tracked by Git.

<details>
<summary><b>Show example local Slurm cluster config</b></summary>

```yaml
# @package _global_

defaults:
  - override /hydra/launcher@_here_: submitit_slurm

data_dir: /mnt/scratch/data/

hydra:
  launcher:
    timeout_min: 1440
    gpus_per_task: 1
    gres: gpu:1
  job:
    env_set:
      MY_VAR: /home/user/my/system/path
      MY_KEY: asdgjhawi8y23ihsghsueity23ihwd
```

</details>

<br>

### Workflow

1. Write your PyTorch Lightning module (see [models/mnist_module.py](src/models/mnist_module.py) for example)
2. Write your PyTorch Lightning datamodule (see [datamodules/mnist_datamodule.py](src/datamodules/mnist_datamodule.py) for example)
3. Write your experiment config, containing paths to your model and datamodule
4. Run training with chosen experiment config: `python train.py experiment=experiment_name`

<br>

### Logs

**Hydra creates new working directory for every executed run.** By default, logs have the following structure:

```
├── logs
│   ├── experiments                     # Folder for the logs generated by experiments
│   │   ├── runs                          # Folder for single runs
│   │   │   ├── experiment_name             # Experiment name
│   │   │   │   ├── YYYY-MM-DD_HH-MM-SS       # Datetime of the run
│   │   │   │   │   ├── .hydra                  # Hydra logs
│   │   │   │   │   ├── csv                     # Csv logs
│   │   │   │   │   ├── wandb                   # Weights&Biases logs
│   │   │   │   │   ├── checkpoints             # Training checkpoints
│   │   │   │   │   └── ...                     # Any other thing saved during training
│   │   │   │   └── ...
│   │   │   └── ...
│   │   │
│   │   └── multiruns                     # Folder for multiruns
│   │       ├── experiment_name             # Experiment name
│   │       │   ├── YYYY-MM-DD_HH-MM-SS       # Datetime of the multirun
│   │       │   │   ├──1                        # Multirun job number
│   │       │   │   ├──2
│   │       │   │   └── ...
│   │       │   └── ...
│   │       └── ...
│   │
│   ├── evaluations                       # Folder for the logs generated during testing
│   │   └── ...
│   │
│   └── debugs                            # Folder for the logs generated during debugging
│       └── ...
```

You can change this structure by modifying paths in [hydra configuration](configs/log_dir).

<br>

### Experiment Tracking

PyTorch Lightning supports many popular logging frameworks:<br>
**[Weights&Biases](https://www.wandb.com/) · [Neptune](https://neptune.ai/) · [Comet](https://www.comet.ml/) · [MLFlow](https://mlflow.org) · [Tensorboard](https://www.tensorflow.org/tensorboard/)**

These tools help you keep track of hyperparameters and output metrics and allow you to compare and visualize results. To use one of them simply complete its configuration in [configs/logger](configs/logger) and run:

```bash
python train.py logger=logger_name
```

You can use many of them at once (see [configs/logger/many_loggers.yaml](configs/logger/many_loggers.yaml) for example).

You can also write your own logger.

Lightning provides convenient method for logging custom metrics from inside LightningModule. Read the docs [here](https://pytorch-lightning.readthedocs.io/en/latest/extensions/logging.html#automatic-logging) or take a look at [MNIST example](src/models/mnist_module.py).

<br>

### Hyperparameter Search

Defining hyperparameter optimization is as easy as adding new config file to [configs/hparams_search](configs/hparams_search).

<details>
<summary><b>Show example</b></summary>

```yaml
defaults:
  - override /hydra/sweeper: optuna

# choose metric which will be optimized by Optuna
optimized_metric: "val/acc_best"

hydra:
  # here we define Optuna hyperparameter search
  # it optimizes for value returned from function with @hydra.main decorator
  # learn more here: https://hydra.cc/docs/next/plugins/optuna_sweeper
  sweeper:
    _target_: hydra_plugins.hydra_optuna_sweeper.optuna_sweeper.OptunaSweeper
    storage: null
    study_name: null
    n_jobs: 1

    # 'minimize' or 'maximize' the objective
    direction: maximize

    # number of experiments that will be executed
    n_trials: 20

    # choose Optuna hyperparameter sampler
    # learn more here: https://optuna.readthedocs.io/en/stable/reference/samplers.html
    sampler:
      _target_: optuna.samplers.TPESampler
      seed: 12345
      consider_prior: true
      prior_weight: 1.0
      consider_magic_clip: true
      consider_endpoints: false
      n_startup_trials: 10
      n_ei_candidates: 24
      multivariate: false
      warn_independent_sampling: true

    # define range of hyperparameters
    search_space:
      datamodule.batch_size:
        type: categorical
        choices: [32, 64, 128]
      model.lr:
        type: float
        low: 0.0001
        high: 0.2
      model.net.lin1_size:
        type: categorical
        choices: [32, 64, 128, 256, 512]
      model.net.lin2_size:
        type: categorical
        choices: [32, 64, 128, 256, 512]
      model.net.lin3_size:
        type: categorical
        choices: [32, 64, 128, 256, 512]
```

</details>

Next, you can execute it with: `python train.py -m hparams_search=mnist_optuna`

Using this approach doesn't require you to add any boilerplate into your pipeline, everything is defined in a single config file.

You can use different optimization frameworks integrated with Hydra, like Optuna, Ax or Nevergrad.

The `optimization_results.yaml` will be available under `logs/multirun` folder.

This approach doesn't support advanced technics like prunning - for more sophisticated search, you probably shouldn't use hydra multirun feature and instead write your own optimization pipeline.

<br>

 

 

### Callbacks

The branch [`wandb-callbacks`](https://github.com/ashleve/lightning-hydra-template/tree/wandb-callbacks) contains example callbacks enabling better Weights&Biases integration, which you can use as a reference for writing your own callbacks (see [wandb_callbacks.py](https://github.com/ashleve/lightning-hydra-template/tree/wandb-callbacks/src/callbacks/wandb_callbacks.py)).

Callbacks which support reproducibility:

- **WatchModel**
- **UploadCodeAsArtifact**
- **UploadCheckpointsAsArtifact**

Callbacks which provide examples of logging custom visualisations:

- **LogConfusionMatrix**
- **LogF1PrecRecHeatmap**
- **LogImagePredictions**

To try all of the callbacks at once, switch to the right branch:

```bash
git checkout wandb-callbacks
```

And then run the following command:

```bash
python train.py logger=wandb callbacks=wandb
```

To see the result of all the callbacks attached, take a look at [this experiment dashboard](https://wandb.ai/hobglob/template-tests/runs/3rw7q70h).

<br>

### Multi-GPU Training

Lightning supports multiple ways of doing distributed training. The most common one is DDP, which spawns separate process for each GPU and averages gradients between them. To learn about other approaches read the [lightning docs](https://pytorch-lightning.readthedocs.io/en/latest/advanced/multi_gpu.html).

You can run DDP on mnist example with 4 GPUs like this:

```bash
python train.py trainer.gpus=4 +trainer.strategy=ddp
```

⚠️ When using DDP you have to be careful how you write your models - learn more [here](https://pytorch-lightning.readthedocs.io/en/latest/advanced/multi_gpu.html).

<br>

### Docker

First you will need to [install Nvidia Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html) to enable GPU support.

The template Dockerfile is provided on branch [`dockerfiles`](https://github.com/ashleve/lightning-hydra-template/tree/dockerfiles). Copy it to the template root folder.

To build the container use:

```bash
docker build -t <project_name> .
```

To mount the project to the container use:

```bash
docker run -v $(pwd):/workspace/project --gpus all -it --rm <project_name>
```

<br>



## Other Repositories

<details>
<summary><b>Inspirations</b></summary>

This template was inspired by:
[PyTorchLightning/deep-learninig-project-template](https://github.com/PyTorchLightning/deep-learning-project-template),
[drivendata/cookiecutter-data-science](https://github.com/drivendata/cookiecutter-data-science),
[tchaton/lightning-hydra-seed](https://github.com/tchaton/lightning-hydra-seed),
[Erlemar/pytorch_tempest](https://github.com/Erlemar/pytorch_tempest),
[lucmos/nn-template](https://github.com/lucmos/nn-template).

</details>

<details>
<summary><b>Useful repositories</b></summary>

- [pytorch/hydra-torch](https://github.com/pytorch/hydra-torch) - resources for configuring PyTorch classes with Hydra,
- [romesco/hydra-lightning](https://github.com/romesco/hydra-lightning) - resources for configuring PyTorch Lightning classes with Hydra
- [lucmos/nn-template](https://github.com/lucmos/nn-template) - similar template
- [PyTorchLightning/lightning-transformers](https://github.com/PyTorchLightning/lightning-transformers) - official Lightning Transformers repo built with Hydra

</details>

<!-- ## :star:&nbsp; Stargazers Over Time
[![Stargazers over time](https://starchart.cc/ashleve/lightning-hydra-template.svg)](https://starchart.cc/ashleve/lightning-hydra-template) -->

<br>

## References

- [The Neural Hawkes Process: A Neurally
Self-Modulating Multivariate Point Process](https://arxiv.org/pdf/1612.09328.pdf)
- [Continuous Cnn For Nonuniform Time Series](https://ieeexplore.ieee.org/document/9414318)  


## License

This project is licensed under the MIT License.

```
MIT License

Copyright (c) 2021 ashleve

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

<br>
<br>
<br>
<br>


---

 

 
