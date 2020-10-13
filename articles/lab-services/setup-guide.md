---
title: 适用于 Azure 实验室服务的加速课堂实验室设置指南
description: 本指南可帮助实验室创建者快速设置实验室帐户以在学校中使用。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 510aa97a0a47e62f627203495c601bb2538e19ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91652204"
---
# <a name="classroom-lab-setup-guide"></a>课堂实验室设置指南

向学生发布实验室的过程可能需要长达几个小时，具体取决于将在实验室中创建的虚拟机 (VM) 的数量。 至少需要一天的时间来设置实验室，以确保其正常工作，并留出足够的时间来发布学生的 VM。

## <a name="understand-the-lab-requirements-of-your-class"></a>了解课程的实验室要求

设置新实验室之前，应考虑以下问题。

### <a name="what-software-requirements-does-the-class-have"></a>课程有哪些软件要求？

基于课程的学习目标，确定需要在实验室的 VM 上安装的操作系统、应用程序和工具。 若要设置实验室 VM，你有三个选项：

- 使用 Azure 市场映像：Azure 市场提供数百个映像，可以在创建实验室时使用。 对于某些课程，这些映像中的一个可能已包含课程所需的所有内容。

- 创建新自定义映像：可以创建自己的自定义映像，具体方法是使用 Azure Marketplace 映像作为起点，并通过安装其他软件和进行配置更改来进行自定义。

- 使用现有自定义映像：可以重复使用你以前创建的现有自定义映像，或是学校的其他管理员或教职员工创建的现有自定义映像。 这要求管理员配置了共享映像库，这是用于保存自定义映像的存储库。

> [!NOTE]
> 管理员负责启用 Azure 市场映像和自定义映像，以便你可以使用它们。 与 IT 部门协调，以确保启用了所需映像。 你创建的自定义映像会自动启用，以便在你拥有的实验室中使用。

### <a name="what-hardware-requirements-does-the-class-have"></a>课程有哪些硬件要求？

有各种计算大小可供选择：

- 嵌套虚拟化大小，以便可以向学生提供对能够托管多个嵌套 VM 的 VM 的访问权限。 例如，可以对网络课程使用此计算大小。

- GPU 大小，以便学生可以使用计算机密集型类型的应用程序。 例如，此选项可能适用于人工智能和机器学习。

请参阅有关 [VM 大小调整](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing)的指南，以了解可用计算大小的完整列表。

> [!NOTE]
> 根据实验室的区域，你可能会看到较少的可用计算大小，因为这会因区域而异。 通常，应选择最接近需求的最小计算大小。 借助 Azure 实验室服务，可以在以后需要时设置具有不同计算大小的新实验室。

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>课程对外部 Azure 或网络资源有哪些依赖？

如果实验室 VM 需要使用外部资源（例如数据库、文件共享或授权服务器），请与管理员协调，以确保实验室有权访问这些资源。

若要访问不受虚拟网络保护的 Azure 资源，无需由管理员进行其他配置。 可以通过公共 Internet 访问这些资源。

> [!NOTE]
> 应考虑是否可以通过直接在 VM 上提供资源，减少实验室对外部资源的依赖。 例如，若要无需从外部数据库读取数据，可以直接在 VM 上安装数据库。  

### <a name="how-will-costs-be-controlled"></a>如何控制成本？

实验室服务使用即用即付定价模型，这意味着只需为实验室 VM 的运行时间付款。 若要控制成本，你有三个选项，它们通常相互结合使用：

- **计划**：计划使你可以自动控制实验室的 VM 的启动和关闭时间。
- 配额：配额控制学生在计划时间之外有权访问 VM 的小时数。 如果在学生正在使用时达到配额，则 VM 会自动关闭。 除非增加了配额，否则学生无法重新启动 VM。
- 自动关闭：启用时，在学生与远程桌面协议 (RDP) 会话断开连接后，自动关闭设置会使 Windows VM 在一段特定时间后自动关闭。 此设置默认已禁用。  

### <a name="how-will-students-save-their-work"></a>学生如何保存其工作？

每个学生都分配有自己的 VM（在实验室的生存期内分配给他们）。 他们可以选择：

- 直接保存到 VM。
- 保存到外部位置，如 OneDrive 或 GitHub。

可以在其实验室 VM 上为学生自动配置 OneDrive。

> [!NOTE]
> 若要确保学生在实验室外部以及课程结束之后可继续访问其保存的工作，建议学生将其工作保存到外部存储库。

### <a name="how-will-students-connect-to-their-vm"></a>学生如何连接到其 VM？

若要通过 RDP 连接到 Windows VM，建议学生使用 [Microsoft 远程桌面客户端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)。 远程桌面客户端支持 Mac、Chromebook 和 Windows。

对于 Linux VM，学生可以使用 SSH 或 RDP。 若要让学生使用 RDP 进行连接，必须安装并配置必要的 RDP 和 GUI 包。

## <a name="set-up-your-lab"></a>设置实验室

了解课程实验室的要求之后，便可以对它进行设置了。 按照此部分中的链接了解如何设置实验室。

1. 创建实验室。 请参阅有关[创建课堂实验室](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab)的教程以获得说明。

    > [!NOTE]
    > 如果课程需要嵌套虚拟化，请参阅[启用嵌套虚拟化](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)中的步骤。

1. 自定义映像并发布实验室 VM。 连接到称为模板 VM 的特殊 VM。 请参阅以下指南中的步骤：
    - [创建和管理模板 VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [使用共享映像库](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > 如果在使用 Windows，还应参阅[准备 Windows 模板 VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template) 中的说明。 这些说明包括设置 OneDrive 和 Office 以供学生使用的步骤。

1. 管理 VM 池和容量。 可以根据课程需要轻松增加或缩减 VM 容量。 请记住，增加 VM 容量可能需要数小时，因为这涉及到设置新 VM。 请参阅[设置和管理 VM 池](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)中的步骤。

1. 添加和管理实验室用户。 若要将用户添加到实验室，请参阅以下教程中的步骤：
   - [将用户添加到实验室](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [向用户发送邀请](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    有关学生可以使用的帐户类型的信息，请参阅[学生帐户](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)。
  
1. 设置成本控制。 若要控制实验室的成本，请设置计划、配额和自动关闭。 参阅以下教程：

   - [设置日程安排](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > VM 可能需要几分钟才能启动，具体取决于所安装的操作系统类型。 若要确保实验室 VM 在计划时间内可随时使用，建议提前 30 分钟启动 VM。

   - [为用户设置配额](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)和[为特定用户设置额外配额](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [启用在断开连接时自动关闭](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > 计划和配额不适用于模板 VM，但自动关闭设置适用于这种 VM。 
        > 
        > 创建实验室时，模板 VM 将会创建，但不会启动。 可以启动它，连接到它，并为实验室安装任何必备软件，然后发布它。 发布模板 VM 时，如果未关闭它，则它会自动关闭。 
        > 
        > 模板 VM 在运行时会产生成本，因此，请确保在不需要运行模板 VM 时将它关闭。 


1. 使用仪表板。 有关说明，请参阅[使用实验室的仪表板](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)。

    > [!NOTE]
    > 仪表板中显示的估计成本是针对学生使用实验室所预计的最大成本。 例如，不会对学生未使用的配额时数向你收费。 估计成本 *不会* 反映使用模板 VM、共享图像库或实验室创建者启动用户计算机的任何费用。

## <a name="next-steps"></a>后续步骤

- [跟踪教室实验室的使用情况](tutorial-track-usage.md)
  
- [访问教室实验室](tutorial-connect-virtual-machine-classroom-lab.md)
