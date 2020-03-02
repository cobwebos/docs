---
title: 如何部署窗体识别器示例标签工具
titleSuffix: Azure Cognitive Services
description: 了解部署表单识别器示例标记工具的不同方法，以帮助进行监督。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: pafarley
ms.openlocfilehash: fa419d7dd9668ac2ce8f2b0eb904117c7e22692d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207840"
---
# <a name="deploy-the-sample-labeling-tool"></a>部署示例标签工具

窗体识别器示例标记工具是在 Docker 容器中运行的应用程序。 它提供了一个有用的 UI，你可以使用该 UI 手动为窗体文档添加标签，以便进行监督。 [带标签的训练](./quickstarts/label-tool.md)快速入门介绍了如何在本地计算机上运行该工具，这是最常见的方案。 

本指南将介绍可用于部署和运行示例标记工具的替代方法。 

## <a name="deploy-with-azure-container-instances"></a>部署 Azure 容器实例

可以在 Docker web 应用容器中运行 "标签" 工具。 首先，在 Azure 门户上[创建新的 Web 应用资源](https://ms.portal.azure.com/#create/Microsoft.WebSite)。 在窗体中填写订阅和资源组的详细信息。 在必填字段中输入以下信息：
* **发布**： Docker 容器
* **操作**系统： Linux

在下一页上，为 Docker 容器安装程序填写以下字段：

* **选项**：单个容器
* **映像源**： Azure 容器注册表
* **访问类型**：公用
* **图像和标记**： mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest

下面的步骤是可选的。 应用部署完成后，可以运行该应用并联机访问 "标签" 工具。

### <a name="connect-to-azure-ad-for-authorization"></a>连接到 Azure AD 进行授权

建议将 web 应用连接到 Azure Active Directory （AAD），以便只有具有凭据的人员才能登录和使用该应用。 按照[将应用服务应用配置](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)为连接到 AAD 中的说明进行操作。

## <a name="next-steps"></a>后续步骤

返回到[带标签的训练](./quickstarts/label-tool.md)快速入门，了解如何使用该工具手动标记定型数据和进行监督。
