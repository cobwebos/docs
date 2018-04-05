---
title: 在 Azure 中创建 Analysis Services 服务器 | Microsoft Docs
description: 了解如何在 Azure 中创建 Analysis Services 服务器实例。
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
tags: ''
ms.assetid: 7f560216-8a9a-4d06-852e-48cf24deab19
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: 9a80d7e6e9f24ccc730fe747fa0a71b91f22b7e9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="create-an-azure-analysis-services-server-in-azure-portal"></a>在 Azure 门户中创建 Azure Analysis Services 服务器
本文指导如何在 Azure 订阅中创建 Analysis Services 服务器资源。

## <a name="before-you-begin"></a>开始之前
若要完成本快速入门教程，需要：

* **Azure 订阅**：访问 [Azure 免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p/)以创建帐户。
* **Azure Active Directory**：订阅必须与 Azure Active Directory 租户相关联。 并且，需要使用 Azure Active Directory 中的一个帐户登录 Azure。 不支持 Microsoft 帐户。 若要了解详细信息，请参阅[身份验证和用户权限](analysis-services-manage-users.md)。
* **资源组**：使用已有资源组，或[创建新组](../azure-resource-manager/resource-group-overview.md)。

> [!NOTE]
> 创建服务器可能会导致新的计费服务。 有关详细信息，请参阅 [Analysis Services 定价](https://azure.microsoft.com/pricing/details/analysis-services/)。
> 
> 

## <a name="to-create-a-server-in-the-azure-portal"></a>在 Azure 门户中创建服务器
1. 登录到 [Azure 门户](https://portal.azure.com)。  
2. 单击“+ 新建” > “数据 + 分析” > “Analysis Services”。
3. 在“Analysis Services”边栏选项卡中，填写必填字段，并按“创建”。
   
    ![创建服务器](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **服务器名称**：键入用于引用服务器的唯一名称。
   * **订阅**：选择此服务器计费的订阅。
   * **资源组**：这些容器旨在帮助管理 Azure 资源的集合。 有关详细信息，请参阅[资源组](../azure-resource-manager/resource-group-overview.md)。
   * **位置**：此 Azure 数据中心位置托管该服务器。 选择最接近最大用户群的位置。
   * **定价层**：选择定价层。 最多支持 400 GB 的表格模型。 若要了解详细信息，请参阅 [Azure Analysis Services 定价](https://azure.microsoft.com/pricing/details/analysis-services/)。
4. 单击“创建”。

创建通常不超过一分钟，一般几秒钟便可完成。 如果选择“添加到门户”，请导航到门户查看新服务器。 或者，导航到“所有服务” > “Analysis Services”，查看服务器是否就绪。

 ![仪表板](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>后续步骤
创建服务器后，便可使用 SSDT 或 SSMS 对其[部署模型](analysis-services-deploy.md)。

如果部署到服务器的模型连接本地数据源，则需要在网络中的计算机上安装[本地数据网关](analysis-services-gateway.md)。

