---
title: 为 VM 配置路由首选项 - Azure 门户
description: 了解如何使用 Azure 门户来创建具有已设置所选路由首选项的公共 IP 地址的 VM。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 2a501280b817db691fb3b93097669d473443a607
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595476"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>使用 Azure 门户为 VM 配置路由首选项

本文介绍了如何为虚拟机配置路由首选项。 如果你选择“Internet”作为路由首选项，来自 VM 的 Internet 限制流量会通过 ISP 网络进行路由。 默认是通过 Microsoft 全球网络路由。

本文介绍了如何使用 Azure 门户创建具有设置为通过公共 Internet 路由流量的公共 IP 的虚拟机。

> [!IMPORTANT]
> 路由首选项目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-feature-for-your-subscription"></a>为订阅注册此功能
路由首选项功能目前为预览版。 必须使用 Azure PowerShell 为订阅注册此功能，如下所示：
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature ProviderNamespace Microsoft.Network
```

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://preview.portal.azure.com/)。

## <a name="create-a-virtual-machine"></a>创建虚拟机

1. 选择 Azure 门户左上角的“+ 创建资源”。
2. 选择“计算”，然后选择“Windows Server 2016 VM”或另一操作系统。 
3. 输入或选择以下信息，保留剩下的默认设置，然后选择“确定”：

    |设置|值|
    |---|---|
    |名称|myVM|
    |用户名| 输入所选用户名。|
    |密码| 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    |订阅| 选择订阅。|
    |资源组| 选择“使用现有资源组”，再选择“myResourceGroup” 。|
    |位置| 选择“美国东部”|

4. 选择 VM 的大小，然后选择“选择”。
5. 在“网络”选项卡下，单击“公共 IP 地址”对应的“新建”。
6. 输入“myPublicIpAddress”，依次选择“标准”SKU 和路由首选项“Internet”，然后点击“确定”，如下图所示：

   ![选择“静态”](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. 在“选择公共入站端口”下选择一个端口，或者不选择任何端口。 选择了端口 3389，目的是能够通过 Internet 对 Windows Server 虚拟机进行远程访问。 对于生产型工作负荷，建议不要打开从 Internet 进行访问的端口 3389。

   ![选择端口](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. 接受其余默认设置，然后选择“确定”。
8. 在“摘要”页中，选择“创建”。 部署虚拟机需要数分钟。
9. 部署虚拟机以后，请在门户顶部的搜索框中输入 *myPublicIpAddress*。 当“myPublicIpAddress”出现在搜索结果中时，将其选中。
10. 可以查看分配的公共 IP 地址，以及分配给 **myVM** 虚拟机的地址，如下图所示：

    ![查看公共 IP 地址](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. 选择“网络”，单击 nic“mynic”，然后选择公共 IP 地址，以确认路由首选项被指定为“Internet”。
    ![查看公共 IP 地址](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将资源组及其包含的所有资源一并删除：

1. 在门户顶部的“搜索”框中输入“myResourceGroup”。 当在搜索结果中看到“myResourceGroup”时，将其选中。
2. 选择“删除资源组”。
3. 对于“键入资源组名称:”，输入“myResourceGroup”，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤
- 详细了解[具有路由首选项的公共 IP](routing-preference-overview.md)。
- 详细了解 Azure 中的[公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。
