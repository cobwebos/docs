---
title: 部署到 Azure Stack 的 Azure 认知服务 |Microsoft Docs
description: 了解如何将 Azure 认知服务部署到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: mabrigg
ms.reviewer: guanghu
ms.openlocfilehash: 5af508714b5eae5cdd23c940af0ae21300c0c5b8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53194659"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>将 Azure 认知服务部署到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!Note]  
> Azure Stack 上的 Aure 认知服务处于预览状态。

通过 Azure Stack 上的容器支持，可以使用 Azure 认知服务。 Azure 认知服务中的容器支持，可使用相同的丰富 Api 在 Azure 中可用。 你的容器使用在何处部署并托管在所提供的服务实现灵活性[Docker 容器](https://www.docker.com/what-container)。 容器支持目前以预览版的 Azure 认知服务，包括的部分子集[计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)，[人脸](https://docs.microsoft.com/azure/cognitive-services/face/overview)，并[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)，和[语言理解](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)(LUIS)。

容器化是在其中应用程序或服务，包括其依赖项和配置打包为容器映像的软件分发的方法。 进行很少或没有修改后，可以将映像部署到容器主机。 每个容器是独立的从其他容器，从基础操作系统。 系统本身，只有运行你的映像所需的组件。 容器主机具有较小的占用比虚拟机。 此外，您可以通过为短期任务的映像创建容器，并删除不再需要时。

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>Azure Stack 上的认知服务中使用容器

- **控制数据**  
  允许你应用的用户能够使用认知服务时对其数据的控制。 你可向应用程序的用户不能发送数据到全球版 Azure 或公有云提供认知服务。

- **控件对模型进行更新**  
  提供应用程序用户版本和更新的部署在其解决方案中的模型。

- **可移植的体系结构**  
  启用可移植应用体系结构的创建，以便可以将你的解决方案部署到使用公共云，私有云上的本地或边缘。 可将容器部署到 Azure Kubernetes 服务，Azure 容器实例，或到 Azure Stack 中的 Kubernetes 群集。 有关详细信息，请参阅[将 Kubernetes 部署到 Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)。

- **高吞吐量和低延迟**  
   提供你的应用用户能够缩放具有高吞吐量和低延迟的流量高峰。 启用认知服务运行在 Azure Kubernetes 服务中以物理方式靠近其应用程序逻辑和数据。

使用 Azure Stack 部署以及用于实现高可用性和弹性缩放应用程序容器的 Kubernetes 群集中的认知服务容器。 可以通过将与构建在应用服务、 函数、 Blob 存储或 SQL 或 mySQL 数据库的组件结合使用认知服务开发应用程序。 

有关认知服务容器的更多详细信息，请转到[Azure 认知服务中的容器支持](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support)。

## <a name="deploy-the-azure-face-api"></a>部署 Azure 的人脸 API

本文介绍如何部署 Azure Stack 上的 Kubernetes 群集上的 Azure 人脸 API。 可以使用相同的方法来部署 Azure Stack Kubernetes 群集上的其他认知服务容器。

## <a name="prerequisites"></a>必备组件

在开始之前，您将需要：

1.  请求访问容器注册表从 Azure 认知服务容器注册表提取人脸容器映像。 有关详细信息，请转到的部分[请求访问专用容器注册表](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry)。

2.  准备 Azure Stack 上的 Kubernetes 群集。 可以按照本文[到 Azure Stack 部署 Kubernetes](azure-stack-solution-template-kubernetes-deploy.md)。

## <a name="create-azure-resources"></a>创建 Azure 资源

若要预览的人脸、 LUIS 或识别的文本容器，分别在 Azure 上创建认知服务资源。 需要使用从资源的订阅密钥和终结点 URL 来实例化认知服务容器。

1.  在 Azure 门户中创建 Azure 资源。 如果你想要预览的人脸容器，必须首先在 Azure 门户中创建相应的人脸资源。 有关更多信息，请参见 Windows 开发中心中的[快速入门：在 Azure 门户中创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。

    >  [!Note]  
    >  人脸或计算机视觉资源必须使用 F0 定价层。

2.  获取 Azure 资源的终结点 URL 和订阅密钥。 Azure 资源创建后，必须使用从该资源的订阅密钥和终结点 URL 来实例化预览相应的人脸、 LUIS 或识别的文本容器。

## <a name="create-a-kubernetes-secret"></a>创建 Kubernetes 机密 

需要使用 Kubectl 创建机密命令来访问专用容器注册表。 替换**&lt;用户名&gt;** 的用户名和**&lt;密码&gt;** 从 Azure 收到利用凭据中提供的密码认知服务团队。

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>准备 YAML 配置文件

要使用 YAML 配置文件以简化 Kubernetes 群集上的认知服务的部署。

下面是示例 YAML 配置文件以将人脸服务部署到 Azure Stack:

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

在此 YAML 配置文件，请使用你用于从 Azure 容器注册表获取认知服务容器映像的机密。 你和使用密钥文件部署的容器的特定副本。 您还可以创建负载均衡器，请确保用户可以访问此服务从外部。

有关的键字段的详细信息：

| 字段 | 说明 |
| --- | --- |
| replicaNumber | 定义要创建实例的初始的副本。 肯定可以在更高版本在部署之后扩展它。 |
| ImageLocation | 指示特定的认知服务容器映像在 ACR 中的位置。 例如，人脸服务： `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |记下的步骤中的终结点 URL[创建 Azure 资源](#create-azure-resources) |
| ApiKey | 订阅密钥的步骤中记下[创建 Azure 资源](#create-azure-resources) |
| SecretName | 只需的步骤中记下的机密名称[创建机密的保护来访问专用容器注册表](#create-secrete-to-access-the-private-container-registry) |

## <a name="deploy-the-cognitive-service"></a>部署认知服务

使用以下命令以部署认知服务容器

```bash  
    Kubectl apply -f <yamlFineName>
```
以下命令来监视它将部署的使用： 
```bash  
    Kubectl get pod – watch
```

## <a name="test-the-cognitive-service"></a>测试认知服务

访问[OpenAPI 规范](https://swagger.io/docs/specification/about/)（以前称为 Swagger 规范），描述的实例化的容器，从支持的操作 **/swagger**该容器的相对 URI。 例如，以下 URI 提供对上一示例中实例化的情绪分析容器 OpenAPI 规范的访问：

```HTTP  
http:<External IP>:5000/swagger
```

可以从以下命令来获取外部 IP 地址： 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>请尝试与 Python 配合使用的服务

您可以尝试通过运行一些简单的 Python 脚本验证 Azure Stack 上的认知服务。 有的正式 Python 快速入门示例[计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)，[人脸](https://docs.microsoft.com/azure/cognitive-services/face/overview)，并[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)，以及[语言理解](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)(LUIS) 以供参考。

有两个问题需要请记住，以使对在容器中运行的服务运行的 Python 应用： 
1. 认知服务容器中的不需要子密钥进行身份验证，但我们仍需要输入任何字符串作为一个占位符，以满足 SDK。 
2. 使用实际的服务终结点 IP 地址替换 base_URL 

下面是示例 Python 脚本使用人脸服务 Python SDK 来检测和定格图像中的人脸。 

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>后续步骤

[如何安装和运行计算机视觉 API 容器。](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[如何安装和运行人脸 API 容器](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#create-a-face-resource-on-azure)

[如何安装和运行文本分析 API 容器](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[如何安装和运行语言理解 (LIUS) 容器](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)