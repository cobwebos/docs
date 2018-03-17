---
title: "从 Azure 中添加 Azure 堆栈应用商店项 |Microsoft 文档"
description: "描述如何将基于 Azure 的 Windows Server 虚拟机映像添加到 Azure 堆栈应用商店。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 25f179f825526e20377c0e94ccb38a788cadd898
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-azure"></a>教程： 从 Azure 中添加 Azure 堆栈应用商店项

作为 Azure 堆栈操作员，你需要要做，以使用户能够部署虚拟机 (VM) 的第一个操作是将 VM 映像添加到 Azure 堆栈应用商店。 默认情况下，执行任何操作将发布到 Azure 堆栈应用商店，但你可以从下载的项目[策展的 Azure 应用商店项列表](.\.\azure-stack-marketplace-azure-items.md)，已被预先测试，在 Azure 堆栈上运行。 如果在连接方案中执行操作，并且已向 Azure 注册 Azure Stack 实例，请使用此选项。

在本教程中，你添加 Windows Server 2016 VM 映像从 Azure 应用商店到 Azure 堆栈市场。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 添加 Windows Server VM Azure 堆栈应用商店项
> * 验证 VM 映像可用 
> * 测试的 VM 映像

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

完成本教程：

- 安装[Azure 堆栈兼容 Azure PowerShell 模块](asdk-post-deploy.md#install-azure-stack-powershell)
- 下载[Azure 堆栈工具](asdk-post-deploy.md#download-the-azure-stack-tools)
- [注册 ASDK](asdk-register.md)与 Azure 订阅

## <a name="add-a-windows-server-vm-image"></a>添加 Windows Server VM 映像
通过从 Azure 应用商店下载映像，可以向 Azure 堆栈应用商店添加 Windows Server 2016 映像。 如果你正在连接方案中工作并且你已使用此选项[注册 Azure 堆栈实例](asdk-register.md)使用 Azure。

1. 登录到[ASDK 管理员门户](https://adminportal.local.azurestack.external)。

2. 单击“更多服务” > “Marketplace 管理” > “从 Azure 添加”。 

   ![从 Azure 将添加](media/asdk-marketplace-item/azs-marketplace.png)

3. 查找或搜索**Windows Server 2016 Datacenter**映像。

4. 选择**Windows Server 2016 Datacenter**图像，然后选择**下载**。

   ![从 Azure 下载映像](media/asdk-marketplace-item/azure-marketplace-ws2016.png)


> [!NOTE]
> 它需要大约 1 小时下载 VM 映像并将其发布到 Azure 堆栈市场。 

下载完成时，图像将发布并且不可下**应用商店管理**。 映像中也有下**计算**来创建新的虚拟机。

## <a name="verify-the-marketplace-item-is-available"></a>验证应用商店项目可用
使用以下步骤以验证新的 VM 映像 Azure 堆栈应用商店中可用。

1. 登录到[ASDK 管理员门户](https://adminportal.local.azurestack.external)。

2. 选择**更多的服务** > **应用商店管理**。 

3. 验证已成功添加 Windows Server 2016 数据中心 VM 映像。

   ![从 Azure 下载的映像](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>测试的 VM 映像
作为 Azure 堆栈运算符，你可以使用[管理员门户](https://adminportal.local.azurestack.external)来创建测试 VM 以验证映像可成功。 

1. 登录到[ASDK 管理员门户](https://adminportal.local.azurestack.external)。

2. 单击**新** > **计算** > **Windows Server 2016 数据中心** > **创建**。  
 ![从应用商店映像创建 VM](media/asdk-marketplace-item/new-compute.png)

3. 在**基础知识**边栏选项卡，输入以下信息，然后单击**确定**:
  - **名称**： 测试-vm-1
  - **用户名称**: AdminTestUser
  - **密码**: AzS TestVM01
  - **订阅**： 接受默认提供程序订阅
  - **资源组**： 测试 vm rg
  - **位置**： 本地

4. 在“选择大小”边栏选项卡中，单击“A1 标准”，并单击“选择”。  

5. 在“设置”边栏选项卡中接受默认值，然后单击“确定”

6. 在“摘要”边栏选项卡中，单击“确定”创建虚拟机。  
> [!NOTE]
> 虚拟机部署需要几分钟才能完成。

7. 若要查看到新的 VM，请单击**虚拟机**，然后搜索**测试 vm 1**并单击其名称。
    ![显示的第一个测试 VM 映像](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>清理资源
您已成功登录到 VM，并验证测试映像正常工作后，应删除 test 资源组。 这将释放到单节点 ASDK 安装的可用资源有限。

当不再需要时，删除资源组、 VM 和所有相关的资源，通过执行以下步骤：

1. 登录到[ASDK 管理员门户](https://adminportal.local.azurestack.external)。
2. 单击**资源组** > **测试 vm rg** > **删除资源组**。
3. 类型**测试 vm rg**作为资源组名称然后单击**删除**。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 添加 Windows Server VM Azure 堆栈应用商店项
> * 验证 VM 映像可用 
> * 测试的 VM 映像

前进到下一步的教程，若要了解如何创建 Azure 堆栈优惠和计划。

> [!div class="nextstepaction"]
> [提供 Azure 堆栈 IaaS 服务](asdk-offer-services.md)