# kind

kindでkubernetesクラスタを作成し、deployment,service,ingressを導入して外部からアクセスできるまでのサンプル

## install kubectl(for mac)

for kubernetes v1.19

```
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.19.0/bin/darwin/amd64/kubectl
$ chmod 755 kubectl
$ mv kubectl /usr/local/bin/.
```

## install kind(for mac)

```
$ brew install kind
```

## create cluster

cluster name = kind (default)

```
$ cat <<EOF | kind create cluster --config=-
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
EOF
```

## create ingress(nginx ingress)

```
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml
```

## sample app (nginx)

```
$ kubectl apply -f deployment.yaml
$ kubectl apply -f service.yaml
```

## clean up

```
$ kind delete cluster
```
