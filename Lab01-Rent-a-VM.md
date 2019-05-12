# [ML on GCP C1] Rent-a-VM to process earthquake data
In this lab, you spin up a virtual machine, configure its security, access it remotely, and then carry out the steps of an ingest-transform-and-publish data pipeline manually.

## Objectives:
* Create a Compute Engine instance with the necessary Access and Security
  
  To find some information about the Compute Engine instance, type the following into the command-line:

  ```
  cat /proc/cpuinfo
  ```

* SSH into the instance
* Install the software package Git (for source code version control)
  ```
  sudo apt-get update
  sudo apt-get -y -qq install git
  ```
  To check version of git

  ```
  git --version
  ```

* Ingest data

  ```
  git clone https://github.com/GoogleCloudPlatform/training-data-analyst
  cd training-data-analyst/courses/machine_learning/deepdive/01_googleml/earthquakes
  bash ingest.sh
  ```

* Transform data on the Compute Engine instance after installing all the missing Python packages on the Compute Engine instance:

  ```
  bash install_missing.sh
  ./transform.py
  ```
  
  A png image is created here.

* Store the transformed data on Cloud Storage (after creating the necessary bucket with a unique bucket name)

  ```
  gsutil cp earthquakes.* gs://<YOUR-BUCKET>/earthquakes/
  ```

* Publish Cloud Storage data to the web

  ```
  gsutil acl ch -u AllUsers:R gs://<YOUR-BUCKET>/earthquakes/*
  ```
