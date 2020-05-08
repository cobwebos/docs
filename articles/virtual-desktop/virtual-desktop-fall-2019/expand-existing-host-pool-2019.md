---
title: 利用新的会话主机展开现有的主机池-Azure
description: 如何使用 Windows 虚拟桌面中的新会话主机扩展现有的主机池。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f4313f36a0b250be9646c6658b98f15037374729
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615521"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>使用新的会话主机展开现有的主机池

>[!IMPORTANT]
>此内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的秋季2019版本。 如果尝试管理春季2020更新中引入的 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../expand-existing-host-pool.md)。

当你在主机池内提高使用率时，可能需要使用新的会话主机扩展现有的主机池以处理新负载。

本文将介绍如何使用新的会话主机展开现有的主机池。

## <a name="what-you-need-to-expand-the-host-pool"></a>扩展主机池所需的条件

在开始之前，请确保已使用以下方法之一创建了主机池和会话主机虚拟机（Vm）：

- [Azure Marketplace 产品/服务](create-host-pools-azure-marketplace-2019.md)
- [GitHub Azure 资源管理器模板](create-host-pools-arm-template.md)
- [使用 PowerShell 创建主机池](create-host-pools-powershell-2019.md)

当你首次创建主机池和会话主机 Vm 时，还需要以下信息：

- VM 大小、映像和名称前缀
- 域加入和 Windows 虚拟桌面租户管理员凭据
- 虚拟网络名称和子网名称

接下来的三个部分是可以用来展开主机池的三个方法。 你可以通过你熟悉的任何部署工具来执行。

>[!NOTE]
>在部署阶段，如果以前的会话主机 VM 资源当前已关闭，你将看到这些资源的错误消息。 发生这些错误的原因是，Azure 无法运行 PowerShell DSC 扩展来验证是否已将会话主机 Vm 正确地注册到现有主机池。 您可以安全地忽略这些错误，也可以通过在开始部署过程之前启动现有主机池中的所有会话主机 Vm 来避免这些错误。

## <a name="redeploy-from-azure"></a>从 Azure 重新部署

如果已使用[Azure Marketplace 产品/服务](create-host-pools-azure-marketplace-2019.md)或[GitHub azure 资源管理器模板](create-host-pools-arm-template.md)创建了主机池和会话主机 vm，则可以从 Azure 门户重新部署同一模板。 重新部署模板会自动重新输入您输入到原始模板中的所有信息（密码除外）。

下面介绍如何重新部署 Azure 资源管理器模板来扩展主机池：

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 从 "Azure 门户顶部的搜索栏中，搜索"**资源组**"，然后选择"**服务**"下的项。
3. 查找并选择在创建主机池时创建的资源组。
4. 在浏览器左侧的面板中，选择 "**部署**"。
5. 为主机池创建过程选择适当的部署：
     - 如果创建了包含 Azure Marketplace 产品/服务的原始主机池，请选择 " **wvd**"。
     - 如果已创建包含 GitHub Azure 资源管理器模板的原始主机池，请选择名为 " **Microsoft. template**" 的部署。
6. 选择 "重新**部署**"。
     
     >[!NOTE]
     >如果选择 "重新**部署**" 后模板不会自动重新部署，请在浏览器左侧的面板中选择 "**模板**"，然后选择 "**部署**"。

7. 选择包含现有主机池中的当前会话主机 Vm 的资源组。
     
     >[!NOTE]
     >如果你看到一个错误，该错误指示你选择不同的资源组（即使你输入的资源组正确），请选择另一个资源组，然后选择原始资源组。

8. 输入 *_artifactsLocation*的以下 URL：`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. 输入所*需的新*会话主机总数。 例如，如果要将主机池从5个会话主机扩展到8个，请输入**8**。
10. 输入用于现有域 UPN 的相同现有域密码。 请勿更改用户名，因为这会导致在运行模板时出现错误。
11. 输入用于为*租户管理员 Upn 或应用程序 id*输入的用户或应用程序 id 的相同租户管理员密码。同样，不要更改用户名。
12. 完成提交以扩展主机池。

## <a name="run-the-azure-marketplace-offering"></a>运行 Azure Marketplace 产品/服务

按照[使用 Azure Marketplace 创建主机池](create-host-pools-azure-marketplace-2019.md)中的说明进行操作，直到[运行 azure marketplace 产品/服务来预配新的主机池](create-host-pools-azure-marketplace-2019.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool)。 转到该点时，需要输入每个选项卡的下列信息：

### <a name="basics"></a>基础知识

本部分中的所有值应与首次创建主机池和会话主机 Vm 时提供的值匹配（*默认桌面用户*除外）：

1.    对于 "*订阅*"，请选择你在其中首次创建主机池的订阅。
2.    对于 "*资源组*"，请选择现有主机池会话所在的同一资源组。
3.    对于 "*区域*"，请选择现有主机池会话所在的同一区域。
4.    对于 " *Hostpool 名称*"，请输入现有主机池的名称。
5.    对于 "*桌面类型*"，请选择与现有主机池匹配的桌面类型。
6.    对于*默认的桌面用户*，请输入一个逗号分隔列表，其中列出了你希望在 Azure Marketplace 产品完成后登录到 Windows 虚拟桌面客户端并访问桌面的任何其他用户。 例如，如果要user3@contoso.com分配和user4@contoso.com访问，请输入。 user3@contoso.comuser4@contoso.com
7.    选择 "**下一步：配置虚拟机**"。

>[!NOTE]
>除了*默认的桌面用户*，所有字段必须与现有主机池中配置的内容完全匹配。 如果存在不匹配的情况，则会导致新的主机池。

### <a name="configure-virtual-machines"></a>配置虚拟机

本部分中的所有参数值应与首次创建主机池和会话主机 Vm 时提供的值匹配，但 Vm 的总数除外。 输入的 Vm 数将是扩展的主机池中的 Vm 数：

1. 选择与现有会话主机 Vm 匹配的 VM 大小。
    
    >[!NOTE]
    >如果要查找的特定 VM 大小未出现在 VM 大小选择器中，那是因为我们尚未将其加入到 Azure 市场工具中。 若要请求 VM 大小，请在 [Windows 虚拟桌面 UserVoice 论坛](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)中创建请求或投票赞成现有请求。

2. 自定义*使用情况配置文件*、*总用户**数和虚拟机数*参数，以选择要在主机池中使用的会话主机总数。 例如，如果要将主机池从5个会话主机扩展到8个，请将这些选项配置为获取8个虚拟机。
3. 输入虚拟机名称的前缀。 例如，如果输入“prefix”作为名称，则虚拟机的名称将是“prefix-0”、“prefix-1”，依此类推。
4. 选择 "**下一步：虚拟机设置**"。

### <a name="virtual-machine-settings"></a>虚拟机设置

本部分中的所有参数值应与首次创建主机池和会话主机 Vm 时提供的值匹配：

1. 对于 "*映像源*" 和 "*映像操作系统版本*"，输入在首次创建主机池时提供的相同信息。
2. 对于*AD 域加入 UPN*和关联的密码，输入在首次创建主机池以将 vm 加入到 Active Directory 域时所提供的相同信息。 这些凭据将用于在虚拟机上创建本地帐户。 稍后可以重置这些本地帐户以更改其凭据。
3. 对于虚拟网络信息，为现有的主机池会话主机所在的位置选择同一个虚拟网络和子网。
4. 选择**下一步：配置 Windows 虚拟桌面信息**。

### <a name="windows-virtual-desktop-information"></a>Windows 虚拟桌面信息

本部分中的所有参数值应与首次创建主机池和会话主机 Vm 时提供的值匹配：

1. 对于“Windows 虚拟桌面租户组名称”**，请输入租户所在的租户组的名称。 将此字段保留默认值，除非为你提供了具体的租户组名称。
2. 对于“Windows 虚拟桌面租户名称”**，请输入要在其中创建此主机池的租户的名称。
3. 指定在首次创建主机池和会话主机 Vm 时使用的相同凭据。 如果你使用的是服务主体，请输入服务主体所在的 Azure Active Directory 实例的 ID。
4. 选择**下一步：查看 + 创建**。

## <a name="run-the-github-azure-resource-manager-template"></a>运行 GitHub Azure 资源管理器模板

按照[运行 Azure 资源管理器模板中的说明来预配新的主机池](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool)，并提供所有相同的参数值，而不是*Rdsh 的实例数*。 在运行模板之后，输入主机池中所需的会话主机 Vm 的数量。 例如，如果要将主机池从5个会话主机扩展到8个，请输入**8**。

## <a name="next-steps"></a>后续步骤

现在，你已扩展现有的主机池，你可以登录到 Windows 虚拟桌面客户端，以将其作为用户会话的一部分进行测试。 你可以使用以下任一客户端连接到会话：

- [使用 Windows 桌面客户端进行连接](../connect-windows-7-and-10.md)
- [使用 Web 客户端进行连接](connect-web-2019.md)
- [使用 Android 客户端进行连接](connect-android-2019.md)
- [使用 macOS 客户端进行连接](connect-macos-2019.md)
- [使用 iOS 客户端进行连接](connect-ios-2019.md)
