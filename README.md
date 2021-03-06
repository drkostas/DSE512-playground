# DSE-512 Playground<img src='https://github.com/drkostas/DSE512-playground/blob/master/img/snek.png' align='right' width='180' height='104'>

[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/drkostas/DSE512-playground/master/LICENSE)

## Table of Contents

+ [About](#about)
+ [Getting Started](#getting_started)
    + [Prerequisites](#prerequisites)
+ [Installing, Testing, Building](#installing)
    + [Available Make Commands](#check_make_commamnds)
    + [Clean Previous Builds](#clean_previous)
    + [Create a new virtual environment](#create_env)
    + [Build Locally (and install requirements)](#build_locally)
    + [Run the tests](#tests)
+ [Running locally](#run_locally)
    + [Configuration](#configuration)
    + [Environment Variables](#env_variables)
    + [Execution Options](#execution_options)
        + [DSE-playground Main](#playground_main)
        + [DSE-playground Greet CLI](#playground_cli)
+ [Deployment](#deployment)
+ [Continuous Ιntegration](#ci)
+ [Todo](#todo)
+ [Built With](#built_with)
+ [License](#license)
+ [Acknowledgments](#acknowledgments)

## About <a name = "about"></a>

A playground repo for the DSE-512 course.

## Getting Started <a name = "getting_started"></a>

These instructions will get you a copy of the project up and running on your local machine for development and testing
purposes. See deployment for notes on how to deploy the project on a live system.

### Prerequisites <a name = "prerequisites"></a>

You need to have a machine with Python > 3.6 and any Bash based shell (e.g. zsh) installed.

```ShellSession

$ python3.8 -V
Python 3.8.5

$ echo $SHELL
/usr/bin/zsh

```

You will also need to install MPI in your system. [Ref for Ubuntu](https://stackoverflow.com/a/41862957/7043716)

## Installing, Testing, Building <a name = "installing"></a>

All the installation steps are being handled by the [Makefile](Makefile). The `server=local` flag basically specifies
that you want to use conda instead of venv, and it can be changed easily in the lines `#25-28`. `local`  is also the
default flag, so you can omit it.

<i>If you don't want to go through the detailed setup steps but finish the installation and run the tests quickly,
execute the following command:</i>

```ShellSession
$ make install server=local
```

<i>If you executed the previous command, you can skip through to the [Running locally section](#run_locally).</i>

### Check the available make commands <a name = "check_make_commamnds"></a>

```ShellSession

$ make help
-----------------------------------------------------------------------------------------------------------
                                              DISPLAYING HELP                                              
-----------------------------------------------------------------------------------------------------------
Use make <make recipe> [server=<prod|circleci|local>] to specify the server
Prod, and local are using conda env, circleci uses virtualenv. Default: local

make help
       Display this message
make install [server=<prod|circleci|local>]
       Call clean delete_conda_env create_conda_env setup run_tests
make clean [server=<prod|circleci|local>]
       Delete all './build ./dist ./*.pyc ./*.tgz ./*.egg-info' files
make delete_env [server=<prod|circleci|local>]
       Delete the current conda env or virtualenv
make create_env [server=<prod|circleci|local>]
       Create a new conda env or virtualenv for the specified python version
make setup [server=<prod|circleci|local>]
       Call setup.py install
make run_tests [server=<prod|circleci|local>]
       Run all the tests from the specified folder
-----------------------------------------------------------------------------------------------------------

```

### Clean any previous builds <a name = "clean_previous"></a>

```ShellSession
$ make clean delete_env server=local
```

### Create a new virtual environment <a name = "create_env"></a>

For creating a conda virtual environment run:

```ShellSession
$ make create_env server=local 
```

### Build Locally (and install requirements) <a name = "build_locally"></a>

To build the project locally using the setup.py install command (which also installs the requirements), execute the
following command:

```ShellSession
$ make setup server=local
```

### Run the tests <a name = "tests"></a>

The tests are located in the `tests` folder. To run all of them, execute the following command:

```ShellSession
$ make run_tests server=local
```

## Running the code locally <a name = "run_locally"></a>

In order to run the code, you will only need to change the yml file if you need to, and either run its file directly or
invoke its console script.

<i>If you don't need to change yml file, skip to [Execution Options](#execution_options).

### Modifying the Configuration <a name = "configuration"></a>

There is an already configured yml file under [confs/template_conf.yml](confs/template_conf.yml) with the following
structure:

```yaml
tag: template
example_db:
  - config:
      hostname: example.host.name
      username: my_name
      password: !ENV ${PASS}
      db_name: my_db1
      port: 3306
    type: mysql
```

The `!ENV` flag indicates that you are passing an environmental value to this attribute. You can change the
values/environmental var names as you wish. If a yaml variable name is changed/added/deleted, the corresponding changes
should be reflected on the [yml_schema.json](configuration/yml_schema.json) too which validates it.

### Set the required environment variables <a name = "env_variables"></a>

In order to run the [main.py](playground/main.py)  you will need to set the environmental variables you are using in your
configuration yml file. Example:

```ShellSession
$ export PASS=my_password
```

The best way to do that, is to create a .env file ([example](env_example)), and source it before running the code.

### Execution Options <a name = "execution_options"></a>

First, make sure you are in the correct virtual environment:

```ShellSession
$ conda activate dse512_playground

$ which python
/home/drkostas/anaconda3/envs/dse512_playground/bin/python

```

#### DSE-playground Main <a name = "playground_main"></a>

Now, in order to run the code you can either call the [main.py](playground/main.py) directly, or invoke the `playground_main`
console script.

```ShellSession
$ python playground/main.py --help
usage: main.py -c CONFIG_FILE [-m {run_mode_1,run_mode_2,run_mode_3}] [-l LOG] [-d] [-h]

A template for python projects.

Required Arguments:
  -c CONFIG_FILE, --config-file CONFIG_FILE
                        The configuration yml file

Optional Arguments:
  -m {run_mode_1,run_mode_2,run_mode_3}, --run-mode {run_mode_1,run_mode_2,run_mode_3}
                        Description of the run modes
  -l LOG, --log LOG     Name of the output log file
  -d, --debug           Enables the debug log messages
  -h, --help            Show this help message and exit


# Or

$ playground_main --help
usage: main.py -c CONFIG_FILE [-m {run_mode_1,run_mode_2,run_mode_3}] [-l LOG] [-d] [-h]

A template for python projects.

Required Arguments:
  -c CONFIG_FILE, --config-file CONFIG_FILE
                        The configuration yml file

Optional Arguments:
  -m {run_mode_1,run_mode_2,run_mode_3}, --run-mode {run_mode_1,run_mode_2,run_mode_3}
                        Description of the run modes
  -l LOG, --log LOG     Name of the output log file
  -d, --debug           Enables the debug log messages
  -h, --help            Show this help message and exit
```

#### DSE-playground CLI <a name = "playground_cli"></a>

There is also a [cli.py](playground/cli.py) which you can also invoke it by its console script too
(`cli`).

```ShellSession
$ cli --help
Usage: cli [OPTIONS] COMMAND [ARGS]...

Options:
  --install-completion [bash|zsh|fish|powershell|pwsh]
                                  Install completion for the specified shell.
  --show-completion [bash|zsh|fish|powershell|pwsh]
                                  Show completion for the specified shell, to
                                  copy it or customize the installation.

  --help                          Show this message and exit.

Commands:
  bye
  hello
```

## Deployment <a name = "deployment"></a>

The deployment is being done to <b>Heroku</b>. For more information you can check
the [setup guide](https://devcenter.heroku.com/articles/getting-started-with-python).

Make sure you check the
defined [Procfile](Procfile) ([reference](https://devcenter.heroku.com/articles/getting-started-with-python#define-a-procfile))
and that you set
the [above-mentioned environmental variables](#env_variables) ([reference](https://devcenter.heroku.com/articles/config-vars))
.

## Continuous Integration <a name = "ci"></a>

$ which python /home/drkostas/anaconda3/envs/DSE512-playground/bin/python
(DSE512-playground)

``` 

### Execution Options <a name = "execution_options"></a>

Depending on the file you want to run, you'll need to follow the corresponding instructions. To view them, just run:

```bash
$ python <your file name>.py --help
usage: <your file name>.py -m {run_mode_1,run_mode_2,run_mode_3} -c CONFIG_FILE [-l LOG]
               [-d] [-h]

<Your python file\'s description.

required arguments:
  -m {run_mode_1,run_mode_2,run_mode_3}, --run-mode {run_mode_1,run_mode_2,run_mode_3}
                        Description of the run modes
  -c CONFIG_FILE, --config-file CONFIG_FILE
                        The configuration yml file
  -l LOG, --log LOG     Name of the output log file

optional arguments:
  -d, --debug           enables the debug log messages
```

To run it following the instructions.