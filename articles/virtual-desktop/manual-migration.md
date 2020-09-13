---
title: 从 Windows 虚拟桌面 (经典) 手动迁移-Azure
description: 如何从 Windows 虚拟桌面 (经典) 手动迁移到 Windows 虚拟桌面。
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 74527f57340f850b60dd00dcd054992c423a49c7
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90038977"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>从 Windows 虚拟桌面 (经典) 手动迁移

Windows 虚拟桌面 (经典) 创建其服务环境，其中包含 PowerShell cmdlet、REST Api 和服务对象。 Windows 虚拟桌面服务环境中的 "对象" 是 Windows 虚拟桌面创建的内容。 服务对象包括租户、主机池、应用程序组和会话主机。

但是，Windows 虚拟桌面 (经典) 未与 Azure 集成。 如果没有 Azure 集成，则 Azure 门户不会自动管理任何创建的对象，因为这些对象未连接到你的 Azure 订阅。

Windows 虚拟桌面的最近一次更新会将服务中的转换标记为完整的 Azure 集成。 在 Windows 虚拟桌面中创建的对象由 Azure 门户自动管理。

本文将介绍为什么要考虑迁移到最新版本的 Windows 虚拟桌面。 接下来，我们将告诉你如何从 Windows 虚拟桌面 (经典) 中手动迁移到最新的 Windows 虚拟桌面更新。

## <a name="why-migrate"></a>为何要迁移？

主要更新可能很不方便，尤其是你必须手动执行的更新。 但是，无法自动迁移的原因有以下几点：

- 使用经典版本创建的现有服务对象在 Azure 中没有任何表示形式。 它们的作用域并未超出 Windows 虚拟桌面服务。
- 利用最新更新，已更改服务的应用程序 ID，以删除应用对 Windows 虚拟桌面 (经典) 的许可。 你将无法通过 Windows 虚拟桌面创建新的 Azure 对象，除非这些对象通过新的应用程序 ID 进行身份验证。

尽管有麻烦，但从经典版本中迁移仍很重要。 下面是迁移后可以执行的操作：

- 通过 Azure 门户管理 Windows 虚拟桌面。
- 将 Azure Active Directory (AD) 用户组分配到应用程序组。
- 使用改进后的 Log Analytics 功能对你的部署进行故障排除。
- 使用 Azure 本机基于角色的访问控制来管理管理访问权限。

## <a name="when-should-i-migrate"></a>应该在何时迁移？

询问你是否应该迁移时，还应考虑部署当前和未来的情况。

在某些情况下，我们建议你手动迁移：

- 使用少量用户的测试主机池设置。
- 使用少量用户的生产主机池设置，但计划最终增加到数百个用户。
- 可以轻松地复制简单的设置。 例如，如果 Vm 使用库映像。

> [!IMPORTANT]
> 如果你使用的是需要很长时间才能稳定的高级配置，或者有大量用户，则我们不建议手动迁移。

## <a name="prepare-for-migration"></a>准备迁移

在开始之前，您需要确保您的环境已准备好进行迁移。

下面是启动迁移过程所需的内容：

- Azure 订阅，可在其中创建新的 Azure 服务对象。
- 请确保已将你分配到下列角色：
    
    - 参与者
    - 用户访问管理员
    
    "参与者" 角色允许您在订阅上创建 Azure 对象，"用户访问管理员" 角色允许您将用户分配到应用程序组。

## <a name="how-to-migrate-manually"></a>如何手动迁移

现在，你已准备好迁移过程，是时候真正迁移了。

若要从 Windows 虚拟桌面 (经典) 手动迁移到 Windows 虚拟桌面：

1. 按照 [使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md) 中的说明使用 Azure 门户创建所有高级对象。
2. 如果要引入已在使用的虚拟机，请按照向 [Windows 虚拟桌面主机池注册虚拟机](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) 中的说明进行操作，以将虚拟机手动注册到步骤1中创建的新主机池。
3. 创建新的 RemoteApp 应用组。
4. 将用户或用户组发布到新的桌面和 RemoteApp 应用程序组。
5. 按照 [设置多重身份验证](set-up-mfa.md)中的说明更新你的条件访问策略，以允许新的对象。

若要防止停机，你应该首先将现有会话主机注册到小型组中的 Azure 资源管理器集成主机池。 之后，慢慢地将用户带入新的 Azure 资源管理器集成的应用组。

## <a name="next-steps"></a>后续步骤

迁移后，请通过查看 [教程](create-host-pools-azure-marketplace.md)来了解 Windows 虚拟桌面的工作方式。 了解 [扩展现有主机池](expand-existing-host-pool.md) 的高级管理功能并 [自定义 RDP 属性](customize-rdp-properties.md)。

若要了解有关服务对象的详细信息，请参阅 [Windows 虚拟桌面环境](environment-setup.md)。
