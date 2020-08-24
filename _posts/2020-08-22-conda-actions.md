---
layout: post
title: How to Use Conda With Github Actions
description: "A guide for integrating the Conda package manager with the Github Actions continuous integration service"
cover-img: "/assets/img/far_philly.jpg"
---

# Overview
Around a [year ago](https://github.blog/2019-08-08-github-actions-now-supports-ci-cd/) Github released its continuous integration (CI) service called [Github Actions](https://github.com/features/actions).
While it is nice to have a CI service in the same place as your code, there are fewer guides about how to set it up than there are for more established services like
[Travis CI](https://travis-ci.org/) or [CircleCI](https://circleci.com/).

In particular, there are no tutorials of how to use Github Actions for repos using [Conda](https://docs.conda.io/en/latest/) environments.
There are [several actions](https://github.com/marketplace?query=conda) in the [Marketplace](https://github.com/marketplace) that are designed to perform various tasks with Conda,
but miniconda [is already installed on Github Actions runners](https://docs.github.com/en/actions/reference/software-installed-on-github-hosted-runners) so they shouldn't really be necessary.

This post has three parts.
[First](#parts), it explains the different parts of a Github action and what they do.
[Then](#examples) it gives examples of how to use Github Actions to run tests on both Windows and Linux virtual machines.
[Finally](#marketplace), it discusses when and how to use actions from the marketplace instead of writing all of the logic yourself.

<a id="parts"></a>
## Anatomy of a Github Action
Github does a great job of explaining [how to set up a starter workflow](https://docs.github.com/en/actions/getting-started-with-github-actions/starting-with-preconfigured-workflow-templates).
If you're unfamiliar with how to set up an action, start there.
The tl;dr of it is that you add the file `<project_root>/.github/workflows/<workflow_name>.yml` to your repository.
The only issue is that they don't currently explain much of the syntax, they just refer you to templates or the full reference document.
This section lists explains the basic syntax of an action line by line.

### Walkthrough

```
name: Python Package Using Anaconda
```
At the top level, the `name` command sets the name of your whole workflow.
In the Actions tab on Github, it looks like this:
<img src="/assets/img/post_img/github_workflow_name.png"/>

```
name: Python Package Using Anaconda

on: [push]
```
The `on` keyword tells the workflow when to run.
There are [a ton](https://docs.github.com/en/developers/webhooks-and-events/webhook-events-and-payloads) of different fields you can provide here,
but the most common two are `push` and `pull_request`.
You can also customize which branch the workflow is run on based on the event that triggered it.
For more information on that, there is a [helpful page](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#on) in the Actions reference.


```
name: Python Package Using Anaconda

on: [push]

jobs:
  build-linux:
```
We have now entered the `jobs` block, where most of the substance of the workflow lives.
The only job we're running in this example has the id `build-linux`.
It's worth noting that the id describes what the job does instead of changing anything about the workflow.

The image below shows the locations where the job id shows up in the Actions tab on Github.
<img src="/assets/img/post_img/github_workflow_job.png"/>

```
name: Python Package Using Anaconda

on: [push]

jobs:
  build-linux:
    runs-on: ubuntu-latest
```
The `runs-on` field is required for each job, as it tells the runner which operating system to use.
The typical options are `windows-latest`, `ubuntu-latest`, and `macos-latest`, though there are [multiple OS versions available](https://docs.github.com/en/actions/reference/virtual-environments-for-github-hosted-runners#supported-runners-and-hardware-resources).

You can also run the workflow [on your own server](https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners)
if you want to test more operating systems or exceed Github's [workflow usage limits](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#usage-limits).

```
name: Python Package Using Anaconda

on: [push]

jobs:
  build-linux:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
```
Now we're in the `steps` block.
Our first step is actually a `uses` statement, which is a step that runs another action.
The syntax for uses statements is `uses: <repo>/<action_name>@version`, so we know that this action is from the [official Github Actions repo](https://github.com/actions).
In this case, the `checkout` adds the repository our action is being run on to the virtual machine so we can work with the repo's files.

```
name: Python Package Using Anaconda

on: [push]

jobs:
  build-linux:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.8
      uses: actions/setup-python@v2
      with:
        python-version: 3.8
```
Another `uses` statement!
This one has its own `name`, which changes how it's displayed on github.
It also introduces a new keyword, `with`, which is used to pass key-value pairs as arguments into the action.
This istance of `with` tells the `setup-python` action to set up Python 3.8 specifically.

The names of our two steps can be seen below, along with a default step that sets up the runner.
<img src="/assets/img/post_img/github_workflow_steps.png"/>


```
name: Python Package Using Anaconda

on: [push]

jobs:
  build-linux:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.8
      uses: actions/setup-python@v2
      with:
        python-version: 3.8
    - name: Install dependencies
      run: |
        # $CONDA is an environment variable pointing to the root of the miniconda directory
        $CONDA/bin/conda env update --file environment.yml --name base
```
The first thing to notice here is the `run` keyword, which executes commands in a shell.
Which shell is being run depends on the operating system for the job, though it can be specified manually with [the `shell` keyword](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#using-a-specific-shell).
Since this action uses ubuntu, the shell defaults to bash.

This is also the first conda specific step.
The `$CONDA` environment variable is present across environments and points to the miniconda root directory for the virtual machine.
Typical Conda usage to install an environment from a file would be something like `conda env create -f environment.yml`.
Unfortunately, environment variables don't persist between steps in a workflow, which breaks some things in conda.
Installing the environment's packages into the base environment avoids this issue since the packages are available by default.

```
name: Python Package Using Anaconda

on: [push]

jobs:
  build-linux:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.8
      uses: actions/setup-python@v2
      with:
        python-version: 3.8
    - name: Install dependencies
      run: |
        # $CONDA is an environment variable pointing to the root of the miniconda directory
        $CONDA/bin/conda env update --file environment.yml --name base
    - name: Lint with flake8
      run: |
        $CONDA/bin/conda install flake8
        # stop the build if there are Python syntax errors or undefined names
        $CONDA/bin/flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
        # exit-zero treats all errors as warnings. The GitHub editor is 127 chars wide
        $CONDA/bin/flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics
```
Now we're cooking with fire!
This step runs the flake8 linter to check the python code in the directory.
The first line installs flake8 into the base environment.

The next line throws errors for specific code issues, then the following line runs the full linter to print any style issues.
If this were a normal machine you'd be able to call `flake8` here without specifying a path.
Because of path weirdness in the virtual machines, you have to specify the path from the conda directory instead.

```
name: Python Package Using Anaconda

on: [push]

jobs:
  build-linux:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.8
      uses: actions/setup-python@v2
      with:
        python-version: 3.8
    - name: Install dependencies
      run: |
        # $CONDA is an environment variable pointing to the root of the miniconda directory
        $CONDA/bin/conda env update --file environment.yml --name base
    - name: Lint with flake8
      run: |
        $CONDA/bin/conda install flake8
        # stop the build if there are Python syntax errors or undefined names
        $CONDA/bin/flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
        # exit-zero treats all errors as warnings. The GitHub editor is 127 chars wide
        $CONDA/bin/flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics
    - name: Test with pytest
      run: |
        conda install pytest
        $CONDA/bin/pytest
```
Finally, the last step installs and executes pytest to run any test cases in the repository.
There's nothing new in this step, so the section will conclude with the full image of the action's run.

<img src="/assets/img/post_img/github_workflow_full.png"/>


<a id="examples"></a>
## Examples of Conda Actions
This section contains basic conda actions that can be used in Ubuntu/Macos and Windows.
I wrote them, but a lot of the idiosyncracies and pieces of the implementation come from information people dug up in [this thread](https://github.com/actions/starter-workflows/issues/44).
If you're looking to implement custom workflows, you'll probably want to read through the thread to better understand what's going on.

### Ubuntu Action
```
name: Python Package Using Anaconda

on: [push]

jobs:
  build-linux:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.8
      uses: actions/setup-python@v2
      with:
        python-version: 3.8
    - name: Install dependencies
      run: |
        # $CONDA is an environment variable pointing to the root of the miniconda directory
        $CONDA/bin/conda env update --file environment.yml --name base
    - name: Lint with flake8
      run: |
        $CONDA/bin/conda install flake8
        # stop the build if there are Python syntax errors or undefined names
        $CONDA/bin/flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
        # exit-zero treats all errors as warnings. The GitHub editor is 127 chars wide
        $CONDA/bin/flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics
    - name: Test with pytest
      run: |
        conda install pytest
        $CONDA/bin/pytest
```

This action is based off the [python-package github action](https://github.com/actions/starter-workflows/blob/main/ci/python-package.yml).
It is also listed in the [Github starter workflows](https://github.com/actions/starter-workflows) directory as `python-package-conda`.
Since it's discussed in depth in the first section, I'll avoid going into too much detail here.

### Windows Action
```
name: Github Actions in Windows
job build-windows:
    runs-on: windows-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: 3.8
    - name: Install dependencies
      run: |
        C:\Miniconda\condabin\conda.bat env update --file environment.yml --name base
        C:\Miniconda\condabin\conda.bat init powershell
    - name: Lint with flake8
      run: |
        # Activate the base environment
        C:\Miniconda\condabin\conda.bat activate base
        C:\Miniconda\condabin\conda.bat install flake8
        # stop the build if there are Python syntax errors or undefined names
        flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
        # exit-zero treats all errors as warnings. The GitHub editor is 127 chars wide
        flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics
    - name: Test with pytest
      run: |
        # Activate the base environment
        C:\Miniconda\condabin\conda.bat activate base
        C:\Miniconda\condabin\conda.bat install pytest
        pytest
```
This action is a Windows implementation of the action above.
While the steps and the end results are the same, the implementation is fairly different.
The differences start at the first line of `Install dependencies` when it becomes apparent that the shell that is running is powershell instead of bash.
There is also an added `init powershell` line that tells the virtual machine to look for conda information when loading new shells (which it does for each step).
Because powershell knows where to look for conda environments, the next steps are able to activate an environment, install a package, and use the package without a path from the conda directory.
The final difference is that an absolute that to conda is used instead of a relative path from the `CONDA` environment variable.
The `CONDA` variable still exists in windows, I just don't know powershell well enough to use it.

<a id="marketplace"></a>
## Using Actions from the Marketplace


{% raw %}
```
name: Use Setup-Miniconda From Marketplace
on: [push]

jobs:
  miniconda:
    name: Miniconda ${{ matrix.os }}
    runs-on: ${{ matrix.os }}
    strategy:
        matrix:
            os: ["ubuntu-latest", "windows-latest"]
    steps:
      - uses: actions/checkout@v2
      - uses: goanpeca/setup-miniconda@v1
        with:
          activate-environment: test
          environment-file: environment.yml
          python-version: 3.8
          auto-activate-base: false
      - shell: bash -l {0}
        run: |
          conda info
          conda list
      - name: Lint
        shell: bash -l {0}
        run: |
            conda install flake8
            python -m flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
            python -m flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics
      - name: Run pytest
        shell: bash -l {0}
        run: |
            conda install pytest
            pytest
```
{% endraw %}
