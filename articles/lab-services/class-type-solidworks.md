---
title: 使用 Azure 实验室服务设置用于工程的 SOLIDWORKS 实验室 |Microsoft Docs
description: 了解如何使用 SOLIDWORKS 设置用于工程课程的实验室。
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 5511ad5a517bbd320ce3d66de90a8aec084c7e15
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290733"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>使用 SOLIDWORKS 设置工程类的实验室

[SOLIDWORKS](https://www.solidworks.com/)提供了用于建模实体对象的3d 计算机辅助设计（CAD）环境，并用于不同种类的工程字段。  借助 SOLIDWORKS，工程师可以轻松创建、直观显示、模拟和记录其设计。

大学常使用的授权选项是 SOLIDWORKS 的网络许可。   使用此选项时，用户共享由授权服务器管理的许可证池。  这种类型的许可证有时称为 "浮动" 许可证，因为你只需为并发用户数提供足够的许可证。  使用 SOLIDWORKS 完成某个用户后，他们的许可证会退回到集中管理的许可证池中，以便其他用户重复使用。

本文介绍如何设置使用 SOLIDWORKS 2019 和网络许可的类。

## <a name="license-server"></a>许可证服务器

SOLIDWORKS 网络许可要求你已在许可证服务器上安装并激活 SolidNetWork License Manager。  此许可证服务器通常位于本地网络或 Azure 中的专用网络中。  有关如何在服务器上设置 SolidNetWork 许可证管理器的详细信息，请参阅 SOLIDWORKS 安装指南中的[安装和激活许可证管理](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm)器。  对此进行设置时，请记住使用的**端口号**和[**序列号**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm)，因为在后续步骤中将需要它们。

设置许可证服务器后，需将[虚拟网络（VNet）](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)对等互连到[实验室帐户](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account)。  必须在创建实验室之前完成网络对等互连，以便实验室虚拟机可以访问许可证服务器，以及其他方法。

> [!NOTE]
> 你应验证是否在防火墙上打开了适当的端口，以便允许实验室虚拟机与许可证服务器之间的通信。  例如，请参阅有关为[Windows 防火墙修改许可证管理器计算机端口](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm)的说明，其中显示了如何将入站和出站规则添加到许可证服务器的防火墙。  你可能还需要打开实验室虚拟机的端口。  有关此操作的详细信息，请参阅有关[实验室的防火墙设置](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-firewall-settings)一文中的步骤，包括如何获取实验室的公共 IP 地址。

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，需要一个 Azure 订阅和实验室帐户才能开始使用。 如果你还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 有关创建新实验室帐户的详细信息，请参阅有关[如何设置实验室帐户](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account)的教程。 你还可以使用现有的实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

为实验室帐户启用下表中所述的设置。 有关如何启用 marketplace 映像的详细信息，请参阅有关[如何指定可用于实验室创建者的 marketplace 映像](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)的文章。

| 实验室帐户设置 | Instructions |
| ------------------- | ------------ |
|市场映像| 启用 Windows 10 专业版映像，以便在实验室帐户中使用。|

> [!NOTE]
> 除 Windows 10 外，SOLIDWORKS 还支持其他版本的 Windows。  有关详细信息，请参阅[SOLIDWORKS 系统要求](https://www.solidworks.com/sw/support/SystemRequirements.html)。

### <a name="lab-settings"></a>实验室设置

设置教室实验室时，请使用下表中的设置。 有关如何创建教室实验室的详细信息，请参阅设置课堂实验室教程。

| 实验室设置 | 值/说明 |
| ------------ | ------------------ |
|虚拟机大小| **小型 GPU （可视化）**。  此 VM 最适合用于使用框架（如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏、编码。|  
|虚拟机映像| Windows 10 专业版|

> [!NOTE]
> **小型 GPU （可视化）** 虚拟机大小配置为实现高性能的图形体验。  有关此虚拟机大小的详细信息，请参阅有关[如何使用 gpu 设置实验室](./how-to-setup-lab-gpu.md)的文章。

> [!WARNING]
> 在创建实验室**之前**，不要忘记将实验室帐户的虚拟网络与许可证服务器的虚拟网络[对等](https://www.mathworks.com/support/requirements/matlab-system-requirements.html)。

## <a name="template-virtual-machine-configuration"></a>模板虚拟机配置

本部分中的步骤说明如何通过下载 SOLIDWORKS 安装文件和安装客户端软件来设置模板虚拟机：

1. 启动模板虚拟机，并使用 RDP 连接到计算机。

1. 下载 SOLIDWORKS 客户端软件的安装文件。 有两个选项可供下载：
   - 从[SOLIDWORKS 客户门户](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F)下载。
   - 从服务器上的目录下载。  如果使用此选项，则需要确保可以从模板虚拟机访问服务器。  例如，此服务器可能位于与实验室帐户对等互连的同一虚拟网络中。
  
    有关详细信息，请参阅 SOLIDWORKS in SOLIDWORKS install guide[中的单独计算机上的安装](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0)。

1. 下载安装文件后，使用 SOLIDWORKS 安装管理器安装客户端软件。 请参阅 SOLIDWORKS 安装指南中有关[安装许可证客户端](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm)的详细信息。

    > [!NOTE]
    > 在 "**添加服务器**" 对话框中，系统会提示输入用于许可证服务器的**端口号**以及许可证服务器的名称或 IP 地址。

## <a name="cost"></a>开销

我们来介绍此类的可能的成本估算。 此估计不包括运行许可证服务器的成本。 我们将使用一类25名学生。 计划的类时间有20小时。 此外，每个学生在计划的类时间之外为家庭作业或分配获取10小时配额。 我们选择的虚拟机大小为**小 GPU （可视化）**，即160实验室单位。

25名学生 \* （20个计划小时 + 10 个配额小时） \* 160 实验室单位 * 0.01 美元/小时 = 1200.00 usd

>[!IMPORTANT]
> 成本估算仅用于示例目的。  有关定价的最新详细信息，请参阅[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。  

## <a name="next-steps"></a>后续步骤

下一步是设置任何实验室的常见步骤。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [向学生发送电子邮件注册链接](how-to-configure-student-usage.md#send-invitations-to-users)
