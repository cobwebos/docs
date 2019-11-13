---
title: 将 LetsEncrypt.org 证书用于应用程序网关
description: 本文介绍了如何从 LetsEncrypt.org 获取证书，并将其用于 AKS 群集的应用程序网关。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 92e9747865f1a0910c8bae4001cc597ae9ea3da6
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73957983"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>在应用程序网关上使用带有 LetsEncrypt.org 的证书 AKS 群集

本部分将 AKS 配置为使用[LetsEncrypt.org](https://letsencrypt.org/) ，并自动获得域的 TLS/SSL 证书。 证书将安装在应用程序网关上，这将对 AKS 群集执行 SSL/TLS 终止。 此处所述的设置使用[cert-manager](https://github.com/jetstack/cert-manager) Kubernetes 外接程序，这将自动创建和管理证书。

按照以下步骤在现有 AKS 群集上安装[证书管理器](https://docs.cert-manager.io)。

1. Helm 图

    运行以下脚本以安装 `cert-manager` helm 图表。 这会：

    - 在 AKS 上创建新的 `cert-manager` 命名空间
    - 创建以下 CRDs： Certificate、质询、ClusterIssuer、Issuer、Order
    - 安装证书管理器图表（来自[docs.cert-manager.io）](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. ClusterIssuer 资源

    创建 `ClusterIssuer` 资源。 `cert-manager` 表示获取已签名证书的 `Lets Encrypt` 证书颁发机构，这是必需的。

    通过使用非带命名空间 `ClusterIssuer` 资源，证书管理器将颁发可从多个命名空间中使用的证书。 `Let’s Encrypt` 使用 ACME 协议来验证你是否控制给定的域名并向你颁发证书。 有关在[此处](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html)配置 `ClusterIssuer` 属性的更多详细信息。 `ClusterIssuer` 将指示 `cert-manager` 使用用于测试的 `Lets Encrypt` 过渡环境（浏览器/客户端信任存储区中不存在根证书）颁发证书。

    以下 YAML 中的默认质询类型为 `http01`。 其他难题记录在[letsencrypt.org-挑战类型](https://letsencrypt.org/docs/challenge-types/)上

    > [!IMPORTANT] 
    > 更新以下 YAML 中的 `<YOUR.EMAIL@ADDRESS>`

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. 部署应用

    创建入口资源，以使用应用程序网关提供 "允许加密证书" `guestbook` 应用程序。

    确保应用程序网关具有具有 DNS 名称的公共前端 IP 配置（使用默认的 `azure.com` 域，或预配 `Azure DNS Zone` 服务，并分配你自己的自定义域）。
    请注意批注 `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`，它会告知 cert 管理器处理标记的入口资源。

    > [!IMPORTANT] 
    > 将以下 YAML 中的 `<PLACEHOLDERS.COM>` 更新为你自己的域（或应用程序网关，例如 "kh-aks-ingress.westeurope.cloudapp.azure.com"）

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    几秒钟后，可以通过应用程序网关 HTTPS url 使用自动颁发的**过渡**`Lets Encrypt` 证书来访问 `guestbook` 服务。
    你的浏览器可能会警告你无效的证书颁发机构。 暂存证书由 `CN=Fake LE Intermediate X1`颁发。 这表明系统按预期方式工作，并且你已准备好使用生产证书。

4. 生产证书

    成功设置暂存证书后，可以切换到生产 ACME 服务器：
    1. 用以下内容替换入口资源上的暂存批注： `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. 删除你在上一步中创建的现有暂存 `ClusterIssuer`，并通过将上面的 ClusterIssuer YAML 中的 ACME 服务器替换为 `https://acme-v02.api.letsencrypt.org/directory` 来创建一个新的过渡服务器。

5. 证书过期和续订

    在 `Lets Encrypt` 证书过期之前，`cert-manager` 将自动更新 Kubernetes 密钥存储中的证书。 此时，应用程序网关入口控制器将应用它用于配置应用程序网关的入口资源中引用的更新密钥。
