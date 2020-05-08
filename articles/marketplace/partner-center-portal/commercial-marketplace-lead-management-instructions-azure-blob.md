---
title: 通过 Azure Blob 存储的潜在客户管理-Microsoft 商用 marketplace
description: 了解如何使用 Azure Blob 配置 Microsoft AppSource 和 Azure Marketplace 的潜在客户
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: dsindona
ms.openlocfilehash: 076edc62a467701eaf0de23f280cdaf2abd945de
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792711"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>使用 Azure Blob 存储来管理商业市场主管

>[!Caution]
>Azure Blob 存储的商业 marketplace 支持已弃用，不再是处理产品/服务的潜在客户的选项。 如果你当前拥有为 Azure Blob 配置的主导管理的商业 marketplace 产品，则你将无法再接收客户领导。 请将你的潜在客户管理配置更新为任何其他潜在客户管理选项。 了解[潜在客户管理登录页](./commercial-marketplace-get-customer-leads.md)上的其他选项。

 如果你的客户关系管理（CRM）系统未在合作伙伴中心获得 Microsoft AppSource 和 Azure Marketplace 主管的明确支持，则可以使用 Azure Blob 存储。 然后，可以选择导出数据并将其导入到 CRM 系统中。 本文中的说明将指导你完成创建 Azure 存储帐户的过程，以及该帐户下的 blob。 此外，你可以使用 "电源自动执行" 功能创建一个新的流，以便在你的产品/服务收到潜在顾客时发送电子邮件通知。

>[!NOTE]
>这些说明中使用的电源自动连接器需要付费订阅才能实现自动化。 在按照本文中的说明操作之前，请务必考虑此情况。

## <a name="configure-azure-blob-storage"></a>配置 Azure Blob 存储

1. 如果没有 Azure 帐户，可以[创建免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)。

2. Azure 帐户处于活动状态后，登录到[Azure 门户](https://portal.azure.com)。

3. 在 Azure 门户中，使用以下过程创建存储帐户。  
    1. 在左侧菜单栏中选择 " **+ 创建资源**"。  **新**窗格（边栏选项卡）将显示在右侧。
    2. 在 "**新建**" 窗格中选择 "**存储**"。  **功能**列表显示在右侧。
    3. 选择要开始创建帐户的**存储帐户**。  请按照[创建存储帐户一](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)文中的说明进行操作。

    ![创建 Azure 存储帐户的步骤](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    有关存储帐户的详细信息，请选择[快速入门教程](https://docs.microsoft.com/azure/storage/)。  有关存储定价的详细信息，请参阅[存储定价](https://azure.microsoft.com/pricing/details/storage/)。

4. 等待存储帐户预配，该过程通常要花费几分钟时间。  然后，通过选择 "**查看所有资源**"，或从 Azure 门户的左侧导航菜单栏中选择 "**所有资源**"，从 "Azure 门户"**主页**访问存储帐户。

    ![访问 Azure 存储帐户](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. 从 "存储帐户" 窗格中，选择 "**访问密钥**"，然后复制密钥的*连接字符串*值。 保存此值，因为这是要在发布门户中提供的*存储帐户连接字符串*值，用于接收适用于 marketplace 产品/服务的潜在顾客。

     连接字符串的一个示例是：

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure 存储密钥](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. 在存储帐户页中，选择 " **blob**"。

   ![Azure 存储密钥](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. 在 "blob" 页上，选择 " **+ 容器**" 按钮。

8. 键入新容器的**名称**。 容器名称必须小写，必须以字母或数字开头，并且只能包含字母、数字和短划线 (-) 字符。 有关容器名称和 Blob 名称的详细信息，请参阅 [Naming and referencing containers, blobs, and metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)（命名和引用容器、Blob 和元数据）。

    请保存此值，因为这是你需要在发布门户中提供以便接收适用于 marketplace 产品/服务的潜在客户的 "*容器名称*" 值。

9. 将容器的公共访问级别设置为 "**专用" （无匿名访问）**。

10. 选择“确定”  创建容器。

    ![新容器](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>配置你的产品/服务以将潜在客户发送到 Azure Blob 存储

准备好在发布门户中为产品/服务配置潜在顾客管理信息时，请执行以下步骤：

1. 导航到产品/服务的 "**产品/服务设置**" 页。
2. 选择 "潜在顾客管理" 部分下的 "**连接**"。

    ![连接优惠](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. 在 "连接详细信息" 弹出窗口上，为潜在顾客目标选择 " **Azure Blob** "。

    ![连接详细信息](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. 按照这些说明提供所获得的**容器名称**和**存储帐户连接字符串**。

    * 容器名称示例：`marketplaceleadcontainer`
    * 存储帐户连接字符串示例： `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![连接详细信息](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. 选择“保存”  。

    > [!NOTE]
    > 您必须先完成该产品/服务的其余部分的配置，然后发布该产品/服务，才能接收该产品/服务的潜在客户。


