# metallb lab

オンプレミズKubernetsクラスタにmetallbロードバランサーをセットアップする。

[参考サイト](https://kubernetes.github.io/ingress-nginx/deploy/baremetal/)

![構成図](./images/metallb.jpg)

## 手順

### 1. メタファイルをダウンロード

```
git clonne https://github.com/ntnx-huimin/metallb.git
```

### 2. metallbのデプロイ

```bash
cd 1.metallb/
kubectl apply -f namespace.yaml
kubectl applhy -f metallb.yaml
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
```
### 3. IP Poolを設定

layer2-config.yamlにLB用IPアドレスのプールを設定する
![IP Pool設定](./images/layer2-config_IP_pools.png)

```
kubectl applhy -f layer2-config.yaml
```
![metallbのデプロイ結果](./images/result-metallb.png)

### 4. nginx ingress controllerのデプロイ

サービス「ingress-nginx-controller」の種類を「LoadBalancer」に変更

![ingress-nginx-controller」の種類を「LoadBalancer」に変更](./images/ingress-nginx-change-service-type.png)

```
cd ../2.ingress-nginx
kubectl apply -f nginx-ingress/deploy.yaml
kubectl get all -n ingress-nginx
```
![nginx ingress controllerのデプロイ結果](./images/result-ingress-nginx.png)

### 5. 確認用ワークロードをデプロイ

```
cd ../3.test/
kubectl apply -f test-nginx.yaml test-nginx.yaml
kubectl apply -f test-ingress-resource.yaml
```

### 確認

```
kubectl get all
```

