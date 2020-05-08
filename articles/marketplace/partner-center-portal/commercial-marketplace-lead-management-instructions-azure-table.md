---
title: 通过 Azure 表存储的潜在客户管理-Microsoft 商用 marketplace
description: 了解如何使用 Azure 表存储来配置 Microsoft AppSource 和 Azure Marketplace 的潜在客户
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 2ecca18e9de02bfe5f3bcb972d0b4034ab8012ac
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791011"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>使用 Azure 表存储来管理商业市场主管

如果你的客户关系管理（CRM）系统不能在合作伙伴中心获得 Microsoft AppSource 和 Azure Marketplace 主管，则可以使用 Azure 表存储来处理这些潜在顾客。 然后，可以选择导出数据并将其导入到 CRM 系统中。 本文介绍如何创建 Azure 存储帐户和该帐户下的表。 此外，你可以通过使用 "电源自动执行" 来创建新的流，以便在你的产品/服务收到潜在客户时发送电子邮件通知。

## <a name="configure-an-azure-storage-account"></a>配置 Azure 存储帐户

1. 如果没有 Azure 帐户，可以[创建免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)。
1. Azure 帐户处于活动状态后，登录到[Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户中，使用以下过程创建存储帐户：

    1. 在左侧菜单栏中选择 " **+ 创建资源**"。 **新**窗格将显示在右侧。
    1. 在 "**新建**" 窗格中选择 "**存储**"。 右侧将显示一个**特色**列表。
    1. 选择要开始创建帐户的**存储帐户**。 按照[创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)中的说明进行操作。

        ![创建 Azure 存储帐户的步骤](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        有关存储帐户的详细信息，请参阅[快速入门教程](https://docs.microsoft.com/azure/storage/)。 有关存储定价的详细信息，请参阅[存储定价](https://azure.microsoft.com/pricing/details/storage/)。

1. 等待存储帐户已预配。 此过程通常要花费几分钟时间。 

## <a name="create-a-table-in-your-storage-account"></a>在存储帐户中创建表

1. 在 Azure 门户的 "**主页**" 页上，选择 "**查看所有资源**" 以访问存储帐户。 你还可以从 "Azure 门户" 的左侧菜单栏中选择 "**所有资源**"。

    ![访问 Azure 存储帐户](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

1. 从 "存储帐户" 窗格中，选择 "**访问密钥**"，然后复制密钥的**连接字符串**值。 请保存此值，因为它是**存储帐户连接字符串**值，需要在发布门户中提供此值才能接收 Azure Marketplace 产品/服务的潜在顾客。 

    下面是连接字符串的示例。

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    ![Azure 存储密钥](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

1. 从 "存储帐户" 窗格中选择 "**表**"，然后选择 " **+ 表**" 以创建表。 输入表的名称，然后选择 **"确定"**。 请保存此值，因为如果要配置一个流以在收到潜在顾客时接收电子邮件通知，则需要此值。

    ![Azure 表](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    您可以使用[Azure 存储资源管理器](https://archive.codeplex.com/?p=azurestorageexplorer)或任何其他工具查看存储表中的数据。 还可以导出 Azure 表中的数据。 

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>可有可无使用 Power 自动功能获取潜在顾客通知

每次将潜在客户添加到 Azure 存储表时，都可以使用[电源自动功能](https://docs.microsoft.com/flow/)自动执行通知。 如果没有帐户，可以[注册一个免费帐户](https://flow.microsoft.com/)。

### <a name="lead-notification-example"></a>潜在顾客通知示例

该示例创建一个流，该流在新的潜在客户添加到 Azure 表存储时自动发送电子邮件通知。 如果表存储已更新，此示例将设置一个重复周期，以便每隔一小时发送发送信息。

1. 登录到你的 Power 自动帐户帐户。
1. 在左侧栏上，选择 "**我的流**"。
1. 在顶部栏上，选择 " **+ 新建**"。 
1. 在下拉列表中，选择 " **+ 计划--从空白**"。

   ![我的流 + 计划--从空白](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. 在 "**生成计划的流**" 窗口中，对于 "**重复**间隔"，请选择 " **1** " 作为 "频率" 和 "**小时**"。 此外，为流指定名称（如果需要）。 选择“创建”。 

   >[!NOTE]
   >虽然此示例使用一小时的间隔，但你可以选择最适合你的业务需求的时间间隔和频率。

   ![构建计划的流](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. 选择 " **+ 新步骤**"。
1. 在 "**选择操作**" 窗口中，搜索 "**获取过去时间**"。 然后，在 "**操作**" 下，选择 "**获取过去时间**"。

   ![选择操作](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. 在 "**获取过去的时间**" 窗口中，将 "**间隔**" 设置为**1**。 从 "**时间单位**" 下拉列表中，选择 "**小时**"。

    >[!IMPORTANT]
    >请确保在步骤8中发送的时间间隔和时间单位与你在步骤5中为重复项配置的时间间隔和频率匹配。

    ![设置获取过期时间间隔](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP] 
   >可以随时检查流以验证是否正确配置了每个步骤。 若要检查流，请从**流**菜单栏中选择 "**流检查器**"。

   在下一组步骤中，您将连接到您的表，并设置处理逻辑以处理新的潜在顾客。

1. 选择 " **+ 新步骤**"。 然后在 "**选择操作**" 窗口中搜索 "**获取实体**"。
1. 在 "**操作**" 下，选择 "**获取实体" （Azure 表存储）**。
1. 在 " **Azure 表存储**" 窗口中，提供以下框的信息并选择 "**创建**"：

    * **连接名称**：为在此流和表之间建立的连接提供有意义的名称。
    * **存储帐户名称**：提供表的存储帐户的名称。 可以在存储帐户的 "**访问密钥**" 页上找到此名称。
    * **共享存储密钥**：提供表的存储帐户的密钥值。 可以在存储帐户的 "**访问密钥**" 页上找到此值。

      ![Azure 表存储窗口](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   选择 "**创建**" 后，将显示 "**获取实体**" 窗口。 在此处选择 "**显示高级选项**"，并提供以下框的信息：

   * **表**：选择表的名称（从 "[创建表](#create-a-table-in-your-storage-account)"）。 下图显示了为此示例选择了 "marketplaceleads" 表时的提示。

     ![获取实体窗口](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **筛选查询**：选择此框，并将此函数粘贴到框中：`Timestamp gt datetime'@{body('Get_past_time')}'`

     ![获取实体，筛选器查询框](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. 现在，你已完成连接到 Azure 表的连接，选择 "**新建步骤**"，添加一个条件，用于扫描 azure 表中的新潜在顾客。

1. 在 "**选择操作**" 窗口中，选择 "**操作**"。 然后选择 "**条件控制**"。

    ![选择操作窗口](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. 在 "**条件**" 窗口中，选择 "**选择值**"。 然后在弹出窗口中选择 "**表达式**"。

1. 粘贴`length(body('Get_entities')?['value'])`到**fx**框中。 选择“确定”以添加此功能****。 

1. 要完成条件设置：
    1. 从下拉列表中选择 "**大于**"。
    2. 输入**0**作为值。

        ![条件窗口](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   在接下来的几个步骤中，将根据条件的结果设置要执行的操作：

   * 如果条件**解析为，则不**执行任何操作。
   * 如果条件解析为“如果是”，则触发连接 Office 365 帐户以发送电子邮件的操作****。 

1. **如果选择 "是"**，请选择 "**添加操作**"。

    ![条件窗口，如果是，则添加一个操作](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. 选择 "**发送电子邮件（Office 365 Outlook）**"。

    ![条件窗口，如果是，则发送电子邮件](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >若要使用其他电子邮件提供程序，请搜索并选择 "**发送电子邮件通知（邮件）** " 作为操作。 说明演示了如何使用 Office 365 Outlook 进行配置，但对于不同的电子邮件提供程序，说明是类似的。

1. 在 Office 365 Outlook 窗口中，提供以下框的信息：

    1. **若要**：输入将收到此通知的所有用户的电子邮件地址。
    1. **使用者**：提供电子邮件的主题。 这是一个**新的潜在顾客！**
    1. **正文**：添加要包括在每封电子邮件中的文本（可选），然后粘贴`body('Get_entities')?['value']`。

    >[!NOTE]
    >可将其他静态或动态数据点插入此电子邮件的正文中。

    ![条件窗口，如果是，Office 365 Outlook 窗口](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. 选择“保存”以保存流  。 电源自动测试会自动测试流中的错误。 如果没有任何错误，则流在保存后将开始运行。

下图显示了最终流的外观示例。

![最终流的示例](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>管理你的流

在流运行后管理流非常简单。 可完全控制流。 例如，可以停止它、编辑它、查看运行历史记录，然后获取分析。 下图显示可用于管理流的选项。

 ![管理流](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

流将一直保持运行状态，直到你使用“关闭流量”选项来停止流****。

如果你没有收到任何潜在客户电子邮件通知，则表示新的潜在客户尚未添加到 Azure 表。 如果出现任何流故障，你将收到类似于此示例的电子邮件。

 ![流失败电子邮件通知](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>配置你的产品/服务以将潜在客户发送到 Azure 表

准备好在发布门户中为产品/服务配置潜在顾客管理信息时，请执行以下步骤。

1. 请参阅产品/服务的**产品/服务设置**页。

1. 选择 "**潜在顾客管理**" 部分下的 "**连接**"。
     ![潜在客户管理](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

1. 在 "**连接详细信息**" 弹出窗口中，为**潜在顾客目标**选择**Azure 表**。 
     ![潜在客户管理，连接详细信息](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. 在 "**存储帐户连接字符串**" 框中按照前面的步骤创建的 Azure 存储帐户中粘贴连接字符串。
     ![潜在客户管理，连接详细信息存储帐户](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **联系人电子邮件**：向公司中的人员提供电子邮件，该电子邮件应在收到新潜在客户时收到电子邮件通知。 可以提供多个电子邮件，用分号分隔。

1. 选择“确定”  。

若要确保已成功连接到潜在客户目标，请选择 "**验证**" 按钮。 如果成功，则在潜在客户目标中会出现测试主管。

>[!NOTE]
>您必须先完成该产品/服务的其余部分的配置，然后发布该产品/服务，才能接收该产品/服务的潜在客户。

生成潜在客户后，Microsoft 会将潜在顾客发送到 Azure 表。 如果配置了流，还会将电子邮件发送到配置的电子邮件地址。
