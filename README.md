> [!WARNING]
> **THIS PROJECT IS ARCHIVED**  
> Intel will not provide or guarantee development of or support for this project,
> including but not limited to, maintenance, bug fixes, new releases or updates.
>
> Patches to this project are no longer accepted by Intel.  

# SparseRNNs

[![License: Apache2.0](https://img.shields.io/badge/License-Apache-yellow.svg)](https://opensource.org/licenses/apache-2-0)
![python-support](https://img.shields.io/badge/Python-3.10-3?logo=python)
[![arXiv](https://img.shields.io/badge/arXiv-2502.01330-b31b1b.svg)](https://arxiv.org/abs/2502.01330)

_Training sparse and quantized recurrent neural networks._

## Introduction

## Getting Started

The project dependencies can be installed using the [uv package manager](https://github.com/astral-sh/uv).

```console
wget -qO- https://astral.sh/uv/install.sh | sh
uv python install 3.10
uv sync
```

Instructions to download the dataset are available [on GitHub](https://github.com/IntelLabs/IntelNeuromorphicDNSChallenge). Once the dataset has been downloaded the following env variable smust be set to point to the data location:
- `NDNS_TRAIN_SET` pointing to `.../MicrosoftDNS_4_ICASSP/training_set/`
- `NDNS_TEST_SET` pointing to `.../MicrosoftDNS_4_ICASSP/test_set_1/`
- `NDNS_VALIDATION_SET` pointing to `.../MicrosoftDNS_4_ICASSP/validation_set/`

To train the dense model:

```console
uv run --env-file .env main.py --recipe=ndns.json --dim_scale=0.5 --train  \
   --wandb_entity="intellabs-onboarding" --wandb_project="S5" --run_name=dense.5 \
   --checkpoint_dir=/export/work/apierro/checkpoints/ndns \
```

To train the sparse model:

```console
uv run --env-file .env main.py --recipe=ndns.json --dim_scale=0.5 --train  \
   --wandb_entity="intellabs-onboarding" --wandb_project="S5" --run_name=sparse0.5 \
   --checkpoint_dir=/export/work/apierro/checkpoints/ndns --reset_optimizer \
   --relufication --pruning=iterative-ste-mag-0.9 --load_run_name=dense0.5 \
   --log_act_sparsity="val"
```

To convert the model to static quantization:

```console
uv run --env-file .env main.py --recipe=ndns.json --dim_scale=0.5 --convert  \
  --load_run_name=dense_to_sparse_relu_0.5 --run_name=sparse0.5-w8a16 \
   --wandb_entity="intellabs-onboarding" --wandb_project="S5" \
   --checkpoint_dir=/export/work/apierro/checkpoints/ndns \
   --relufication --pruning=iterative-ste-mag-0.9 \
   --convert_quantization=w8a16 --validate_static_quant --store_activations
```

To convert the model to a fixed-point model, and run it:

```console
uv run --env-file .env sparseRNNs/fxprun.py --recipe=ndns.json \
  --load_run_name=sparse0.5-w8a16 --run_name=sparse0.5-w8a16-fxp \
  --wandb_entity="intellabs-onboarding" --wandb_project="S5" \
  --checkpoint_dir=/export/work/apierro/checkpoints/ndns \
  --quantization=w8a16 --export
```


Contributing
------------

We welcome contributions from the open-source community! If you have any
questions or suggestions, feel free to create an issue in our
repository. We will be happy to work with you to make this project even
better.

License
-------

The code and documentation in this repository are licensed under the Apache 2.0
license. By contributing to this project, you agree that your
contributions will be licensed under this license.

Citation
--------
If you find this repo useful, please consider citing the respective papers.

```bibtex
@article{pierro2025accelerating,
  title={Accelerating Linear Recurrent Neural Networks for the Edge with Unstructured Sparsity},
  author={Pierro, Alessandro and
          Abreu, Steven and
          Timcheck, Jonathan and
          Stratmann, Philipp and
          Wild, Andreas and
          Shrestha, Sumit Bam},
  journal={arXiv preprint arXiv:2502.01330},
  url={https://arxiv.org/abs/2502.01330},
  year={2025}
}
```
