---
title: Azure 表 |Azure Marketplace
description: 为 Azure 表配置潜在顾客管理。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 7151be3ac9f55825fd2e9dde35c9afda6a30726a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902632"
---
# <a name="configure-lead-management-using-an-azure-table"></a>使用 Azure 表配置潜在客户管理

如果你的客户关系管理 (CRM) 系统未在合作伙伴中心获得 Azure Marketplace 和 AppSource 潜在顾客的明确支持, 则可以使用 Azure 表来处理这些潜在顾客。 然后, 可以选择导出数据并将其导入到 CRM 系统中。 本文中的说明将指导你完成创建 Azure 存储帐户的过程, 以及该帐户下的 Azure 表。 此外, 你可以使用 Microsoft Flow 创建新流, 以便在你的产品/服务收到潜在客户时发送电子邮件通知。

## <a name="configure-azure-table"></a>配置 Azure 表

1. 如果没有 Azure 帐户，可以[创建免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)。
2. Azure 帐户处于活动状态后, 登录到[Azure 门户](https://portal.azure.com)。
3. 在 Azure 门户中, 使用以下过程创建存储帐户。  
    1. 在左侧菜单栏中选择 " **+ 创建资源**"。  **新**窗格 (边栏选项卡) 将显示在右侧。
    2. 在 "**新建**" 窗格中选择 "**存储**"。  **功能**列表显示在右侧。
    3. 选择要开始创建帐户的**存储帐户**。  请按照[创建存储帐户一](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)文中的说明进行操作。

        ![创建 Azure 存储帐户的步骤](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        有关存储帐户的详细信息, 请选择[快速入门教程](https://docs.microsoft.com/azure/storage/)。  有关存储定价的详细信息，请参阅[存储定价](https://azure.microsoft.com/pricing/details/storage/)。

4. 等待存储帐户预配, 该过程通常要花费几分钟时间。  然后, 通过选择 "**查看所有资源**", 或从 Azure 门户的左侧导航菜单栏中选择 "**所有资源**", 从 Azure 门户的 "**主页**" 访问存储帐户。

    ![访问 Azure 存储帐户](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. 从 "存储帐户" 窗格中, 选择 "**访问密钥**", 然后复制密钥的*连接字符串*值。 保存此值, 因为这是要在发布门户中提供的*存储帐户连接字符串*值, 用于接收适用于 marketplace 产品/服务的潜在顾客。 

    连接字符串的一个示例是:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Azure 存储密钥](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. 从 "存储帐户" 窗格中, 选择 "**表**" 并选择 " **+ 表**" 以创建表。 输入表的名称, 然后选择 **"确定"** 。 保存此值, 因为如果要配置 MS Flow 以在收到潜在顾客时接收电子邮件通知, 则需要此值。

    ![Azure 表](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    可以使用 [Azure 存储资源管理器](https://archive.codeplex.com/?p=azurestorageexplorer)或任何其他工具来查看存储表中的数据。 还可以导出 Azure 表中的数据。 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>可有可无将 Microsoft Flow 与 Azure 表一起使用  

每次将潜在顾客添加到 Azure 表时，都可使用 [Microsoft Flow](https://docs.microsoft.com/flow/) 自动发送通知。 如果没有帐户, 可以[注册一个免费帐户](https://flow.microsoft.com/)。

### <a name="lead-notification-example"></a>潜在顾客通知示例

使用此示例作为指南，创建一个简单的流，在将新的潜在顾客添加到 Azure 表时自动发送电子邮件通知。 如果表存储已更新, 此示例将设置一个重复周期, 以便每隔一小时发送发送信息。

1. 登录到你的 Microsoft Flow 帐户。
2. 在左侧导航栏中，选择“我的流”。
3. 在顶部导航栏上，选择“+ 新建”。  
4. 在下拉列表中, 选择 " **+ 预定-从空白**

   ![我的流 * * + 计划-从空白 * *](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  在 "*生成计划的流*" 窗口下, 对 "间隔" 和 "小时" 的 "频率" 下的 "*重复每次*选择"。 此外, 为流指定一个名称 (如果需要)。 选择“创建”。

    >[!Note]
    >虽然此示例使用 1 小时间隔，但你可以选择最适合业务需求的间隔和频率。

    ![构建计划的流。](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. 选择“+新建步骤”。
7. 在 "*选择操作*" 窗口中搜索 "获取过去时间", 然后选择 "操作" 下的 "**获取过去时间**"。

   ![选择操作。](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. 在“获取过去时间”窗口中，将间隔设置为 1。 从“时间单位”下拉列表中，选择“小时”。

    >[!Important]
    >请确保此时间间隔和时间单位与您在步骤5中为重复项配置的间隔和频率匹配。

    ![设置过去时间间隔](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>可以随时检查流以验证是否正确配置了每个步骤。 要检查流，请从流菜单栏中选择“流检查器”。

下一组步骤中，将连接到 Azure 表，并设置处理逻辑以处理新的潜在顾客。

9. 在 "获取过去时间" 步骤之后, 选择 " **+ 新建步骤**", 然后在 "*选择操作*" 窗口中搜索 "获取实体"。
10. 在 "**操作**" 下, 选择 "**获取实体" (Azure 表存储)** 。
11. 在 " **Azure 表存储**" 窗口中, 提供以下字段的信息并选择 "**创建**":

    * *连接名称*–为在此流和 Azure 表之间建立的连接提供有意义的名称。
    * *存储帐户名称*–提供 Azure 表的存储帐户的名称。 可以在存储帐户的 "**访问密钥**" 页中找到此项。
    * *共享存储密钥*–提供 Azure 表的存储帐户的密钥值。 可以在存储帐户的 "**访问密钥**" 页中找到此项。

        ![Azure 表存储。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    单击 "创建" 后, 将看到 "*获取实体*" 窗口。 选择 "**显示高级选项**" 并为以下字段提供信息:

       * *表*–选择 Azure 表存储的名称 (从步骤6中有关如何配置 Azure 表的说明)。 在此示例中选择 "marketplaceleads" 表后, 下一屏幕捕获会显示提示。

            ![Azure 表获取实体。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *筛选查询*–选择此字段, 并将此函数粘贴到字段中:`Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Azure 表获取实体-Filter Querry。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. 现在, 你已完成连接到 Azure 表的连接, 选择 "**新建步骤**", 添加一个条件, 用于扫描 azure 表中的新潜在顾客。 

13. 在 "**选择操作**" 窗口中, 选择 "**操作**", 然后选择 "**条件" 控件**。

    ![Azure 表-选择操作。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. 在“条件”窗口中，选择“选择值”字段，然后在弹出窗口中选择“表达式”。

15. 将 `length(body('Get_entities')?['value'])` 粘贴到 fx 字段中。 选择“确定”以添加此功能。 

16. 要完成条件设置：
    1. 从下拉列表中选择“大于”。
    2. 输入 0 作为值

        ![Azure 表-条件。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

在接下来的几个步骤中, 将根据条件的结果设置要执行的操作。

* 如果条件解析为“如果否”，则不执行任何操作。
* 如果条件解析为“如果是”，则触发连接 Office 365 帐户以发送电子邮件的操作。 

17. **如果选择 "是"** , 请选择 "**添加操作**"。

    ![Azure 表-条件, * * 如果是 * *。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. 选择 "**发送电子邮件 (Office 365 Outlook)** "。

    ![Azure 表-条件, * * 如果是, 则发送电子邮件。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >如果要使用不同的电子邮件提供程序搜索, 并选择 "发送电子邮件通知 (邮件)" 作为操作。 说明将演示如何使用 Office 365 Outlook 进行配置, 但对于不同的电子邮件提供程序, 说明是类似的。

19. 在**Office 365 Outlook**窗口中, 为以下字段提供信息:

    1. 收件人 - 输入将收到此通知的所有人的电子邮件地址。
    1. 主题 - 提供电子邮件的主题。 例如：新潜在顾客！
    1. **Body** -添加要包括在每封电子邮件中的文本 (可选), 然后粘贴到正文`body('Get_entities')?['value']`中。

    >[!Note]
    >可将其他静态或动态数据点插入此电子邮件的正文中。

    ![Azure 表-条件, * * If 是 * *, Office 365 Outlook 窗口。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. 选择“保存”以保存流。 Microsoft Flow 将自动测试流中的错误。 如果没有任何错误，流将在保存后开始运行。

下一个屏幕截图显示了最终流应如何显示的示例。

![最终流的示例。](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>管理你的流

在流运行后管理流十分容易。 可完全控制流。 例如，可以停止它、编辑它、查看运行历史记录，然后获取分析。 下一个屏幕截图显示了可用于管理流的选项。 

 ![管理流](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

流将一直保持运行状态，直到你使用“关闭流量”选项来停止流。

若未收到任何潜在顾客电子邮件通知，则表示新的潜在顾客尚未添加到 Azure 表中。 若有任何流故障，将收到类似下一个屏幕截图中的示例的电子邮件。

 ![流失败电子邮件通知](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>配置你的产品/服务以将潜在客户发送到 Azure 表

准备好在发布门户中为产品/服务配置潜在顾客管理信息时, 请执行以下步骤:

1. 导航到产品/服务的 "**产品/服务设置**" 页。
2. 选择 "潜在顾客管理" 部分下的 "**连接**"。
3. 在 "连接详细信息" 弹出窗口上, 为**潜在顾客目标**选择 " **Azure 表**", 然后将以下步骤中创建的 azure 存储帐户中的连接字符串粘贴到**存储帐户连接字符串**中。定义域.
4. 选择**保存**。 

>[!Note]
>您必须先完成该产品/服务的其余部分的配置, 然后发布该产品/服务, 才能接收该产品/服务的潜在客户。

生成潜在客户后, Microsoft 会将潜在顾客发送到 Azure 表。 如果配置了流, 还会将电子邮件发送到配置的电子邮件地址。

![潜在顾客管理](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![潜在客户管理-连接详细信息](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![潜在客户管理-连接详细信息存储帐户](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

