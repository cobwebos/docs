---
title: 排查 Azure Migrate 的设备部署和发现问题
description: 获取有关部署 Azure Migrate 设备和发现计算机的帮助。
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 3f3604205d4aedffdda128ec4a6b895786245e56
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772031"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>排查 Azure Migrate 设备和发现问题

本文将帮助你解决在部署[Azure Migrate](migrate-services-overview.md)设备，并使用设备发现本地计算机时遇到的问题。


## <a name="whats-supported"></a>支持的操作

[查看](migrate-appliance.md)设备支持要求。


## <a name="invalid-ovf-manifest-entry"></a>"OVF 清单条目无效"

如果收到错误消息 "提供的清单文件无效：无效的 OVF 清单条目"，请执行以下操作：

1. 通过检查 Azure Migrate 的设备的哈希值，验证是否已正确下载了设备 .OVA 文件。 [了解详细信息](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware)。 如果哈希值不匹配，请重新下载 .OVA 文件，然后重试部署。
2. 如果部署仍失败，并且你正在使用 VMware vSphere 客户端部署 OVF 文件，请尝试通过 vSphere web 客户端进行部署。 如果部署仍失败，请尝试使用不同的 web 浏览器。
3. 如果你使用的是 vSphere web 客户端并尝试在 vCenter Server 6.5 或6.7 上部署它，请尝试直接在 ESXi 主机上部署 .OVA：
   - 直接连接到 ESXi 主机（而不是 vCenter Server）与 web 客户端（https：//<*主机 IP 地址*>/ui）连接。
   - 在 "**家庭** > **库存**" 中，选择 "**文件** > **部署 OVF 模板**"。 浏览到 .OVA 并完成部署。
4. 如果部署仍然失败，请联系 Azure Migrate 支持部门。

## <a name="cant-connect-to-the-internet"></a>无法连接到 internet

如果设备计算机位于代理后面，则会发生这种情况。

- 如果代理需要，请确保提供身份验证凭据。
- 如果使用基于 URL 的防火墙代理控制出站连接，请将这些 Url 添加到允许列表中：

    - [用于 VMware 评估的 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware)
    - [Hyper-v 评估的 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v)
    - [用于 VMware 无代理迁移的 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware)
    - [基于 VMware 代理的迁移的 URL](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware)
    - [Hyper-v 迁移的 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v)

- 如果使用截取代理连接到 internet，请使用[以下步骤](https://docs.microsoft.com/azure/migrate/concepts-collector)将代理证书导入到设备 VM。

##  <a name="datetime-synchronization-error"></a>日期/时间同步错误

与日期和时间同步相关的错误（802）指示服务器时钟可能与当前时间不同步的时间超过五分钟。 更改收集器 VM 上的时钟时间以匹配当前时间：

1. 在 VM 上打开管理员命令提示符。
2. 若要检查时区，请运行**w32tm/tz**。
3. 若要同步时间，请运行**w32tm/resync**。


## <a name="unabletoconnecttoserver"></a>UnableToConnectToServer

如果出现此连接错误，则可能无法连接到 vCenter Server *Servername*. .com：9443。 错误详细信息表示没有终结点在 https://*servername*： 9443/sdk 上侦听，可以接受消息。

- 检查是否正在运行最新版本的设备。 如果不是，请将该设备升级到[最新版本](https://docs.microsoft.com/azure/migrate/concepts-collector)。
- 如果此问题仍然出现在最新版本中，则设备可能无法解析指定的 vCenter Server 名称，或者指定的端口可能是错误的。 默认情况下，如果未指定端口，收集器会尝试连接到端口号443。

    1. 从设备 Ping *Servername*.com。
    2. 如果步骤1失败，请尝试使用 IP 地址连接到 vCenter 服务器。
    3. 确定要连接到 vCenter Server 的正确端口号。
    4. 验证 vCenter Server 是否已启动并正在运行。


## <a name="error-6005260039-appliance-might-not-be-registered"></a>错误60052/60039：设备可能未注册

- 如果用于注册设备的 Azure 帐户没有足够的权限，则会出现错误 60052 "该设备可能未成功注册到 Azure Migrate 项目"。
    - 请确保用于注册设备的 Azure 用户帐户至少具有订阅的参与者权限。
    - [详细了解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)所需的 Azure 角色和权限。
- 如果注册失败，因为找不到用于注册设备的 Azure Migrate 项目，所以如果注册失败，则会出现 "设备可能未成功注册到 Azure Migrate 项目" 错误60039。
    - 在 Azure 门户中，检查该项目是否存在于资源组中。
    - 如果项目不存在，请在资源组中创建新的 Azure Migrate 项目，并再次注册设备。 [了解如何](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool)创建新项目。

## <a name="error-6003060031-key-vault-management-operation-failed"></a>错误60030/60031： Key Vault 管理操作失败

如果收到错误60030或60031，"Azure Key Vault 管理操作失败"，请执行以下操作：
- 请确保用于注册设备的 Azure 用户帐户至少具有订阅的参与者权限。
- 请确保该帐户具有对错误消息中指定的密钥保管库的访问权限，然后重试该操作。
- 如果问题持续出现，请联系 Microsoft 支持。
- [详细了解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)所需的 Azure 角色和权限。

## <a name="error-60028-discovery-couldnt-be-initiated"></a>错误60028：无法启动发现

错误60028：由于出错，无法启动发现。 对于指定的主机或群集列表，操作失败 "指示无法在错误中列出的主机上启动发现，因为访问或检索 VM 信息时出现问题。 已成功添加其余的主机。

- 使用**add host**选项再次添加错误中列出的主机。
- 如果出现验证错误，请查看更正指南以修复错误，然后再次尝试 "**保存并启动发现**" 选项。

## <a name="error-60025-azure-ad-operation-failed"></a>错误60025： Azure AD 操作失败 
错误60025： "Azure AD 操作失败。 如果用于启动发现的 Azure 用户帐户与用于注册设备的帐户不同，则在创建或更新 Azure AD 应用程序时发生错误。 执行下列操作之一：

- 确保启动发现的用户帐户与用于注册设备的用户帐户相同。
- 为发现操作失败的用户帐户提供 Azure Active Directory 应用程序访问权限。
- 删除先前为 Azure Migrate 项目创建的资源组。 创建另一个资源组以重新开始。
- [了解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)有关 Azure Active Directory 应用程序权限的详细信息。


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>错误50004：无法连接到主机或群集

错误50004： "无法连接到主机或群集，因为无法解析服务器名称。 WinRM 错误代码：如果设备的 Azure DNS 服务无法解析提供的群集或主机名，则可能出现 0x803381B9 "。

- 如果在群集上看到此错误，则为群集 FQDN。
- 对于群集中的主机，你可能还会看到此错误。 这表明设备可以连接到群集，但群集返回的主机名不是 Fqdn。 若要解决此错误，请通过添加 IP 地址和主机名的映射来更新设备上的 hosts 文件：
    1. 以管理员身份打开记事本。
    2. 打开 C:\Windows\System32\Drivers\etc\hosts 文件。
    3. 在行中添加 IP 地址和主机名。 对于出现此错误的每个主机或群集重复此操作。
    4. 保存并关闭 hosts 文件。
    5. 使用设备管理应用检查设备是否可以连接到主机。 30分钟后，应在 Azure 门户中看到这些主机的最新信息。

## <a name="discovered-vms-not-in-portal"></a>未在门户中发现的 Vm

如果发现状态为 "发现正在进行"，但在门户中看不到 Vm，请等待几分钟：
- VMware VM 大约花费15分钟时间。
- 每添加一台主机进行 Hyper-v VM 发现约需两分钟。

如果等待但状态不会更改，请选择 "**服务器**" 选项卡上的 "**刷新**"。这应显示发现的服务器的计数 Azure Migrate：服务器评估和 Azure Migrate：服务器迁移。

如果这不起作用，并且你发现的是 VMware 服务器：

- 验证指定的 vCenter 帐户是否已正确设置权限，并且可以访问至少一个 VM。
- 如果 vCenter 帐户有权在 vCenter VM 文件夹级别授予访问权限，则 Azure Migrate 无法发现 VMware Vm。 [了解](tutorial-assess-vmware.md#set-the-scope-of-discovery)有关范围发现的详细信息。

## <a name="vm-data-not-in-portal"></a>VM 数据不在门户中

如果已发现的 Vm 未显示在门户中，或者 VM 数据已过时，请等待几分钟。 已发现的 VM 配置数据中的更改会在门户中显示，最多需要30分钟。 应用程序数据更改可能需要几个小时才能显示。 如果此时间之后没有任何数据，请尝试刷新，如下所示

1. 在**服务器** > **Azure Migrate 服务器评估**中，选择 "**概述**"。
2. 在 "**管理**" 下，选择**代理运行状况**。
3. 选择 "**刷新代理**"。
4. 等待刷新操作完成。 现在应会看到最新信息。

## <a name="deleted-vms-appear-in-portal"></a>已删除 Vm 显示在门户中

如果删除 Vm 并且它们仍显示在门户中，请等待30分钟。 如果它们仍出现，请按如上所述进行刷新。

## <a name="common-app-discovery-errors"></a>常见的应用发现错误

Azure Migrate 使用 Azure Migrate：服务器评估支持应用程序、角色和功能的发现。 应用发现目前仅适用于 VMware。 [了解](how-to-discover-applications.md)有关设置应用程序发现的要求和步骤的详细信息。

表中汇总了典型的应用发现错误。

**错误** | **原因** | **Action**
--- | --- | --- | ---
10000： "无法发现服务器上安装的应用程序"。 | 如果计算机操作系统不是 Windows 或 Linux，则可能会发生这种情况。 | 仅使用适用于 Windows/Linux 的应用发现。
10001： "无法检索安装了服务器的应用程序"。 | 内部错误-设备中缺少某些文件。 | 请联系 Microsoft 支持。
10002： "无法检索安装了服务器的应用程序"。 | 设备上的发现代理可能不能正常工作。 | 如果问题无法在24小时内自行解决，请联系支持人员。
10003 "无法检索到服务器上安装的应用程序"。 | 设备上的发现代理可能不能正常工作。 | 如果问题无法在24小时内自行解决，请联系支持人员。
10004： "找不到 < Windows/Linux > 计算机的已安装应用程序。" |  设备不提供访问 < Windows/Linux > 计算机的凭据。| 向设备添加可访问 < Windows/Linux > 计算机的凭据。
10005： "无法访问本地服务器"。 | 访问凭据可能是错误的。 | 更新设备凭据确保你可以使用这些凭据访问相关计算机。 
10006： "无法访问本地服务器"。 | 如果计算机操作系统不是 Windows 或 Linux，则可能会发生这种情况。|  仅使用适用于 Windows/Linux 的应用发现。
9000/9001/9002： "无法发现服务器上安装的应用程序"。 | VMware 工具可能未安装或已损坏。 | 在相关计算机上安装/重新安装 VMware 工具，并检查其是否正在运行。
9003：无法发现服务器上安装的应用程序 "。 | 如果计算机操作系统不是 Windows 或 Linux，则可能会发生这种情况。 | 仅使用适用于 Windows/Linux 的应用发现。
9004： "无法发现服务器上安装的应用程序"。 | 如果 VM 已关闭，则可能会发生这种情况。 | 对于发现，请确保 VM 已打开。
9005： "无法发现 VM 上安装的应用程序。 | 如果计算机操作系统不是 Windows 或 Linux，则可能会发生这种情况。 | 仅使用适用于 Windows/Linux 的应用发现。
9006/9007： "无法检索安装了服务器的应用程序"。 | 设备上的发现代理可能不能正常工作。 | 如果问题无法在24小时内自行解决，请联系支持人员。
9008： "无法检索安装了服务器的应用程序"。 | 可能是内部错误。  | Tf 此问题不会在24小时内自行解决，请联系支持人员。
9009： "无法检索安装了服务器的应用程序"。 | 如果服务器上的 Windows 用户帐户控制（UAC）设置受到限制，则可能会发生这种情况，并阻止发现已安装的应用程序。 | 搜索服务器上的 "用户帐户控制" 设置，并将服务器上的 UAC 设置配置为下面两个级别之一。
9010： "无法检索安装了服务器的应用程序"。 | 可能是内部错误。  | Tf 此问题不会在24小时内自行解决，请联系支持人员。
8084： "由于 VMware 错误，无法发现应用程序： <Exception from VMware>" | Azure Migrate 设备使用 VMware Api 来发现应用程序。 如果在尝试发现应用程序时 vCenter Server 引发异常，则可能出现此问题。 VMware 中的错误消息将显示在 "门户" 中显示的错误消息中。 | 在[VMware 文档](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)中搜索该消息，然后按照步骤进行修复。 如果无法解决问题，请联系 Microsoft 支持部门。
9012： "无法发现服务器上安装的应用程序" | 此问题可能是由于内部错误引起的。  | 如果问题无法在24小时内自行解决，请联系支持人员。
9013： "无法发现服务器上安装的应用程序" | 每次登录到 VM 时都会创建一个新的临时配置文件。  | 确保不为提供的来宾用户创建临时配置文件。



## <a name="next-steps"></a>后续步骤
为[VMware](how-to-set-up-appliance-vmware.md)、 [hyper-v](how-to-set-up-appliance-hyper-v.md)或[物理服务器](how-to-set-up-appliance-physical.md)设置设备。
