# Run gcloud Command

If you need to run a gcloud command and you don't want to create an instance you can use
CloudBuild.

This example will elevate the default permissions of the service account used by Deployment Manager.

The Dockerfile for the image registry.hub.docker.com/gcptraining/ql-cloudbuild-gcloud:2.0 is:

```
FROM alpine
RUN apk add --update python curl which bash
RUN curl -sSL https://sdk.cloud.google.com | bash
ENV PATH $PATH:/root/google-cloud-sdk/bin
COPY action.sh /tmp
RUN gcloud components install beta --quiet
RUN chmod +x /tmp/action.sh
ENTRYPOINT ["/tmp/action.sh"]
```

As you can see it installs the latest Google Cloud SDK.  action.sh uses the first four CloudBuild arguments to setup the gcloud environment.  

The KEY variable holds service keys in json for the qwiklabs service account created within the project.  The qwiklabs service account has the same permissions as the user.

The rest of the arguments are passed to gcloud.

```
#!/bin/bash
if [ $# -lt 5 ]
then
  echo "Missing one or more of the required arguments: PROJECT, REGION, ZONE, KEY"
  exit 1
fi
PROJECT=$1
REGION=$2
ZONE=$3
KEY=$4
shift 4
echo ${KEY} > /tmp/k_f.json
gcloud auth activate-service-account ${PROJECT}@${PROJECT}.iam.gserviceaccount.com --key-file=/tmp/k_f.json
gcloud config set project ${PROJECT}
gcloud config set compute/region ${REGION}
gcloud config set compute/zone ${ZONE}
# all other args are for gcloud
gcloud $*
```

This snippet is a simple demonstration of running `gcloud compute instances list`:

```
- name: task_title
  action: gcp-types/cloudbuild-v1:cloudbuild.projects.builds.create
  metadata:
    runtimePolicy:
    - UPDATE_ALWAYS
  properties:
    steps:
    - name: registry.hub.docker.com/gcptraining/ql-cloudbuild-gcloud:2.0
      args:
      - {{ env['project'] }}
      - {{ properties['region'] }}
      - {{ properties['zone'] }}
      - '{{ properties['keyFile'] }}'
      - compute
      - instances
      - list
    timeout: 120s
```

## Notes

*   Give the build a useful name, it appears in the deployment manager list of resources
*   The command is broken down into list items, the example above uses gcloud compute instances list
*   The container has beta commands added
*   Ensure that the qlapi account has rights for the task you are running
