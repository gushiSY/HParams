<h3 align="center">Stop messing around and organize your hyperparameters.</h3>

HParams is a configuration management solution for machine learning projects. With this you can externalize your hyparameters ensuring that they are extensible, accessible and maintainable.

Technically speaking, HParams uses the `@configurable` decorator to inject your hyperparameter
dependencies at runtime from a designated configuration file.

![PyPI - Python Version](https://img.shields.io/pypi/pyversions/hparams.svg?style=flat-square)
[![Codecov](https://img.shields.io/codecov/c/github/PetrochukM/HParams/master.svg?style=flat-square)](https://codecov.io/gh/PetrochukM/HParams)
[![Downloads](http://pepy.tech/badge/hparams)](http://pepy.tech/project/hparams)
[![Build Status](https://img.shields.io/travis/PetrochukM/HParams/master.svg?style=flat-square)](https://travis-ci.org/PetrochukM/HParams)

_Logo by [Chloe Yeo](http://www.yeochloe.com/)_

## Installation

Make sure you have Python 3. You can then install `hparams` using `pip`:

    pip install hparams

Install the latest code via:

    pip install git+https://github.com/PetrochukM/HParams.git


## Basics

Add HParams to your project by following one of the common use cases:

### Configure Training

Configure your training run like so:

```python3
from hparams import configurable, add_config, HParams, HParam

@configurable
def train(batch_size=HParam(int)):
    pass

add_config({ train: HParams(batch_size=32) })
```

HParams supports optional configuration typechecking to help you find bugs.

### Set Defaults

Configure PyTorch and Tensorflow defaults to match, enabling reproducibility, like so:

```python3
from torch.nn import BatchNorm1d
from hparams import configurable, add_config, HParams

# NOTE: `momentum=0.01` to match Tensorflow defaults
BatchNorm1d.__init__ = configurable(BatchNorm1d.__init__)
add_config({ 'torch.nn.BatchNorm1d.__init__': HParams(momentum=0.01) })
```

### CLI

Enable rapid command line experimentation, for example:

```console
foo@bar:~$ file.py --torch.optim.adam.Adam.__init__ HParams(lr=0.1,betas=(0.999,0.99))
```

```python3
import sys
from torch.optim import Adam
from hparams import configurable, add_config, parse_hparam_args

Adam.__init__ = configurable(Adam.__init__)
parsed = parse_hparam_args(sys.argv) # Parse command line arguments
add_config(parsed)
```

### Track Hyperparameters

Easily track your hyperparameters using tools like [Comet](comet.ml).

```python3
from comet_ml import Experiment
from hparams import get_config

experiment = Experiment()
experiment.log_parameters(get_config())
```

### Multiprocessing: Partial Support

Export a Python `functools.partial` to use in another process like so:

```python3
from hparams import configurable, HParam

@configurable
def func(hparam=HParam(int)):
    pass

partial = func.get_configured_partial()
```

With this approach, you don't have to transfer the entire global state to the new process.

## Notable Features

HParams has:

- Only one dependency
- Little to no runtime overhead (< 1e-05 seconds) per configured function
- Support for Python's notorious `multiprocessing` module
- Clear unambiguous errors and warnings for your benefit

## Contributing

We've released HParams because a lack of hyperparameter management solutions. We hope that
other people can benefit from the project. We are thankful for any contributions from the
community.

### Contributing Guide

Read our [contributing guide](https://github.com/PetrochukM/HParams/blob/master/CONTRIBUTING.md) to
learn about our development process, how to propose bugfixes and improvements, and how to build and
test your changes to hparams.

## Authors

This library was initially developed by Michael Petrochuk's from his learnings as a deep learning engineer at Apple
and the Allen Institute for Artificial Intelligence. [Chloe Yeo](http://www.yeochloe.com/) did the logo design.

## Citing

If you find hparams useful for an academic publication, then please use the following BibTeX to
cite it:

```
@misc{hparams,
  author = {Petrochuk, Michael},
  title = {HParams: Hyperparameter management},
  year = {2019},
  publisher = {GitHub},
  journal = {GitHub repository},
  howpublished = {\url{https://github.com/PetrochukM/HParams}},
}
```
