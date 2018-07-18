---
title: 通过 SSDT 部署到 Azure Analysis Services | Microsoft Docs
description: 了解如何通过 SSDT 将表格模型部署到 Azure Analysis Services 服务器。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9a3b969af861d6fb750b8aad666f2e1dfb7c8a3d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447279"
---
# <a name="deploy-a-model-from-ssdt"></a>从 SSDT 部署模型
在 Azure 订阅中创建服务器之后，便可以开始将表格模型数据库部署到其中。 SQL Server Data Tools (SSDT) 可用于生成和部署正在处理的表格模型项目。 

## <a name="prerequisites"></a>先决条件
要开始，需要：

* Azure 中的 **Analysis Services 服务器**。 若要了解详细信息，请参阅[创建 Azure Analysis Services 服务器](analysis-services-create-server.md)。
* SSDT 中的表格模型项目或兼容级别为 1200 或更高的现有表格模型。 从未创建过？ 尝试 [Adventure Works Internet 销售表格建模教程](https://msdn.microsoft.com/library/hh231691.aspx)。
* **本地网关** - 如果组织的网络中有一个或多个本地数据源，则需要安装[本地数据网关](analysis-services-gateway.md)。 网关对于云中的服务器是必需的，可帮助服务器连接到本地数据源，以在模型中处理和刷新数据。

> [!TIP]
> 在部署之前，请确保可以在表中处理数据。 在 SSDT 中，单击“模型” > “处理” > “全部处理”。 如果处理失败，将无法成功部署。
> 
> 

## <a name="get-the-server-name"></a>获取服务器名称

在 **Azure 门户**中，单击“服务器”>“概述” > “服务器名称”，并复制服务器名称。
   
![在 Azure 中获取服务器名称](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-ssdt"></a>若要从 SSDT 部署

1. 在SSDT > “解决方案资源管理器”中，右键单击项目，并选择“属性”。 然后在“部署” > “服务器”中粘贴服务器名称。   
   
    ![将服务器名称粘贴到部署服务器属性](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. 在**解决方案资源管理器**中，右键单击“属性”，并单击“部署”。 系统可能会提示登录到 Azure。
   
    ![部署到服务器](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    部署状态会在“输出”窗口和“部署”中显示。
   
    ![部署状态](./media/analysis-services-deploy/aas-deploy-status.png)

就是这么简单！


## <a name="troubleshooting"></a>故障排除
如果部署元数据时部署失败，可能是因为 SSDT 无法连接到服务器。 确保可以使用 SSMS 连接到服务器。 然后确保项目的“部署服务器”属性正确。

如果部署在表上失败，可能是因为服务器无法连接到数据源。 如果数据源位于组织网络中的本地位置，请务必安装[本地数据网关](analysis-services-gateway.md)。

## <a name="next-steps"></a>后续步骤
现在，已将表格模型部署到服务器，可以与其进行连接了。 可以[使用 SSMS 连接到它](analysis-services-manage.md)以对其进行管理。 并且，可以[使用客户端工具连接到它](analysis-services-connect.md)，如 Power BI、Power BI Desktop 或 Excel，并开始创建报表。

