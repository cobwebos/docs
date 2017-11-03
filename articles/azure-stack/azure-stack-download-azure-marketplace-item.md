---
title: "从 Azure 下载应用商店项 |Microsoft 文档"
description: "我可以从 Azure 到我的 Azure 堆栈部署下载的应用商店项。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: erikje
ms.openlocfilehash: 4d7c335a3c68cc9bb8cb0c823883716a3dd6620a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>从 Azure 应用商店项下载到 Azure 堆栈

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

当您决定在 Azure 堆栈应用商店中包括哪些内容，应考虑从 Azure 应用商店的可用内容。 你可以从策展已预先测试，在 Azure 堆栈上运行的 Azure 应用商店项列表中进行下载。 新项经常会添加到此列表，因此请确保经常回来查看新内容。

若要下载应用商店项，你必须首先[向 Azure 注册 Azure 堆栈](azure-stack-register.md)。 

## <a name="download"></a>下载
1. 登录到 Azure 堆栈管理员门户 (https://portal.local.azurestack.external)。
2. 一些应用商店项可能会非常大。  请检查以确保通过单击你的系统上有足够的空间**资源提供程序** > **存储**。

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

3. 单击**更多的服务** > **应用商店管理**。

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. 单击**从 Azure 中的添加**以查看可供下载项的列表。 你可以单击要查看其说明和下载大小的列表中每一项。

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. 选择你要在列表中，然后单击的项**下载**。 这将启动下载您选定的项的 VM 映像。 下载时间会有所不同。

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. 下载完成后，你可以部署新应用商店项目作为 Azure 堆栈运算符或用户。 单击**+ 新建**，新的应用商店项目中，类别中搜索，然后选择项。
7. 单击**创建**以打开新下载的项的创建体验。 按照分步说明操作中，若要部署你的项目。

## <a name="next-steps"></a>后续步骤

[创建和发布应用商店项目](azure-stack-create-and-publish-marketplace-item.md)
