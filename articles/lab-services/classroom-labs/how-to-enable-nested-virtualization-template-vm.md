---
title: 在 Azure 实验室服务中的模板 VM 上启用嵌套虚拟化 |微软文档
description: 了解如何创建包含多个 VM 的模板 VM。  换句话说，在 Azure 实验室服务中的模板 VM 上启用嵌套虚拟化。
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
ms.openlocfilehash: 59b32834369f76d39bb4a253dad4ec541e7ef999
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502009"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>在 Azure 实验室服务中的模板虚拟机上启用嵌套虚拟化

目前，Azure 实验室服务使您能够在实验室中设置一个模板虚拟机，并使每个用户都可以使用单个副本。 如果您是教授网络、安全或 IT 课程，则可能需要为每个学生提供一个环境，使多个虚拟机可以通过网络相互对话。

嵌套虚拟化使您能够在实验室的模板虚拟机内创建多 VM 环境。 发布模板将为实验室中的每个用户提供一个虚拟机，其中设置多个 VM。  本文介绍如何在 Azure 实验室服务中的模板计算机上设置嵌套虚拟化。

## <a name="what-is-nested-virtualization"></a>什么是嵌套虚拟化？

嵌套虚拟化使您能够在虚拟机中创建虚拟机。 嵌套虚拟化通过 Hyper-V 完成，并且仅在 Windows VM 上可用。

有关嵌套虚拟化的详细信息，请参阅以下文章：

- [在 Azure 中嵌套虚拟化](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [如何在 Azure VM 中启用嵌套虚拟化](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>注意事项

在设置具有嵌套虚拟化的实验室之前，需要考虑一些事项。

- 创建新实验室时，为虚拟机大小选择 **"中等（嵌套虚拟化）"** 或 **"大型（嵌套虚拟化）"** 大小。 这些虚拟机大小支持嵌套虚拟化。
- 选择可同时为主机和客户端虚拟机提供良好的性能的大小。  请记住，使用虚拟化时，您选择的大小必须不仅适合一台计算机，还可用于主机以及必须同时运行的任何客户端计算机。
- 客户端虚拟机将无法访问 Azure 资源，例如 Azure 虚拟网络上的 DNS 服务器。
- 主机虚拟机需要设置，以允许客户端计算机具有互联网连接。
- 客户端虚拟机被许可为独立计算机。 有关 Microsoft 操作系统和产品的许可信息，请参阅[Microsoft 许可](https://www.microsoft.com/licensing/default)。 在设置模板计算机之前，请检查是否有正在使用的任何其他软件的许可协议。

## <a name="enable-nested-virtualization-on-a-template-vm"></a>在模板 VM 上启用嵌套虚拟化

本文假定您已创建实验室帐户和实验室。  有关创建新实验室帐户的详细信息，请参阅[设置实验室帐户的教程](tutorial-setup-lab-account.md)。 有关如何创建实验室的详细信息，请参阅[设置课堂实验室教程](tutorial-setup-classroom-lab.md)。

>[!IMPORTANT]
>创建实验室时，为虚拟机大小选择 **"大型（嵌套虚拟化）"** 或 **"中型"（嵌套虚拟化）。**  嵌套虚拟化将不起作用。  

要连接到模板计算机，请参阅[创建和管理教室模板](how-to-create-manage-template.md)。

### <a name="using-script-to-enable-nested-virtualization"></a>使用脚本启用嵌套虚拟化

要将自动设置用于 Windows Server 2016 或 Windows Server 2019 的嵌套虚拟化，请参阅[使用脚本在 Azure 实验室服务中的模板虚拟机上启用嵌套虚拟化](how-to-enable-nested-virtualization-template-vm-using-script.md)。 您将使用[实验室服务 Hyper-V 脚本中的脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)来安装 Hyper-V 角色。  这些脚本还将建立网络，以便 Hyper-V 虚拟机可以进行 Internet 访问。

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>使用 Windows 工具启用嵌套虚拟化

使用 Windows 角色和管理工具为 Windows Server 2016 或 Windows Server 2019 设置嵌套虚拟化，请参阅[在 Azure 实验室服务中的模板虚拟机上手动启用嵌套虚拟化](how-to-enable-nested-virtualization-template-vm-ui.md)。  说明还将介绍如何设置网络，以便 Hyper-V 虚拟机可以进行 Internet 访问。
