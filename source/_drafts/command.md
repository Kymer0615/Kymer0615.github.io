---
title: Frequently used commands
date: 2021-08-13 11:18:53
tags: ['Conda',"Jupyter"]
categories: 编程 Programming
---
# General
- The z-shell file
    - ~/.zshrc
- Set environment variable
    - alias python=python2
<!-- more -->
# Conda
- Run Jupiter with the condo environment
    - python -m ipykernel install --user --name=deepMetric
- Start conda environment
    - conda activate myenv
- Show env info
    - conda info —envs
- Stop conda env
    - conda deactivate
- Delete conda env
    - conda remove --name python34 --all
- Create an environment
    - conda create -n my_py_env python=3.6.2
    - conda create --prefix="D:\\my_python\\envs\\my_py_env"  python=3.6.3
- Update distribution
    - conda update jupyter

# Jupyter


# Homebrew
- Turn down auto update
    - Add the following in the zsh file
        - export HOMEBREW_NO_AUTO_UPDATE=true
