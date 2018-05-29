---
title: 找不到应用程序代理应用程序的工作连接器组 | Microsoft Docs
description: 对于使用 Azure AD 应用程序代理的应用程序，解决连接器组中没有工作连接器时可能会遇到的问题
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 679b8654ad926ddb80747ad4c93064946c9bc82b
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155416"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>找不到应用程序代理应用程序的工作连接器组

本文将帮助解决与 Azure Active Directory 集成的应用程序代理应用程序在未检测到连接器时遇到的常见问题。

## <a name="overview-of-steps"></a>步骤概述
如果连接器组中没有适用于应用程序的工作连接器，可以通过几种方法解决此问题：

-   如果组中没有任何连接器，可以：

    -   在右侧的本地服务器上下载新的连接器，然后将其分配给此组

    -   将活动连接器移入该组

-   如果组中没有活动连接器，可以：

    -   确定连接器处于不活动状态的原因并解决

    -   将活动连接器移入该组

若想解决问题，请在应用程序中打开“应用程序代理”菜单，查看连接器组警告消息。 如果组中没有任何连接器，将会出现警告消息，指定组至少需要一个连接器。 如果没有任何活动的连接器，会显示警告消息来说明这一点。 拥有非活动状态的连接器是很常见的。 

   ![Azure 门户中的连接器组选择](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

关于这些选项的详细信息，请参阅以下相应部分。 说明假定从连接器管理页开始操作。 如果想要查看上述错误消息，可以通过单击警告消息转到此页。 还可以通过转到“Azure Active Directory”，单击“企业应用程序”，并单击“应用程序代理”到达该页。

   ![Azure 门户中的连接器组管理](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>下载新连接器

若要下载新连接器，请使用该页顶部的“下载连接器”按钮。

在计算机上安装连接器，可以直接看到后端应用程序。 通常，需要在应用程序所在的同一台服务器上安装连接器。 下载后，连接器应显示在此菜单中。 单击该连接器，并使用“连接器组”下拉列表以确保其属于正确的组。 保存更改。

   ![从 Azure 门户下载连接器](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>移动活动连接器

如果具有的活动连接器应属于组并且直接可见目标后端应用程序，可将该连接器移入已分配的组。 若要执行此操作，请单击“连接器”。 在“连接器组”字段中，使用下拉列表选择正确的组，并单击“保存”。

## <a name="resolve-an-inactive-connector"></a>解决处于不活动状态的连接器

如果仅该组中的连接器处于不活动状态，则这些连接器所在的计算机可能并未解除阻止所有必要的端口。

有关调查此问题的详细信息，请参阅端口故障排除文档。

## <a name="next-steps"></a>后续步骤
[了解 Azure AD 应用程序代理连接器](manage-apps/application-proxy-connectors.md)


