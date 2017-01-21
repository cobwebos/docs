---
title: "在 Azure 中创建 Analysis Services 服务器 | Microsoft Docs"
description: "了解如何在 Azure 中创建 Analysis Services 服务器实例。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 7f560216-8a9a-4d06-852e-48cf24deab19
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/20/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 147c27d12b87379ff02b871ceabd8154e53b6c1a
ms.openlocfilehash: f189e19b5b843d6a7d056c8230bc06d85e568202


---
# <a name="create-an-analysis-services-server"></a>创建 Analysis Services 服务器
本文指导如何在 Azure 订阅中创建新的 Analysis Services 服务器资源。

## <a name="before-you-begin"></a>开始之前
若要开始，您需要：

* **Azure 订阅**：访问 [Azure 免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p/)以创建帐户。
* **Azure Active Directory**：订阅必须与 Azure Active Directory 租户相关联。 并且，需要使用 Azure Active Directory 中的一个帐户登录 Azure。 不支持 Microsoft 帐户。 若要了解详细信息，请参阅[用户身份验证](analysis-services-overview.md#secure)。
* **资源组**：使用已有资源组，或[创建新组](../azure-resource-manager/resource-group-overview.md)。

> [!NOTE]
> 创建 Analysis Services 服务器可能会产生新的计费服务。 有关详细信息，请参阅 [Analysis Services 定价](https://azure.microsoft.com/pricing/details/analysis-services/)。
> 
> 

## <a name="create-an-analysis-services-server"></a>创建 Analysis Services 服务器
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击“+ 新建” > “智能 + 分析” > “Analysis Services”。
3. 在“Analysis Services”边栏选项卡中，填写必填字段，然后按“创建”。
   
    ![创建服务器](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **服务器名称**：键入用于引用服务器的唯一名称。
   * **订阅**：选择此服务器计费的订阅。
   * **资源组**：资源组是旨在帮助管理 Azure 资源集合的容器。 有关详细信息，请参阅[资源组](../azure-resource-manager/resource-group-overview.md)。
   * **位置**：此 Azure 数据中心位置托管该服务器。 选择最接近最大用户群的位置。
   * **定价层**：选择定价层。 支持最多 100 GB 的表格模型。 此后可以随时更改定价层。
4. 单击“创建” 。

创建通常不超过一分钟，一般几秒钟便可完成。 如果选择“添加到门户”，请导航到门户查看新服务器。 或者，请导航到**更多服务** > **Analysis Services**，查看服务器是否就绪。 如果未显示，请刷新列表。

 ![仪表板](./media/analysis-services-create-server/aas-create-server-dashboard.png)

## <a name="automate-server-creation"></a>自动执行服务器创建
可使用 Azure 资源管理器模板文件自动执行运行状态下的服务器预配。 观看此帮助视频，了解详细信息。

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesAutomation/player]
>
>


## <a name="next-steps"></a>后续步骤
创建服务器后，便可使用 SSDT 或 SSMS 对其[部署模型](analysis-services-deploy.md)。

如果部署到服务器的模型连接本地数据源，则需要在网络中的计算机上安装[本地数据网关](analysis-services-gateway.md)。




<!--HONumber=Nov16_HO4-->


