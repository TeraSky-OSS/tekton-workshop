# Tekton Pipelines Workshop

## Prerequisites:
### 1. Install `Tekton Pipelines` on your cluster
Install the relevant `Tekton Pipelines` CRDs.
This should be done once per cluster and not once per user.
```bash
kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
```

## Steps:
### 1. Install `tkn` CLI
```bash
# Get the tar.xz
curl -LO https://github.com/tektoncd/cli/releases/download/v0.34.0/tkn_0.34.0_Linux_x86_64.tar.gz
# Extract tkn to your PATH (e.g. /usr/local/bin)
sudo tar xvzf tkn_0.34.0_Linux_x86_64.tar.gz -C /usr/local/bin/ tkn
```

### 2. View `Tekton` resources
We will deploy several `Tekton` resources:
```bash
# Inspect Tekton resources
cat tasks.yaml
cat pipeline.yaml
cat pipelinerun.yaml
```
- `git-clone-task`: Clones a Git repository from the specified `repo-url` parameter.
- `maven-unit-test-task`: Runs maven unit tests on the specified `source-path` parameter.
- `git-clone-and-test-pipeline`: Clones a Git repository and runs maven unit tests on the source code.
- `git-clone-and-test-pipeline-run`: Runtime configuration for running `git-clone-and-test-pipeline`.

Both `Pipeline` and `Task` resources are used as templates and specify parameters required for running those templates.
`PipelineRun` resource is the runtime configuration for running a pipeline.

### 3. Deploy `Task`s and `Pipeline` resources
```bash
# Deploy Tasks and Pipeline resources
kubectl apply -f tasks.yaml -f pipeline.yaml 

# View pipeline resource
tkn pipeline describe git-clone-and-test-pipeline
kubectl describe pipeline git-clone-and-test-pipeline

# View git clone task resource
tkn task describe git-clone-task
kubectl describe task git-clone-task

# View maven unit tests task resource
tkn task describe maven-unit-test-task
kubectl describe task maven-unit-test-task
```

### 4. Create a `PipelineRun` resource
```bash
# Deploy Pipeline resource
kubectl apply -f pipelinerun.yaml

# View pipelinerun resource
tkn pipelinerun describe git-clone-and-test-pipeline-run

# Stream pipelinerun logs
tkn pipelinerun logs git-clone-and-test-pipeline-run

# Running the pipeline resulted in `TaskRun` resources to run the specified `Tasks`
tkn taskrun describe git-clone-and-test-pipeline-run-git-clone
tkn taskrun logs git-clone-and-test-pipeline-run-git-clone

tkn taskrun describe git-clone-and-test-pipeline-run-maven-unit-test
tkn taskrun logs git-clone-and-test-pipeline-run-maven-unit-test

# All containers running on pods
kubectl get pods
```

### 5. Clean up
```bash
kubectl delete -f pipelinerun.yaml -f pipeline.yaml -f tasks.yaml
```