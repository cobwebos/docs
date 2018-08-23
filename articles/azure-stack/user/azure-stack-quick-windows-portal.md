---
title: Azure Stack 快速入门 - 创建 Windows 虚拟机
description: Azure Stack 快速入门 - 使用门户创建 Windows VM
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: mabrigg
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: efe6213e5c0261fb26ac40e74c2b0f6e0c9252dd
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139466"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>快速入门：使用 Azure Stack 门户创建 Windows Server 虚拟机

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 Azure Stack 门户创建 Windows Server 2016 虚拟机。 请按照本文中的步骤创建和使用虚拟机。

## <a name="sign-in-to-the-azure-stack-portal"></a>登录到 Azure Stack 门户

登录到 Azure Stack 门户。 Azure Stack 门户的地址取决于要连接到的 Azure Stack 产品：

* 对于 Azure Stack 开发工具包 (ASDK)，请转到：https://portal.local.azurestack.external。
* 对于 Azure Stack 集成系统，请转到 Azure Stack 运营商提供的 URL。

## <a name="create-a-virtual-machine"></a>创建虚拟机

1. 单击“新建” > “计算” > “Windows Server 2016 Datacenter Eval” > “创建”。 如果未看到“Windows Server 2016 Datacenter Eval”项，请联系 Azure Stack 运营商。 根据[将 Windows Server 2016 VM 映像添加到 Azure Stack 市场](../azure-stack-add-default-image.md)一文中所述，请求运营商将此映像添加到市场。

    ![在门户中创建 Windows 虚拟机的步骤](media/azure-stack-quick-windows-portal/image01.png)
2. 在“基本信息”下，键入**名称**、**用户名**和**密码**。 选择“订阅”。 创建一个**资源组**或选择现有资源组，选择一个**位置**，然后单击“确定”。

    ![配置基本设置](media/azure-stack-quick-windows-portal/image02.png)
3. 在“选择大小”下，单击“D1 标准” > “选择”。
    ![选择虚拟机大小](media/azure-stack-quick-windows-portal/image03.png)
4. 在“设置”下，接受默认值，然后单击“确定”。
    ![配置虚拟机设置](media/azure-stack-quick-windows-portal/image04.png)
5. 在“摘要”下，单击“确定”创建虚拟机。
    ![查看摘要和创建虚拟机](media/azure-stack-quick-windows-portal/image05.png)
6. 若要查看新虚拟机，请单击“所有资源”，搜索该虚拟机名称，然后在搜索结果中单击其名称。
    ![查看虚拟机](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>清理资源

使用完虚拟机后，请删除虚拟机及其资源。 为此，请在虚拟机页上选择该资源组，并单击“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，已部署了一个基本 Windows Server 虚拟机。 有关 Azure Stack 虚拟机的详细信息，请转到 [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)。
