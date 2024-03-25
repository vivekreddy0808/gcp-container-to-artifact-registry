### Steps to pull and push images to Artifact Registry: 

### Command

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
# Tag images created
sudo docker tag bitnami/keycloak:latest us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak:latest
```

```bash
# Output 
REPOSITORY                                                   TAG       IMAGE ID       CREATED      SIZE
bitnami/keycloak                                             latest    a537a304adfa   3 days ago   695MB
us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak   latest    a537a304adfa   3 days ago   695MB
```


### Error
```bash
rsweety751@demo:~$ sudo docker push us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak
Using default tag: latest
The push refers to repository [us-east1-docker.pkg.dev/container-kafka-2023/demo/keycloak]
2f4780293bf6: Preparing 
denied: Unauthenticated request. Unauthenticated requests do not have permission "artifactregistry.repositories.uploadArtifacts" on resource "projects/container-kafka-2023/locations/us-east1/repositories/demo" (or it may not exist)
```

### Fix

```bash
# Add required permission and IAM roles for Artifact registry.
# We need to create google service account and add the IAM role to the service account. 

```