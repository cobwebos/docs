---
title: 将虚拟机提供给 Azure Stack 用户使用 | Microsoft Docs
description: 了解如何使虚拟机在 Azure 堆栈上可用
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 9329cb0dbfa24cf239b820573ef7f642cdca9103
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248153"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>教程： 将虚拟机提供给你 Azure 堆栈用户

Azure Stack 云管理员可以创建产品/服务供用户（有时称为租户）订阅。 通过订阅到产品/服务，用户可以使用某项服务提供的 Azure 堆栈服务。

本教程演示如何创建虚拟机，产品，然后登录以用户身份测试产品/服务。

要学习的知识：

> [!div class="checklist"]
> * 创建产品
> * 添加映像
> * 测试产品/服务

在 Azure Stack 中，可通过订阅、产品/服务和计划将服务交付给用户。 用户可以订阅多个产品/服务。 某项服务可以有一个或多个计划，并计划可以有一个或多个服务。

![订阅、产品/服务和计划](media/azure-stack-key-features/image4.png)

有关详细信息，请参阅 [Azure Stack 中的重要功能和概念](azure-stack-key-features.md)。

## <a name="create-an-offer"></a>创建产品

产品/服务是提供者提供给用户购买或订阅的一个或多个计划的组合。 创建提议的过程具有几个步骤。 首先，将提示你创建产品/服务，则某一计划、 和最后，配额。

1. [登录](azure-stack-connect-azure-stack.md)到作为云管理员联系，然后选择门户**新建** > **提供 + 计划** > **提供**。

   ![新产品/服务](media/azure-stack-tutorial-tenant-vm/image01.png)

2. 在**新提供**，输入**显示名称**和**资源名称**，然后选择一个新的或现有**资源组**。 “显示名称”是产品/服务的友好名称。 只有云操作员可以看到“资源名称”。 管理员使用此名称将该产品/服务作为 Azure 资源管理器资源处理。

   ![显示名称](media/azure-stack-tutorial-tenant-vm/image02.png)

3. 选择**基本计划**，然后在**计划**部分中，选择**添加**到产品/服务中添加新的计划。

   ![添加计划](media/azure-stack-tutorial-tenant-vm/image03.png)

4. 在“新建计划”部分填写“显示名称”和“资源名称”。 显示名称是用户可看到的计划的友好名称。 只有云操作员可以看到“资源名称”。 云操作员使用该名称以 Azure 资源管理器资源的形式处理计划。

   ![计划显示名称](media/azure-stack-tutorial-tenant-vm/image04.png)

5. 选择**服务**。 从服务列表中，选择**Microsoft.Compute**， **Microsoft.Network**，和**Microsoft.Storage**。 选择**选择**向计划添加这些服务。

   ![计划服务](media/azure-stack-tutorial-tenant-vm/image05.png)

6. 选择**配额**，然后选择你想要创建的配额的第一个服务。 对于 IaaS 配额，请使用下面的示例作为指南配置的计算、 网络和存储服务的配额。

   - 首先，创建计算服务的配额。 在命名空间列表中，选择**Microsoft.Compute** ，然后选择**创建新配额**。

     ![创建新配额](media/azure-stack-tutorial-tenant-vm/image06.png)

   - 在**创建配额**，输入配额的名称。 可以更改，也可以接受任何演示了你要创建的配额的配额值。 我们在此示例中，接受默认设置，然后选择**确定**。

     ![配额名称](media/azure-stack-tutorial-tenant-vm/image07.png)

   - 选取**Microsoft.Compute**在命名空间列表，然后选择你创建的配额。 这链接到计算服务的配额。

     ![选择配额](media/azure-stack-tutorial-tenant-vm/image08.png)

      网络和存储服务中重复这些步骤。 完成后，选择**确定**中**配额**以保存所有配额。

7. 在**新计划**，选择**确定**。

8. 下**计划**，选择新的计划，然后**选择**。

9. 在**新提议**，选择**创建**。 创建提议时，你将看到一条通知。

10. 在仪表板菜单上，选择**提供**然后选择你创建的提议。

11. 选择**更改状态**，然后选择和**公共**。

    ![公共状态](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>添加映像

部署虚拟机之前，必须先将一个映像添加到 Azure Stack Marketplace。 可以从 Azure Marketplace 添加所选的映像，包括 Linux 映像。

如果在联网场景中操作，且已向 Azure 注册 Azure Stack 实例，则可以使用[将 Marketplace 项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md) 主题中所述的步骤，从 Azure Marketplace 下载 Windows Server 2016 VM 映像。

有关将不同的项添加到 Marketplace 的信息，请参阅 [Azure Stack Marketplace](azure-stack-marketplace.md)。

## <a name="test-the-offer"></a>测试产品/服务

创建产品/服务后，可对其进行测试。 将登录的用户订阅产品，然后再添加虚拟机。

1. **订阅产品/服务**

   a. 登录到用户门户与用户帐户并选择**获取订阅**磁贴。
   - 对于集成系统，URL 将取决于运算符的区域和外部域名称，和将采用格式https://portal.&lt;*区域*&gt;。&lt;*FQDN*&gt;。
   - 如果使用 Azure Stack 开发工具包，则门户地址为 https://portal.local.azurestack.external。

   ![获取订阅](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. 在**获取订阅**，输入你的订阅中的名称**显示名称**字段。 选择**提供**，然后选择一个产品/服务**选择提议**列表。 选择**创建**。

   ![创建产品](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. 若要查看订阅，请选择**更多的服务**，然后选择**订阅**。 选择你新订阅，以确定哪些服务正在订阅的一部分。

   >[!NOTE]
   >在订阅产品/服务后，你可能需要刷新门户后，若要确定哪些服务正在新订阅的一部分。

2. **预配虚拟机**

   从用户门户中，你可以设置使用新的订阅的虚拟机。

   a. 登录到用户门户与用户帐户。
      - 对于集成系统，URL 将取决于运算符的区域和外部域名称，和将采用格式https://portal.&lt;*区域*&gt;。&lt;*FQDN*&gt;。
   - 如果使用 Azure Stack 开发工具包，则门户地址为 https://portal.local.azurestack.external。

   b.  在仪表板中，选择**新建** > **计算** > **Windows Server 2016 数据中心 Eval**，然后选择**创建**.

   c. 在**基础知识**，提供以下信息：
      - 输入**名称**
      - 输入**用户名称**
      - 输入**密码**
      - 选择**订阅**
      - 创建**资源组**（或选择一个现有。） 
      - 选择**确定**若要保存此信息。

   d. 在**选择大小**，选择**A1 标准**，，然后**选择**。  

   e. 在**设置**，选择**虚拟网络**。

   f. 在**选择虚拟网络**，选择**新建**。

   g. 在**创建虚拟网络**，接受所有默认值，然后选择**确定**。

   h. 选择**确定**中**设置**保存网络配置。

   ![创建虚拟网络](media/azure-stack-provision-vm/image04.png)

   i. 在**摘要**，选择**确定**创建虚拟机。  

   j. 若要查看新的虚拟机，选择**的所有资源**。 搜索虚拟机，然后从搜索结果中选择其名称。

   ![所有资源](media/azure-stack-provision-vm/image06.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建产品
> * 添加映像
> * 测试产品/服务

转到下一教程，了解如何执行以下操作：
> [!div class="nextstepaction"]
> [让 Azure 堆栈用户能够使用 SQL 数据库](azure-stack-tutorial-sql-server.md)