#  Pod is invalid: spec.volumes[3].name: Invalid value. must be no more than 63 characters

## Issue

Missing or invalid Task kabanero/java-spring-boot2-build-push-task: Pod "java-spring-boot2-build-push-deploy-pipeline-run-15794601-n96kl-pod-02ad5b" is invalid: spec.volumes[3].name: Invalid value: "java-spring-boot2-build-push-deploy-pipeline-run-157946019zbdmp-pvc": must be no more than 63 characters

![Issue](images/01-issue.png?raw=true "Issue")

## Reason

Name of the pipeline could be long.

## Solution

Change pipeline name from `java-spring-boot2-build-push-deploy-pipeline` to something shorter (Ex: `j-spring-bt2-bd-ph-dy-pipeline`) by doing the below steps.

1. Run the below command to extract the definition of the pipeline into a `new.yaml`
```
kubectl get pipeline java-spring-boot2-build-push-deploy-pipeline -o yaml -n kabanero  > new.yaml
```

2. Open the `new.yaml` in some text editor and change the `metadata>name` tag into `j-spring-bt2-bd-ph-dy-pipeline`

![New Pipeline name](images/02-new-pipeline.png?raw=true "New Pipeline name")

3. Create a new pipeline with the above yaml file.
```
kubectl apply -f new.yaml
```

4. Create new webhook, where you have to give this new pipeline name `j-spring-bt2-bd-ph-dy-pipeline` in the Pipeline parameter.

