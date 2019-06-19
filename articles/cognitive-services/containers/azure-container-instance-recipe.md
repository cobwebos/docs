---
title: Azure 容器实例方案
titleSuffix: Azure Cognitive Services
description: 了解如何部署 Azure 容器实例上的认知服务容器
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 06/12/2019
ms.author: dapine
ms.openlocfilehash: 5de11b62bb2a2302c247907627e27339400fcf25
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207487"
---
# <a name="deploy-and-run-container-on-azure-container-instance-aci"></a>部署和运行在 Azure 容器实例 (ACI) 中的容器

使用以下步骤中，Azure 认知服务应用程序中轻松地使用 Azure 在云中缩放[容器实例](https://docs.microsoft.com/azure/container-instances/)(ACI)。 本帮助你专注于构建您的应用程序而不是管理基础结构。

## <a name="prerequisites"></a>必备组件

此解决方案适用于任何认知服务容器。 使用此方案之前，必须在 Azure 门户中创建认知服务资源。 每个支持容器的认知服务具有专门为安装和配置容器的服务的"如何安装"文档。 由于某些服务需要作为容器的输入文件或一组文件，务必了解并使用该容器已成功使用此解决方案之前。

* 在 Azure 门户中创建认知服务资源。
* 认知服务**终结点 URL** -容器查看在特定服务的"如何安装"，要找到其中的终结点 URL 是在 Azure 门户中，以及一个正确的 URL 的示例，如下所示。 确切格式可以更改服务到服务。
* 认知服务**键**-密钥位于**密钥**Azure 资源页。 您只需要两个密钥中的一个即可。 该密钥为 32 个字母数字字符的字符串。
* 单个认知服务容器在本地主机 （计算机） 上。 请确保你可以：
  * 拉取映像`docker pull`命令。
  * 使用所有所需的配置设置已成功运行本地容器`docker run`命令。
  * 调用容器的终结点，取回 2xx 响应和 JSON 响应。

在尖括号内的所有变量`<>`，需要替换为你自己的值。 这种替换包含在尖括号。

## <a name="step-2-launch-your-container-on-azure-container-instances-aci"></a>步骤 2：启动容器 Azure 容器实例 (ACI)

**创建 Azure 容器实例 (ACI) 资源。**

1. 转到[创建](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances)容器实例的页面。

1. 上**基础知识**选项卡上，输入以下详细信息：

    |页|设置|值|
    |--|--|--|
    |基础|订阅|选择订阅。|
    |基础|资源组|选择可用的资源组或创建一个新如`cognitive-services`。|
    |基础|容器名称|输入一个名称，例如`cognitive-container-instance`。 此名称必须是较低的大写。|
    |基础|Location|选择用于部署的区域。|
    |基础|映像类型|`Public`|
    |基础|映像名称|输入的认知服务容器位置。 这可以是你在中使用的同一位置`docker pull`命令，_例如_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |基础|OS 类型|`Linux`|
    |基础|大小|将大小更改为特定的认知服务容器的建议建议。:<br>2 个内核<br>4 GB
    ||||
  
1. 上**网络**选项卡上，输入以下详细信息：

    |页|设置|值|
    |--|--|--|
    |网络|端口|对于从 TCP 编辑现有的端口`80`到`5000`。 这意味着你将公开端口 5000 上的容器。|
    ||||

1. 上**高级**选项卡上，输入以下详细信息，以通过所需设置应用于容器实例资源计费的容器：

    |高级页密钥|高级页值|
    |--|--|
    |`apikey`|从复制**密钥**资源页面。 需要两个键之一。 它是不包含空格或短划线的 32 字母数字字符字符串`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|
    |`billing`|从复制**概述**资源页面。 |
    |`eula`|`accept`|

    如果容器具有其他配置设置，例如输入的装载、 输出装载或日志记录，这些设置还需要添加。

1. 选择**查看和创建**。
1. 验证通过后，选择**创建**完成创建过程。
1. 在顶部导航栏中选择钟形图标。 这是通知窗口。 它将显示一个蓝色**转到资源**按钮时创建该资源。 选择该按钮以转到新的资源。

## <a name="use-the-container-instance"></a>使用容器实例

1. 选择**概述**和复制的 IP 地址。 它将是数字 IP 地址，如`55.55.55.55`。
1. 打开新的浏览器选项卡，并使用的 IP 地址，例如， `http://<IP-address>:5000 (http://55.55.55.55:5000`)。 您将看到容器的主页上，让您知道正在运行容器。

1. 选择**服务 API 说明**若要查看容器的 swagger 页。

1. 选择任一**POST** Api，然后选择**试试看**。这些参数会显示包括输入。 填写参数。

1. 选择**Execute**将请求发送到容器实例。

    已成功创建并在 Azure 容器实例中使用认知服务容器。
