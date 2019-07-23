---
title: 运行 Kubernetes 服务
titleSuffix: Text Analytics - Azure Cognitive Services
description: 使用正在运行的示例将语言检测容器部署到 Azure Kubernetes 服务，并在 Web 浏览器中对其进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: ba6fde66b6173bdbff8e9acc08b16f47c5bf7ea4
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377099"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>将语言检测容器部署到 Azure Kubernetes 服务

了解如何部署语言检测容器。 此过程说明如何创建本地 Docker 容器, 将容器推送到自己的专用容器注册表, 在 Kubernetes 群集中运行容器, 并在 web 浏览器中对其进行测试。 使用容器可以从管理基础结构中转移, 而不是关注应用程序开发。

## <a name="prerequisites"></a>系统必备

此过程要求必须在本地安装和运行多个工具。 不要使用 Azure Cloud Shell。 需要满足以下条件：

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 适用于你的操作系统的[Git](https://git-scm.com/downloads) , 以便可以克隆此过程中使用的[示例](https://github.com/Azure-Samples/cognitive-services-containers-samples)。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [Docker 引擎](https://www.docker.com/products/docker-engine), 并验证 docker CLI 是否在控制台窗口中工作。
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe)。
* 具有适当定价层的 Azure 资源。 并非所有定价层都适用于此容器：
    * 仅具有 F0 或标准定价层的**Azure 文本分析**资源。
    * 具有 S0 定价层的**Azure 认知服务**资源。

## <a name="run-the-sample"></a>运行示例

此过程加载并运行用于语言检测的认知服务容器示例。 该示例有两个容器，一个用于客户端应用程序，另一个用于认知服务容器。 需要将这两个映像都推送到自己的 Azure 容器注册表实例中。 在它们位于自己的注册表中后, 创建 Azure Kubernetes Service (AKS) 的实例来访问这些映像并运行容器。 容器运行时, 请使用**kubectl** CLI 来监视容器的性能。 使用 HTTP 请求访问客户端应用程序，并查看结果。

![运行示例容器的概念性想法](../media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>示例容器

该示例包含两个容器映像。 一种用于前端网站。 第二个图像是语言检测容器, 该容器返回文本的检测到的语言 (区域性)。 完成后, 可从外部 IP 访问这两个容器。

### <a name="the-language-front-end-container"></a>语言前端容器

此网站相当于自己的进行语言检测终结点的请求的客户端应用程序。 完成此过程后，使用 `http://<external-IP>/<text-to-analyze>` 在浏览器中访问网站容器，即可获得检测到的字符串语言。 此 URL 的示例为 `http://132.12.23.255/helloworld!`。 浏览器中的结果为 `English`。

### <a name="the-language-container"></a>语言容器

在此特定过程中, 任何外部请求都可以访问语言检测容器。 容器不会以任何方式更改, 因此提供了标准认知服务容器特定语言检测 API。

对于此容器，该 API 是进行语言检测的 POST 请求。 与所有认知服务容器一样，可以从容器的托管 Swagger 信息 (`http://<external-IP>:5000/swagger/index.html`) 了解有关容器的详细信息。

端口 5000 是用于认知服务容器的默认端口。

## <a name="create-an-azure-container-registry-service"></a>创建 Azure 容器注册表服务

若要将容器部署到 Azure Kubernetes 服务, 容器映像必须可访问。 创建自己的 Azure 容器注册表服务以托管映像。

1. 登录到 Azure CLI。

    ```azurecli
    az login
    ```

1. 创建名为 `cogserv-container-rg` 的资源组来保存此过程中创建的每一个资源。

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. 使用名称的格式创建自己的 Azure 容器注册表实例, 后跟`registry`。 例如 `pattyregistry`。 不要在名称中使用短划线或下划线字符。

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    保存结果，以获取 loginServer 属性  。 此属性是托管容器的地址的一部分, 该地址稍后会在`language.yml`文件中使用。

    ```console
    > az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. 登录容器注册表。 必须先登录, 然后才能将映像推送到注册表。

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-the-website-docker-image"></a>获取网站 Docker 映像

1. 此过程中使用的示例代码位于认知服务容器样本存储库中。 克隆存储库以生成示例的本地副本。

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    将存储库存储在本地计算机上之后, 在[\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService)目录中找到该网站。 此网站充当客户端应用程序, 该应用程序调用语言检测容器中托管的语言检测 API。 

1. 为网站生成 Docker 映像。 运行以下命令时, 请确保控制台位于 Docker 文件所在的[\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService)目录中:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    若要在容器注册表上跟踪版本，请添加具有版本格式的标记，如 `v1`。 

1. 将映像推送到容器注册表。 此步骤可能需要几分钟的时间。 

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    如果遇到`unauthorized: authentication required`错误, 请`az acr login --name <your-container-registry-name>`使用命令进行登录。 

    完成此过程后, 结果类似于:

    ```console
    > docker push pattyregistry.azurecr.io/language-frontend:v1
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-the-language-detection-docker-image"></a>获取语言检测 Docker 映像 

1. 将 Docker 映像的最新版本拉取到本地计算机上。 此步骤可能需要几分钟的时间。 如果此容器的更新版本, 请将的值从`1.1.006770001-amd64-preview`更改为较新的版本。 

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. 用容器注册表标记映像。 找到最新版本, 并替换`1.1.006770001-amd64-preview`版本 (如果你有更新版本)。 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. 将映像推送到容器注册表。 此步骤可能需要几分钟的时间。 

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>获取容器注册表凭据

需要执行以下步骤来获取所需的信息, 以便将你的容器注册表连接到你稍后在此过程中创建的 Azure Kubernetes 服务的实例。

1. 创建服务主体。

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    为步骤 3 中代理人参数 `<appId>` 的保存结果 `appId` 值。 保存下一部分的客户端密钥参数`<client-secret>`的密码。

    ```console
    > az ad sp create-for-rbac --skip-assignment
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. 获取容器注册表 ID。

    ```azurecli
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    为下一步中范围参数值 `<acrId>` 保存输出。 输出如下所示：

    ```console
    > az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    为本节中步骤 3 保存完整值。 

1. 若要授予 AKS 群集使用容器注册表中存储的映像的适当访问权限，请创建角色分配。 将 `<appId>` 和 `<acrId>` 替换为在前两个步骤中收集的值。

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-the-azure-kubernetes-service-cluster"></a>创建 Azure Kubernetes 服务群集

1. 创建 Kubernetes 群集。 所有参数值都来自前一部分，name 参数除外。 选择一个名称，指明其创建者及其用途，例如 `patty-kube`。 

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    此步骤可能需要几分钟的时间。 结果为： 

    ```console
    > az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    已创建该服务, 但它还没有网站容器或语言检测容器。 

1. 获取 Kubernetes 群集的凭据。 

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>将业务流程定义加载到 Kubernetes 服务中

本部分使用**kubectl** CLI 与 Azure Kubernetes 服务的实例进行通信。 

1. 在加载业务流程定义之前, 请检查以确保**kubectl**有权访问节点。

    ```console
    kubectl get nodes
    ```

    响应如下所示：

    ```console
    > kubectl get nodes
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. 复制以下文件, 并将其`language.yml`命名为。 该文件含有一个 `service` 部分和一个 `deployment` 部分，各自用于两种容器类型，即 `language-frontend` 网站容器和 `language` 检测容器。 

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. `language.yml`根据下表更改的语言前端部署行, 以添加自己的容器注册表映像名称、客户端机密和文本分析设置。

    语言前端部署设置|用途|
    |--|--|
    |第 32 行<br> `image` 属性|容器注册表中前端映像的图像位置<br>`<container-registry-name>.azurecr.io/language-frontend:v1`。|
    |第 44 行<br> `name` 属性|映像的容器注册表机密, `<client-secret>`在前面的部分中称为。|

1. `language.yml`根据下表更改的语言部署行, 以添加自己的容器注册表映像名称、客户端密钥和文本分析设置。

    |语言部署设置|用途|
    |--|--|
    |第 78 行<br> `image` 属性|容器注册表中的语言图像的图像位置<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`。|
    |第 95 行<br> `name` 属性|映像的容器注册表机密, `<client-secret>`在前面的部分中称为。|
    |第 91 行<br> `apiKey` 属性|文本分析的资源键。|
    |第 92 行<br> `billing` 属性|文本分析资源的计费终结点。<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`。|

    由于**apiKey**和**计费终结点**属性设置为 Kubernetes 业务流程定义的一部分, 因此网站容器无需知道这些属性, 也不需要将其作为请求的一部分传递。 网站容器按其业务流程协调程序名称 `language` 引用语言检测容器。 

1. 从创建和保存 `language.yml` 的文件夹中加载此示例的业务流程定义文件。 

    ```console
    kubectl apply -f language.yml
    ```

    响应为：

    ```console
    > kubectl apply -f language.yml
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>获取容器的外部 IP

对于这两个容器, 验证`language-frontend`和`language`服务正在运行, 并获取外部 IP 地址。 

```console
kubectl get all
```

```console
> kubectl get all
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

如果服务`EXTERNAL-IP`的显示为挂起, 请重新运行该命令, 直到显示 IP 地址, 然后再转到下一步。 

## <a name="test-the-language-detection-container"></a>测试语言检测容器

打开浏览器, 并从上一节中转到`language`容器的外部 IP:。 `http://<external-ip>:5000/swagger/index.html` 使用 API 的功能来测试语言检测终结点。 `Try it` 

![查看容器的 Swagger 文档](../media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>测试客户端应用程序容器

使用以下格式, 将浏览器中的 URL 更改为`language-frontend`容器的外部 IP:。 `http://<external-ip>/helloworld` `helloworld` 的英文区域性文本预测为 `English`。

## <a name="clean-up-resources"></a>清理资源

完成群集后, 请删除 Azure 资源组。 

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>相关信息

* [Docker 用户的 kubectl](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>后续步骤 

* 使用更多[认知服务容器](../../cognitive-services-container-support.md)。
* 使用[文本分析连接的服务](../vs-text-connected-service.md)。


<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->