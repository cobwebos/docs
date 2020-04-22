---
title: 排除 Azure 迁移设备部署和发现故障
description: 获取有关部署 Azure 迁移设备和发现计算机的帮助。
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 6cb83a87f2e96eb62696e5d92095ef2b8d7c7def
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677329"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>排除 Azure 迁移设备和发现故障

本文可帮助您在部署[Azure 迁移](migrate-services-overview.md)设备以及使用该设备发现本地计算机时解决问题。


## <a name="whats-supported"></a>支持的操作

[查看](migrate-appliance.md)设备支持要求。


## <a name="invalid-ovf-manifest-entry"></a>"无效 OVF 清单条目"

如果您收到错误"提供的清单文件无效：OVF清单条目无效"，则执行以下操作：

1. 通过检查 Azure 迁移设备 OVA 文件是否正确下载。 [了解详细信息](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware)。 如果哈希值不匹配，请再次下载 OVA 文件并重试部署。
2. 如果部署仍然失败，并且您正在使用 VMware vSphere 客户端来部署 OVF 文件，请尝试通过 vSphere Web 客户端进行部署。 如果部署仍然失败，请尝试使用其他 Web 浏览器。
3. 如果您使用的是 vSphere Web 客户端并尝试在 vCenter Server 6.5 或 6.7 上部署它，请尝试直接在 ESXi 主机上部署 OVA：
   - 直接与 Web 客户端（https：//<*主机 IP 地址*>/ui）连接到 ESXi 主机（而不是 vCenter 服务器）。
   - 在 **"家庭** > **库存"** 中，选择 **"文件** > **部署 OVF"模板**。 浏览到 OVA 并完成部署。
4. 如果部署仍然失败，请联系 Azure Migrate 支持部门。

## <a name="cant-connect-to-the-internet"></a>无法连接到互联网

如果设备机器位于代理后面，则可能发生此情况。

- 如果代理需要授权凭据，请确保提供授权凭据。
- 如果使用基于 URL 的防火墙代理来控制出站连接，请[将这些 URL](migrate-appliance.md#url-access)添加到允许列表中。
- 如果您使用拦截代理连接到互联网，请使用[以下步骤](https://docs.microsoft.com/azure/migrate/concepts-collector)将代理证书导入设备 VM。

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>无法从设备 Web 应用登录到 Azure

如果您使用不正确的 Azure 帐户登录到 Azure，则会出现"对不起，但我们登录时遇到问题"错误。 出现此错误有几个原因：

- 如果您为公共云登录到设备 Web 应用程序，请使用政府云门户的用户帐户凭据。
- 如果使用私有云门户的用户帐户凭据登录政府云的设备 Web 应用程序。

确保您使用的凭据正确。

##  <a name="datetime-synchronization-error"></a>日期/时间同步错误

有关日期和时间同步 （802） 的错误指示服务器时钟可能与当前时间不同步超过五分钟。 更改收集器 VM 上的时钟时间以匹配当前时间：

1. 在 VM 上打开管理员命令提示符。
2. 要检查时区，请运行**w32tm /tz**。
3. 要同步时间，运行**w32tm /重新同步**。


## <a name="unabletoconnecttoserver"></a>"无法连接服务器"

如果收到此连接错误，可能无法连接到 vCenter*服务器名称*.com：9443。 错误详细信息指示没有终结点侦听`https://\*servername*.com:9443/sdk`，可以接受该消息。

- 检查是否运行最新版本的设备。 如果不是，请将设备升级到[最新版本](https://docs.microsoft.com/azure/migrate/concepts-collector)。
- 如果最新版本中仍出现问题，则设备可能无法解析指定的 vCenter Server 名称，或者指定的端口可能是错误的。 默认情况下，如果未指定端口，收集器将尝试连接到端口号 443。

    1. 从设备*ping 服务器名称*.com。
    2. 如果步骤 1 失败，请尝试使用 IP 地址连接到 vCenter 服务器。
    3. 标识要连接到 vCenter 服务器的正确端口号。
    4. 验证 vCenter 服务器是否启动并运行。


## <a name="error-6005260039-appliance-might-not-be-registered"></a>错误 60052/60039：设备可能未注册

- 错误 60052，"如果用于注册设备的 Azure 帐户的权限不足，则发生"设备可能无法成功注册到 Azure 迁移项目"的情况。
    - 确保用于注册设备的 Azure 用户帐户至少具有订阅的"参与者"权限。
    - [详细了解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)所需的 Azure 角色和权限。
- 错误 60039，"如果注册失败，则可能会发生"设备可能无法成功注册到 Azure 迁移项目"，因为找不到用于注册设备的 Azure 迁移项目。
    - 在 Azure 门户中，检查项目是否存在在资源组中。
    - 如果项目不存在，请在资源组中创建新的 Azure 迁移项目，然后再次注册设备。 [了解如何](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool)创建新项目。

## <a name="error-6003060031-key-vault-management-operation-failed"></a>错误 60030/60031：密钥保管库管理操作失败

如果收到错误 60030 或 60031，"Azure 密钥保管库管理操作失败"，则执行以下操作：
- 确保用于注册设备的 Azure 用户帐户至少具有订阅的"参与者"权限。
- 确保帐户有权访问错误消息中指定的密钥保管库，然后重试该操作。
- 如果问题持续出现，请联系 Microsoft 支持。
- [详细了解所需的](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)Azure 角色和权限。

## <a name="error-60028-discovery-couldnt-be-initiated"></a>错误 60028：无法启动发现

错误 60028："由于错误，无法启动发现。 指定主机或群集列表的操作失败"表示由于访问或检索 VM 信息时出现问题，无法在错误中列出的主机上启动发现。 已成功添加其余主机。

- 使用 **"添加主机"** 选项再次添加错误中列出的主机。
- 如果存在验证错误，请查看修复指南以修复错误，然后重试 **"保存"并再次启动发现**选项。

## <a name="error-60025-azure-ad-operation-failed"></a>错误 60025：Azure AD 操作失败 
错误 60025："Azure AD 操作失败。 创建或更新 Azure AD 应用程序时发生错误"，当用于启动发现的 Azure 用户帐户与用于注册设备的帐户不同时发生。 执行下列操作之一：

- 确保启动发现的用户帐户与用于注册设备的用户帐户相同。
- 向发现操作失败的用户帐户提供 Azure 活动目录应用程序访问权限。
- 删除以前为 Azure 迁移项目创建的资源组。 创建另一个资源组以重新开始。
- [了解有关](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)Azure 活动目录应用程序权限的更多内容。


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>错误 50004： 无法连接到主机或群集

错误 50004："无法连接到主机或群集，因为无法解析服务器名称。 如果设备的 Azure DNS 服务无法解决您提供的群集或主机名，则可能发生 WinRM 错误代码：0x803381B9"。

- 如果在群集、群集 FQDN 上看到此错误。
- 您可能还会看到群集中的主机出现此错误。 这表示设备可以连接到群集，但群集返回不是 FQDN 的主机名。 要解决此错误，请通过添加 IP 地址和主机名的映射来更新设备上的主机文件：
    1. 以管理员身份打开记事本。
    2. 打开 C：[视窗]系统32\驱动程序\等\主机文件。
    3. 在行中添加 IP 地址和主机名。 对看到此错误的每个主机或群集重复上述操作。
    4. 保存然后关闭 hosts 文件。
    5. 使用设备管理应用检查设备是否可以连接到主机。 30 分钟后，您应该在 Azure 门户中看到这些主机的最新信息。

## <a name="discovered-vms-not-in-portal"></a>未在门户中发现的 VM

如果发现状态为"正在发现"，但尚未在门户中看到 VM，请等待几分钟：
- VMware VM 大约需要 15 分钟。
- 对于 Hyper-V VM 发现，每个添加的主机大约需要两分钟。

如果等待且状态不变，请在"**服务器"** 选项卡上选择 **"刷新**"。这将显示 Azure 迁移中发现的服务器的计数：服务器评估和 Azure 迁移：服务器迁移。

如果这不起作用，并且您正在发现 VMware 服务器：

- 验证您指定的 vCenter 帐户是否具有正确设置的权限，并有权访问至少一个 VM。
- 如果 vCenter 帐户具有 vCenter VM 文件夹级别授予的访问权限，则 Azure 迁移无法发现 VMware VM。 [了解有关](set-discovery-scope.md)范围发现数的更多。

## <a name="vm-data-not-in-portal"></a>不在门户中的 VM 数据

如果发现的 VM 未显示在门户中，或者 VM 数据已过期，请等待几分钟。 发现 VM 配置数据的更改最多需要 30 分钟才能显示在门户中。 应用程序数据更改可能需要几个小时才能显示。 如果在此时间之后没有数据，请尝试刷新，如下所示

1. 在**服务器** > **Azure 迁移服务器评估中**，选择 **"概述**"。
2. 在 **"管理"** 下，选择 **"代理运行状况**"。
3. 选择**刷新代理**。
4. 等待刷新操作完成。 您现在应该看到最新信息。

## <a name="deleted-vms-appear-in-portal"></a>已删除的 VM 显示在门户中

如果删除 VM 并且它们仍显示在门户中，请等待 30 分钟。 如果它们仍然出现，请刷新上述内容。

## <a name="common-app-discovery-errors"></a>常见应用发现错误

Azure 迁移支持使用 Azure 迁移：服务器评估发现应用程序、角色和功能。 当前仅支持 VMware 应用发现。 [详细了解](how-to-discover-applications.md)设置应用发现的要求和步骤。

典型的应用发现错误汇总在表中。 

**错误** | **原因** | **操作**
--- | --- | --- | ---
10000："无法发现服务器上安装的应用程序"。 | 如果计算机操作系统不是 Windows 或 Linux，则可能发生此情况。 | 仅对 Windows/Linux 使用应用发现。
10001："无法检索安装的服务器的应用程序"。 | 内部错误 - 设备中缺少一些文件。 | 请联系 Microsoft 支持。
10002："无法检索安装的服务器的应用程序"。 | 产品上的发现代理可能无法正常工作。 | 如果问题在 24 小时内无法自行解决，请与支持人员联系。
10003 "无法检索安装的服务器的应用程序"。 | 产品上的发现代理可能无法正常工作。 | 如果问题在 24 小时内无法自行解决，请与支持人员联系。
10004："无法发现 <Windows/Linux>计算机的已安装应用程序。 |  设备中未提供访问 windows/Linux <>计算机的凭据。| 向有权访问<Windows/Linux>计算机的设备添加凭据。
10005："无法访问本地服务器"。 | 访问凭据可能是错误的。 | 更新设备凭据请确保可以使用它们访问相关计算机。 
10006："无法访问本地服务器"。 | 如果计算机操作系统不是 Windows 或 Linux，则可能发生此情况。|  仅对 Windows/Linux 使用应用发现。
10007："无法处理检索的元数据" | 此内部错误在尝试反序列化 JSON 时发生 | 请与微软支持部门联系，了解解决方案
9000/9001/9002："无法发现服务器上安装的应用程序"。 | VMware 工具可能未安装或已损坏。 | 在相关计算机上安装/重新安装 VMware 工具，并检查其是否正在运行。
9003：无法发现服务器上安装的应用程序"。 | 如果计算机操作系统不是 Windows 或 Linux，则可能发生此情况。 | 仅对 Windows/Linux 使用应用发现。
9004："无法发现服务器上安装的应用程序"。 | 如果关闭 VM 电源，则可能发生此情况。 | 要发现，请确保 VM 处于打开点。
9005："无法发现安装在 VM 上的应用程序。 | 如果计算机操作系统不是 Windows 或 Linux，则可能发生此情况。 | 仅对 Windows/Linux 使用应用发现。
9006/9007："无法检索安装的服务器的应用程序"。 | 产品上的发现代理可能无法正常工作。 | 如果问题在 24 小时内无法自行解决，请与支持人员联系。
9008："无法检索安装的服务器的应用程序"。 | 可能是内部错误。  | Tf 问题无法在 24 小时内自行解决，请联系支持人员。
9009："无法检索安装的服务器的应用程序"。 | 如果服务器上的 Windows 用户帐户控制 （UAC） 设置受到限制，并且阻止发现已安装的应用程序，则可能发生。 | 在服务器上搜索"用户帐户控制"设置，并将服务器上的 UAC 设置配置为较低的两个级别之一。
9010："无法检索安装的服务器的应用程序"。 | 可能是内部错误。  | Tf 问题无法在 24 小时内自行解决，请联系支持人员。
9011："在来宾 VM 上找不到从来宾下载的文件" | 由于内部错误，可能会出现此问题。 | 问题应在 24 小时内自动解决。 如果问题仍然存在，请联系 Microsoft 支持。
9012："结果文件内容为空。 | 由于内部错误，可能会出现此问题。 | 问题应在 24 小时内自动解决。 如果问题仍然存在，请联系 Microsoft 支持。
9013："每次登录 VMware VM 都会创建新的临时配置文件" | 为每次登录到 VM 创建新的临时配置文件 | 确保来宾 VM 凭据中提供的用户名为 UPN 格式。
9015："由于 vCenter 上的权限不足，无法连接到 VMware VM" | vCenter 用户帐户上未启用来宾操作角色 | 确保在 vCenter 用户帐户上启用了来宾操作角色。
9016："由于来宾操作代理数据已失，无法连接到 VMware VM" | VMware 工具安装不正确或不是最新的。 | 确保 VMware 工具安装正确且最新。
9017："在 VM 上找不到包含已发现的元数据的文件。 | 由于内部错误，可能会出现此问题。 | 有关解决方案，请与 Microsoft 支持部门联系。
9018："来宾 VM 中未安装 PowerShell。 | 电源外壳在来宾 VM 中不可用。 | 在来宾 VM 中安装 PowerShell。
9019："由于来宾 VM 操作失败而无法发现" | VM 上的 VMware 来宾操作失败。 | 确保 VM 凭据有效，来宾 VM 凭据中提供的用户名为 UPN 格式。
9020："文件创建权限被拒绝。 | 与用户或组策略关联的角色正在限制用户在文件夹中创建文件 | 检查提供的来宾用户是否具有文件夹中的文件创建权限。 有关文件夹的名称，请参阅服务器评估中**的通知**。
9021："在文件夹系统临时路径中拒绝文件创建权限。 | 不支持 VM 上的 VMware 工具版本 | 将 VMware 工具版本升级到 10.2.0 以上。
9022："获取 WMI 对象访问被拒绝。 | 与用户或组策略关联的角色正在限制用户访问 WMI 对象。 | 请联系 Microsoft 支持。
9023："系统根环境变量值为空。 | 不知道 | 请联系 Microsoft 支持。
9024："TEMP 环境变量值为空。 | 不知道 | 请联系 Microsoft 支持。
9025："电源外壳在来宾 VM 中已损坏。 | 不知道 | 在来宾 VM 中重新安装 PowerShell，并检查是否可以在来宾 VM 上运行 PowerShell。
8084："由于 VMware 错误，无法发现应用程序：" <Exception from VMware> | Azure 迁移设备使用 VMware API 来发现应用程序。 如果 vCenter Server 在尝试发现应用程序时引发异常，则可能发生此问题。 来自 VMware 的错误消息显示在门户中显示的错误消息中。 | 在[VMware 文档中](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)搜索消息，然后按照修复步骤进行操作。 如果无法修复，请与 Microsoft 支持部门联系。



## <a name="next-steps"></a>后续步骤
为[VMware、Hyper-V](how-to-set-up-appliance-vmware.md)或[物理服务器](how-to-set-up-appliance-physical.md)设置设备。 [Hyper-V](how-to-set-up-appliance-hyper-v.md)
