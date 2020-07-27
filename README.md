# Overview
![image](img/overview.JPG?raw=true "Overview") <br />
<br />
## 使用說明

### 1. 創建一個 pvc 存放 mavem repository
$ kubectl apply -f maven-repo-pvc.yaml <br />
### 2. 創建一個 cm 做為 Dockerfile
$ kubectl apply -f dockerfile-cm.yaml <br />
### 3. 創建 git pipelineresource, source code 來源
$ kubectl apply -f appgit-pipelineresource.yaml <br />
#### 設定url參數:
- name: url <br />
  value: https://github.com/spring-projects/spring-petclinic <br />

### 4. 創建 image pipelineresource, image 推送位置
$ kubectl apply -f appimage-pipelineresource.yaml <br />
#### 設定url參數:
- name: url <br />
  value: bastion.ks.example.com:5000/tekton/java:v1 <br />

### 4. 創建 task
$ kubectl apply -f maven-task.yaml <br />
### 5. 創建 pipeline
$ kubectl apply -f maven-pipeline.yaml <br />
### 6. 執行 pipeline
$ kubectl apply -f maven-pipelinerun.yaml <br />
#### 範例:
apiVersion: tekton.dev/v1beta1 <br />
kind: PipelineRun <br />
metadata: <br />
  name: maven-build-run-0 #pipeline 執行名稱 <br />
spec: <br />
  pipelineRef: <br />
    name: maven-build <br />
  resources: <br />
  - name: app-git <br />
    resourceRef: <br />
      name: app-git #git pipelineresource 名稱 <br />
  - name: app-image <br />
    resourceRef: <br />
      name: app-image #image pipelineresource 名稱 <br />
  workspaces: <br />
  - name: local-maven-repo <br />
    persistentVolumeClaim: <br />
      claimName: maven-repo-pvc #mavem repository 的 pvc <br />
  - name: local-dockerfile <br />
    configMap: <br />
      name: dockerfile #Dockerfile 的 configmap <br />
### 6. 拉取image進行測試
$ docker run -it --rm bastion.ks.example.com:5000/tekton/java:v1