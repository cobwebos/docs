---
title: 利用新的会话主机展开现有的主机池-Azure
description: 如何使用 Windows 虚拟桌面中的新会话主机扩展现有的主机池。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d46d5618d7e3dc26775401f4a90d0c98d75ea31a
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929207"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>使用新的会话主机展开现有的主机池

>[!IMPORTANT]
>此内容适用于带有 Azure 资源管理器 Windows 虚拟桌面对象的弹簧2020更新。 如果使用的是不带 Azure 资源管理器对象的 Windows 虚拟桌面2019版，请参阅[此文](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md)。
>
> Windows 虚拟桌面春季2020更新目前为公共预览版。 此预览版本在提供时没有服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

当你在主机池内提高使用率时，可能需要使用新的会话主机扩展现有的主机池以处理新负载。

本文将介绍如何使用新的会话主机展开现有的主机池。

## <a name="what-you-need-to-expand-the-host-pool"></a>扩展主机池所需的条件

在开始之前，请确保已使用以下方法之一创建了主机池和会话主机虚拟机（Vm）：

- [Azure 门户](./create-host-pools-azure-marketplace.md)
- [使用 PowerShell 创建主机池](./create-host-pools-powershell.md)

当你首次创建主机池和会话主机 Vm 时，还需要以下信息：

- VM 大小、映像和名称前缀
- 域加入管理员凭据
- 虚拟网络名称和子网名称

## <a name="add-virtual-machines-with-the-azure-portal"></a>添加具有 Azure 门户的虚拟机

通过添加虚拟机扩展主机池：

1. 登录到 Azure 门户。

2. 搜索并选择 " **Windows 虚拟桌面**"。

3. 在屏幕左侧的菜单中，选择 "**主机池**"，然后选择要将虚拟机添加到的主机池的名称。

4. 在屏幕左侧的菜单中选择 "**虚拟机**"。

5. 选择 " **+ 添加**" 开始创建主机池。

6. 忽略 "基本信息" 选项卡，然后选择 " **VM 详细信息**" 选项卡。可在此处查看和编辑要添加到主机池的虚拟机（VM）的详细信息。

7. 选择要在其下创建 Vm 的资源组，然后选择区域。 您可以选择当前使用的区域或新区域。
   
8. 输入要添加到主机池的会话主机数，并将其添加到**vm 的数目**。 例如，如果要将主机池扩展到五台主机，请输入**5**。
   
    >[!NOTE]
    >不能编辑 Vm 的大小或图像，因为确保主机池中的所有 Vm 大小相同都非常重要。
    
9. 对于**虚拟网络信息**，请选择要将虚拟机加入到的虚拟网络和子网。 你可以选择现有计算机当前使用的相同虚拟网络，或选择更适用于在步骤7中选择的区域的其他虚拟网络。

10. 对于**管理员帐户**，输入与所选虚拟网络关联的 Active Directory 域用户名和密码。 这些凭据将用于将虚拟机加入虚拟网络。

      >[!NOTE]
      >确保你的管理员名称符合此处提供的信息。 并且不会对该帐户启用 MFA。

11. 如果你有要将虚拟机分组到其中的任何标记，请选择 "**标记**" 选项卡。 否则，请跳过此选项卡。 

12. 选择 "**查看**" 和 "创建" 选项卡。查看你的选择，如果一切正常，请选择 "**创建**"。 

## <a name="next-steps"></a>后续步骤

现在，你已扩展现有的主机池，你可以登录到 Windows 虚拟桌面客户端，以将其作为用户会话的一部分进行测试。 你可以使用以下任一客户端连接到会话：

- [使用 Windows 桌面客户端进行连接](./connect-windows-7-and-10.md)
- [使用 Web 客户端进行连接](./connect-web.md)
- [使用 Android 客户端进行连接](./connect-android.md)
- [使用 macOS 客户端进行连接](./connect-macos.md)
- [使用 iOS 客户端进行连接](./connect-ios.md)
