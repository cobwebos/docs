---
title: "结合使用 Draft 与 Azure 容器服务和 Azure 容器注册表 | Microsoft Docs"
description: "创建 ACS Kubernetes 群集和 Azure 容器注册表，使用 Draft 在 Azure 中创建首个应用程序。"
services: container-service
documentationcenter: 
author: squillace
manager: gamonroy
editor: 
tags: draft, helm, acs, azure-container-service
keywords: "Docker, 容器, 微服务, Kubernetes, Draft, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: rasquill
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: b70d2340c0f1286fa355a78a4cd0cb1ce37cbc39
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---

# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>结合使用 Draft 与 Azure 容器服务和 Azure 容器注册表，生成应用程序并将其部署到 Kubernetes

[Draft](https://aka.ms/draft) 是一种新的开放源工具，使用它可轻松地开发基于容器的应用程序并将其部署到 Kubernetes 群集，而无需了解大量有关 Docker 和 Kubernetes 的信息，甚至无需安装它们。 使用 Draft 之类的工具，你和你的团队可专注于使用 Kubernetes 生成应用程序，而不用太过关注基础结构。

可将 Draft 与任何 Docker 映像注册表以及任何 Kubernetes 群集一起使用（在本地也可）。 本教程演示如何结合使用 ACS、Kubernetes、ACR 和 Azure DNS，通过 Draft 创建实时 CI/CD 开发人员管道。


## <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表
可以轻松[创建新的 Azure 容器注册表](../../container-registry/container-registry-get-started-azure-cli.md)，步骤如下：

1. 创建 Azure 资源组，在 ACS 中管理 ACR 注册表和 Kubernetes 群集。
      ```azurecli
      az group create --name draft --location eastus
      ```

2. 使用 [az acr create](/cli/azure/acr#create) 创建 ACR 映像注册表
      ```azurecli
      az acr create -g draft -n draftacs --sku Basic --admin-enabled true -l eastus
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>使用 Kubernetes 创建 Azure 容器服务

现已准备好使用 [az acs create](/cli/azure/acs#create) 创建 ACS 群集，使用 Kubernetes 作为 `--orchestrator-type` 值。
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes
```

> [!NOTE]
> 由于 Kubernetes 不是默认 orchestrator 类型，因此请确保使用 `--orchestrator-type kubernetes` 开关。

成功后输出与下面类似。

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

现在有了一个群集，可以使用 [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) 命令导入凭据。 现在群集有了本地配置文件，Helm 和 Draft 需要它来完成工作。

## <a name="install-and-configure-draft"></a>安装并配置 Draft
[Draft 存储库](https://github.com/Azure/draft/blob/master/docs/install.md)中提供 Draft 的安装说明。 安装相对简单，但需要一些配置，因为它依赖于 [Helm](https://aka.ms/helm) 来创建 Helm 图表并将图表部署到 Kubernetes 群集。

1. [下载并安装 Helm](https://aka.ms/helm#install)。
2. 使用 Helm 搜索并安装 `stable/traefik` 和入口控制器，为生成启用入站请求。
    ```bash
    $ helm search traefik
    NAME            VERSION DESCRIPTION
    stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

    $ helm install stable/traefik --name ingress
    ```
    现对 `ingress` 控制器设置监视，在部署时捕获外部 IP 值。 此 IP 地址将是下一节中[映射到部署域](#wire-up-deployment-domain)的地址。

    ```bash
    kubectl get svc -w
    NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
    ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
    kubernetes                    10.0.0.1       <none>          443/TCP                      7h
    ```

    在这种情况下，部署域的外部 IP 为 `13.64.108.240`。 现在，可以将域映射到该 IP。

## <a name="wire-up-deployment-domain"></a>连接部署域

Draft 每创建一个 Helm 图表（即每个正在处理的应用程序），就为其创建一个版本。 每个图表获得一个生成的名称，该名称由 Draft 用作所控制的根部署域之上的子域。 （此示例中，我们使用 `squillace.io` 作为部署域。）若要启用这一子域行为，必须在部署域的 DNS 条目中为 `'*'` 创建一个 A 记录，使每个生成的子域都路由到 Kubernetes 群集的入口控制器。

域提供商有其自己的方法来分配 DNS 服务器；若要[将域的名称服务器委托给 Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)，请执行以下步骤：

1. 为区域创建资源组。
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. 为域创建 DNS 区域。
使用 [az network dns zone create](/cli/azure/network/dns/zone#create) 命令获取名称服务器，为域将 DNS 控件委托给 Azure DNS。
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. 将提供的 DNS 服务器添加到部署域的域提供商，通过此可使用 Azure DNS 根据需要重新定位域。
4. 为上一节步骤 2 中映射到 `ingress` IP 的部署域创建 A 记录集条目。
    ```azurecli
    az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*' -g squillace.io -z squillace.io
    ```
输出与下面类似：
    ```json
    {
      "arecords": [
        {
          "ipv4Address": "13.64.108.240"
        }
      ],
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
      "metadata": null,
      "name": "*",
      "resourceGroup": "squillace.io",
      "ttl": 3600,
      "type": "Microsoft.Network/dnszones/A"
    }
    ```

5. 配置 Draft 来使用注册表并为所创建的每个 Helm 图表创建子域。 若要配置 Draft，需要：
  - Azure 容器注册表名称（此示例中为 `draft`）
  - `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"` 中的注册表项或密码。
  - 已配置为映射到 Kubernetes 入口外部 IP 地址的根部署域（此处为 `squillace.io`）

  调用 `draft init`，然后配置进程会提示你输入上述值。 该进程会在你首次运行时，要求你提供如下所示的信息。
 ```bash
    $ draft init
    Creating pack ruby...
    Creating pack node...
    Creating pack gradle...
    Creating pack maven...
    Creating pack php...
    Creating pack python...
    Creating pack dotnetcore...
    Creating pack golang...
    $DRAFT_HOME has been configured at /Users/ralphsquillace/.draft.

    In order to install Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io, quay.io, myregistry.azurecr.io): draft.azurecr.io
    2. Enter your username: draft
    3. Enter your password:
    4. Enter your org where Draft will push images [draft]: draft
    5. Enter your top-level domain for ingress (e.g. draft.example.com): squillace.io
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
    ```

现已准备好部署应用程序。


## <a name="build-and-deploy-an-application"></a>生成和部署应用程序

在 Draft 中，存储库是 [6 个简单的示例应用程序](https://github.com/Azure/draft/tree/master/examples)。 克隆存储库并使用 [Python 示例](https://github.com/Azure/draft/tree/master/examples/python)。 更改为 examples/Python 目录，并键入 `draft create` 来生成应用程序。 结果应与以下示例类似。
```bash
$ draft create
--> Python app detected
--> Ready to sail
```

输出包括一个 Dockerfile 和一个 Helm 图表。 若要生成并部署，只需键入 `draft up`。 输出的内容很多，但开头类似于以下示例。
```bash
$ draft up
--> Building Dockerfile
Step 1 : FROM python:onbuild
onbuild: Pulling from library/python
10a267c67f42: Pulling fs layer
fb5937da9414: Pulling fs layer
9021b2326a1e: Pulling fs layer
dbed9b09434e: Pulling fs layer
ea8a37f15161: Pulling fs layer
<snip>
```

成功后，结尾类似于以下示例的内容。
```bash
ab68189731eb: Pushed
53c0ab0341bee12d01be3d3c192fbd63562af7f1: digest: sha256:bb0450ec37acf67ed461c1512ef21f58a500ff9326ce3ec623ce1e4427df9765 size: 2841
--> Deploying to Kubernetes
--> Status: DEPLOYED
--> Notes:

  http://gangly-bronco.squillace.io to access your application

Watching local files for changes...
```

无论图表的名称是什么，现都可以 `curl http://gangly-bronco.squillace.io`接收回复 `Hello World!`。

## <a name="next-steps"></a>后续步骤

现在有了 ACS Kubernetes 群集，可以使用 [Azure 容器注册表](../../container-registry/container-registry-intro.md)进行研究，创建此种方案的更多不同部署。 例如，可以创建 draft.basedomain.toplevel 域 DNS 记录集，控制特定 ACS 部署的更深子域中的内容。







