Simple Flask Webapp Deployment
======================

# 1. Introduction
simple-flask-webapp 프로젝트로부터 빌드된 Container Image를 Kubernetes에 배포하는 프로젝트입니다.  
kustomize를 사용하여 다양한 환경을 쉽게 배포할 수 있게 구성하였으며, Argo CD를 통한 GitOps 방식의 Continuous Deployment 역시 가능합니다.
***
# 2. How to Deploy
## Manual
프로젝트 루트 디렉토리에서 아래 명령어를 실행합니다. 예를 들어 dev 환경을 사용하여 배포할 경우,
``` bash
kustomize build overlays/dev | kubectl apply -f -
```
Image 이름 또는 태그 변경시, overlays/<env> 디렉토리의 kustomization.yaml 파일 내용 중 images 항목을 수정합니다.  
또는 해당 디렉토리에서 아래와 같은 명령어를 사용할 수 있습니다.
``` bash
kustomize edit set image simple-flask-webapp=arm7tdmi/simple-flask-webapp:latest
```
## Argo CD
Argo CD의 Application URL로 본 프로젝트 Repository를 선택합니다. Path는 overlays 디렉토리에서 원하는 환경을 지정합니다(ex: overlays/dev).  
다음은 Application Manifest 예시입니다.
``` yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: simple-flask-webapp
spec:
  destination:
    name: k8s-demo-spoke-dev-workload-cluster
    namespace: default
    server: ''
  source:
    path: overlays/dev
    repoURL: 'https://github.com/cure4itches/simple-flask-webapp-deployment.git'
    targetRevision: HEAD
  project: default
```
Image 태그 변경 후 Repository에 반영시, Argo CD Application이 Out-of-Sync 상태로 바뀌면서 수동 또는 Auto 방식의 Sync를 수행할 수 있습니다.
***
