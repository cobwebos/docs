---
title: 使用适用于 hackathon 的 Azure 实验室服务
description: 本文介绍如何使用 Azure 实验室服务来创建可用于运行编程马拉松的实验室。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8727fad6f37d1365983127a1a514e87e3a5003b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445876"
---
# <a name="use-azure-lab-services-for-your-next-hackathon"></a>将 Azure 实验室服务用于下一 hackathon
Azure 实验室服务设计为轻量级且易于使用，因此你可以为 hackathon 快速启动虚拟机 (Vm) 的新实验室。  使用以下清单可确保你的 hackathon 尽可能顺利地进行。 此清单应由负责创建和管理 hackathon 实验室的 IT 部门或教职员完成。 

若要为 hackathon 使用实验室服务，请确保在 hackathon 开始之前至少在几天内创建实验室帐户和实验室。 此外，请遵循以下指南：

## <a name="guidance"></a>指南

- **在离参与者最近的区域或位置创建实验室**。 

    为了减少延迟，请在与 hackathon 参与者最近的区域中创建实验室。  如果你的参与者遍布世界各地，则需要使用最佳判断来创建一个集中定位的实验室。  或者，将 hackathon 拆分为基于参与者所在位置使用多个实验室。
- **选择最适合使用需求的计算大小**。

    通常，计算大小越大，虚拟机执行的速度就越快。 但是，若要限制成本，你需要根据参与者需求选择合适的计算大小。 有关可用计算大小的详细信息，请参阅 [管理员指南中的 VM 大小调整信息](administrator-guide.md#vm-sizing) 。
- **配置 RDP\SSH 以便与 Linux vm 建立远程桌面连接**。

    如果你的 hackathon 使用 Linux Vm，请确保已启用远程桌面，以便你的参与者可以使用 RDP (远程桌面协议) 或 SSH (安全外壳) 来连接到其 Vm。 此步骤仅适用于 Linux Vm，创建实验室时必须启用此步骤。 此外，对于 RDP，你可能需要在发布之前在模板 VM 上安装和配置 RDP 服务器和 GUI 包。  有关详细信息，请参阅 [启用适用于 Linux 的远程桌面中的操作方法指南](how-to-enable-remote-desktop-linux.md)。

- **安装和停止 Windows 更新**。 

    如果你使用的是 Windows 映像，则建议你先在实验室 [模板 VM](how-to-create-manage-template.md) 上安装最新的 Windows 更新，然后再将其发布到创建实验室的 vm。 这是为了安全起见，并防止在 hackathon 安装更新时使参与者中断，这也会导致 Vm 重新启动。 你还可以考虑关闭 Windows 更新以防止将来发生任何中断。 请参阅 [如何安装和配置 Windows 更新中的操作方法指南](how-to-prepare-windows-template.md#install-and-configure-updates)。
- **决定学生如何备份其工作**。 

    在 hackathon 的生命周期内，每个学生都被分配了一个虚拟机。 他们可以将其工作直接保存到计算机，但建议学生备份工作，以便他们在 hackathon 结束后可以访问。 例如，它们应保存到外部位置，如 OneDrive、GitHub 等。 若要使用 OneDrive，你可以选择在实验室虚拟机上为学生自动配置它。 请参阅 [如何安装和配置 OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive)。
- **根据参与者数量设置 VM 容量**。 

    确保实验室的虚拟机容量是根据你在 hackathon 中预期的参与者数量设置的。 发布模板虚拟机时，可能需要花费几个小时来创建实验室中的所有计算机。 这就是我们建议你将其提前到 hackathon 开头的原因。 有关详细信息，请参阅 [更新实验室容量](how-to-set-virtual-machine-passwords.md#update-the-lab-capacity)中的操作方法指南。

- **决定是否限制实验室访问权限**。 

    向实验室添加用户时，默认情况下会启用 "限制访问" 选项。 此功能需要将所有 hackathon 参与者的电子邮件添加到列表中，然后才能使用注册链接注册和访问实验室。 如果你有一个 hackathon，其中你不知道参与者将在事件之前出现的位置，则可以选择禁用 "限制访问" 选项，这允许任何人使用注册链接注册到实验室。 有关详细信息，请参阅 [添加用户指南](how-to-configure-student-usage.md#add-users-to-a-lab)。

- **验证计划、配额和自动关闭设置**。 

    实验室服务提供多个成本控制来限制 Vm 的使用。 但是，如果这些设置配置不当，则可能会导致实验室的虚拟机意外关闭。 若要确保为 hackathon 正确配置这些设置，请验证以下设置：

    **计划**： [计划](how-to-create-schedules.md) 允许您自动控制实验室的计算机启动和关闭的时间。 默认情况下，在创建新实验室时不配置计划。 但是，应确保已根据 hackathon 的意义设置实验室的计划。  例如，如果你的 hackathon 在星期六凌晨8:00 开始，并在上午5:00 结束，则你可以创建一个计划，该计划将在星期六的 7:30 AM 上自动启动计算机， (大约30分钟后才开始 hackathon) 并5:00 关闭该计算机。 相反，你可能还决定根本不使用计划。

    **配额**： [配额](how-to-configure-student-usage.md#set-quotas-for-users) 控制参与者在计划的时间以外将有权访问虚拟机的小时数。 如果在参与者使用配额时达到了配额，则计算机将自动关闭，并且参与者将无法重新启动它，除非增加了配额。 默认情况下，当你创建实验室时，配额设置为10小时。 同样，您应该一定要设置配额，使其为 hackathon 留出足够的时间，这在尚未创建计划时尤为重要。

    **自动关闭**：启用后， [自动关闭](how-to-enable-shutdown-disconnect.md) 设置会导致 Windows 虚拟机在一段时间后自动关闭，一旦学生从其 RDP 会话中断开连接。 默认情况下，此设置处于禁用状态。

- **将防火墙设置配置为允许与实验室 vm 的连接**。 

    确保你的学校或组织的防火墙设置允许使用 RDP\SSH. 连接到实验室 Vm 有关详细信息，请参阅 [配置网络防火墙设置的操作方法指南](how-to-configure-firewall-settings.md)。

- **在参与者的平板电脑、mac、pc 等上安装 RDP\SSH 客户端**。

    Hackathon 参与者必须在其要用于连接到实验室 Vm 的平板电脑或便携式计算机上安装 RDP 和/或 SSH 客户端。 可以从不同的 RDP 或 SSH 客户端中进行选择，例如：

    - 适用于 RDP 连接的 Microsoft **远程桌面连接** 应用。 不同类型的平台（包括 Chromebook 和 [Mac](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162)）支持远程桌面连接应用。
    - [Putty](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162) 使用 SSH 连接到 Linux VM。
- **验证实验室虚拟机**。 

    发布实验室 Vm 后，应验证它们是否已正确配置。 只需为某个参与者实验室虚拟机执行此验证：

    1. 使用 RDP 和/或 SSH 进行连接。
    2. 打开安装的每个其他应用程序和工具以自定义基础虚拟机映像。
    3. 演练几个基本方案，这些方案代表参与者将执行的活动，以确保 VM 性能足以满足所选的计算大小。

## <a name="on-the-day-of-hackathon"></a>在 hackathon 日期
本部分概述了完成 hackathon 日期的步骤。

1. **启动实验室 vm**。

    根据你的操作系统，实验室计算机可能需要长达30分钟的时间才能启动。 因此，请务必在启动 hackathon 之前启动计算机，使参与者不必等待。 如果使用的是计划，请确保 Vm 至少在提前30分钟后自动启动。
2. **邀请学生注册并访问其实验室虚拟机**。 

    向你的参与者提供以下信息，以便参与者可以访问其实验室 Vm。 

    - 实验室的注册链接。 
    - 应该用于连接到计算机的凭据。 仅当实验室使用基于 Windows 的映像，并将所有 Vm 配置为使用同一密码时，此步骤才适用。
    - 有关参与者如何将 RDP 和/或到其计算机的说明。

        有关详细信息，请参阅 [如何向用户发送邀请](how-to-configure-student-usage.md?branch=master#send-invitations-to-users) 并 [连接到 Linux vm](how-to-use-remote-desktop-linux-student.md?branch=master)。 

## <a name="next-steps"></a>后续步骤
首先，按照下面这篇文章中的说明操作，在课堂实验室中创建实验室帐户：[教程：使用 Azure 实验室服务创建实验室帐户](tutorial-setup-lab-account.md)。