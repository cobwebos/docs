---
title: 在 Azure 实验室服务中对模板 VM 启用嵌套虚拟化 |Microsoft Docs
description: 了解如何在内部创建包含多个 Vm 的模板 VM。  换句话说，在 Azure 实验室服务中的模板 VM 上启用嵌套虚拟化。
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
ms.openlocfilehash: 3c954c4689281838ea8c61c932cdcc3b74bac442
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184667"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>在 Azure 实验室服务中的模板虚拟机上启用嵌套虚拟化

目前，Azure 实验室服务可让你在实验室中设置一个模板虚拟机，并向每个用户提供单个副本。 如果你是教授网络、安全性或 IT 类的教授，你可能需要为每个学生提供一个环境，在此环境中，多个虚拟机可通过网络彼此通信。

利用嵌套虚拟化，你可以在实验室模板虚拟机内创建多 VM 环境。 发布模板将在实验室中为每个用户提供一个虚拟机，并在其中设置多个虚拟机。  本文介绍如何在 Azure 实验室服务中的模板计算机上设置嵌套虚拟化。

## <a name="what-is-nested-virtualization"></a>什么是嵌套虚拟化？

利用嵌套虚拟化，你可以在虚拟机中创建虚拟机。 嵌套虚拟化是通过 Hyper-v 完成的，并且仅适用于 Windows Vm。

有关嵌套虚拟化的详细信息，请参阅以下文章：

- [Azure 中的嵌套虚拟化](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [如何在 Azure VM 中启用嵌套虚拟化](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>注意事项

使用嵌套虚拟化设置实验室之前，需注意以下几个事项。

- 创建新实验室时，为虚拟机大小选择 "**中" （嵌套虚拟化）** 或 "**大型" （嵌套虚拟化）** 大小。 这些虚拟机大小支持嵌套虚拟化。
- 选择将为主机和客户端虚拟机提供良好性能的大小。  请记住，在使用虚拟化时，所选的大小必须足以满足一台计算机的需要，但该主机以及同时运行的任何 Hyper-v 计算机都必须足够。
- 客户端虚拟机将无法访问 Azure 虚拟网络上的 Azure 资源（如 DNS 服务器）。
- 主机虚拟机要求安装程序允许客户端计算机具有 internet 连接。
- 客户端虚拟机被授权为独立计算机。 有关 Microsoft 操作系统和产品许可的信息，请参阅[Microsoft 许可](https://www.microsoft.com/licensing/default)。 在设置模板计算机之前，请检查正在使用的任何其他软件的许可协议。

## <a name="enable-nested-virtualization-on-a-template-vm"></a>在模板 VM 上启用嵌套虚拟化

本文假设已创建实验室帐户和实验室。  有关创建新实验室帐户的详细信息，请参阅[设置实验室帐户教程](tutorial-setup-lab-account.md)。 有关如何创建实验室的详细信息，请参阅[设置课堂实验室教程](tutorial-setup-classroom-lab.md)。

>[!IMPORTANT]
>创建实验室时，为虚拟机大小选择 "**大" （嵌套虚拟化）** 或 "**中" （嵌套虚拟化）** 。  否则，嵌套虚拟化将不起作用。  

若要连接到模板计算机，请参阅[创建和管理教室模板](how-to-create-manage-template.md)。

若要启用嵌套虚拟化，需要完成几个任务。  

- **启用 hyper-v 角色**。 若要在实验室服务虚拟机上创建和运行 Hyper-v 虚拟机，必须启用 hyper-v 角色。
- **启用 DHCP**。  实验室服务虚拟机启用了 DHCP 角色后，可以自动为 Hyper-v 虚拟机分配 IP 地址。
- **为 Hyper-v Vm 创建 NAT 网络**。  NAT 网络已设置为允许 Hyper-v 虚拟机访问 internet。  Hyper-v 虚拟机可以相互通信。

>[!NOTE]
>在实验室服务 VM 上创建的 NAT 网络将允许 Hyper-v VM 在同一实验室服务 VM 上访问 internet 和其他 Hyper-v Vm。  Hyper-v VM 将无法访问 Azure 虚拟网络上的 Azure 资源（如 DNS 服务器）。

可以使用脚本或使用 Windows 工具完成上面列出的任务。  有关更多详细信息，请参阅下面的部分。

### <a name="using-script-to-enable-nested-virtualization"></a>使用脚本启用嵌套虚拟化

若要对 Windows Server 2016 或 Windows Server 2019 使用嵌套虚拟化的自动安装，请参阅[使用脚本在 Azure 实验室服务中的模板虚拟机上启用嵌套虚拟化](how-to-enable-nested-virtualization-template-vm-using-script.md)。 你将使用[实验室服务](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)版的脚本安装 hyper-v 角色。  脚本还将设置网络，以便 Hyper-v 虚拟机可以访问 internet。

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>使用 Windows 工具启用嵌套虚拟化

使用 Windows 角色和管理工具为 Windows Server 2016 或 Windows Server 2019 设置嵌套虚拟化，请参阅[在 Azure 实验室服务中的模板虚拟机上手动启用嵌套虚拟化](how-to-enable-nested-virtualization-template-vm-ui.md)。  说明还将介绍如何设置网络，以便 Hyper-v 虚拟机可以访问 internet。
