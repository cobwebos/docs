---
title: Azure 容器实例方案
titleSuffix: Azure Cognitive Services
description: 了解如何部署 Azure 容器实例上的认知服务容器
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 288894705e1108d6dd511b60cd2bc3bcee4c6d41
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704335"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>部署和 Azure 容器实例上运行容器

使用以下步骤中，Azure 认知服务应用程序中轻松地使用 Azure 在云中缩放[容器实例](https://docs.microsoft.com/azure/container-instances/)。 本帮助你专注于构建您的应用程序而不是管理基础结构。

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

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>使用容器实例

1. 选择**概述**和复制的 IP 地址。 它将是数字 IP 地址，如`55.55.55.55`。
1. 打开新的浏览器选项卡，并使用的 IP 地址，例如， `http://<IP-address>:5000 (http://55.55.55.55:5000`)。 您将看到容器的主页上，让您知道正在运行容器。

1. 选择**服务 API 说明**若要查看容器的 swagger 页。

1. 选择任一**POST** Api，然后选择**试试看**。这些参数会显示包括输入。 填写参数。

1. 选择**Execute**将请求发送到容器实例。

    已成功创建并在 Azure 容器实例中使用认知服务容器。
