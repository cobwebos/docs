---
title: 适用于 Azure 实验室服务的加速课堂实验室安装指南
description: 本指南可帮助实验室创建者快速设置实验室帐户，使其在学校内使用。
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
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: e00b6dd5af1cb489aee9e8b4c9f3337eb02e4b14
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878096"
---
# <a name="classroom-lab-setup-guide"></a>课堂实验室安装指南

向学生发布实验室的过程可能需要长达几个小时，具体取决于将在实验室中创建的虚拟机（Vm）的数量。 至少需要一天的时间来设置实验室，以确保其正常工作，并留出足够的时间来发布学生的虚拟机。

## <a name="understand-the-lab-requirements-of-your-class"></a>了解类的实验室要求

设置新实验室之前，应考虑以下问题。

### <a name="what-software-requirements-does-the-class-have"></a>类有哪些软件要求？

根据您的类的学习目标，确定需要在实验室的 Vm 上安装哪些操作系统、应用程序和工具。 若要设置实验室 Vm，你有三个选项：

- **使用 Azure marketplace 映像**： azure marketplace 提供了数百个可在创建实验室时使用的映像。 对于某些类，其中一个映像可能已包含你的类所需的所有内容。

- **创建新的自定义映像**：你可以通过使用 Azure Marketplace 映像作为起点来创建自己的自定义映像，并通过安装其他软件并进行配置更改来对其进行自定义。

- **使用现有的自定义映像**：你可以重复使用以前创建的现有自定义映像，或者你的学校创建的现有自定义映像。 这要求管理员配置了共享映像库，这是保存自定义映像的存储库。

> [!NOTE]
> 管理员负责启用 Azure Marketplace 映像和自定义映像，以便可以使用它们。 与你的 IT 部门协调，以确保已启用所需的映像。 你创建的自定义映像会自动启用，以便在你拥有的实验室内使用。

### <a name="what-hardware-requirements-does-the-class-have"></a>该类具有哪些硬件要求？

有多种计算大小可供选择：

- 嵌套虚拟化大小，以便可以向学生提供可托管多个嵌套 Vm 的 VM 的访问权限。 例如，你可以对网络课程使用此计算大小。

- GPU 大小，以便您的学生可以使用计算机密集型类型的应用程序。 例如，此选项适用于人工智能和机器学习。

若要查看可用计算大小的完整列表，请参阅[VM 大小调整](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing)指南。

> [!NOTE]
> 根据实验室的区域，可能会看到更少的可用计算大小，因为这会因区域而异。 通常，应选择最接近需求的最小计算大小。 使用 Azure 实验室服务，可以在以后根据需要使用不同的计算容量来设置新实验室。

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>类对外部 Azure 或网络资源有哪些依赖项？

如果实验室 Vm 需要使用外部资源（例如数据库、文件共享或授权服务器），请与管理员联系，以确保实验室有权访问这些资源。

对于*不*受虚拟网络保护的 Azure 资源的访问权限，你不需要由管理员查找其他配置。 可以通过公共 internet 访问这些资源。

> [!NOTE]
> 你应考虑是否可以通过直接在 VM 上提供资源，将实验室的依赖项降低到外部资源。 例如，若要消除从外部数据库中读取数据的需要，可以直接在 VM 上安装数据库。  

### <a name="how-will-costs-be-controlled"></a>如何控制成本？

实验室服务使用即用即付定价模型，这意味着只需支付实验室 VM 的运行时间。 若要控制成本，你有三个通常与其他选项一起使用的选项：

- **计划**：计划允许您自动控制实验室的 vm 的启动和关闭时间。
- **配额**：配额控制学生在计划时间之外有权访问 VM 的小时数。 如果在学生使用配额时达到了配额，则 VM 会自动关闭。 除非增加了配额，否则无法重新启动 VM。
- **自动关闭**：启用时，自动关闭设置会导致 Windows vm 在经过一段时间后、在学生与远程桌面协议（RDP）会话断开连接后自动关闭。 此设置默认已禁用。  

    > [!NOTE]
    > 此设置目前仅适用于 Windows。

### <a name="how-will-students-save-their-work"></a>学生如何保存工作？

每个学生都分配有自己的 VM，在实验室的生存期内分配给他们。 他们可以选择：

- 直接保存到 VM。
- 保存到外部位置，如 OneDrive 或 GitHub。

可以为其实验室 Vm 上的学生自动配置 OneDrive。

> [!NOTE]
> 若要确保你的学生在实验室外继续访问其保存的工作，并且在类结束后，建议学生将其工作保存到外部存储库。

### <a name="how-will-students-connect-to-their-vm"></a>学生如何连接到其 VM？

对于适用于 Windows Vm 的 RDP，我们建议学生使用[Microsoft 远程桌面的客户端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)。 远程桌面客户端支持 Mac、Chromebook 和 Windows。

对于 Linux Vm，学生可以使用 SSH 或 RDP。 若要让学生使用 RDP 进行连接，必须安装并配置必要的 RDP 和 GUI 包。

## <a name="set-up-your-lab"></a>设置实验室

了解类实验室的要求后，就可以对其进行设置了。 按照此部分中的链接，了解如何设置实验室。

1. **创建实验室。** 有关说明，请参阅[创建教室实验室](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab)教程。

    > [!NOTE]
    > 如果你的类需要嵌套虚拟化，请参阅[启用嵌套虚拟化](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)中的步骤。

1. **自定义映像并发布实验室 Vm。** 连接到称为模板 VM 的特殊虚拟机。 请参阅以下指南中的步骤：
    - [创建和管理模板 VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [使用共享映像库](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > 如果使用的是 Windows，还应参阅[准备 windows 模板 VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template)中的说明。 这些说明包括设置 OneDrive 和 Office 以供学生使用的步骤。

1. **管理 VM 池和容量。** 你可以根据类的需要轻松增加或减少 VM 容量。 请记住，增加 VM 容量可能需要几个小时，因为这涉及到设置新的 Vm。 请参阅[设置和管理 VM 池](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)中的步骤。

1. **添加和管理实验室用户。** 若要将用户添加到实验室，请参阅以下教程中的步骤：
   - [将用户添加到实验室](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [向用户发送邀请](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    有关学生可以使用的帐户类型的信息，请参阅[学生帐户](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)。
  
1. **设置成本控件。** 控制实验室的成本、设置计划、配额和自动关闭。 参阅以下教程：

   - [设置计划](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > VM 可能需要几分钟才能启动，这取决于所安装的操作系统类型。 为了确保实验室 VM 可以在计划的时间内随时使用，建议提前30分钟启动 Vm。

   - [为用户设置配额](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)并为[特定用户设置额外的配额](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [允许在断开连接时自动关闭](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > 计划、配额和自动关闭不适用于模板 VM。 因此，你必须确保在未使用模板 VM 时将其关闭。 否则，它会继续产生费用。 另外，默认情况下，当你创建实验室时，模板 VM 会自动启动。 请确保立即完成实验室设置，并关闭模板 VM。

1. **使用 "仪表板"。** 有关说明，请参阅[使用实验室的仪表板](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)。

    > [!NOTE]
    > 仪表板中显示的预估成本是学生使用实验室所需的最大成本。 例如，你的学生*不*会对未使用的配额小时收费。 估计成本*不会*反映使用模板 VM 或共享映像库的任何费用。

## <a name="next-steps"></a>后续步骤

- [跟踪课堂实验室的使用情况](tutorial-track-usage.md)
  
- [访问教室实验室](tutorial-connect-virtual-machine-classroom-lab.md)