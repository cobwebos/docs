---
title: "在 Marketplace 中使用 Azure 托管应用程序 | Microsoft Docs"
description: "介绍如何创建可通过 Marketplace 使用的 Azure 托管应用程序。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/11/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: baf456740bddd562391ed64d707f990c8921d710
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---

# <a name="consume-azure-managed-applications-in-the-marketplace"></a>在 Marketplace 中使用 Azure 托管应用程序

如[托管应用程序概述](managed-application-overview.md)一文中所述，在端到端体验中有两种应用场景。 一种是发布者或供应商希望创建供客户使用的托管应用程序。 第二种是最终客户或使用者使用托管应用程序。 本文介绍第二种场景。 其中说明了如何从 Microsoft Azure Marketplace 部署托管应用程序。

## <a name="create-from-the-marketplace"></a>通过 Marketplace 创建

通过 Marketplace 部署托管应用程序的过程类似于通过 Marketplace 部署任何类型的资源。 

在门户中选择“+ 新建”，搜索要部署的解决方案类型。 在可用的选项中，选择所需的选项。

![搜索解决方案](./media/managed-application-consume-marketplace/search-apps.png)

查看应用程序的摘要，选择“创建”。

![创建托管应用程序](./media/managed-application-consume-marketplace/create-marketplace-managed-app.png)

像创建任何其他解决方案时一样，屏幕上会显示要提供值的字段。 这些字段根据所要创建的托管应用程序的类型而异。 

## <a name="view-support-information"></a>查看支持信息

部署托管应用程序后，查看该应用程序的支持信息。 托管应用程序边栏选项卡中列出了支持信息。

![support](./media/managed-application-consume-marketplace/support.png)

## <a name="view-publisher-permissions"></a>查看发布者权限

管理应用程序的供应商已获得相应资源的访问权限。 若要查看这些权限，请选择“授权”。

![授权](./media/managed-application-consume-marketplace/authorizations.png)

## <a name="next-steps"></a>后续步骤

* 有关在 Marketplace 中发布托管应用程序的信息，请参阅 [Marketplace 中的 Azure 托管应用程序](managed-application-author-marketplace.md)。
* 若要发布仅供组织中用户使用的托管应用程序，请参阅[创建和发布服务目录托管应用程序](managed-application-publishing.md)。
* 若要使用仅供组织中用户使用的托管应用程序，请参阅[使用服务目录托管应用程序](managed-application-consumption.md)。
