---
title: Azure 实验室服务的加速课堂实验室设置指南
description: 本指南可帮助实验室创建者快速设置实验室帐户，供其学校内使用。
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878096"
---
# <a name="classroom-lab-setup-guide"></a>课堂实验室设置指南

向学生发布实验室的过程最多可能需要几个小时，具体取决于将在实验室中创建的虚拟机 （VM） 数量。 至少留出一天时间设置实验室，确保实验室正常工作，并留出足够的时间发布学生的 VM。

## <a name="understand-the-lab-requirements-of-your-class"></a>了解您班级的实验室要求

在设置新实验室之前，应考虑以下问题。

### <a name="what-software-requirements-does-the-class-have"></a>该类有哪些软件要求？

根据课堂的学习目标，决定需要在实验室的 VM 上安装哪些操作系统、应用程序和工具。 要设置实验室 VM，有三个选项：

- **使用 Azure 应用商店映像**：Azure 应用商店提供了数百个映像，您可以在创建实验室时使用这些映像。 对于某些类，这些映像之一可能已包含类所需的一切。

- **创建新的自定义映像**：可以使用 Azure 应用商店映像作为起点，并通过安装其他软件和进行配置更改来自定义映像。

- **使用现有的自定义映像**：您可以重用以前创建或由学校其他管理员或教职员工创建的现有自定义映像。 这要求管理员配置共享映像库，这是用于保存自定义映像的存储库。

> [!NOTE]
> 管理员负责启用 Azure 应用商店映像和自定义映像，以便可以使用它们。 与 IT 部门协调，确保启用所需的映像。 您创建的自定义映像将自动启用，以便在您拥有的实验室中使用。

### <a name="what-hardware-requirements-does-the-class-have"></a>该类有哪些硬件要求？

有多种计算大小可供选择：

- 嵌套虚拟化大小，以便您可以允许学生访问能够承载多个嵌套 VM 的 VM。 例如，对于网络课程，可以使用此计算大小。

- GPU 大小，以便学生可以使用计算机密集型类型的应用程序。 例如，此选择可能适用于人工智能和机器学习。

请参阅有关 VM[大小调整](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing)的指南，查看可用计算大小的完整列表。

> [!NOTE]
> 根据实验室的区域，您可能会看到可用的计算大小更少，因为区域而异。 通常，应选择最接近您需求的最小计算大小。 使用 Azure 实验室服务，可以在需要时稍后设置具有不同计算容量的新实验室。

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>类对外部 Azure 或网络资源具有哪些依赖关系？

如果实验室 VM 需要使用外部资源（如数据库、文件共享或许可服务器），请与管理员协调以确保实验室有权访问这些资源。

要访问*不受*虚拟网络保护的 Azure 资源，不需要管理员寻求其他配置。 您可以通过公共互联网访问这些资源。

> [!NOTE]
> 应考虑是否可以通过在 VM 上直接提供资源来减少实验室对外部资源的依赖关系。 例如，为了消除从外部数据库读取数据的需要，可以直接在 VM 上安装数据库。  

### <a name="how-will-costs-be-controlled"></a>如何控制成本？

实验室服务使用即用即付定价模型，这意味着您只为实验室 VM 运行时付费。 为了控制成本，您有三个选项通常同时使用：

- **计划**：一个时间表允许您自动控制实验室的 VM 何时启动和关闭。
- **配额**：配额控制学生将在计划时数之外访问 VM 的小时数。 如果在学生使用配额时达到配额，则 VM 将自动关闭。 除非增加了配额，否则学生无法重新启动 VM。
- **自动关闭**：启用后，自动关闭设置会导致 Windows VM 在学生与远程桌面协议 （RDP） 会话断开连接后，在一定时间长度后自动关闭。 此设置默认已禁用。  

    > [!NOTE]
    > 此设置当前仅存在于 Windows 中。

### <a name="how-will-students-save-their-work"></a>学生将如何保存他们的工作？

每个学生都分配自己的 VM，在实验室的生存期内分配给他们。 他们可以选择：

- 直接保存到 VM。
- 保存到外部位置，如 OneDrive 或 GitHub。

可以自动为实验室 VM 上的学生配置 OneDrive。

> [!NOTE]
> 为了确保学生能够继续访问实验室外保存的工作，并在课程结束后，我们建议学生将工作保存到外部存储库。

### <a name="how-will-students-connect-to-their-vm"></a>学生如何连接到他们的 VM？

对于 Windows VM 的 RDP，我们建议学生使用[Microsoft 远程桌面客户端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)。 远程桌面客户端支持 Mac、Chromebook 和 Windows。

对于 Linux VM，学生可以使用 SSH 或 RDP。 要让学生使用 RDP 进行连接，必须安装和配置必要的 RDP 和 GUI 包。

## <a name="set-up-your-lab"></a>设置实验室

了解课堂实验室的要求后，即可设置它。 请按照本节中的链接了解如何设置实验室。

1. **创建实验室。** 有关[创建教室实验室](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab)的说明，请参阅教程。

    > [!NOTE]
    > 如果类需要嵌套虚拟化，请参阅[启用嵌套虚拟化](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)的步骤。

1. **自定义映像并发布实验室 VM。** 连接到称为模板 VM 的特殊 VM。 请参阅以下指南中的步骤：
    - [创建和管理模板 VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [使用共享映像库](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > 如果使用 Windows，还应看到[准备 Windows 模板 VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template)的说明。 这些说明包括设置 OneDrive 和 Office 供学生使用的步骤。

1. **管理 VM 池和容量。** 您可以根据需要轻松扩展或减少 VM 容量。 请记住，增加 VM 容量可能需要几个小时，因为这涉及到设置新的 VM。 请参阅[设置和管理 VM 池](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)的步骤。

1. **添加和管理实验室用户。** 要将用户添加到实验室，请参阅以下教程中的步骤：
   - [将用户添加到实验室](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [向用户发送邀请](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    有关学生可以使用的帐户类型的信息，请参阅[学生帐户](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)。
  
1. **设置成本控制。** 要控制实验室的成本，请设置计划、配额和自动关闭。 参阅以下教程：

   - [设置计划](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > 根据已安装的操作系统类型，VM 可能需要几分钟才能启动。 为确保实验室 VM 在预定时间内准备就绪，我们建议您提前 30 分钟启动 VM。

   - [为用户设置配额](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)并为[特定用户设置其他配额](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [允许在断开连接时自动关闭](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > 计划、配额和自动关机不适用于模板 VM。 因此，必须确保在不使用模板 VM 时关闭该模板 VM。 否则，它将继续产生成本。 此外，默认情况下，当您创建实验室时，模板 VM 将自动启动。 请确保立即完成实验室的设置，并关闭模板 VM。

1. **使用仪表板。** 有关说明，请参阅[使用实验室的仪表板](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)。

    > [!NOTE]
    > 仪表板中显示的估计成本是学生使用实验室时可以期望的最大成本。 例如，您的学生*不会*向您收取未使用的配额小时费。 估计成本*不*反映使用模板 VM 或共享映像库的任何费用。

## <a name="next-steps"></a>后续步骤

- [跟踪课堂实验室的使用情况](tutorial-track-usage.md)
  
- [访问教室实验室](tutorial-connect-virtual-machine-classroom-lab.md)