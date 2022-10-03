# Kubernetes
## Error : ImagePullBackOff

### Case
Pod is not up on minikube

### How To Check
```
kubectl get pod [pod-name]
```

If the status is `ImagePullBackOff`, then it might because slow internet, and minikube got timeout

### How To Solve
```
kubectl describe pod [pod-name]
```

Find error message like this (example)

```
Back-off pulling image "jenkins/jenkins:2.346.3-jdk11"
```

SSH to minikube

```
minikube ssh
```

Pull docker image manually from ssh (example)

```
docker pull jenkins/jenkins:2.346.3-jdk11
```

