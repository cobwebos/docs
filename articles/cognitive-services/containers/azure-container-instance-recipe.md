---
title: Azure 容器实例配方
titleSuffix: Azure Cognitive Services
description: 了解如何在 Azure 容器实例上部署认知服务容器
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876651"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>在 Azure 容器实例上部署和运行容器

通过以下步骤，使用 Azure[容器实例](https://docs.microsoft.com/azure/container-instances/)轻松在云中扩展 Azure 认知服务应用程序。 容器化可帮助您专注于构建应用程序，而不是管理基础结构。 有关使用容器的详细信息，请参阅[功能和优点](../cognitive-services-container-support.md#features-and-benefits)。

## <a name="prerequisites"></a>先决条件

该配方适用于任何认知服务容器。 在使用配方之前，必须在 Azure 门户中创建认知服务资源。 每个支持容器的认知服务都有一个"如何安装"文档，专门用于安装和配置容器的服务。 某些服务需要文件或文件集作为容器的输入，请务必了解并成功使用此解决方案之前使用该容器。

* 在 Azure 门户中创建的认知服务资源。
* 认知服务**终结点 URL** - 查看特定服务的容器的"如何安装"，查找终结点 URL 在 Azure 门户中的位置，以及 URL 的正确示例是什么样子。 确切的格式可以从服务更改为服务。
* 认知服务**密钥**- 键位于 Azure 资源的 **"密钥"** 页上。 您只需要两个密钥中的一个即可。 该键是一个 32 个字母数字字符的字符串。
* 本地主机（计算机）上的单个认知服务容器。 确保您可以：
  * 使用`docker pull`命令向下拉图像。
  * 使用`docker run`命令使用所有必需的配置设置成功运行本地容器。
  * 调用容器的终结点，获取 HTTP 2xx 的响应和 JSON 响应。

角括号`<>`中的所有变量都需要用您自己的值替换。 此更换包括角括号。

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>使用容器实例

1. 选择 **"概述"** 并复制 IP 地址。 它将是一个数字 IP 地址，`55.55.55.55`如 。
1. 打开新的浏览器选项卡，并使用 IP 地址（例如 `http://<IP-address>:5000 (http://55.55.55.55:5000`）。 您将看到容器的主页，让您知道容器正在运行。

1. 选择 **"服务 API 描述"** 以查看容器的摇曳页。

1. 选择任何**POST** API 并选择 **"试用"。** 将显示参数，包括输入。 填写参数。

1. 选择 **"执行"** 以将请求发送到容器实例。

    您已成功在 Azure 容器实例中创建和使用认知服务容器。
