---
title: 运行 Kubernetes 服务
titleSuffix: Text Analytics -  Azure Cognitive Services
description: 使用正在运行的示例将语言检测容器部署到 Azure Kubernetes 服务，并在 Web 浏览器中对其进行测试。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 01/22/2019
ms.author: diberry
ms.openlocfilehash: 19ac1819c3b098597819ec75adcedeca929e802d
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464538"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>将语言检测容器部署到 Azure Kubernetes 服务

了解如何部署语言检测容器。 此过程说明如何创建本地 Docker 容器，将容器推送到自己的专用容器注册表，在 Kubernetes 群集中运行容器，以及在 Web 浏览器中对其进行测试。 

## <a name="prerequisites"></a>先决条件

此过程要求必须在本地安装和运行多个工具。 请勿使用 Azure Cloud Shell。 

* 使用 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 适用于操作系统的 [Git](https://git-scm.com/downloads)，以便克隆此过程中使用的[示例](https://github.com/Azure-Samples/cognitive-services-containers-samples)。 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 
* [Docker 引擎](https://www.docker.com/products/docker-engine)并验证 Docker CLI 是否可在控制台窗口中工作。
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe)。 
* 具有适当定价层的 Azure 资源。 并非所有定价层都适用于此容器：
    * 仅具有 F0 或标准定价层的文本分析资源。
    * 具有 S0 定价层的认知服务资源。

## <a name="running-the-sample"></a>运行示例

此过程加载并运行认知服务容器示例以进行语言检测。 该示例有两个容器，一个用于客户端应用程序，另一个用于认知服务容器。 需要将这些映像都推送到自己的 Azure 容器注册表。 这些映像推送到自己的注册表后，请创建 Azure Kubernetes 服务来访问这些映像和运行容器。 容器在运行时，请使用 kubectl CLI，监视容器性能。 使用 HTTP 请求访问客户端应用程序，并查看结果。 

![运行示例容器的概念性想法](../media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>示例容器

该示例有两个容器映像，一个用于前端网站。 第二个映像是可返回检测到的文本语言（区域性）的语言检测容器。 完成后，这两个容器都可以从外部 IP 进行访问。 

### <a name="the-language-frontend-container"></a>语言前端容器

此网站相当于自己的进行语言检测终结点的请求的客户端应用程序。 完成此过程后，使用 `http://<external-IP>/<text-to-analyze>` 在浏览器中访问网站容器，即可获得检测到的字符串语言。 此 URL 的示例为 `http://132.12.23.255/helloworld!`。 浏览器中的结果为 `English`。

### <a name="the-language-container"></a>语言容器

在此特定过程中，任何外部访问请求都可以访问语言检测容器。 容器未以任何方式更改，因此标准的特定于的认知服务容器的语言检测 API 可供使用。 

对于此容器，该 API 是进行语言检测的 POST 请求。 与所有认知服务容器一样，可以从容器的托管 Swagger 信息 (`http://<external-IP>:5000/swagger/index.html`) 了解有关容器的详细信息。 

端口 5000 是用于认知服务容器的默认端口。 

## <a name="create-azure-container-registry-service"></a>创建 Azure 容器注册表服务

若要将容器部署到 Azure Kubernetes 服务，需要能够访问容器映像。 创建自己的 Azure 容器注册表服务以托管映像。 

1. 登录 Azure CLI 

    ```azurecli
    az login
    ```

1. 创建名为 `cogserv-container-rg` 的资源组来保存此过程中创建的每一个资源。

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. 使用名字后加 `registry` 这一格式（如 `pattyregistry`）创建自己的 Azure 容器注册表。 请勿在名称中使用短划线或下划线字符。

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    保存结果，以获取 loginServer 属性。 这将是托管容器地址的一部分，稍后将在 `language.yml` 文件中使用。

    ```console
    >az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
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

1. 登录容器注册表。 需登录后才能将映像推送到注册表。

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>获取网站 Docker 映像 

1. 此过程中使用的示例代码位于认知服务容器样本存储库中。 克隆存储库以生成示例的本地副本。

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    如果存储库已在本地计算机上，请在 [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) 目录中查找网站。 该网站用作客户端应用程序，可调用语言检测容器中托管的语言检测 API。  

1. 为网站生成 Docker 映像。 运行以下命令时，确保控制台位于 Dockerfile 所在的[\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) 目录中：

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 . 
    ```

    若要在容器注册表上跟踪版本，请添加具有版本格式的标记，如 `v1`。 

1. 将映像推送到容器注册表。 这可能需要几分钟的时间。 

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    如果收到 `unauthorized: authentication required` 错误，请使用 `az acr login --name <your-container-registry-name>` 命令登录。 

    该过程完成后，结果应类似于以下内容：

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

## <a name="get-language-detection-docker-image"></a>获取语言检测 Docker 映像 

1. 将 Docker 映像的最新版本拉取到本地计算机上。 这可能需要几分钟的时间。 如果有此容器的较新版本，请将值从 `1.1.006770001-amd64-preview` 更改到较新版本。 

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. 使用容器注册表标记图像。 查找最新版本，如果有更新的版本，请替换版本 `1.1.006770001-amd64-preview`。 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. 将映像推送到容器注册表。 这可能需要几分钟的时间。 

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>获取容器注册表凭据

需要执行以下步骤来获取所需信息，以便将容器注册表与稍后此过程中要创建的 Azure Kubernetes 服务进行连接。

1. 创建服务主体。

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    为步骤 3 中代理人参数 `<appId>` 的保存结果 `appId` 值。 为下一节中 client-secret 参数 `<client-secret>` 保存 `password`。

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

1. 若要授予 AKS 群集使用容器注册表中存储的映像的适当访问权限，请创建角色分配。 将 <appId> 和 <acrId> 替换为在前两个步骤中收集的值。

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>创建 Azure Kubernetes 服务

1. 创建 Kubernetes 群集。 所有参数值都来自前一部分，name 参数除外。 选择一个名称，指明其创建者及其用途，例如 `patty-kube`。 

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    这个步骤可能需要几分钟的时间。 结果为： 

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

    服务已创建，但还没有网站容器或语言检测容器。  

1. 获取 Kubernetes 群集的凭据。 

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>将业务流程定义加载到 Kubernetes 服务中

本部分使用 kubectl CLI 与 Azure Kubernetes 服务通信。 

1. 在加载业务流程定义之前，请检查 kubectl 是否有权访问节点。

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

1. 复制以下文件并将其命名为 `language.yml`。 该文件含有一个 `service` 部分和一个 `deployment` 部分，各自用于两种容器类型，即 `language-frontend` 网站容器和 `language` 检测容器。 

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. 根据下表更改 `language.yml` 的语言前端部署行，以添加自己的容器注册表映像名称、客户端密码和文本分析设置。

    语言前端部署设置|目的|
    |--|--|
    |第 32 行<br> `image` 属性|容器注册表中的前端映像的映像位置<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |第 44 行<br> `name` 属性|映像的容器注册表机密，在上一节中称为 `<client-secret>`。|

1. 根据下表更改 `language.yml` 的语言部署行，以添加自己的容器注册表映像名称、客户端密码和文本分析设置。

    |语言部署设置|目的|
    |--|--|
    |第 78 行<br> `image` 属性|容器注册表中语言映像的映像位置<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |第 95 行<br> `name` 属性|映像的容器注册表机密，在上一节中称为 `<client-secret>`。|
    |第 91 行<br> `apiKey` 属性|文本分析资源密钥|
    |第 92 行<br> `billing` 属性|文本分析资源的账单终结点。<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

    由于 apiKey 和账单终结点已设置为 Kubernetes 业务流程定义的一部分，因此网站容器无需了解这些内容或将其作为请求的一部分传递。 网站容器按其业务流程协调程序名称 `language` 引用语言检测容器。 

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

对于这两个容器，请验证 `language-frontend` 和 `language` 服务是否正在运行，并获取外部 IP 地址。 

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

如果服务的 `EXTERNAL-IP` 显示为挂起，请重新运行该命令，直到显示 IP 地址，然后再转到下一步。 

## <a name="test-the-language-detection-container"></a>测试语言检测容器

打开浏览器并导航到上一节中 `language` 容器的外部 IP：`http://<external-ip>:5000/swagger/index.html`。 可以使用 API 的 `Try it` 功能来测试语言检测终结点。 

![查看容器的 swagger 文档](../media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>测试客户端应用程序容器

使用以下格式将浏览器中的 URL 更改为 `language-frontend` 容器的外部 IP：`http://<external-ip>/helloworld`。 `helloworld` 的英文区域性文本预测为 `English`。

## <a name="clean-up-resources"></a>清理资源

群集操作完成后，请删除 Azure 资源组。 

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>相关信息

* [kubectl for Docker Users](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)（面向 Docker 用户的 kubectl）

## <a name="next-steps"></a>后续步骤 

* 使用更多[认知服务容器](../../cognitive-services-container-support.md)
* 使用文本分析连接服务](../vs-text-connected-service.md)


<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->