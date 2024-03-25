#### Steps to pull and push images to Artifact Registry: 

##### Command

```bash 
docker --version
Docker version 26.0.0, build 2ae903e
```

```bash
sudo docker pull bitnami/keycloak
```

```bash 
# Output: 
Using default tag: latest
latest: Pulling from bitnami/keycloak
7bad0c861f89: Pull complete 
Digest: sha256:c4a2bf092d1afeac972d5f66db58e431d3fb53beedde18b7f54e45c37f5d1be5
Status: Downloaded newer image for bitnami/keycloak:latest
docker.io/bitnami/keycloak:latest
```

```bash 
# Navigate to Artifact Registry and get the command:
us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak:latest

# Then add docker tag command: 
sudo docker tag bitnami/keycloak:latest us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak:latest
```

```bash
# Output:
REPOSITORY                                                   TAG       IMAGE ID       CREATED      SIZE
bitnami/keycloak                                             latest    a537a304adfa   3 days ago   695MB
us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak   latest    a537a304adfa   3 days ago   695MB
```

```bash

# Next step is to configure, gcloud auth:

gcloud auth configure-docker us-east1-docker.pkg.dev
```

```bash
# Output:

WARNING: Your config file at [/home/rsweety751/.docker/config.json] contains these credential helper entries:

{
  "credHelpers": {
    "us-east1-docker.pkg.dev": "gcloud"
  }
}
Adding credentials for: us-east1-docker.pkg.dev
gcloud credential helpers already registered correctly.

```


#### Error
```bash

# Error 1: ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

rsweety751@demo:~$ sudo docker push us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak
Using default tag: latest
The push refers to repository [us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak]
2f4780293bf6: Preparing 
denied: Unauthenticated request. Unauthenticated requests do not have permission "artifactregistry.repositories.uploadArtifacts" on resource "projects/container-kafka-2023/locations/us-east1/repositories/demo" (or it may not exist)

# Error 2: ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

rsweety751@demo:~$ gcloud auth print-access-token |sudo docker login -u oauth2accesstoken --password-stdin us-east1-docker.pkg.dev/container-kafka-2023/demo | sudo docker push us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak:latest 
The push refers to repository [us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak]
2f4780293bf6: Preparing 
denied: Unauthenticated request. Unauthenticated requests do not have permission "artifactregistry.repositories.uploadArtifacts" on resource "projects/container-kafka-2023/locations/us-east1/repositories/demo" (or it may not exist)

WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store
```

#### Fix
##### Reference: " https://www.googlecloudcommunity.com/gc/Developer-Tools/Permission-quot-artifactregistry-repositories-uploadArtifacts/m-p/541769 " 

```bash
# Add required permission and IAM roles for Artifact registry.
# We need to create google service account and add the IAM role to the service account. 

| Navigate to IAM & Admin 
    -> Service Accounts
        -> Create Service Account
            -> Service account details 
                - name: artifact-registry-sa-2024
                - sa-id: artifact-registry-sa-2024@container-kafka-2023.iam.gserviceaccount.com
            -> Click on Create and Continue
        -> Grant this service account access to project 
            - roles: Artifact Registry Reader, Artifact Registry Writer 
        -> Done

# By execute this command: 

gcloud auth print-access-token |sudo docker login -u oauth2accesstoken --password-stdin us-east1-docker.pkg.dev/container-kafka-2023/demo | sudo docker push us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak:latest 

```

```bash 
# We can pull and Push Images:

# Output for Pushing Images: 
rsweety751@demo:~$ gcloud auth print-access-token |sudo docker login -u oauth2accesstoken --password-stdin us-east1-docker.pkg.dev/container-kafka-2023/demo | sudo docker push us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak:latest 
The push refers to repository [us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak]
2f4780293bf6: Pushing [>                                                  ]  6.109MB/694.8MB
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store
2f4780293bf6: Pushing [>                                                  ]  8.894MB/694.8MB
2f4780293bf6: Pushed 
latest: digest: sha256:f1ca92ec752193a978d3ad4c25836432dce9b7e12d51a9cc79336378f816cd4a size: 530


# Output for Pulling images: 
rsweety751@demo:~$ sudo docker pull us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak:latest
latest: Pulling from container-kafka-2023/demo/keycloak

23ba7b864fc7: Pull complete 
Digest: sha256:f1ca92ec752193a978d3ad4c25836432dce9b7e12d51a9cc79336378f816cd4a
Status: Downloaded newer image for us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak:latest
us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak:latest
rsweety751@demo:~$ sudo docker images
REPOSITORY                                                   TAG       IMAGE ID       CREATED      SIZE
us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak   latest    a537a304adfa   3 days ago   695MB

```