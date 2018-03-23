---
title: 将虚拟机提供给 Azure Stack 用户使用 | Microsoft Docs
description: 有关使虚拟机可在 Azure Stack 上使用的教程
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
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: af97f32736959f8ebf8f3c4fbca400d6b0c41f3e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>将虚拟机提供给 Azure Stack 用户使用
Azure Stack 云管理员可以创建产品供用户（有时称为租户）订阅。 然后，用户可以通过其订阅来使用 Azure Stack 服务。

本文介绍如何创建产品，然后对其进行测试。 测试时，需要以用户身份登录到门户，订阅产品，然后使用订阅创建虚拟机。

要学习的知识：

> [!div class="checklist"]
> * 创建产品
> * 添加映像
> * 测试产品


在 Azure Stack 中，可通过订阅、产品和计划将服务交付给用户。 用户可以订阅多个产品。 产品可以包含一个或多个计划，计划可以包含一个或多个服务。

![订阅、产品和计划](media/azure-stack-key-features/image4.png)

有关详细信息，请参阅 [Azure Stack 中的重要功能和概念](azure-stack-key-features.md)。

## <a name="create-an-offer"></a>创建产品

现在可为用户做好准备。 开始执行此过程时，系统首先会提示依次创建产品、计划和配额。

3. **创建产品/服务**

   产品是提供者提供给用户购买或订阅的一个或多个计划。

   a. 以云管理员的身份[登录](azure-stack-connect-azure-stack.md)到门户，单击“新建” > “产品 + 计划” > “产品”。
   ![新建产品](media/azure-stack-tutorial-tenant-vm/image01.png)

   b. 在“新建产品”部分填写“显示名称”和“资源名称”，然后选择一个新的或现有的**资源组**。 “显示名称”是产品的友好名称。 只有云操作员可以看到“资源名称”。 管理员使用此名称将该产品/服务作为 Azure 资源管理器资源处理。

   ![显示名称](media/azure-stack-tutorial-tenant-vm/image02.png)

   c. 单击“基本计划”，在“计划”部分单击“添加”，将新计划添加到产品。

   ![添加计划](media/azure-stack-tutorial-tenant-vm/image03.png)

   d.单击“下一步”。 在“新建计划”部分填写“显示名称”和“资源名称”。 显示名称是用户可看到的计划的友好名称。 只有云操作员可以看到“资源名称”。 云操作员使用该名称以 Azure 资源管理器资源的形式处理计划。

   ![计划显示名称](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. 单击“服务”，选择 **Microsoft.Compute**、**Microsoft.Network** 和 **Microsoft.Storage**，然后单击“选择”。

   ![计划服务](media/azure-stack-tutorial-tenant-vm/image05.png)

   f. 单击“配额”，选择要为其创建配额的第一个服务。 对于 IaaS 配额，请遵循适用于计算、网络和存储服务的步骤。

   本示例先为计算服务创建配额。 在命名空间列表中，选择“Microsoft.Compute”命名空间，然后单击“创建新配额”。
   
   ![创建新配额](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. 在“创建配额”部分，输入配额名称并为配额设置所需的参数，然后单击“确定”。

   ![配额名称](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. 现在，对于 **Microsoft.Compute**，请选择创建的配额。

   ![选择配额](media/azure-stack-tutorial-tenant-vm/image08.png)

   针对网络和存储服务重复这些步骤，然后单击“配额”部分中的“确定”。

   i. 单击“新建计划”部分中的“确定”。

   j. 在“计划”部分，选择新计划并单击“选择”。

   k. 在“新建产品”部分单击“创建”。 创建产品后，会看到通知。

   l. 在仪表板菜单中单击“产品”，然后单击创建的产品。

   m. 依次单击“更改状态”、“公共”。

   ![公共状态](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>添加映像

预配虚拟机之前，必须先将一个映像添加到 Azure Stack Marketplace。 可以从 Azure Marketplace 添加所选的映像，包括 Linux 映像。

如果在连接方案中操作，且已向 Azure 注册 Azure Stack 实例，则可以使用[将 Marketplace 项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md) 主题中所述的步骤，从 Azure Marketplace 下载 Windows Server 2016 VM 映像。

有关将不同的项添加到 Marketplace 的信息，请参阅 [Azure Stack Marketplace](azure-stack-marketplace.md)。

## <a name="test-the-offer"></a>测试产品

创建产品后，可对其进行测试。 以用户身份登录并订阅该产品，然后添加虚拟机。

1. **订阅产品/服务**

   现在，可以用户身份登录到门户，以订阅产品。

   a. 以用户身份登录到用户门户，并单击“获取订阅”。
   - 对于集成系统，URL 根据操作员所在的区域和外部域名的不同而异，格式为 https://portal.&lt;*区域*&gt;.&lt;*FQDN*&gt;。
   - 如果使用 Azure Stack 开发工具包，则门户地址为 https://portal.local.azurestack.external。

   ![获取订阅](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. 在“显示名称”字段中键入订阅的名称，单击“产品”，单击“选择产品”部分中的某个产品，然后单击“创建”。

   ![创建产品](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. 若要查看创建的订阅，请单击“更多服务”、单击“订阅”，然后单击新订阅。  

   订阅产品之后，请刷新门户以查看哪些服务是新订阅的一部分。

2. **预配虚拟机**

   现在，可以用户身份登录到门户，以使用订阅预配虚拟机。 

   a. 以用户身份登录到用户门户。
      - 对于集成系统，URL 根据操作员所在的区域和外部域名的不同而异，格式为 https://portal.&lt;*区域*&gt;.&lt;*FQDN*&gt;。
   - 如果使用 Azure Stack 开发工具包，则门户地址为 https://portal.local.azurestack.external。

   b.  在仪表板上，单击“新建” > “计算” > “Windows Server 2016 Datacenter Eval”，然后单击“创建”。

   c. 在“基本信息”部分，输入“名称”、“用户名”和“密码”，选择“订阅”，创建一个**资源组**，然后单击“确定”。

   d.单击“下一步”。 在“选择大小”部分单击“A1 标准”，然后单击“选择”。  

   e. 在“设置”部分单击“虚拟网络”。 在“选择虚拟网络”部分单击“新建”。 在“创建虚拟网络”部分接受所有默认值，然后单击“确定”。 在“设置”部分单击“确定”。

   ![创建虚拟网络](media/azure-stack-provision-vm/image04.png)

   f. 在“摘要”部分，单击“确定”创建虚拟机。  

   g. 若要查看新虚拟机，请单击“所有资源”，然后搜索该虚拟机并单击其名称。

    ![所有资源](media/azure-stack-provision-vm/image06.png)

本教程已介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建产品
> * 添加映像
> * 测试产品

> [!div class="nextstepaction"]
> [将 Web、移动和 API 应用提供给 Azure Stack 用户使用](azure-stack-tutorial-app-service.md)