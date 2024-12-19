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
                  image: curlimages/curl:latest
                  command:
                    - /bin/sh
                    - -c
                    - |
                      echo "hey"
              restartPolicy: OnFailure
```

## Example ArgoCD App Manifest

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: cronjob-nightly
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/hepapi/helm-gen-objects-from-values.git  
    targetRevision: HEAD  
    path: gen-objects-from-values  
    helm:
      valuesObject:
        generateObjects:
          - apiVersion: batch/v1
            kind: CronJob
            metadata:
              name: update-cronjob
              namespace: argocd
            spec:
              timeZone: 'Asia/Istanbul'
              schedule: "51 14 * * *"
              jobTemplate:
                spec:
                  template:
                    spec:
                      containers:
                        - name: curl
                          image: curlimages/curl:latest
                          command:
                            - /bin/sh
                            - -c
                            - |
                              echo "hey"
                      restartPolicy: OnFailure

  destination:
    name: in-cluster
    namespace: argocd
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```