# About aws-mwaa-local-runner

This repository provides a command line interface (CLI) utility that replicates an Amazon Managed Workflows for Apache Airflow (MWAA) environment locally.

The code in this repo refer to this [repo](https://github.com/aws/aws-mwaa-local-runner) which is offically recognized by AWS for running the MWAA locally.

## About the CLI

The CLI builds a Docker container image locally that’s similar to a MWAA production image. This allows you to run a local Apache Airflow environment to develop and test DAGs, custom plugins, and dependencies before deploying to MWAA.

## What this repo contains

```text
docker/
  .gitignore
  mwaa-local-env
  README.md
  config/
    airflow.cfg
    constraints.txt
    requirements.txt
    webserver_config.py
  script/
    bootstrap.sh
    entrypoint.sh
  docker-compose-dbonly.yml
  docker-compose-local.yml
  docker-compose-sequential.yml
  Dockerfile
```

## What we changed
1. We mount the dags folder from our local dir `~/development/airflow/dags` to the docker container so that the code changed locally will be automatically sync.

2. We mount the `requirements.txt` from our local dir `~/development/airflow/dags/requirements.txt` to the docker container so that the dependencies will be automatically sync.

**Reminder**: As this repo is forked so cannot be set as `Private`. By using this mount way, we don't need to publish any of our dags code to this repo but do remember that this repo is `Public` so please do not push any sensitive code onto it.d 

## Prerequisites

- Have docker installed and enabled
- Clone the `ssp-airflow` repo to local at dir such as `~/development/airflow/`


## Get started

```bash
git clone https://github.com/mbrtargeting/aws-mwaa-local-runner.git
cd aws-mwaa-local-runner
```

### Step one: Building the Docker image

Build the Docker container image using the following command:

```bash
./mwaa-local-env build-image
```

**Note**: it takes several minutes to build the Docker image locally.

### Step two: Running Apache Airflow

Run Apache Airflow using one of the following database backends.

#### Local runner

Runs a local Apache Airflow environment that is a close representation of MWAA by configuration.

```bash
./mwaa-local-env start
```

To stop the local environment, Ctrl+C on the terminal and wait till the local runner and the postgres containers are stopped.

### Step three: Accessing the Airflow UI

By default, the `bootstrap.sh` script creates a username and password for your local Airflow environment.

- Username: `admin`
- Password: `test`

#### Airflow UI

- Open the Apache Airlfow UI: <http://localhost:8080/>.

### Step four: Add DAGs and supporting files

The following section describes where to add your DAG code and supporting files. We recommend creating a directory structure similar to your MWAA environment.

#### DAGs

Add DAG code to the local dir such as `~/development/airflow/dags`, then will be sync to `dags/` folder.

#### Requirements.txt

1. Add Python dependencies to local dir such as `~/development/airflow/dags/requirements.txt`.  
2. To test a requirements.txt without running Apache Airflow, use the following script:

```bash
./mwaa-local-env test-requirements
```

Let's say you add `aws-batch==0.6` to your `~/development/airflow/dags/requirements.txt` file. You should see an output similar to:

```bash
Installing requirements.txt
Collecting aws-batch (from -r /usr/local/airflow/dags/requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/5d/11/3aedc6e150d2df6f3d422d7107ac9eba5b50261cf57ab813bb00d8299a34/aws_batch-0.6.tar.gz
Collecting awscli (from aws-batch->-r /usr/local/airflow/dags/requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/07/4a/d054884c2ef4eb3c237e1f4007d3ece5c46e286e4258288f0116724af009/awscli-1.19.21-py2.py3-none-any.whl (3.6MB)
    100% |████████████████████████████████| 3.6MB 365kB/s 
...
...
...
Installing collected packages: botocore, docutils, pyasn1, rsa, awscli, aws-batch
  Running setup.py install for aws-batch ... done
Successfully installed aws-batch-0.6 awscli-1.19.21 botocore-1.20.21 docutils-0.15.2 pyasn1-0.4.8 rsa-4.7.2
```

#### Custom plugins

**TODO**
The plugins part hasn't been fully tested so the instructions below are just directly from the original repo.

- Create a directory at the root of this repository, and change directories into it. This should be at the same level as `dags/` and `docker`. For example:

```bash
mkdir plugins
cd plugins
```

- Create a file for your custom plugin. For example:

```bash
virtual_python_plugin.py
```

- (Optional) Add any Python dependencies to `~/development/airflow/dags/requirements.txt`.

**Note**: this step assumes you have a DAG that corresponds to the custom plugin. For examples, see [MWAA Code Examples](https://docs.aws.amazon.com/mwaa/latest/userguide/sample-code.html).

## What's next?

See more in the original [README](https://github.com/aws/aws-mwaa-local-runner/blob/main/README.md)
