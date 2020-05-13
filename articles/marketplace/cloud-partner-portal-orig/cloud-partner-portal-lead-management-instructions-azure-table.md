---
title: Azure 表存储 |Azure Marketplace
description: 配置 Azure 表存储中的潜在客户管理。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: e65f2041cdb2e25341bfd63783c70ec09c1216b5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124697"
---
# <a name="lead-management-instructions-for-table-storage"></a>表存储的潜在客户管理说明

本文介绍如何配置 Azure 表存储以管理销售线索。 表存储有助于存储和修改客户信息。

## <a name="configure-table-storage"></a>配置表存储

1. 如果没有 Azure 帐户，请[创建一个免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)。
1. 帐户处于活动状态后，登录到[Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户中，执行以下步骤：  
    1. 在左侧窗格中选择 " **+ 创建资源**"。 这将打开**新**窗格。
    1. 在 "**新建**" 窗格中，选择 "**存储**"。 一个**特色**列表将在右侧打开。
    1. 选择“存储帐户”****。 然后，按照[创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)中的说明进行操作。

    ![创建 Azure 存储帐户](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    有关存储帐户的详细信息，请参阅[快速入门教程](https://docs.microsoft.com/azure/storage/)。 有关定价信息，请参阅[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

1. 等待存储帐户预配，这通常要花费几分钟时间。 然后，从 "Azure 门户" 的 "主页" 页访问帐户：在导航窗格中选择 "**查看所有资源**" 或 "**所有资源**"。

    ![访问 Azure 存储帐户](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. 从 "存储帐户" 窗格中，复制密钥的存储帐户连接字符串。 将其粘贴到云合作伙伴门户中存储帐户的 "**连接字符串**" 字段。

    示例连接字符串：

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Azure 存储密钥](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

您可以使用[Azure 存储资源管理器](https://azurestorageexplorer.codeplex.com/)或类似的工具来查看表存储中的数据。 您还可以导出数据。

## <a name="use-microsoft-flow-with-table-storage-optional"></a>将 Microsoft Flow 用于表存储（*可选*）

当向表存储添加潜在顾客时，可以使用[Microsoft Flow](https://docs.microsoft.com/flow/)自动发送通知。 如果没有 Microsoft Flow 帐户，请[注册免费帐户](https://flow.microsoft.com/)。

### <a name="lead-notification-example"></a>潜在顾客通知示例

此示例演示如何创建基本流。 当向表存储中添加新的潜在顾客时，每小时自动发送一封电子邮件通知。

1. 登录 Microsoft Flow 帐户。
1. 在左侧的导航窗格中，选择 "**我的流**"。
1. 在顶部导航栏中，选择 " **+ 新建**"。  
1. 从下拉列表中，选择 " **+ 从空白创建**"。
1. 在 "**从空白创建流**" 下，选择 "**从空白创建**"。

   ![从头开始创建新的流](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. 在连接器和触发器搜索页面上，选择“触发器”****。
1. 在“触发器”下，选择“重复周期”********。
1. 在 "**重复周期**" 窗口中，将 "**间隔**" 保留为默认设置 " **1** "。 从 "**频率**" 下拉列表中，选择 "**小时**"。

   >[!NOTE] 
   >此示例使用一个小时的间隔。 但你可以选择最适合你的业务需求的时间间隔和频率。

   ![为定期设置1小时频率](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. 选择 " **+ 新步骤**"。
1. 搜索 "**获取过去时间**"，然后选择 "**选择操作**" 下的 "**获取过去时间**"。

    ![找到并选择 "获取过去时间" 操作](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. 在 "**获取过去的时间**" 窗口中，将 "**间隔**" 设置为**1**。  从 "**时间单位**" 下拉列表中，选择 "**小时**"。
    >[!IMPORTANT] 
    >请确保**时间间隔**和**时间单位**与你为重复周期配置的间隔和频率匹配（步骤8）。

    ![设置获取过期时间间隔](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >您可以随时检查您的流，验证是否已正确配置每个步骤：从 "流" 菜单栏中选择 "**流检查器**"。

在下一组步骤中，将连接到存储表，并设置处理逻辑以处理新的潜在顾客。

1. 在 "**获取过去时间**" 步骤之后，选择 " **+ 新建步骤**"，然后搜索 "**获取实体**"。
1. 在“操作”下，选择“获取实体”，然后选择“显示高级选项”************。
1. 在 "**获取实体**" 窗口中，填写以下字段：

   - **表**：表存储的名称。 下图显示输入了 "MarketPlaceLeads"：

     ![为 Azure 表名选择自定义值](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **筛选查询**：如果选择此字段，"**获取过去时间**" 图标将显示在弹出窗口中。 选择 "**过去时间**" 可将此值用作时间戳来筛选查询。 或者，可以将以下函数粘贴到字段中：
   
      `CreatedTime Timestamp gt '@{body('Get_past_time')}'` 

     ![设置 filter query 函数](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. 选择 "**新建步骤**"，添加一个条件，以扫描表存储中的新潜在顾客。

   ![使用 "新建步骤" 添加扫描表存储的条件](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. 在 "**选择操作**" 窗口中，选择 "**操作**"，然后选择 "**条件控制**"。

     ![添加条件控件](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. 在 "**条件**" 窗口中，选择 "**选择值**"，然后在弹出窗口中选择 "**表达式**"。
1. 粘贴 `length(body('Get_entities')?['value'])` 到***fx***字段。 选择“确定”以添加此功能****。 



     ![在条件中添加一个函数](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. 根据条件的结果设置要采取的操作。

    1. 从下拉列表中选择 "**大于**"。
   1. 输入**0**作为值。

     ![基于条件结果设置操作](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. 如果条件解析为 "If no"，则不执行任何操作。

    如果条件解析为 "If yes"，则会触发连接 Office 365 帐户以发送电子邮件的操作：
   1. 选择 "**添加操作**"。
   1. 选择“发送电子邮件”****。
   1. 在 "**发送电子邮件**" 窗口中，在以下字段中输入信息：

      - **若为，** 则为将收到通知的所有人发送电子邮件地址。
      - **Subject**：电子邮件的主题。 例如：*新潜在顾客！*
      - **Body**：要包含在每封电子邮件中的文本（可选）。 还可将 `body('Get_entities')?['value']` 作为函数粘贴，以插入潜在客户信息。

        >[!NOTE] 
        >可以在电子邮件的正文中插入其他静态或动态数据点。

      ![设置潜在顾客通知的电子邮件](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. 选择“保存”以保存流  。 Microsoft Flow 将自动对其进行测试以查找错误。 如果没有任何错误，则流在保存后将开始运行。

    下图显示了最终流的外观示例。

    [![最终流序列](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    （*选择要放大的图像。*）

### <a name="manage-your-flow"></a>管理你的流

在流运行后，可以轻松地对其进行管理。 可完全控制流。 例如，可以停止它、编辑它、查看运行历史记录，然后获取分析。 下图显示了流管理选项。

 ![流管理选项](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

在选择 "**关闭流**" 之前，流将一直保持运行状态。

如果你没有收到任何潜在客户电子邮件通知，则不会向你的表存储中添加新的潜在顾客。
如果出现流故障，你将收到如下所示的电子邮件：

 ![流失败电子邮件通知](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>后续步骤

[配置潜在顾客](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
