### Github-notifier is an operator that watches taskRuns and reports the results back to Github.

#### Usage
The operator watches all taskRuns on a cluster. When a taskRun completes,
it parses the annotations looking for the repository and commit sha.
This information is used to update the commit status with the taskRun results.

Example annotations would be `github.integrations.tekton.dev/commit` and
`github.integrations.tekton.dev/repo-url`. Below is an example TriggerTemplate
that triggers from a pull request.

```yaml
apiVersion: triggers.tekton.dev/v1alpha1
kind: TriggerTemplate
metadata:
  name: gitops-service-cluster-agent
spec:
  params:
    - name: pullreq-sha
    - name: git-repo-url
  resourcetemplates:
    - apiVersion: tekton.dev/v1beta1
      kind: PipelineRun
      metadata:
        generateName: gitops-service-cluster-agent-
        annotations:
          github.integrations.tekton.dev/commit: $(tt.params.pullreq-sha)
          github.integrations.tekton.dev/repo-url: $(tt.params.git-repo-url)
      spec:
        pipelineRef:
          name: basic-build
          bundle: quay.io/redhat-appstudio/build-templates-bundle:latest
```
