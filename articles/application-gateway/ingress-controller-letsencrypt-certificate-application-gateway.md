---
title: 将 LetsEncrypt.org 证书与应用程序网关配合使用
description: 本文介绍如何从 LetsEncrypt.org 获取证书并将其用在 AKS 群集的应用程序网关上。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: df8722e8160538daa1535711092790dbb2405097
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807035"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>将 LetsEncrypt.org 的证书用在 AKS 群集的应用程序网关上

此部分配置 AKS，以便利用 [LetsEncrypt.org](https://letsencrypt.org/) 并自动获取域的 TLS/SSL 证书。 该证书将安装在应用程序网关上，后者会针对 AKS 群集执行 SSL/TLS 终止操作。 此处介绍的设置使用 [cert-manager](https://github.com/jetstack/cert-manager) Kubernetes 加载项，该加载项可自动创建并管理证书。

请按以下步骤在现有的 AKS 群集上安装 [cert-manager](https://docs.cert-manager.io)。

1. Helm Chart

    请运行以下脚本来安装 `cert-manager` Helm Chart。 这会：

    - 在 AKS 上创建新的 `cert-manager` 命名空间
    - 创建以下 CRD：证书、质询、ClusterIssuer、颁发者、顺序
    - 安装 cert-manager Chart（来自 [docs.cert-manager.io](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)）

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

    请创建 `ClusterIssuer` 资源。 它是 `cert-manager` 所需的，用于表示 `Lets Encrypt` 证书颁发机构（将从其中获取签名的证书）。

    cert-manager 会使用不带命名空间的 `ClusterIssuer` 资源颁发可以通过多个命名空间使用的证书。 `Let’s Encrypt` 使用 ACME 协议来验证你是否控制给定的域名，以及向你颁发证书。 [此处](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html)提供有关如何配置 `ClusterIssuer` 属性的更多详细信息。 `ClusterIssuer` 会指示 `cert-manager` 使用 `Lets Encrypt` 过渡环境来颁发证书，该过渡环境用于测试（不存在于浏览器/客户端信任存储中的根证书）。

    在下面的 YAML 中，默认的质询类型为 `http01`。 其他质询记录在 [letsencrypt.org - 验证方式](https://letsencrypt.org/docs/challenge-types/) 中

    > [!IMPORTANT] 
    > 在下面的 YAML 中更新 `<YOUR.EMAIL@ADDRESS>`

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

    请创建一项入口资源，以便使用应用程序网关和 Lets Encrypt 证书来公开 `guestbook` 应用程序。

    确保应用程序网关有一个带 DNS 名称的公共前端 IP 配置（可以使用默认的 `azure.com` 域，也可以先预配 `Azure DNS Zone` 服务，然后分配你自己的自定义域）。
    注意注释 `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`，它告知 cert-manager 处理标记的入口资源。

    > [!IMPORTANT] 
    > `<PLACEHOLDERS.COM>`在下面的 YAML 中将更新为您自己的域 (或应用程序网关一，例如 "kh-aks-ingress.westeurope.cloudapp.azure.com" ) 

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

    几秒钟后，可以使用自动颁发的**暂存** `Lets Encrypt` 证书通过应用程序网关 HTTPS URL 访问 `guestbook` 服务。
    浏览器可能会警告你：证书颁发机构无效。 暂存证书由 `CN=Fake LE Intermediate X1` 颁发。 这表示系统的运行符合预期，你可以获取生产证书了。

4. 生产证书

    成功设置暂存证书以后，即可切换到生产 ACME 服务器：
    1. 将入口资源上的暂存注释替换为：`certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. 将上面的 ClusterIssuer YAML 中的 ACME 服务器替换为 `https://acme-v02.api.letsencrypt.org/directory`，以便删除在上一步创建的现有暂存 `ClusterIssuer` 并创建一个新的

5. 证书过期和续订

    在 `Lets Encrypt` 证书过期之前，`cert-manager` 会自动更新 Kubernetes 机密存储中的证书。 那时候，应用程序网关入口控制器会应用在入口资源中引用的已更新机密（用于配置应用程序网关）。
