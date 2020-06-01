## Without caching

```bash
[Container] 2020/05/23 08:18:57 Entering phase BUILD
[Container] 2020/05/23 08:18:57 Running command echo "BEFORE CREATING THE FILE"
BEFORE CREATING THE FILE
[Container] 2020/05/23 08:18:57 Running command cat /root/test_file & true
cat: /root/test_file: No such file or directory
[Container] 2020/05/23 08:18:57 Running command echo $(date -uIs) > /root/test_file
[Container] 2020/05/23 08:18:57 Running command echo "AFTER CREATING THE FILE"
AFTER CREATING THE FILE
[Container] 2020/05/23 08:18:57 Running command cat /root/test_file
2020-05-23T08:18:57,247666830+00:00
[Container] 2020/05/23 08:18:57 Phase complete: BUILD State: SUCCEEDED
[Container] 2020/05/23 08:18:57 Phase context status code:  Message: 
[Container] 2020/05/23 08:18:57 Entering phase POST_BUILD
[Container] 2020/05/23 08:18:57 Phase complete: POST_BUILD State: SUCCEEDED
[Container] 2020/05/23 08:18:57 Phase context status code:  Message: 
```

## With LOCAL caching of 'root/test_folder/**/*'

```bash
[Container] 2020/05/23 08:30:48 Running command echo "BEFORE CREATING THE FILE"
BEFORE CREATING THE FILE
[Container] 2020/05/23 08:30:48 Running command cat /root/test_file & true
cat: /root/test_file: No such file or directory
[Container] 2020/05/23 08:30:48 Running command echo $(date -uIs) > /root/test_file
[Container] 2020/05/23 08:30:48 Running command echo "AFTER CREATING THE FILE"
AFTER CREATING THE FILE
[Container] 2020/05/23 08:30:48 Running command cat /root/test_file
2020-05-23T08:30:48,522977314+00:00
[Container] 2020/05/23 08:30:48 Phase complete: BUILD State: SUCCEEDED
[Container] 2020/05/23 08:30:48 Phase context status code:  Message: 
[Container] 2020/05/23 08:30:48 Entering phase POST_BUILD
[Container] 2020/05/23 08:30:48 Phase complete: POST_BUILD State: SUCCEEDED
[Container] 2020/05/23 08:30:48 Phase context status code:  Message: 
```

consequent run - same..

## With S3 caching of 'root/test_folder/**/*'

```bash
BEFORE CREATING THE FILE
[Container] 2020/05/23 09:07:02 Running command mkdir -p /root/test_folder
[Container] 2020/05/23 09:07:02 Running command cat /root/test_folder/test_file & true
cat: /root/test_folder/test_file: No such file or directory
[Container] 2020/05/23 09:07:02 Running command echo $(date -uIs) > /root/test_folder/test_file
[Container] 2020/05/23 09:07:02 Running command echo "AFTER CREATING THE FILE"
AFTER CREATING THE FILE
[Container] 2020/05/23 09:07:02 Running command cat /root/test_folder/test_file
2020-05-23T09:07:02,850185697+00:00
[Container] 2020/05/23 09:07:02 Phase complete: BUILD State: SUCCEEDED
[Container] 2020/05/23 09:07:02 Phase context status code:  Message: 
[Container] 2020/05/23 09:07:02 Entering phase POST_BUILD
[Container] 2020/05/23 09:07:02 Uploading S3 cache...
[Container] 2020/05/23 09:07:02 Phase complete: POST_BUILD State: SUCCEEDED
[Container] 2020/05/23 09:07:02 Phase context status code:  Message: 
```


second run

```bash
[Container] 2020/05/23 09:08:24 Running command echo "BEFORE CREATING THE FILE"
BEFORE CREATING THE FILE
[Container] 2020/05/23 09:08:24 Running command mkdir -p /root/test_folder
[Container] 2020/05/23 09:08:24 Running command cat /root/test_folder/test_file & true
2020-05-23T09:07:02,850185697+00:00
[Container] 2020/05/23 09:08:24 Running command echo $(date -uIs) > /root/test_folder/test_file
[Container] 2020/05/23 09:08:24 Running command echo "AFTER CREATING THE FILE"
AFTER CREATING THE FILE
[Container] 2020/05/23 09:08:24 Running command cat /root/test_folder/test_file
2020-05-23T09:08:24,357517919+00:00
[Container] 2020/05/23 09:08:24 Phase complete: BUILD State: SUCCEEDED
[Container] 2020/05/23 09:08:24 Phase context status code:  Message: 
[Container] 2020/05/23 09:08:24 Entering phase POST_BUILD
[Container] 2020/05/23 09:08:24 Uploading S3 cache...
[Container] 2020/05/23 09:08:24 Phase complete: POST_BUILD State: SUCCEEDED
[Container] 2020/05/23 09:08:24 Phase context status code:  Message: 
```


The s3 cache file is a zip containing the "detection" log 

```json
{"version":"1.0","content":{"files":[{"path":"test_folder/test_file","rel":"home"}]}}
```

and the file itself.


## With a builder image

### Create a separate CodeBuild for builder image

building the builder:

```bash
[Container] 2020/05/23 11:03:07 Entering phase INSTALL
[Container] 2020/05/23 11:03:07 Phase complete: INSTALL State: SUCCEEDED
[Container] 2020/05/23 11:03:07 Phase context status code:  Message: 
[Container] 2020/05/23 11:03:07 Entering phase PRE_BUILD
[Container] 2020/05/23 11:03:07 Running command echo Logging in to Amazon ECR...
Logging in to Amazon ECR...
[Container] 2020/05/23 11:03:07 Running command $(aws ecr get-login --no-include-email --region $AWS_DEFAULT_REGION)
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store
Login Succeeded
[Container] 2020/05/23 11:03:12 Phase complete: PRE_BUILD State: SUCCEEDED
[Container] 2020/05/23 11:03:12 Phase context status code:  Message: 
[Container] 2020/05/23 11:03:12 Entering phase BUILD
[Container] 2020/05/23 11:03:12 Running command echo "BEFORE CREATING THE FILE"
BEFORE CREATING THE FILE
[Container] 2020/05/23 11:03:13 Running command echo "FROM busybox" > Dockerfile
[Container] 2020/05/23 11:03:13 Running command echo "RUN mkdir -p /root/test_folder" >> Dockerfile
[Container] 2020/05/23 11:03:13 Running command echo "RUN echo \"FROM BUILDER IMAGE - $(date -uIs)\" > /root/test_folder/test_file"  >> Dockerfile
[Container] 2020/05/23 11:03:13 Running command cat Dockerfile
FROM busybox
RUN mkdir -p /root/test_folder
RUN echo "FROM BUILDER IMAGE - 2020-05-23T11:03:13+00:00" > /root/test_folder/test_file
[Container] 2020/05/23 11:03:13 Running command docker build -t $IMAGE_REPO_NAME .
Sending build context to Docker daemon  2.048kB
Step 1/3 : FROM busybox
latest: Pulling from library/busybox
d9cbbca60e5f: Pulling fs layer
d9cbbca60e5f: Download complete
d9cbbca60e5f: Pull complete
Digest: sha256:836945da1f3afe2cfff376d379852bbb82e0237cb2925d53a13f53d6e8a8c48c
Status: Downloaded newer image for busybox:latest
---> 78096d0a5478
Step 2/3 : RUN mkdir -p /root/test_folder
---> Running in 43b9df50ed91
Removing intermediate container 43b9df50ed91
---> 5c7f41999f66
Step 3/3 : RUN echo "FROM BUILDER IMAGE - 2020-05-23T11:03:13+00:00" > /root/test_folder/test_file
---> Running in c10180d601fa
Removing intermediate container c10180d601fa
---> a0b6c7aa9fb7
Successfully built a0b6c7aa9fb7
Successfully tagged builder-repository:latest
[Container] 2020/05/23 11:03:16 Running command docker tag $IMAGE_REPO_NAME:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:latest
[Container] 2020/05/23 11:03:16 Running command docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:latest
The push refers to repository [XXXXXXXXX.dkr.ecr.us-east-1.amazonaws.com/builder-repository]
742b6731d4b4: Preparing
739b37f6f5ac: Preparing
1079c30efc82: Preparing
1079c30efc82: Layer already exists
739b37f6f5ac: Pushed
742b6731d4b4: Pushed
latest: digest: sha256:87ca72fb5c9a3035be77eb89447a4b7d0cbe929bb1f4bd7ce8e2e54aca49cb03 size: 941
[Container] 2020/05/23 11:03:17 Phase complete: BUILD State: SUCCEEDED
[Container] 2020/05/23 11:03:17 Phase context status code:  Message: 
[Container] 2020/05/23 11:03:17 Entering phase POST_BUILD
```

and now the actual job:

```bash
[Container] 2020/05/23 11:06:43 Entering phase INSTALL
[Container] 2020/05/23 11:06:43 Phase complete: INSTALL State: SUCCEEDED
[Container] 2020/05/23 11:06:43 Phase context status code:  Message: 
[Container] 2020/05/23 11:06:43 Entering phase PRE_BUILD
[Container] 2020/05/23 11:06:43 Phase complete: PRE_BUILD State: SUCCEEDED
[Container] 2020/05/23 11:06:43 Phase context status code:  Message: 
[Container] 2020/05/23 11:06:43 Entering phase BUILD
[Container] 2020/05/23 11:06:43 Running command echo "BEFORE CREATING THE FILE"
BEFORE CREATING THE FILE
[Container] 2020/05/23 11:06:43 Running command mkdir -p /root/test_folder
[Container] 2020/05/23 11:06:43 Running command cat /root/test_folder/test_file & true
FROM BUILDER IMAGE - 2020-05-23T11:03:13+00:00
[Container] 2020/05/23 11:06:43 Running command echo "AFTER CREATING THE FILE"
AFTER CREATING THE FILE
[Container] 2020/05/23 11:06:43 Running command cat /root/test_folder/test_file
2020-05-23T11:06:43UTC
[Container] 2020/05/23 11:06:43 Phase complete: BUILD State: SUCCEEDED
[Container] 2020/05/23 11:06:43 Phase context status code:  Message: 
[Container] 2020/05/23 11:06:43 Entering phase POST_BUILD
[Container] 2020/05/23 11:06:43 Phase complete: POST_BUILD State: SUCCEEDED
[Container] 2020/05/23 11:06:43 Phase context status code:  Message: 
```