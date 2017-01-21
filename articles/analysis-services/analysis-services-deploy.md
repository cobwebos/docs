---
title: "部署到 Azure Analysis Services | Microsoft Docs"
description: "了解如何将表格模型部署到 Azure Analysis Services 服务器。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 5f1f0ae7-11de-4923-a3da-888b13a3638c
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/20/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 18d64f1ad4ef0dd41ae9302d08e02e94d1c608f5


---
# <a name="deploy-to-azure-analysis-services"></a>部署到 Azure Analysis Services
在 Azure 订阅中创建服务器之后，便可以开始将表格模型数据库部署到其中。 SQL Server Data Tools (SSDT) 可用于生成和部署正在处理的表格模型项目。 或者，可以使用 SQL Server Management Studio (SSMS) 从 Analysis Services 实例部署现有的表格模型数据库。

## <a name="before-you-begin"></a>开始之前
若要开始，您需要：

* Azure 中的 **Analysis Services 服务器**。 若要了解详细信息，请参阅[在 Azure 中创建 Analysis Services](analysis-services-create-server.md)。
* SSDT 中的**表格模型项目**或 Analysis Services 实例上兼容级别为 1200 的现有表格模型。 从未创建过？ 请尝试 [Adventure Works 教程](https://msdn.microsoft.com/library/hh231691.aspx)。
* **本地网关** - 如果你组织的网络中有一个或多个本地数据源，则需要安装[本地数据网关](analysis-services-gateway.md)。 网关对于云中的服务器是必需的，可帮助服务器连接到本地数据源，以在模型中处理和刷新数据。

## <a name="to-deploy-a-tabular-model-from-ssdt"></a>从 SSDT 部署表格模型
若要从 SSDT 部署，请确保使用更新于 2016 年 9 月 30 日或之后的[最新版本](https://msdn.microsoft.com/library/mt204009.aspx)。

> [!TIP]
> 在部署之前，请确保可以在表中处理数据。 在 SSDT 中，单击“模型” > “处理” > “全部处理”。 如果处理失败，则部署也将失败。
> 
> 

1. 在部署之前，需要获取服务器名称。 在 **Azure 门户**中，单击“服务器”>“概述” > “服务器名称”，然后复制服务器名称。
   
    ![在 Azure 中获取服务器名称](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. 在 SSDT >“解决方案资源管理器”中，右键单击项目 >“属性”。 然后在“部署” > “服务器”中粘贴服务器名称。   
   
    ![将服务器名称粘贴到部署服务器属性](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
3. 在**解决方案资源管理器**中，右键单击“属性”，然后单击“部署”。 系统可能会提示登录到 Azure。
   
    ![部署到服务器](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    部署状态将在“输出”窗口和“部署”中显示。
   
    ![部署状态](./media/analysis-services-deploy/aas-deploy-status.png)

就是这么简单！

## <a name="to-deploy-using-xmla-script"></a>使用 XMLA 脚本部署
1. 在 SSMS 中，右键单击想要部署的表格模型数据库，单击“脚本” > “脚本数据库形式” > “创建到”，然后选择一个位置。
2. 在想要向其部署的服务器实例上执行查询。 如果要部署到用同一服务器，则必须至少更改 XMLA 脚本中的**名称**属性。  

## <a name="but-something-went-wrong"></a>但是，出错了
如果部署元数据时部署失败，可能是因为 SSDT 无法连接到服务器。 确保可以使用 SSMS 连接到服务器。 然后确保项目的“部署服务器”属性正确。

如果部署在表上失败，可能是因为服务器无法连接到数据源。 如果数据源位于组织网络中的本地位置，请务必安装[本地数据网关](analysis-services-gateway.md)。

## <a name="next-steps"></a>后续步骤
现在，你已将表格模型部署到服务器，可以与其进行连接了。 可以[使用 SSMS 连接到它](analysis-services-manage.md)以对其进行管理。 并且，可以[使用客户端工具连接到它](analysis-services-connect.md)，如 Power BI、Power BI Desktop 或 Excel，然后开始创建报表。




<!--HONumber=Nov16_HO3-->


