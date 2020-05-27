---
title: 在 Azure 实验室服务中对模板 VM 启用嵌套虚拟化 | Microsoft Docs
description: 了解如何创建包含多个 VM 的模板 VM。  也就是说，在 Azure 实验室服务中对模板 VM 启用嵌套虚拟化。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: b7831e554fcca20134d5cf8608481ff0c82eb8a0
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591992"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>在 Azure 实验室服务中对模板虚拟机启用嵌套虚拟化

目前，使用 Azure 实验室服务，可以在实验室中设置一个模板虚拟机，并向每个用户提供一个副本。 如果你是教授网络、安全性或 IT 课程的教师，可能需要为每个学生提供一个环境，在这个环境中，多个虚拟机可以通过网络相互通信。

使用嵌套虚拟化，可以在实验室的模板虚拟机内创建多 VM 环境。 发布模板会为实验室中的每个用户提供一个虚拟机，其中包含多个 VM。  本文介绍了如何在 Azure 实验室服务中对模板计算机设置嵌套虚拟化。

## <a name="what-is-nested-virtualization"></a>什么是嵌套虚拟化？

使用嵌套虚拟化，可以在一个虚拟机中创建多个虚拟机。 嵌套虚拟化是通过 Hyper-V 完成的，并且只在 Windows VM 上可用。

若要详细了解嵌套虚拟化，请参阅以下文章：

- [Azure 中的嵌套虚拟化](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [如何在 Azure VM 中启用嵌套虚拟化](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>注意事项

创建使用嵌套虚拟化的实验室之前，请注意以下几点。

- 新建实验室时，选择“中等(嵌套虚拟化)”或“大型(嵌套虚拟化)”作为虚拟机大小。 这些虚拟机大小支持嵌套虚拟化。
- 选择可以为主机和客户端虚拟机提供良好性能的大小。  请注意，在使用虚拟化时，所选大小必须不仅足以容纳一台计算机，而且还能容纳主机以及同时运行的任何 Hyper-V 计算机。
- 客户端虚拟机无法访问 Azure 虚拟网络上的 Azure 资源（如 DNS 服务器）。
- 主机虚拟机需要设置来允许客户端计算机连接到 Internet。
- 客户端虚拟机被许可为独立计算机。 若要了解 Microsoft 操作系统和产品的许可，请参阅 [Microsoft 许可](https://www.microsoft.com/licensing/default)。 在设置模板计算机前，请先检查在使用的其他任何软件的许可协议。

## <a name="enable-nested-virtualization-on-a-template-vm"></a>在模板 VM 上启用嵌套虚拟化

若要更好地理解本文，请确保已创建实验室帐户和实验室。  若要详细了解如何新建实验室帐户，请参阅[创建实验室帐户](tutorial-setup-lab-account.md)教程。 若要详细了解如何创建实验室，请参阅[创建课堂实验室](tutorial-setup-classroom-lab.md)教程。

>[!IMPORTANT]
>创建实验室时，选择“大型(嵌套虚拟化)”或“中等(嵌套虚拟化)”作为虚拟机大小。  否则，嵌套虚拟化将不起作用。  

若要连接到模板计算机，请参阅[创建和管理课堂模板](how-to-create-manage-template.md)。

若要启用嵌套虚拟化，需要完成几个任务。  

- 启用 Hyper-V 角色。 必须启用 Hyper-V 角色，才能在实验室服务虚拟机上创建和运行 Hyper-V 虚拟机。
- 启用 DHCP。  在实验室服务虚拟机启用了 DHCP 角色后，可以自动为 Hyper-V 虚拟机分配 IP 地址。
- 为 Hyper-V VM 创建 NAT 网络。  创建 NAT 网络可以让 Hyper-V 虚拟机连接到 Internet。  Hyper-V 虚拟机可以相互通信。

>[!NOTE]
>实验室服务 VM 上创建的 NAT 网络可便于 Hyper-V VM 访问 Internet 和同一实验室服务 VM 上的其他 Hyper-V VM。  Hyper-V VM 无法访问 Azure 虚拟网络上的 Azure 资源（如 DNS 服务器）。

可以使用脚本或 Windows 工具来完成上面列出的任务。  如需了解更多详情，请阅读以下部分。

### <a name="using-script-to-enable-nested-virtualization"></a>使用脚本启用嵌套虚拟化

若要对 Windows Server 2016 或 Windows Server 2019 使用嵌套虚拟化的自动化设置，请参阅 [使用脚本在 Azure 实验室服务中对模板虚拟机启用嵌套虚拟化](how-to-enable-nested-virtualization-template-vm-using-script.md)。 你将使用[实验室服务 Hyper-V 脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)中的脚本来安装 Hyper-V 角色。  脚本还会设置网络，以便 Hyper-V 虚拟机能够连接到 Internet。

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>使用 Windows 工具启用嵌套虚拟化

若要使用 Windows 角色和管理工具为 Windows Server 2016 或 Windows Server 2019 设置嵌套虚拟化，请参阅[手动在 Azure 实验室服务中对模板虚拟机启用嵌套虚拟化](how-to-enable-nested-virtualization-template-vm-ui.md)。  说明还包括如何设置网络，以便 Hyper-V 虚拟机能够连接到 Internet。
