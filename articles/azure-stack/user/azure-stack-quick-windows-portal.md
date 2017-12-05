---
title: "Azure 堆栈快速启动-创建 Windows 虚拟机"
description: "Azure 堆栈快速启动-创建 Windows VM 使用门户"
services: azure-stack
author: ErikjeMS
manager: byronr
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/15/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: 64a623b3f5cb443e0676e7d89d7040837be95737
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-windows-virtual-machine-with-the-azure-stack-portal"></a>使用 Azure 堆栈门户创建 Windows 虚拟机

你可以通过 Azure 堆栈门户来创建 Windows 虚拟机。 该门户是一个基于浏览器的用户界面可以在其中创建、 配置和管理资源。

## <a name="sign-in-to-the-azure-stack-portal"></a>登录到 Azure 堆栈门户

登录到 Azure 堆栈门户。 Azure 堆栈门户的地址取决于您连接到哪种 Azure 堆栈产品：

* 有关 Azure 堆栈开发工具包 (ASDK) 转到： https://portal.local.azurestack.external。
* 对于 Azure 堆栈集成系统，转至 Azure 堆栈运算符提供的 URL。

## <a name="create-a-virtual-machine"></a>创建虚拟机

1. 单击**新** > **计算** > **评估版的 Windows Server 2016 数据中心** > **创建**。 如果看不到**Windows Server 2016 数据中心 Eval**条目，请与你的 Azure 堆栈运算符。 要求，它们将其添加到应用商店中所述[将 Windows Server 2016 VM 映像添加到 Azure 堆栈 marketplace](../azure-stack-add-default-image.md)文章。 
    ![](media/azure-stack-quick-windows-portal/image01.png)
2. 下**基础知识**，键入一个**名称**，**用户名**，和**密码**。 选择**订阅**。 创建**资源组**，或选择现有的一个，选择**位置**，然后单击**确定**。

    ![](media/azure-stack-quick-windows-portal/image02.png)
3. 下**选择大小**，单击**D1 标准** > **选择**。
    ![](media/azure-stack-quick-windows-portal/image03.png)
4. 下**设置**，接受默认设置，然后单击**确定**。
    ![](media/azure-stack-quick-windows-portal/image04.png)
5. 下**摘要**，单击**确定**创建虚拟机。 
    ![](media/azure-stack-quick-windows-portal/image05.png)
6. 若要查看新的虚拟机，单击**的所有资源**，然后搜索虚拟机并单击其名称。
    ![](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>清理资源

当您不再需要虚拟机时，删除资源组、 虚拟机和所有相关的资源。 为此，请从虚拟机页上选择资源组，然后单击**删除**。

## <a name="next-steps"></a>后续步骤
在此快速入门中，你已部署简单的 Windows 虚拟机。 若要了解有关 Azure 堆栈的虚拟机的详细信息，继续到[Azure 堆栈中的虚拟机的注意事项](azure-stack-vm-considerations.md)。
