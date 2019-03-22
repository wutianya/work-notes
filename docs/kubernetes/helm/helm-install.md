# Helm install

## 安装helm客户端

> 版本 v2.10.0

```docker run --rm -v /tmp:/systembindir --entrypoint /bin/cp lachlanevenson/k8s-helm:v2.10.0 -f /usr/local/bin/helm /systembindir/helm```

## 创建tiller的sa

```kubectl create serviceaccount tiller --namespace=kube-system```

## 创建clusterrolebinding

```kubectl create clusterrolebinding tiller-cluster --clusterrole=cluster-admin --serviceaccount=kube-system:tiller```

## 安装服务端tiller(使用阿里云镜像)

```helm init --history-max 10 --service-account tiller -i registry.cn-hangzhou.aliyuncs.com/google_containers/tiller:v2.10.0 --stable-repo-url  https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts```

## 验证

```helm version```

## 安装补全

```helm completion bash &> /etc/bash_completion.d/helm.sh```

> 记录时间: 2019年 03月 20日 星期三 17:41:03 CST