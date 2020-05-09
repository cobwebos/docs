---
title: （已弃用）将 Draft 与 Azure 容器服务及 Azure 容器注册表配合使用
description: 创建 ACS Kubernetes 群集和 Azure 容器注册表，使用 Draft 在 Azure 中创建首个应用程序。
author: squillace
ms.service: container-service
ms.topic: conceptual
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 039879cb725c20af3c40698947ea9abe189d08ad
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731757"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>（已弃用）结合使用 Draft 与 Azure 容器服务和 Azure 容器注册表，生成应用程序并将其部署到 Kubernetes

> [!TIP]
> 有关本文中使用 Azure Kubernetes 服务的更新版本，请参阅 [结合使用 Draft 与 Azure Kubernetes 服务 (AKS)](../../aks/kubernetes-draft.md)。

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Draft](https://aka.ms/draft) 是一种新的开放源工具，使用它可轻松地开发基于容器的应用程序并将其部署到 Kubernetes 群集，而无需了解大量有关 Docker 和 Kubernetes 的信息，甚至无需安装它们。 使用 Draft 之类的工具，你和你的团队可专注于使用 Kubernetes 生成应用程序，而不用太过关注基础结构。

可将 Draft 与任何 Docker 映像注册表以及任何 Kubernetes 群集一起使用（在本地也可）。 本教程演示如何搭配 ACS 使用 Kubernetes 和 ACR，在 Kubernetes 中通过 Draft 创建实时但安全的开发人员管道，以及如何使用 Azure DNS 公开该开发人员管道，让其他人可以在域中看到。


## <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表
可以轻松[创建新的 Azure 容器注册表](../../container-registry/container-registry-get-started-azure-cli.md)，步骤如下：

1. 创建 Azure 资源组来管理你的 ACR 注册表和 ACS 中的 Kubernetes 群集。
      ```azurecli
      az group create --name draft --location eastus
      ```

2. 使用 [az acr create](/cli/azure/acr#az-acr-create) 创建 ACR 映像注册表，并确保 `--admin-enabled` 选项设置为 `true`。
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>使用 Kubernetes 创建 Azure 容器服务

现已准备好使用 [az acs create](/cli/azure/acs#az-acs-create) 创建 ACS 群集，使用 Kubernetes 作为 `--orchestrator-type` 值。
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
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

现在有了一个群集，可以使用 [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes) 命令导入凭据。 现在群集有了本地配置文件，Helm 和 Draft 需要它来完成工作。

## <a name="install-and-configure-draft"></a>安装并配置 Draft


1. 在 https://github.com/Azure/draft/releases 为环境下载 Draft 并安装到路径中，以便可使用此命令。
2. 在 https://github.com/kubernetes/helm/releases 为环境下载 Helm 并[安装到路径中，以便可使用此命令](https://helm.sh/docs/intro/quickstart#install-helm)。
3. 配置 Draft 来使用注册表并为所创建的每个 Helm 图表创建子域。 若要配置 Draft，需要：
   - Azure 容器注册表名称（此示例中为 `draftacsdemo`）
   - `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"` 中的注册表项或密码。

   调用 `draft init`，配置过程会提示用户输入上面的值；请注意，注册表 URL 的 URL 格式是注册表名称（本例中为 `draftacsdemo`）加上 `.azurecr.io`。 用户名是注册表自身的名称。 该进程会在你首次运行时，要求你提供如下所示的信息。
   ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
   ```

现已准备好部署应用程序。


## <a name="build-and-deploy-an-application"></a>生成和部署应用程序

在 Draft 中，存储库是 [6 个简单的示例应用程序](https://github.com/Azure/draft/tree/master/examples)。 克隆存储库并使用 [Java 示例](https://github.com/Azure/draft/tree/master/examples/example-java)。 切换为 examples/java 目录，并键入 `draft create` 生成应用程序。 结果应与以下示例类似。
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

输出包括一个 Dockerfile 和一个 Helm 图表。 若要生成并部署，只需键入 `draft up`。 输出的内容很广泛，但应当类似于以下示例。
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>安全查看应用程序

容器现在在 ACS 中运行。 若要查看它，请使用 `draft connect` 命令，与具有面向应用程序的特定端口的群集 IP 建立安全连接，以便在本地查看。 如果成功，则在 **SUCCESS** 指示符后面的第一行上查找用于连接到应用的 URL。

> [!NOTE]
> 如果收到一条消息，指出没有已准备就绪的 Pod，则等一会再重试，也可以一边使用 `kubectl get pods -w` 准备 Pod 一边观察，等 Pod 就绪后再重试。

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

在前面的示例中，可以键入 `curl -s http://localhost:46143`，会收到回复 `Hello World, I'm Java!`。 按 CTRL+ 或 CMD+C（取决于 OS 环境）时，安全隧道被撤销，用户可以继续循环访问。

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>使用 Azure DNS 配置部署域来共享应用程序

你已执行前面步骤中 Draft 创建的开发人员迭代循环。 但是，可以通过以下方式在 Internet 上共享应用程序：
1. 在 ACS 群集中安装入口（以提供用于显示应用的公共 IP 地址）
2. 将自定义域委托给 Azure DNS 并将域映射到 ACS 分配给入口控制器的 IP 地址

### <a name="use-helm-to-install-the-ingress-controller"></a>使用 Helm 安装入口控制器。
使用 **Helm** 搜索并安装 `stable/traefik`（一种入口控制器），为生成启用入站请求。
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
现对 `ingress` 控制器设置监视，在部署时捕获外部 IP 值。 此 IP 地址将是下一节中映射到部署域的地址。

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

在这种情况下，部署域的外部 IP 为 `13.64.108.240`。 现在，可以将域映射到该 IP。

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>将入口 IP 映射到自定义子域

Draft 每创建一个 Helm 图表（即每个正在处理的应用程序），就为其创建一个版本。 每个帐户都将获取一个生成的名称，该名称由**草稿**用作所控制的根_部署域_顶层的_子域_。 （在此示例中，我们`squillace.io`使用作为部署域。）若要启用此子域行为，必须`'*.draft'`在部署域的 DNS 条目中创建 A 记录，以便每个生成的子域都路由到 Kubernetes 群集的入口控制器。 

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
   使用 [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) 命令获取名称服务器，为域将 DNS 控件委托给 Azure DNS。
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
3. 将提供的 DNS 服务器添加到部署域的域提供商，通过此可使用 Azure DNS 根据需要重新定位域。 执行此操作的方式因所提供域的不同而不同；[将域名服务器委托给 Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) 包含一些应当了解的详细信息。 
4. 将域委托给 Azure DNS 之后，就会为映射到前一部分步骤 2 中的 `ingress` IP 的部署域创建一个 A 记录集条目。
   ```azurecli
   az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
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
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
   }
   ```
5. 重新安装 **Draft**

   1. 通过键入 `helm delete --purge draft` 从群集中删除 **draftd**。 
   2. 使用相同的 `draft-init` 命令重新安装 **Draft**，唯一不同的是该命令带有 `--ingress-enabled` 选项：
      ```bash
      draft init --ingress-enabled
      ```
      与前面第一次一样回答提示问题。 但是，这一次要回答的问题比第一次多一个，即使用为 Azure DNS 配置的完整域路径。

6. 输入入口（例如 draft.example.com）的顶级域：draft.squillace.io
7. 这次调用 `draft up` 时，可以在 `<appname>.draft.<domain>.<top-level-domain>` 形式的 URL 中看到应用程序（或对它执行 `curl`）。 本例中为 `http://handy-labradoodle.draft.squillace.io`。 
   ```bash
   curl -s http://handy-labradoodle.draft.squillace.io
   Hello World, I'm Java!
   ```


## <a name="next-steps"></a>后续步骤

现在有了 ACS Kubernetes 群集，可以使用 [Azure 容器注册表](../../container-registry/container-registry-intro.md)进行研究，创建此种方案的更多不同部署。 例如，可以创建 draft.basedomain.toplevel__ 域 DNS 记录集，控制特定 ACS 部署的更深子域中的内容。






