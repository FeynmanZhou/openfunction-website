---
title: "Your First Function: Java"
linkTitle: "Your First Function: Java"
weight: 2240
description: >	
    Learn how to create a function in Java.
---

This document describes how to create a function in Java.

## Prerequisites

You have [installed OpenFunciton](../../installation/).

## Create a Function in Java

### Create a secret

Define the values of `REGISTRY_SERVER`, `REGISTRY_USER` and `REGISTRY_PASSWORD` in the following command, and then run it to create a secret for accessing your image registry.

```shell
REGISTRY_SERVER=https://index.docker.io/v1/ REGISTRY_USER=<your_registry_username> REGISTRY_PASSWORD=<your_registry_password>
kubectl create secret docker-registry push-secret \
    --docker-server=$REGISTRY_SERVER \
    --docker-username=$REGISTRY_USER \
    --docker-password=$REGISTRY_PASSWORD
```

### Create a function in Java

1. Use the following example YAML file to create a manifest `function-sample.yaml` for your function, and modify the value of `spec.image` to set your own image registry address.

   ```yaml
   apiVersion: core.openfunction.io/v1beta1
   kind: Function
   metadata:
     name: java-sample
   spec:
     version: "v1.0.0"
     image: "<your registry name>/sample-java-func:v1"
     imageCredentials:
       name: push-secret
     port: 8080 # Default to 8080.
     build:
       builder: "openfunction/gcp-builder:v1"
       env:
         GOOGLE_FUNCTION_TARGET: "com.openfunction.HelloWorld"
         GOOGLE_FUNCTION_SIGNATURE_TYPE: "http"
       srcRepo:
         url: "https://github.com/OpenFunction/samples.git"
         sourceSubPath: "functions/Knative/hello-world-java"
         revision: "release-0.6"
     serving:
       runtime: knative # Default to knative.
       template:
         containers:
           - name: function
             imagePullPolicy: IfNotPresent
   ```

2. Run the following command to create the function.

   ```shell
   kubectl apply -f function-sample.yaml
   ```

### Access the function

For more information about how to access the function, refer to [Access the function](../function-go#access-the-function).
