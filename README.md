# helm-gen-objects-from-values

Generates each Kubernetes object in the `generateObjects` in values.yaml as a helm chart.

Useful for generating ad-hoc manifests using helm chart — can be used in ArgoCD.

## Example values.yaml

```yaml
generateObjects:
  - apiVersion: batch/v1
    kind: CronJob
    metadata:
      name: test1
    spec:
      schedule: "00 20 * * 1-5"
      jobTemplate:
        spec:
          template:
            spec:
              containers:
                - name: abc
                  image: alpine/curl:latest
                  command:
                    - /bin/bash
                    - -c
                    - |
                      echo "hey"
              restartPolicy: OnFailure
```
