---
title: 创建和管理 B2B 解决方案的集成帐户 - Azure 逻辑应用 | Microsoft Docs
description: 使用 Azure 逻辑应用为企业集成和 B2B 解决方案创建、链接、移动和删除集成帐户
services: logic-apps
documentationcenter: ''
author: ecfan
manager: cfowler
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: e661920974c2b0d28200d4c3d82bd644a7a55395
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166227"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>使用逻辑应用创建和管理 B2B 解决方案的集成帐户

在使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)构建[企业集成和 B2B 解决方案](../logic-apps/logic-apps-enterprise-integration-overview.md)之前，必须先拥有一个集成帐户，你可以在其中创建、存储和管理 B2B 项目，例如交易合作伙伴、协议、地图、架构、证书等等。 必须先[将集成帐户链接](#link-account)到逻辑应用，然后逻辑应用才能处理集成帐户中的项目并使用逻辑应用 B2B 连接器（如 XML 验证）。 若要链接它们，集成帐户和逻辑应用必须具有*相同*的 Azure 位置或区域。

本文演示如何执行以下任务：

* 创建集成帐户。
* 将集成帐户链接到逻辑应用。
* 将集成帐户移到另一个 Azure 资源组或订阅。
* 删除集成帐户。

如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

使用 Azure 帐户凭据登录到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>。

## <a name="create-integration-account"></a>创建集成帐户

1. 在 Azure 主菜单中，选择“所有服务”。 在搜索框中，输入“集成帐户”作为筛选器，然后选择“集成帐户”。

   ![查找集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. 在“集成帐户”下，选择“添加”。

   ![选择“添加”以创建集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. 提供有关集成帐户的信息： 

   ![提供集成帐户的详细信息](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | 属性 | 必选 | 示例值 | 说明 | 
   |----------|----------|---------------|-------------|
   | 名称 | 是 | test-integration-account | 集成帐户的名称。 对于此示例，请使用指定的名称。 | 
   | 订阅 | 是 | <*Azure-subscription-name*> | 要使用的 Azure 订阅的名称 | 
   | 资源组 | 是 | test-integration-account-rg | 用于组织相关资源的 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)的名称。 对于此示例，请使用指定的名称创建新的资源组。 | 
   | 定价层 | 是 | 免费 | 要使用的定价层。 对于此示例，请选择“免费”，但有关详细信息，请参阅[逻辑应用限制和配置](../logic-apps/logic-apps-limits-and-config.md)和[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 | 
   | Location | 是 | 美国西部 | 用于存储集成帐户信息的区域。 选择与逻辑应用相同的位置，或者在与集成帐户相同的位置创建逻辑应用。 对于此示例， | 
   | Log Analytics | 否 | 关闭 | 对于诊断日志记录，请保留“关闭”设置。 | 
   ||||| 

4. 准备就绪后，请选择“固定到仪表板”，并选择“创建”。

   Azure 将集成帐户部署到所选位置（通常在一分钟内完成）后，Azure 将打开集成帐户。

   ![Azure 打开集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

现在，必须先将集成帐户链接到逻辑应用，然后逻辑应用才能使用集成帐户。

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>链接到逻辑应用

为了让逻辑应用能够访问包含 B2B 项目（如贸易合作伙伴、协议、地图和架构）的集成帐户，必须将集成帐户链接到逻辑应用。 

> [!NOTE]
> 集成帐户和逻辑应用必须位于同一区域。

1. 在 Azure 门户中，查找并打开逻辑应用。

2. 在逻辑应用的菜单中，在“设置”下，选择“工作流设置”。 从“选择集成帐户”列表中，选择想要链接到逻辑应用的集成帐户。

   ![选择集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. 若要完成链接，请选择“保存”。

   ![选择集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   当集成帐户成功链接后，Azure 会显示一条确认消息。 

   ![Azure 确认成功链接](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

现在，逻辑应用可以使用集成帐户中的任何和所有项目以及 B2B 连接器，例如 XML 验证和平面文件编码或解码。  

## <a name="unlink-from-logic-app"></a>从逻辑应用取消链接

若要将逻辑应用链接到其他集成帐户，或不再将集成帐户用于逻辑应用程序，可以通过 Azure 资源浏览器删除链接。

1. 在浏览器中，转到 <a href="https://resources.azure.com" target="_blank">Azure 资源浏览器 (https://resources.azure.com)</a>。 确保已使用相同的 Azure 凭据登录。

   ![Azure 资源浏览器](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. 在搜索框中，输入逻辑应用的名称，然后查找并选择逻辑应用。

   ![查找并选择逻辑应用](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. 在浏览器标题栏上，选择“读/写”。

   ![启用“读/写”模式](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. 在“数据”选项卡上，选择“编辑”。

   ![在“数据”选项卡上，选择“编辑”](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. 在编辑器中，找到集成帐户的 `integrationAccount` 属性并删除具有以下格式的该属性：

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   例如：

   ![找到“integrationAccount”属性定义](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. 在“数据”选项卡上，选择 **Put** 保存所做的更改。 

   ![选择“Put”保存更改](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. 在 Azure 门户中，在逻辑应用的“工作流设置”下，检查“集成帐户”属性现在是否显示为空。

   ![检查集成帐户是否未链接](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>移动集成帐户

可以将集成帐户移到另一个 Azure 订阅或资源组。

1. 在 Azure 主菜单中，选择“所有服务”。 在搜索框中，输入“集成帐户”作为筛选器，然后选择“集成帐户”。

   ![查找集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. 在“集成帐户”下，选择要移动的集成帐户。 在集成帐户菜单上的“设置”下，选择“属性”。

   ![在“设置”下选择“属性”](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. 更改集成帐户的 Azure 资源组或订阅。

   ![选择“更改资源组”或“更改订阅”](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. 完成后，请确保使用项目的新资源 ID 更新任何和所有脚本。  

## <a name="delete-integration-account"></a>删除集成帐户

1. 在 Azure 主菜单中，选择“所有服务”。 在搜索框中，输入“集成帐户”作为筛选器，然后选择“集成帐户”。

   ![查找集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. 在“集成帐户”下，选择要删除的集成帐户。 在集成帐户菜单上，选择“概述”，然后选择“删除”。 

   ![选择“集成帐户”。 在“概述”页上，选择“删除”](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. 若要确认要删除集成帐户，请选择“是”。

   ![若要确认删除，请选择“是”](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>后续步骤

* [创建贸易合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [创建协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)