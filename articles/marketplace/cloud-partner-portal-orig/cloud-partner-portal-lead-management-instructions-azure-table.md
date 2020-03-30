---
title: Azure 表存储 |Azure 应用商店
description: 在 Azure 表存储中配置潜在顾客管理。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: cee28c65b97d4ac163f78b746dc7be9ee210843c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280348"
---
# <a name="lead-management-instructions-for-table-storage"></a>表存储的引线管理说明

本文介绍如何配置 Azure 表存储来管理销售线索。 表存储可帮助您存储和修改客户信息。

## <a name="configure-table-storage"></a>配置表存储

1. 如果没有 Azure 帐户，[请创建一个免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)。
1. 帐户处于活动状态后，登录到[Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户中，按照以下步骤操作：  
    1. 选择 "在左侧的窗格中**创建资源**"。 "**新建**"窗格将打开。
    1. 在 **"新建"** 窗格中，选择 **"存储**"。 **"精选"** 列表将在右侧打开。
    1. 选择“存储帐户”****。 然后，按照[创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)中的说明操作。

    ![创建 Azure 存储帐户](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    有关存储帐户的更多信息，请参阅[快速入门教程](https://docs.microsoft.com/azure/storage/)。 有关定价信息，请参阅[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

1. 等待存储帐户预配，这通常需要几分钟时间。 然后，从 Azure 门户的主页访问帐户：选择 **"查看**导航窗格中的所有资源"或 **"所有资源**"。

    ![访问 Azure 存储帐户](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. 从存储帐户窗格中复制密钥的存储帐户连接字符串。 将其粘贴到云合作伙伴门户中的存储帐户的连接**字符串**字段中。

    连接字符串示例：

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Azure 存储密钥](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

您可以使用[Azure 存储资源管理器](https://azurestorageexplorer.codeplex.com/)或类似工具查看表存储中的数据。 您还可以从中导出数据。

## <a name="use-microsoft-flow-with-table-storage-optional"></a>将 Microsoft 流与表存储一起使用 （*可选*）

当潜在顾客添加到表存储时，可以使用[Microsoft Flow](https://docs.microsoft.com/flow/)自动发送通知。 如果您没有 Microsoft Flow 帐户，[请注册一个免费帐户](https://flow.microsoft.com/)。

### <a name="lead-notification-example"></a>潜在顾客通知示例

此示例演示如何创建基本流。 当新潜在顾客添加到表存储时，流会自动每小时发送电子邮件通知。

1. 登录 Microsoft Flow 帐户。
1. 在左侧的导航窗格中，选择 **"我的流**"。
1. 在顶部导航栏中，选择 **"新建**"。  
1. 从下拉列表中选择 **"从空白中创建**"。
1. 在 **"从空白创建流**"下，选择**从空白创建**。

   ![从头开始创建新的流](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. 在连接器和触发器搜索页面上，选择“触发器”****。
1. 在“触发器”下，选择“重复周期”********。
1. 在 **"定期"** 窗口中，将 **"间隔**"的默认设置保持**1。** 从 **"频率**"下拉列表中，选择 **"小时**"。

   >[!NOTE] 
   >此示例使用一小时间隔。 但是，您可以选择最适合您业务需求的间隔和频率。

   ![设置 1 小时频率以进行重复](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. 选择 **+新步骤**。
1. 搜索 **"获取过去时间**"，然后在 **"选择操作**"下选择 **"获取过去时间**"。

    ![查找并选择"过去时间"操作](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. 在 **"获取过去时间"窗口中**，将**间隔**设置为**1**。  从 **"时间单位**"下拉列表中，选择 **"小时**"。
    >[!IMPORTANT] 
    >确保**间隔**和**时间单元**与为重复配置的间隔和频率匹配（步骤 8）。

    ![设置过去时间间隔](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >您可以随时检查流以验证每个步骤是否配置正确：从"流"菜单栏中选择 **"流检查器**"。

在下一组步骤中，您将连接到存储表并设置处理逻辑以处理新潜在顾客。

1. **在"获取过去时间**"步骤后，选择 **"新建步骤**"，然后搜索**获取实体**。
1. 在“操作”下，选择“获取实体”，然后选择“显示高级选项”************。
1. 在 **"获取实体"** 窗口中，填写以下字段：

   - **表**：表存储的名称。 下图显示了输入的"市场线索"：

     ![为 Azure 表名选择自定义值](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **筛选器查询**：当您选择此字段时，"**过去时间"** 图标将显示在弹出窗口中。 选择 **"过去时间**"以将此值用作时间戳以筛选查询。 或者，您可以将以下函数粘贴到字段中：
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![设置筛选器查询功能](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. 选择 **"新建步骤**"以添加条件以扫描表存储以寻找新潜在顾客。

   ![使用"新步骤"添加条件以扫描表存储](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. 在 **"选择操作"** 窗口中，选择 **"操作**"，然后选择 **"条件控制**"。

     ![添加条件控制](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. 在 **"条件"** 窗口中，**选择"选择值**"，然后在弹出窗口中选择 **"表达式**"。
1. 粘贴`length(body('Get_entities')?['value'])`到***fx***字段中。 选择“确定”以添加此功能****。 



     ![在条件中添加一个函数](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. 根据条件的结果设置要采取的操作。

    1. 选择**大于**下拉列表中。
   1. 输入**0**作为值。

     ![根据条件结果设置操作](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. 如果条件解析为"如果没有"，则不要执行任何操作。

    如果条件解析为"如果是"，则触发连接 Office 365 帐户以发送电子邮件的操作：
   1. 选择 **"添加操作**"。
   1. 选择“发送电子邮件”****。
   1. 在 **"发送电子邮件**"窗口中，在以下字段中输入信息：

      - **到**： 收到通知的每个人的电子邮件地址。
      - **主题**：电子邮件的主题。 例如：*新线索！*
      - **正文**：您希望在每封电子邮件中包含的文本（可选）。 还粘贴`body('Get_entities')?['value']`为函数以插入潜在顾客信息。

        >[!NOTE] 
        >您可以在电子邮件正文中插入其他静态或动态数据点。

      ![设置潜在顾客通知的电子邮件](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. 选择“保存”以保存流****。 微软流将自动测试其错误。 如果没有任何错误，则流在保存后开始运行。

    下图显示了最终流的外观示例。

    [![最终流序列](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    （*选择图像以放大它。*

### <a name="manage-your-flow"></a>管理您的流程

运行后，可以轻松管理流。 可完全控制流。 例如，可以停止它、编辑它、查看运行历史记录，然后获取分析。 下图显示了流管理选项。

 ![流管理选项](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

流继续运行，直到选择 **"关闭流**"。

如果您未收到任何潜在顾客电子邮件通知，则表存储中未添加新潜在顾客。
如果发生流故障，您将看到类似以下示例的电子邮件：

 ![流失败电子邮件通知](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>后续步骤

[配置客户潜在客户](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
