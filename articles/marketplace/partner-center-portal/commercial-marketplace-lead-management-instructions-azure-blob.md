---
title: 商业市场程序中的 Azure 表*Azure 应用商店
description: 配置 Azure Blob 的潜在客户管理
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285242"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Azure Blob 的潜在客户管理说明

>[!Caution]
>处理市场产品/服务的潜在顾客的 Azure Blob 选项已被弃用。 如果当前已发布具有 Azure Blob 潜在顾客管理配置的产品/服务，则不再接收客户潜在客户。 请将潜在顾客管理配置更新为任何其他潜在顾客管理选项。 了解[潜在客户管理登录页上](./commercial-marketplace-get-customer-leads.md)的其他选项。

如果合作伙伴中心中没有明确支持客户关系管理 （CRM） 系统以接收 Azure 应用商店和 AppSource 潜在顾客，则可以使用 Azure Blob 来处理这些潜在顾客。 然后，您可以选择导出数据并将其导入 CRM 系统。 本文中的说明将为您提供创建 Azure 存储帐户和该帐户下的 Azure Blob 的过程。 此外，您可以使用 Microsoft Flow 创建新流，在产品/服务收到潜在顾客时发送电子邮件通知。


## <a name="how-to-configure-azure-blob"></a>如何配置 Azure Blob

1. 如果没有 Azure 帐户，可以[创建免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)。
1. Azure 帐户处于活动状态后，请登录到 Azure[门户](https://portal.azure.com)。
1. 在 Azure 门户中，使用以下过程创建存储帐户。  
    1. 选择 "在左侧菜单栏中**创建资源**"。  "**新建**"窗格（边栏）将向右显示。
    2. 在 **"新建"** 窗格中选择 **"存储**"。  **右侧将显示"精选**"列表。
    3. 选择 **"存储帐户**"以开始创建帐户。  按照文章"[创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)"中的说明进行操作。

    ![创建 Azure 存储帐户的步骤](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    有关存储帐户的详细信息，请选择[快速入门教程](https://docs.microsoft.com/azure/storage/)。  有关存储定价的详细信息，请参阅[存储定价](https://azure.microsoft.com/pricing/details/storage/)。

4. 等待存储帐户预配，此过程通常需要几分钟时间。  然后，通过选择 **"查看所有资源**"或从 Azure 门户的左侧导航菜单栏中选择 **"所有资源**"，从 Azure 门户的**主页**访问存储帐户。

    ![访问 Azure 存储帐户](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. 从存储帐户窗格中，选择 **"访问密钥"** 并复制密钥的连接*字符串*值。 保存此值，因为这是存储*帐户连接字符串*值，您需要在发布门户中提供该值才能接收市场产品的潜在顾客。

     连接刺痛的一个示例是：

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure 存储密钥](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. 从存储帐户页面中，选择**Blob**。

   ![Azure 存储密钥](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. 在 Blob 上一次，选择 **"+ 容器"** 按钮。

8. 键入新容器**的名称**。 容器名称必须小写，必须以字母或数字开头，并且只能包含字母、数字和短划线 (-) 字符。 有关容器和 blob 名称的详细信息，请参阅[命名和引用容器、blob 和元数据](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

    保存此值，因为这是您需要在发布门户中提供的*容器名称*值，以便接收市场产品/服务的潜在顾客。

9. 将对容器的公共访问级别设置为 **"私有"（无匿名访问）。**

10. 选择“确定”**** 创建容器。

    ![新容器](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>将产品/服务配置为将潜在顾客发送到 Azure Blob

准备好在发布门户中配置产品/服务的首席管理信息时，请按照以下步骤操作：

1. 导航到产品 **/服务"产品/服务"设置**页面。
2. 在"潜在顾客管理"部分下选择 **"连接**"。

    ![连接优惠](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. 在"连接详细信息"弹出窗口上，为潜在顾客目标选择**Azure Blob。**

    ![连接详细信息](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. 提供您按照这些说明提供的**容器名称**和**存储帐户连接字符串**。

    * 容器名称示例：`marketplaceleadcontainer`
    * 存储帐户连接字符串示例：`DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net`![连接详细信息](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. 选择“保存”。****

    > [!NOTE]
    > 您必须完成配置产品/服务的其余部分并发布它，然后才能收到产品/服务的潜在顾客。


