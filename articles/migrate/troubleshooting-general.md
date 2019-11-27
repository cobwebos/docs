---
title: 排查 Azure Migrate 问题 | Microsoft 文档
description: 概述 Azure Migrate 服务中的已知问题，以及常见错误的疑难解答提示。
author: musa-57
ms.manager: abhemraj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: hamusa
ms.openlocfilehash: 96c5190988d79885f3a1335b6fd431e028bba8fc
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384058"
---
# <a name="troubleshoot-azure-migrate"></a>排查 Azure Migrate 问题

[Azure Migrate](migrate-services-overview.md)提供了一个用于评估和迁移的工具中心，以及第三方独立软件供应商（ISV）产品。 本文将帮助你排查 Azure Migrate、Azure Migrate 服务器评估和 Azure Migrate 服务器迁移的问题。


## <a name="find-a-project"></a>查找项目

Azure Migrate 有[两个版本](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)。


如果在 Azure Migrate 的当前版本中创建 Azure Migrate 项目，请执行以下操作：

1. 在[Azure 门户](https://portal.azure.com)中，搜索**Azure Migrate**。
2. 在 Azure Migrate 面板 >**服务器**"中，选择右上角的"**更改**"。

    ![切换到现有 Azure Migrate 项目](./media/troubleshooting-general/switch-project.png)

3. 选择相应的订阅并 Azure Migrate 项目。


如果在 Azure Migrate 的以前版本中创建了项目，请执行以下操作：

1. 在[Azure 门户](https://portal.azure.com)中，搜索**Azure Migrate**。
2. 在 Azure Migrate "仪表板" 中，如果已在以前的版本中创建了一个项目，则将显示引用较旧项目的标题。 选择标题。

    ![访问现有项目](./media/troubleshooting-general/access-existing-projects.png)

3. 查看旧项目的列表。


## <a name="create-additional-projects"></a>创建其他项目

创建新的 Azure Migrate 项目，如下所示：

1. 在[Azure 门户](https://portal.azure.com)中，搜索**Azure Migrate**。
2. 在 Azure Migrate 仪表板 >**服务器**上，选择右上角的 "**更改**"。

   ![更改 Azure Migrate 项目](./media/troubleshooting-general/switch-project.png)

3. 若要创建新项目，请选择 **"单击此处"** 。

   ![创建第二个 Azure Migrate 项目](./media/troubleshooting-general/create-new-project.png)

## <a name="review-supported-geographies"></a>查看支持的地理位置

查看[VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects)和[hyper-v](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)支持的地理位置。

## <a name="delete-projectsworkspaces"></a>删除项目/工作区

删除 Azure Migrate 项目和 Log Analytics 工作区时，请注意：

- 删除 Azure Migrate 项目时，将删除项目*和*有关发现的计算机的元数据。
- 如果已将 Log Analytics 工作区附加到服务器评估工具，则不会自动删除此工作区。
- 同一 Log Analytics 工作区可用于多个方案。
- 如果要删除 Log Analytics 工作区，则必须手动执行此操作。


### <a name="delete-a-project"></a>删除项目

若要删除当前版本的 Azure Migrate 中的项目：

1. 打开在其中创建项目的 Azure 资源组。
2. 在 "资源组" 页上，选择 "**显示隐藏的类型**"。
3. 选择要删除的迁移项目。 资源类型为 migrateprojects/，并将其删除。

删除较旧版本的 Azure Migrate 中的项目：

1. 打开在其中创建项目的 Azure 资源组。
2. 选择要删除的迁移项目。 资源类型是迁移项目，并将其删除。


### <a name="delete-a-workspace"></a>创建工作区

浏览到附加到该项目的 Log Analytics 工作区。
* 如果你尚未删除 Azure Migrate 项目，可以在**Essentials** > **服务器评估**中找到工作区的链接。
       ![LA 工作区](./media/troubleshooting-general/loganalytics-workspace.png)

     * If you've already deleted the Azure Migrate project, select **Resource Groups** in the left pane of the Azure portal. Locate the workspace in the relevant resources group, and [follow the instructions](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) to delete it.


## <a name="error-requests-must-contain-user-identity-headers"></a>错误 "请求必须包含用户标识标头"

创建项目时，此错误可能表示无权访问组织的 Azure Active Directory （Azure AD）租户。

- 当你首次添加到 Azure AD 租户时，你将收到加入租户的电子邮件邀请。
- 接受邀请以成功添加到租户。
    - 如果看不到此电子邮件，请与有权访问租户的用户联系，要求他们[重新发送邀请](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)。
    - 收到邀请电子邮件后，将其打开，然后选择接受邀请的链接。 然后，注销 Azure 门户并重新登录。 （刷新浏览器将不起作用。）然后，你可以开始创建迁移项目。


## <a name="error-invalid-ovf-manifest-entry"></a>错误 "OVF 清单条目无效"

如果收到错误消息 "提供的清单文件无效：无效的 OVF 清单条目"，请执行以下操作：

1. 通过检查 Azure Migrate 的设备的哈希值，验证是否已正确下载了设备 .OVA 文件。 [了解详细信息](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware)。 如果哈希值不匹配，请重新下载 .OVA 文件，然后重试部署。
2. 如果部署仍失败，并且你正在使用 VMware vSphere 客户端部署 OVF 文件，请尝试通过 vSphere web 客户端进行部署。 如果部署仍失败，请尝试使用不同的 web 浏览器。
3. 如果你使用的是 vSphere web 客户端并尝试在 vCenter Server 6.5 或6.7 上部署它，请尝试直接在 ESXi 主机上部署 .OVA：
   - 直接连接到 ESXi 主机（而不是 vCenter Server）与 web 客户端（https：//<*主机 IP 地址*>/ui）连接。
   - 在 "**家庭** > **库存**" 中，选择 "**文件** > **部署 OVF 模板**"。 浏览到 .OVA 并完成部署。
4. 如果部署仍然失败，请联系 Azure Migrate 支持部门。

## <a name="appliance-cant-connect-to-the-internet"></a>设备无法连接到 internet

如果设备计算机位于代理后面，则会发生这种情况。

- 如果代理需要，请确保提供身份验证凭据。
- 如果使用基于 URL 的防火墙代理控制出站连接，请将这些 Url 添加到允许列表中：

    - [用于 VMware 评估的 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [Hyper-v 评估的 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [用于 VMware 无代理迁移的 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [基于 VMware 代理的迁移的 URL](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [Hyper-v 迁移的 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- 如果使用截取代理连接到 internet，请使用[以下步骤](https://docs.microsoft.com/azure/migrate/concepts-collector)将代理证书导入到设备 VM。

## <a name="errordatetime-synchronization"></a>错误：日期/时间同步

与日期和时间同步相关的错误（802）指示服务器时钟可能与当前时间不同步的时间超过五分钟。 更改收集器 VM 上的时钟时间以匹配当前时间：

1. 在 VM 上打开管理员命令提示符。
2. 若要检查时区，请运行**w32tm/tz**。
3. 若要同步时间，请运行**w32tm/resync**。


## <a name="error-unabletoconnecttoserver"></a>错误： UnableToConnectToServer

如果出现此连接错误，则可能无法连接到 vCenter Server *Servername*. .com：9443。 错误详细信息表示没有终结点在 https://*servername*： 9443/sdk 上侦听，可以接受消息。

- 检查是否正在运行最新版本的设备。 如果不是，请将该设备升级到[最新版本](https://docs.microsoft.com/azure/migrate/concepts-collector)。
- 如果此问题仍然出现在最新版本中，则设备可能无法解析指定的 vCenter Server 名称，或者指定的端口可能是错误的。 默认情况下，如果未指定端口，收集器会尝试连接到端口号443。

    1. 从设备 Ping *Servername*.com。
    2. 如果步骤1失败，请尝试使用 IP 地址连接到 vCenter 服务器。
    3. 确定要连接到 vCenter Server 的正确端口号。
    4. 验证 vCenter Server 是否已启动并正在运行。


## <a name="error-appliance-might-not-be-registered"></a>错误：设备可能未注册

- 如果用于注册设备的 Azure 帐户没有足够的权限，则会出现错误 60052 "该设备可能未成功注册到 Azure Migrate 项目"。
    - 请确保用于注册设备的 Azure 用户帐户至少具有订阅的参与者权限。
    - [详细了解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)所需的 Azure 角色和权限。
- 如果注册失败，因为找不到用于注册设备的 Azure Migrate 项目，所以如果注册失败，则会出现 "设备可能未成功注册到 Azure Migrate 项目" 错误60039。
    - 在 Azure 门户中，检查该项目是否存在于资源组中。
    - 如果项目不存在，请在资源组中创建新的 Azure Migrate 项目，并再次注册设备。 [了解如何](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool)创建新项目。

## <a name="error-key-vault-management-operation-failed"></a>错误： Key Vault 管理操作失败

如果收到错误60030或60031，"Azure Key Vault 管理操作失败"，请执行以下操作：
- 请确保用于注册设备的 Azure 用户帐户至少具有订阅的参与者权限。
- 请确保该帐户具有对错误消息中指定的密钥保管库的访问权限，然后重试该操作。
- 如果问题持续出现，请联系 Microsoft 支持。
- [详细了解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)所需的 Azure 角色和权限。

## <a name="fix-discovery-couldnt-be-initiated"></a>修复：无法启动发现

错误60028：由于出错，无法启动发现。 对于指定的主机或群集列表，操作失败 "指示无法在错误中列出的主机上启动发现，因为访问或检索 VM 信息时出现问题。 已成功添加其余的主机。

- 使用**add host**选项再次添加错误中列出的主机。
- 如果出现验证错误，请查看更正指南以修复错误，然后再次尝试 "**保存并启动发现**" 选项。

## <a name="fix-azure-ad-operation-failed-60025"></a>修复： Azure AD 操作失败（60025）

错误60025： "Azure AD 操作失败。 如果用于启动发现的 Azure 用户帐户与用于注册设备的帐户不同，则在创建或更新 Azure AD 应用程序时发生错误。 执行下列操作之一：

- 确保启动发现的用户帐户与用于注册设备的用户帐户相同。
- 为发现操作失败的用户帐户提供 Azure Active Directory 应用程序访问权限。
- 删除先前为 Azure Migrate 项目创建的资源组。 创建另一个资源组以重新开始。
- [了解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)有关 Azure Active Directory 应用程序权限的详细信息。


## <a name="discovered-vms-not-in-portal"></a>未在门户中发现的 Vm

如果启动发现以便**服务器评估**和**服务器迁移**显示**正在进行的发现**，但尚未在门户中看到 vm，请注意以下事项：

- 从设备启动发现后，VMware VM 发现大约要花费15分钟，对于 Hyper-v VM 发现，每个添加的主机大约要等待两分钟。
- 如果继续查看正在**进行的发现**，甚至在这些等待时间之后，请在 "**服务器**" 选项卡上选择 "**刷新**"。这会显示发现服务器在**服务器评估**和**服务器迁移**中的计数。


## <a name="deleted-vms-in-the-portal"></a>已在门户中删除 Vm

如果你部署了一台持续发现本地环境的设备，但已删除的 Vm 仍会显示在门户中，请注意以下事项：  

- 设备收集的发现数据需要长达30分钟的时间才能在门户中反映出来。
- 如果在30分钟后看不到最新信息，请按照以下步骤刷新数据：

    1. 在**服务器** > **Azure Migrate 服务器评估**中，选择 "**概述**"。
    2. 在 "**管理**" 下，选择**代理运行状况**
    3. 选择 "**刷新代理**"。
    1. 等待刷新操作完成。 现在应会看到最新信息。

## <a name="vm-information-isnt-in-the-portal"></a>VM 信息不在门户中

- 设备收集的发现数据需要长达30分钟的时间才能在门户中反映出来。
- 如果在30分钟后看不到最新信息，请按照以下步骤刷新数据：

    1. 在**服务器** > **Azure Migrate 服务器评估**中，选择 "**概述**"。
    2. 在 "**管理**" 下，选择**代理运行状况**
    3. 选择 "**刷新代理**"。
    1. 等待刷新操作完成。 现在应会看到最新信息。


## <a name="fix-cant-connect-to-host-or-cluster"></a>修复：无法连接到主机或群集

错误50004： "无法连接到主机或群集，因为无法解析服务器名称。 WinRM 错误代码：如果设备的 Azure DNS 服务无法解析提供的群集或主机名，则可能出现 0x803381B9 "。

- 如果在群集上看到此错误，则为群集 FQDN。
- 对于群集中的主机，你可能还会看到此错误。 这表明设备可以连接到群集，但群集返回的主机名不是 Fqdn。 若要解决此错误，请通过添加 IP 地址和主机名的映射来更新设备上的 hosts 文件：
    1. 以管理员身份打开记事本。
    2. 打开 C:\Windows\System32\Drivers\etc\hosts 文件。
    3. 在行中添加 IP 地址和主机名。 对于出现此错误的每个主机或群集重复此操作。
    4. 保存并关闭 hosts 文件。
    5. 使用设备管理应用检查设备是否可以连接到主机。 30分钟后，应在 Azure 门户中看到这些主机的最新信息。

## <a name="application-discovery-issues"></a>应用程序发现问题

目前仅支持对 VMware Vm 的应用程序的发现。 以后会启用对 Hyper-v Vm 和物理服务器的支持。

应用程序的发现要求在设备中提供 VM 凭据。如果未在设备中提供 VM 凭据，则应用程序发现将不起作用。 [详细了解](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions)VCenter Server 和 VMware vm 所需的访问权限。 如果在设备中提供了 VM 凭据并且应用程序发现失败，请查看下表以确定失败和修正操作的原因：

**错误代码** | **消息** | **可能的原因** | **建议的操作**
--- | --- | --- | ---
10000 | 无法发现服务器上安装的应用程序。 | 如果在服务器上运行的操作系统既不是 Windows 也不是 Linux，则可能会发生这种情况。 | 只有 Windows 和 Linux 服务器才支持发现已安装的应用程序。
10001 | 无法检索安装了服务器的应用程序。 | 这是由于出现内部错误，因为设备中缺少某些文件。 | 请联系 Microsoft 支持部门。
10002 | 无法检索安装了服务器的应用程序。 | 如果 Azure Migrate 设备中的发现代理运行不正常，则可能会发生这种情况。 | 此问题应在24小时内自动解决。 如果问题仍然存在，请联系 Microsoft 支持部门。
10003 | 无法检索安装了服务器的应用程序。 | 如果发现代理运行不正常，则可能会发生这种情况。 | 此问题应在24小时内自动解决。 如果问题仍然存在，请联系 Microsoft 支持部门。
10004 | 无法发现 < Windows/Linux > 计算机的已安装应用程序。 |  Azure Migrate 设备中未提供用于访问 < Windows/Linux > 计算机的凭据 | 请在 Azure Migrate 设备中添加一个可以访问 < Windows/Linux > 计算机的凭据。
10005 | 无法访问本地服务器。 | 如果为计算机提供的凭据不正确，则可能会发生这种情况。 | 请更新设备中提供的凭据，并确保可以使用凭据访问服务器。
10006 | 无法访问本地服务器。 | 如果在服务器上运行的操作系统既不是 Windows 也不是 Linux，则可能会发生这种情况。 | 只有 Windows 和 Linux 服务器才支持发现已安装的应用程序。
9000 | 无法发现 VM 上安装的应用程序。 | VMware 工具可能未安装或已损坏。 | 在 VM 中安装/重新安装 VMware 工具，并检查其是否正在运行。
9001 | 无法发现 VM 上安装的应用程序。 | VMware 工具可能未安装或已损坏。 | 在 VM 中安装/重新安装 VMware 工具，并检查其是否正在运行。
9002 | 无法发现 VM 上安装的应用程序。 | VMware 工具可能未在运行。 | 在 VM 中安装/重新安装 VMware 工具，并检查其是否正在运行。
9003 | 无法发现服务器上安装的应用程序。 | 如果在服务器上运行的操作系统既不是 Windows 也不是 Linux，则可能会发生这种情况。 | 只有 Windows 和 Linux 服务器才支持发现已安装的应用程序。
9004 | 无法发现服务器上安装的应用程序。 | 如果 VM 已关闭，则可能会发生这种情况。 | 若要发现服务器上已安装的应用程序，请确保已打开 VM。
9005 | 无法发现 VM 上安装的应用程序。 | 如果在 VM 上运行的操作系统既不是 Windows 也不是 Linux，则可能会发生这种情况。 | 只有 Windows 和 Linux 服务器才支持发现已安装的应用程序。
9006 | 无法检索安装了服务器的应用程序。 | 如果发现代理运行不正常，则可能会发生这种情况。 | 此问题应在24小时内自动解决。 如果问题仍然存在，请联系 Microsoft 支持部门。
9007 | 无法检索安装了服务器的应用程序。 | 如果发现代理运行不正常，则可能会发生这种情况。 | 此问题应在24小时内自动解决。 如果问题仍然存在，请联系 Microsoft 支持部门。
9008 | 无法检索安装了服务器的应用程序。 | 此问题可能是由于内部错误引起的。  | 此问题应在24小时内自动解决。 如果问题仍然存在，请联系 Microsoft 支持部门。
9009 | 无法检索安装了服务器的应用程序。 | 如果服务器上的 Windows 用户帐户控制（UAC）设置受到限制并且阻止发现已安装的应用程序，则会出现此问题。 | 搜索服务器上的 "用户帐户控制" 设置，并将服务器上的 UAC 设置配置为位于下面两个级别之一。
9010 | 无法检索安装了服务器的应用程序。 | 此问题可能是由于内部错误引起的。  | 此问题应在24小时内自动解决。 如果问题仍然存在，请联系 Microsoft 支持部门。
8084 | 由于 VMware 错误，无法发现应用程序： <Exception from VMware> | Azure Migrate 设备使用 VMware Api 来发现应用程序。 出现此问题的原因可能是，在尝试发现应用程序时 vCenter Server 引发了异常。 VMware 中的错误消息将显示在 "门户" 中显示的错误消息中。 | 查看[vmware 文档](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)，搜索错误消息并按照 VMware 文章中的故障排除步骤解决问题。 如果仍然无法解决问题，请联系 Microsoft 支持部门。


## <a name="fix-assessment-readiness"></a>修复评估准备情况

解决评估准备情况问题，如下所示：

**问题** | **修补程序**
--- | ---
引导类型不受支持 | Azure 不支持具有 EFI 启动类型的虚拟机。 在运行迁移之前，建议将启动类型转换为 BIOS。 <br/><br/>你可以使用 Azure Migrate 服务器迁移来处理此类 Vm 的迁移。 在迁移过程中，它会将 VM 的启动类型转换为 BIOS。
有条件支持的 Windows 操作系统 | 操作系统已超过其支持的截止日期，需要自定义支持协议（CSA）以[支持 Azure 中的支持](https://aka.ms/WSosstatement)。 请考虑在迁移到 Azure 之前升级。
不受支持的 Windows 操作系统 | Azure 仅支持[所选的 WINDOWS 操作系统版本](https://aka.ms/WSosstatement)。 请考虑在迁移到 Azure 之前升级计算机。
有条件认可的 Linux OS | Azure 予以认可仅[选择 LINUX 操作系统版本](../virtual-machines/linux/endorsed-distros.md)。 请考虑在迁移到 Azure 之前升级计算机。
未经认可的 Linux OS | 计算机可能会在 Azure 中启动，但 Azure 不提供操作系统支持。 在迁移到 Azure 之前，请考虑升级到[认可的 Linux 版本](../virtual-machines/linux/endorsed-distros.md)。
未知操作系统 | 在 vCenter Server 中，将 VM 的操作系统指定为 "其他"。 此行为会阻止 Azure Migrate 验证 VM 的 Azure 准备情况。 在迁移计算机之前，请确保 Azure[支持](https://aka.ms/azureoslist)该操作系统。
不支持的位版本 | 具有32位操作系统的 Vm 可能会在 Azure 中启动，但建议在迁移到 Azure 之前升级到64位。
需要 Microsoft Visual Studio 订阅 | 计算机正在运行 Windows 客户端操作系统，该系统仅通过 Visual Studio 订阅支持。
找不到所需存储性能的 VM | 计算机所需的存储性能（每秒输入/输出操作数 [IOPS] 和吞吐量）超过了 Azure VM 支持。 在迁移之前，减少计算机的存储需求。
找不到所需网络性能的 VM | 计算机所需的网络性能（输入/输出）超出了 Azure VM 支持。 减少计算机的网络要求。
在指定位置找不到 VM | 在迁移之前使用不同目标位置。
一个或多个不合适的磁盘 | 附加到 VM 的一个或多个磁盘不满足 Azure 要求。的<br/><br/> Azure Migrate：服务器评估目前不支持超级 SSD 磁盘，并根据高级托管磁盘（32 TB）的磁盘限制来评估磁盘。<br/><br/> 对于附加到 VM 的每个磁盘，请确保磁盘大小为 < 64 TB （超级 SSD 磁盘支持）。<br/><br/> 如果不是这样，请在迁移到 Azure 之前减小磁盘大小，或者在 Azure 中使用多个磁盘并将它们放在[一起](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)，以获得更高的存储限制。 确保 Azure[托管的虚拟机磁盘](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)支持每个磁盘所需的性能（IOPS 和吞吐量）。
存在一个或多个不合适网络适配器。 | 在迁移之前从计算机中删除未使用的网络适配器。
磁盘计数超过限制 | 在迁移之前从计算机中删除未使用的磁盘。
磁盘大小超过限制 | Azure Migrate：服务器评估目前不支持超级 SSD 磁盘，并基于高级磁盘限制（32 TB）评估磁盘。<br/><br/> 但是，Azure 支持最大为 64 TB 大小的磁盘（超级 SSD 磁盘支持）。 在迁移之前将磁盘缩减为小于 64 TB，或在 Azure 中使用多个磁盘并将它们放在[一起](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)，以获得更高的存储限制。
指定位置中磁盘不可用 | 在迁移之前请确保磁盘已在目标位置。
不可用于指定冗余的磁盘 | 磁盘应使用在评估设置中定义的冗余存储类型（默认为 LRS）。
由于内部错误，无法确定磁盘的适用性 | 请尝试为组创建一个新评估。
未找到具有所需内核和内存的 VM | Azure 找不到合适的 VM 类型。 在迁移之前请减少本地计算机的内存和内核数。
由于内部错误，无法确定 VM 适用性 | 请尝试为组创建一个新评估。
由于出现内部错误，无法确定一个或多个磁盘的适用性 | 请尝试为组创建一个新评估。
由于出现内部错误，无法确定一个或多个网络适配器的适用性 | 请尝试为组创建一个新评估。

## <a name="linux-vms-are-conditionally-ready"></a>Linux Vm 的 "有条件准备"

服务器评估将 Linux Vm 标记为 "有条件就绪"，因为服务器评估中有一个已知的间隔。

- 该间隔使其无法检测本地 Vm 上安装的 Linux 操作系统的次版本。
- 例如，对于 RHEL 6.10，当前服务器评估只检测到 RHEL 6 作为操作系统版本。
-  由于 Azure 予以认可仅特定版本的 Linux，因此 Linux Vm 当前在服务器评估中被标记为有条件准备就绪。
- 可以通过查看[Azure Linux 支持](https://aka.ms/migrate/selfhost/azureendorseddistros)来确定本地 VM 上运行的 Linux 操作系统是否已在 azure 中认可。
-  验证了认可的分发后，可以忽略此警告。

## <a name="azure-skus-exceed-on-premises-sizing"></a>Azure Sku 超出本地大小

基于评估类型，Azure Migrate Server 评估可能会推荐 Azure VM Sku，其中包含的内核和内存比当前本地分配更多：


- VM SKU 建议取决于评估属性。
- 这受您在 "服务器评估" 中执行的评估类型的影响： "*基于性能*" 或 *"本地*"。
- 对于基于性能的评估，服务器评估会考虑本地 Vm 的使用率数据（CPU、内存、磁盘和网络使用率），以确定本地 Vm 的正确目标 VM SKU。 它还在确定有效利用率时增加了舒适的因素。
- 对于本地大小调整，不考虑性能数据，建议根据本地分配使用目标 SKU。

为了说明这会如何影响建议，我们举个例子：

我们有一个具有四个核心和 8 GB 内存的本地 VM，其 CPU 50 使用率为50%，内存使用率为%，指定的舒适系数为1.3。

-  如果评估为 **"本地**"，则建议使用具有4个核心和 8 GB 内存的 AZURE VM SKU。
- 如果评估基于性能，则基于有效的 CPU 和内存利用率（4个内核的 50% * 1.3 = 2.6 个核心和 8 GB 内存的 50% * 1.3 = 5.3 = = =）、四个核心的最便宜 VM SKU （最接近支持的核心数）和 8 GB 内存（最接近支持内存大小）。
- [详细了解](concepts-assessment-calculation.md#sizing)评估大小。

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Azure 磁盘 Sku 大于本地

Azure Migrate Server 评估可能会根据评估类型建议更大的磁盘。
- 服务器评估中的磁盘大小调整取决于两个评估属性：大小调整条件和存储类型。
- 如果大小调整条件是**基于性能**的，并且存储类型设置为 "**自动**"，则在标识目标磁盘类型（标准 HDD、标准 SSD 或高级）时，将考虑磁盘的 IOPS 和吞吐量值。 然后建议使用磁盘类型的磁盘 SKU，建议考虑本地磁盘的大小要求。
- 如果大小调整条件**基于性能**并且存储类型为 "**高级**"，则建议使用 Azure 中的高级磁盘 SKU，根据本地磁盘的 IOPS、吞吐量和大小需求进行推荐。 当大小调整条件为**本地**且存储类型为**标准 HDD**、**标准 SSD**或**高级**时，将使用相同的逻辑来执行磁盘大小调整。

例如，如果你有一个具有 32 GB 内存的本地磁盘，但该磁盘的聚合读取和写入 IOPS 为 800 IOPS，则服务器评估建议使用高级磁盘（由于 IOPS 要求较高），然后建议一个可支持 r 的磁盘 SKUe) IOPS 和大小。 本示例中最接近的匹配项将是 P15（256 GB，1100 IOPS）。 尽管本地磁盘所需的大小为 32 GB，服务器评估建议使用较大的磁盘，因为本地磁盘的 IOPS 要求较高。

## <a name="fix-percentage-of-utilized-core-or-memory-missing"></a>修复：已用核心或内存缺失百分比

当 Azure Migrate 设备无法收集相关本地 Vm 的性能数据时，服务器评估将报告 "PercentageOfCoresUtilizedMissing" 或 "PercentageOfMemoryUtilizedMissing"。

- 如果 Vm 在评估期间关闭，则会发生这种情况。 当 VM 关闭时，它无法收集 VM 的性能数据。
- 如果仅缺少内存计数器，而你要尝试评估 Hyper-v Vm，请检查是否已在这些 Vm 上启用动态内存。 Hyper-v Vm 仅有一个已知问题，在这种情况下，Azure Migrate 设备无法为未启用动态内存的 Vm 收集内存使用率数据。
- 如果缺少任何性能计数器，Azure Migrate 服务器评估将回退到分配的内核和内存，并建议相应的 VM 大小。

## <a name="is-the-vm-os-license-cost-included-in-cost-assessment"></a>VM 操作系统许可证成本是否包括在成本评估中？

Azure Migrate Server 评估目前只考虑 Windows 计算机的操作系统许可成本。 目前未考虑 Linux 计算机的许可成本。

## <a name="performance-history-and-percentile-use"></a>性能历史记录和百分比使用

这些属性仅适用于 Azure Migrate 服务器评估中基于性能的大小调整。

服务器评估不断收集本地计算机的性能数据，并使用它来针对 Azure 中的 VM SKU 和磁盘 SKU 提出建议。 服务器评估将收集此性能数据，如下所示：
- Azure Migrate 设备连续对本地环境进行配置，以便每隔20秒为 VMware Vm 收集实时利用率数据，每隔30秒收集一次 Hyper-v Vm。
- 该设备汇总了20或30秒的样本，每10分钟创建一个数据点。 若要创建单一数据点，设备将从所有20秒和30秒的示例中选择峰值值，然后将其发送到 Azure。
- 在服务器评估中创建评估时，根据性能持续时间和性能历史记录百分位值，确定代表性利用率值。 例如，如果性能历史记录为一周，百分比利用率为95，则 Azure Migrate 按升序排序最后一周的所有10分钟示例点，然后选择第95个百分点作为代表值。
- 95% 的值可以确保忽略任何离群值，如果选择99% 的百分点值，则可能会包含这些离群值。
- 如果要选择该时间段的高峰使用量，而不想错过任何离群值，则应选择99% 百分位以实现百分比利用率。


## <a name="i-cant-find-dependency-visualization-for-azure-government"></a>找不到 Azure 政府的依赖项可视化

Azure Migrate 依赖于依赖项可视化功能服务映射。 由于服务映射当前在 Azure 政府版中不可用，因此此功能在 Azure 政府版中不可用。

## <a name="dependencies-dont-show-after-installing-agents"></a>安装代理后不显示依赖关系

在本地 Vm 上安装了依赖关系可视化代理后，Azure Migrate 通常需要15-30 分钟的时间才能在门户中显示依赖项。 如果等待超过30分钟，请确保 Microsoft Monitoring Agent （MMA）可以连接到 Log Analytics 工作区。

对于 Windows VM：
1. 在控制面板中，启动 MMA。
2. 在 > **Azure Log Analytics （OMS）** **Microsoft Monitoring Agent 属性**中，确保工作区的**状态**为绿色。
3. 如果状态不是绿色，请尝试删除工作区，并再次将其添加到 MMA。

      !["MMA 属性" 对话框](./media/troubleshooting-general/mma-status.png)

对于 Linux Vm，请确保 MMA 和依赖关系代理的安装命令已成功。

## <a name="supported-mma-os"></a>支持的 MMA OS

 查看支持的[Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)和[Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)操作系统。

## <a name="supported-dependency-agent-os"></a>支持的依赖关系代理操作系统

查看支持的[Windows 和 Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)操作系统。

## <a name="dependencies-for-more-than-an-hour"></a>超过一小时的依赖项

尽管 Azure Migrate 允许您返回到上个月的特定日期，但您可以对依赖项进行可视化的最长持续时间为一小时。

例如，你可以使用 "依赖关系映射" 中的 "持续时间" 功能来查看昨天的依赖项，但你可以仅查看一小时的时间段。

但是，可以使用 Azure Monitor 日志来查询更长时间[的依赖项数据](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

## <a name="i-cant-visualize-dependencies-for-groups-with-more-than-10-vms"></a>无法直观显示包含10个以上 Vm 的组的依赖项

在 Azure Migrate 服务器评估中，可以可视化最多包含10个 Vm[的组的依赖项](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 对于更大的组，我们建议将 Vm 拆分为较小的组，以可视化依赖项。

## <a name="machines-show-install-agent-not-view-dependencies"></a>计算机显示 "安装代理" 不是 "查看依赖项"

将启用了依赖关系可视化的计算机迁移到 Azure 后，计算机可能会显示 "安装代理" 操作，而不是 "查看依赖项"，原因如下：


- 迁移到 Azure 后，本地计算机将关闭，并在 Azure 中启动等效 Vm。 这些计算机将获得一个不同的 MAC 地址。
- 计算机还可能具有不同的 IP 地址，具体取决于是否保留了本地 IP 地址。
- 如果 MAC 和 IP 地址与本地不同，Azure Migrate 不会将本地计算机与任何服务映射的依赖关系数据相关联。 在这种情况下，它将显示安装代理的选项，而不是查看依赖项。
- 在测试迁移到 Azure 后，本地计算机仍按预期方式开启。 在 Azure 中启动的等效计算机获取不同的 MAC 地址，并可能获取不同的 IP 地址。 除非您阻止来自这些计算机的传出 Azure Monitor 日志流量，否则 Azure Migrate 不会将本地计算机与任何服务映射依赖关系数据相关联，因此将显示安装代理的选项，而不是查看依赖项。


## <a name="collect-network-traffic-logs-in-portal"></a>收集门户中的网络流量日志

收集日志，如下所示：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 按 F12 开始开发人员工具。 如果需要，请清除 "**在导航上清除条目**" 设置。
3. 选择 "**网络**" 选项卡，开始捕获网络流量：
   - 在 Chrome 中，选择“保留日志”。 记录应自动启动。 红色圆圈表示正在捕获流量。 如果未显示红色圆圈，请选择要开始的黑色圆圈。
   - 在 Microsoft Edge 和 Internet Explorer 中，记录应自动启动。 如果没有，请选择绿色的 "播放" 按钮。
4. 尝试再现该错误。
5. 在记录过程中遇到错误后，停止记录，并保存一份已记录的活动：
   - 在 Chrome 中，右键单击并选择 "**另存为 HAR 内容**"。 此操作将日志压缩并导出为 har 文件。
   - 在 Microsoft Edge 或 Internet Explorer 中，选择 "**导出捕获的流量**" 选项。 此操作将压缩并导出日志。
6. 选择 "**控制台**" 选项卡以检查是否有任何警告或错误。 保存控制台日志：
   - 在 Chrome 中，右键单击控制台日志中的任意位置。 选择 "**另存为**"、"导出" 和 "压缩日志"。
   - 在 Microsoft Edge 或 Internet Explorer 中，右键单击错误并选择 "**全部复制**"。
7. 关闭“开发人员工具”。
