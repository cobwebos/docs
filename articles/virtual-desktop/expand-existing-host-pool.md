---
title: 利用新的会话主机展开现有的主机池-Azure
description: 如何使用 Windows 虚拟桌面中的新会话主机扩展现有的主机池。
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b65560a3b10d04887040c4da1e137912810b3095
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929585"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>使用新的会话主机展开现有的主机池

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md)。

当你在主机池内提高使用率时，可能需要使用新的会话主机扩展现有的主机池以处理新负载。

本文将介绍如何使用新的会话主机展开现有的主机池。

## <a name="what-you-need-to-expand-the-host-pool"></a>扩展主机池所需的条件

在开始之前，请确保已使用以下方法之一) 创建了主机池和会话主机虚拟机 (Vm：

- [Azure 门户](./create-host-pools-azure-marketplace.md)
- [使用 PowerShell 创建主机池](./create-host-pools-powershell.md)

当你首次创建主机池和会话主机 Vm 时，还需要以下信息：

- VM 大小、映像和名称前缀
- 域加入管理员凭据
- 虚拟网络名称和子网名称

## <a name="add-virtual-machines-with-the-azure-portal"></a>添加具有 Azure 门户的虚拟机

通过添加虚拟机扩展主机池：

1. 登录到 Azure 门户。

2. 搜索并选择“Windows 虚拟桌面”  。

3. 在屏幕左侧的菜单中，选择 " **主机池**"，然后选择要将虚拟机添加到的主机池的名称。

4. 在屏幕左侧的菜单中选择 " **会话主机** "。

5. 选择 " **+ 添加** " 开始创建主机池。

6. 忽略 "基本信息" 选项卡，然后选择 " **VM 详细信息** " 选项卡。在此处，你可以查看和编辑要添加到主机池的虚拟机 (VM) 的详细信息。

7. 选择要在其下创建 Vm 的资源组，然后选择区域。 您可以选择当前使用的区域或新区域。

8. 输入要添加到主机池的会话主机数，并将其添加到 **vm 的数目**。 例如，如果要将主机池扩展到五台主机，请输入 **5**。

    >[!NOTE]
    >尽管可以编辑 Vm 的映像和前缀，但如果 Vm 在同一主机池中具有不同的映像，则不建议对其进行编辑。 仅当你计划从受影响的主机池中删除包含旧映像的 Vm 时，才编辑映像和前缀。

9. 对于 **虚拟网络信息**，请选择要将虚拟机加入到的虚拟网络和子网。 你可以选择现有计算机当前使用的相同虚拟网络，或选择更适用于在步骤7中选择的区域的其他虚拟网络。

10. 对于 **管理员帐户**，输入与所选虚拟网络关联的 Active Directory 域用户名和密码。 这些凭据将用于将虚拟机加入虚拟网络。

      >[!NOTE]
      >确保你的管理员名称符合此处提供的信息。 并且不会对该帐户启用 MFA。

11. 如果你有要将虚拟机分组到其中的任何标记，请选择 " **标记** " 选项卡。 否则，请跳过此选项卡。

12. 选择 " **查看** " 和 "创建" 选项卡。查看您的选择，如果一切正常，请选择 " **创建**"。

## <a name="next-steps"></a>后续步骤

现在，你已扩展现有的主机池，你可以登录到 Windows 虚拟桌面客户端，以将其作为用户会话的一部分进行测试。 你可以使用以下任一客户端连接到会话：

- [使用 Windows 桌面客户端进行连接](./connect-windows-7-10.md)
- [使用 Web 客户端进行连接](./connect-web.md)
- [使用 Android 客户端进行连接](./connect-android.md)
- [使用 macOS 客户端进行连接](./connect-macos.md)
- [使用 iOS 客户端进行连接](./connect-ios.md)
