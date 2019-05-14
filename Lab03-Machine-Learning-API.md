# [ML on GCP C1] Invoking Machine Learning APIs

ML APIs are microservices. 
When we build ML models ourselves, it should be our goal to make them so easy to use and stand-alone.
In this lab, the goal is to learn how to use Machine Learning APIs from within Datalab.

## Objectives:

### Clone the code repo within the Cloud Datalab environment

* First, launch Cloud Data Lab from a Cloud Shell:

  ```
  gcloud compute zones list
  datalab create mydatalabvm --zone us-central1-c
  ```
  (change port to 8081)

* Rename the notebook as `repocheckout`, and in one notebook cell:

  ```
  %bash
  git clone https://github.com/GoogleCloudPlatform/training-data-analyst
  rm -rf training-data-analyst/.git
  ```

### Invoke ML APIs from Datalab to carry out some representative tasks:
* Vision API to detect text in an image
* Translate API to translate that text into English
* Natural Language API to find the sentiment of some famous quotes
* Speech API to transcribe an audio file
