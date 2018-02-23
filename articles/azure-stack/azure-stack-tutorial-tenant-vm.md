---
title: "让 Azure 堆栈用户能够使用虚拟机 |Microsoft 文档"
description: "若要使虚拟机在 Azure 堆栈上可用的教程"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2017
ms.author: brenduns
ms.reviewer: 
ms.custom: mvc
ms.openlocfilehash: d169a18df8916cfb8dd30b00248099a3d374aacc
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>让 Azure 堆栈用户能够使用虚拟机

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

作为 Azure 堆栈云管理员，你可以创建你的用户 （有时称为租户） 可以订阅的产品/服务。 使用其订阅，用户便可以使用 Azure 堆栈服务。

这篇文章演示了如何创建优惠后，，然后测试它。 为了使测试，你将在登录到门户的用户，订阅产品，，然后创建使用订阅的虚拟机。

你将学习：

> [!div class="checklist"]
> * 创建产品
> * 添加图像
> * 测试产品/服务


Azure 堆栈中传递给使用订阅、 服务和计划的用户的服务。 用户可以订阅多个产品/服务。 产品可以包含一个或多个计划，计划可以包含一个或多个服务。

![订阅、 服务和计划](media/azure-stack-key-features/image4.png)

若要了解详细信息，请参阅[主要功能和 Azure 堆栈中的概念](azure-stack-key-features.md)。

## <a name="create-an-offer"></a>创建产品

现在，你可以收到操作准备好你的用户。 当你启动该过程时，你首先系统提示创建产品/服务，则某一计划、 和最后的配额。

3. **创建产品/服务**

   提供是组的一个或多个提供程序显示给用户购买或订阅的计划。

   a. [登录](azure-stack-connect-azure-stack.md)到与云管理员联系，然后单击门户**新建** > **提供 + 计划** > **提供**。
   ![新提议](media/azure-stack-tutorial-tenant-vm/image01.png)

   b. 在**新提供**部分中，填写**显示名称**和**资源名称**，然后选择一个新的或现有**资源组**。 显示名称是产品/服务的友好名称。 仅云操作员可以查看资源名称。 它是管理员使用，以便为 Azure 资源管理器资源提供的名称。

   ![显示名称](media/azure-stack-tutorial-tenant-vm/image02.png)

   c. 单击**基本计划**，然后在**计划**部分中，单击**添加**到产品/服务中添加新的计划。

   ![添加计划](media/azure-stack-tutorial-tenant-vm/image03.png)

   d.单击“下一步”。 在**新的计划**部分中，填写**显示名称**和**资源名称**。 显示名称是用户看到的计划的友好名称。 仅云操作员可以查看资源名称。 它是云操作员用于在使用 Azure 资源管理器资源作为计划的名称。

   ![计划的显示名称](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. 单击**服务**，选择**Microsoft.Compute**， **Microsoft.Network**，和**Microsoft.Storage**，然后单击**选择**。

   ![计划服务](media/azure-stack-tutorial-tenant-vm/image05.png)

   f. 单击**配额**，然后选择你想要创建的配额的第一个服务。 对于 IaaS 配额，请按照这些步骤的计算、 网络和存储服务。

   在此示例中，我们首先要创建计算服务的配额。 在命名空间列表中，选择**Microsoft.Compute**命名空间，然后单击**创建新配额**。
   
   ![创建新配额](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. 上**创建配额**节中，键入配额的名称和设置配额然后单击所需的参数**确定**。

   ![配额名称](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. 现在，要使**Microsoft.Compute**，选择你创建的配额。

   ![选择配额](media/azure-stack-tutorial-tenant-vm/image08.png)

   对于网络和存储服务，请重复上述步骤，然后单击**确定**上**配额**部分。

   i. 单击**确定**上**新计划**部分。

   j. 上**计划**部分，选择新的计划并单击**选择**。

   k. 上**新提议**部分中，单击**创建**。 创建提议后，你会看到一个通知。

   l. 在仪表板菜单上，单击**提供**，然后单击你创建的提议。

   m. 单击**更改状态**，然后单击**公共**。

   ![公共状态](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>添加图像

你可以设置虚拟机之前，你必须将映像添加到 Azure 堆栈应用商店。 您可以添加您的选择，包括 Linux 映像，从 Azure 应用商店的映像。

如果你正在连接方案中工作，并且如果你向 Azure 注册你的 Azure 堆栈实例，然后你可以下载 Windows Server 2016 VM 映像从 Azure 应用商店使用中所述的步骤[下载应用商店从 Azure 到 Azure 堆栈的项](azure-stack-download-azure-marketplace-item.md)主题。

有关将不同的项添加到应用商店的信息，请参阅[Azure 堆栈应用商店](azure-stack-marketplace.md)。

## <a name="test-the-offer"></a>测试产品/服务

现在，你已创建某项服务，你可以对其进行测试。 以用户身份登录并订阅产品并将虚拟机。

1. **订阅产品/服务**

   现在你可以登录到门户以用户订阅产品。

   a. 某个用户，然后单击以登录到用户门户**获取订阅**。
   - 对于集成系统，URL 将取决于运算符的区域和外部的域名，并将采用格式 https://portal。&lt;*区域*&gt;。&lt;*FQDN*&gt;。
   - 如果你使用 Azure 堆栈开发工具包，该门户的地址是 https://portal.local.azurestack.external。

   ![获取订阅](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. 在**显示名称**字段中，键入你的订阅的名称，单击**提供**，单击一个产品/服务**选择提议**部分，并依次**创建**。

   ![创建产品](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. 若要查看你创建的订阅，请单击**更多的服务**，单击**订阅**，然后单击你的新订阅。  

   在订阅产品/服务后，刷新门户后，若要确定哪些服务正在新订阅的一部分。

2. **预配虚拟机**

   现在你可以登录到门户设置虚拟机使用订阅的用户。 

   a. 以用户身份登录到用户门户。
      - 对于集成系统，URL 将取决于运算符的区域和外部的域名，并将采用格式 https://portal。&lt;*区域*&gt;。&lt;*FQDN*&gt;。
   - 如果你使用 Azure 堆栈开发工具包，该门户的地址是 https://portal.local.azurestack.external。

   b.  在仪表板中，单击**新建** > **计算** > **Windows Server 2016 数据中心 Eval**，然后单击**创建**.

   c. 在**基础知识**部分中，键入**名称**，**用户名**，和**密码**，选择**订阅**，创建**资源组**（或选择一个现有），然后单击**确定**。

   d.单击“下一步”。 在**选择大小**部分中，单击**A1 标准**，然后单击**选择**。  

   e. 在**设置**部分中，单击**虚拟网络**。 在**选择虚拟网络**部分中，单击**新建**。 在**创建虚拟网络**部分，接受所有默认值，然后单击**确定**。 在**设置**部分中，单击**确定**。

   ![创建虚拟网络](media/azure-stack-provision-vm/image04.png)

   f. 在**摘要**部分中，单击**确定**创建虚拟机。  

   g. 若要查看新的虚拟机，单击**的所有资源**，然后搜索虚拟机并单击其名称。

    ![所有资源](media/azure-stack-provision-vm/image06.png)

什么您学习了在本教程中：

> [!div class="checklist"]
> * 创建产品
> * 添加图像
> * 测试产品/服务

> [!div class="nextstepaction"]
> [向 Azure 堆栈用户提供 web、 移动和 API 应用](azure-stack-tutorial-app-service.md)