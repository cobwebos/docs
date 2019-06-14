---
title: 访问和自定义新的开发人员门户-Azure API 管理 |Microsoft Docs
description: 了解如何在 API 管理中使用新的开发人员门户。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: deb3fdf3069aaad4982d71806c209fe516e3c3d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743570"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>访问和自定义 Azure API 管理中的新开发人员门户

本文介绍如何访问新的 Azure API 管理开发人员门户。 它将引导您完成在可视编辑器体验-添加和编辑内容-以及自定义网站的外观。

![新的 API 管理开发人员门户](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>必备组件

- 请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
- 导入并发布 Azure API 管理实例。 有关详细信息，请参阅[导入和发布](import-and-publish.md)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> 新的开发人员门户当前处于预览状态。

## <a name="managed-and-self-hosted-versions"></a>托管和自承载版本

你可以生成开发人员门户中通过两种方式：

- **托管的版本**-作者： 编辑和自定义内置于门户对 API 管理实例，可通过 URL 访问`<your-api-management-instance-name>.developer.azure-api.net`。
- **自承载的版本**-作者： 部署和自承载你的 API 管理实例之外的门户。 此方法允许您编辑门户的基本代码和扩展提供的核心功能。 有关详细信息和说明，请参阅[GitHub 存储库与在门户的源代码][1]。

## <a name="access-the-managed-version-of-the-portal"></a>访问托管版本的门户

请按照以下步骤来访问托管的版本的门户。

1. 请转到 Azure 门户对 API 管理服务实例。
1. 单击**新的开发人员门户 （预览版）** 顶部导航栏中的按钮。 使用管理门户的版本新的浏览器选项卡将打开。 如果首次访问门户，将自动设置默认内容。

## <a name="edit-and-customize-the-managed-version-of-the-portal"></a>编辑和自定义托管的版本的门户

在以下视频中，我们将演示如何以编辑在门户的内容、 自定义网站的外观和发布所做的更改。

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="next-steps"></a>后续步骤

了解有关新的开发人员门户的详细信息：

- [GitHub 存储库的源代码][1]
- [自承载在门户的说明][2]
- [项目的公共路线图][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects