---
title: 排查 Azure Migrate 的设备部署和发现问题
description: 获取有关部署 Azure Migrate 设备和发现计算机的帮助。
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: b2a01e816c0f0569c207aa65e5027b935210e3b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331771"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>排查 Azure Migrate 设备和发现问题

本文将帮助你解决在部署 [Azure Migrate](migrate-services-overview.md) 设备，并使用设备发现本地计算机时遇到的问题。


## <a name="whats-supported"></a>支持的功能

[查看](migrate-appliance.md) 设备支持要求。


## <a name="invalid-ovf-manifest-entry"></a>"OVF 清单条目无效"

如果收到错误消息 "提供的清单文件无效：无效的 OVF 清单条目"，请执行以下操作：

1. 通过检查 Azure Migrate 的设备的哈希值，验证是否已正确下载了设备 .OVA 文件。 [了解详细信息](./tutorial-prepare-vmware.md)。 如果哈希值不匹配，请重新下载 .OVA 文件，然后重试部署。
2. 如果部署仍失败，并且你正在使用 VMware vSphere 客户端部署 OVF 文件，请尝试通过 vSphere web 客户端进行部署。 如果部署仍失败，请尝试使用不同的 web 浏览器。
3. 如果你使用的是 vSphere web 客户端并尝试在 vCenter Server 6.5 或6.7 上部署它，请尝试直接在 ESXi 主机上部署 .OVA：
   - 直接连接到 ESXi 主机 (而不是 vCenter Server) 与 web 客户端 (<>) *主机 IP 地址* 。
   - 在 "**主**  >  **目录**" 中，选择 "**文件**" "  >  **部署 OVF 模板**"。 浏览到 .OVA 并完成部署。
4. 如果部署仍然失败，请联系 Azure Migrate 支持部门。

## <a name="cant-connect-to-the-internet"></a>无法连接到 internet

如果设备计算机位于代理后面，则会发生这种情况。

- 请确保提供代理所需的授权凭据。
- 如果使用基于 URL 的防火墙代理控制出站连接，请将 [这些 url](migrate-appliance.md#url-access) 添加到允许列表中。
- 如果使用截取代理连接到 internet，请使用 [以下步骤](./migrate-appliance.md)将代理证书导入到设备 VM。

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>无法从设备 web 应用登录 Azure

如果使用不正确的 Azure 帐户登录到 Azure，则会显示错误 "抱歉，在登录时遇到问题"。 发生此错误的原因有以下几个：

- 如果使用政府云门户的用户帐户凭据登录到公共云的设备 web 应用程序。
- 如果使用私有云门户的用户帐户凭据登录到政府云的设备 web 应用程序。

确保使用正确的凭据。

##  <a name="datetime-synchronization-error"></a>日期/时间同步错误

有关日期和时间同步 (802) 的错误指示服务器时钟可能与当前时间不同步的时间超过五分钟。 更改收集器 VM 上的时钟时间以匹配当前时间：

1. 在 VM 上打开管理员命令提示符。
2. 若要检查时区，请运行 **w32tm/tz**。
3. 若要同步时间，请运行 **w32tm/resync**。


## <a name="unabletoconnecttoserver"></a>UnableToConnectToServer

如果出现此连接错误，则可能无法连接到 vCenter Server *Servername*. .com：9443。 错误详细信息指示没有侦听方 `https://\*servername*.com:9443/sdk` 可以接受消息的终结点。

- 检查是否正在运行最新版本的设备。 如果不是，请将该设备升级到 [最新版本](./migrate-appliance.md)。
- 如果此问题仍然出现在最新版本中，则设备可能无法解析指定的 vCenter Server 名称，或者指定的端口可能是错误的。 默认情况下，如果未指定端口，收集器会尝试连接到端口号443。

    1. 从设备 Ping *Servername*.com。
    2. 如果步骤1失败，请尝试使用 IP 地址连接到 vCenter 服务器。
    3. 确定要连接到 vCenter Server 的正确端口号。
    4. 验证 vCenter Server 是否已启动并正在运行。


## <a name="error-6005260039-appliance-might-not-be-registered"></a>错误60052/60039：设备可能未注册

- 如果用于注册设备的 Azure 帐户没有足够的权限，则会出现错误 60052 "该设备可能未成功注册到 Azure Migrate 项目"。
    - 请确保用于注册设备的 Azure 用户帐户至少具有订阅的参与者权限。
    - [详细了解](./migrate-appliance.md#appliance---vmware) 所需的 Azure 角色和权限。
- 如果注册失败，因为找不到用于注册设备的 Azure Migrate 项目，所以如果注册失败，则会出现 "设备可能未成功注册到 Azure Migrate 项目" 错误60039。
    - 在 Azure 门户中，检查该项目是否存在于资源组中。
    - 如果项目不存在，请在资源组中创建新的 Azure Migrate 项目，并再次注册设备。 [了解如何](./how-to-add-tool-first-time.md#create-a-project-and-add-a-tool) 创建新项目。

## <a name="error-6003060031-key-vault-management-operation-failed"></a>错误60030/60031： Key Vault 管理操作失败

如果收到错误60030或60031，"Azure Key Vault 管理操作失败"，请执行以下操作：
- 请确保用于注册设备的 Azure 用户帐户至少具有订阅的参与者权限。
- 请确保该帐户具有对错误消息中指定的密钥保管库的访问权限，然后重试该操作。
- 如果问题持续出现，请联系 Microsoft 支持。
- [详细了解](./migrate-appliance.md#appliance---vmware) 所需的 Azure 角色和权限。

## <a name="error-60028-discovery-couldnt-be-initiated"></a>错误60028：无法启动发现

错误60028：由于出错，无法启动发现。 对于指定的主机或群集列表，操作失败 "指示无法在错误中列出的主机上启动发现，因为访问或检索 VM 信息时出现问题。 已成功添加其余的主机。

- 使用 **add host** 选项再次添加错误中列出的主机。
- 如果出现验证错误，请查看更正指南以修复错误，然后再次尝试 " **保存并启动发现** " 选项。

## <a name="error-60025-azure-ad-operation-failed"></a>错误60025： Azure AD 操作失败 
错误60025： "Azure AD 操作失败。 如果用于启动发现的 Azure 用户帐户与用于注册设备的帐户不同，则在创建或更新 Azure AD 应用程序时发生错误。 执行下列操作之一：

- 确保启动发现的用户帐户与用于注册设备的用户帐户相同。
- 为发现操作失败的用户帐户提供 Azure Active Directory 应用程序访问权限。
- 删除先前为 Azure Migrate 项目创建的资源组。 创建另一个资源组以重新开始。
- [了解](./migrate-appliance.md#appliance---vmware) 有关 Azure Active Directory 应用程序权限的详细信息。


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>错误50004：无法连接到主机或群集

错误50004： "无法连接到主机或群集，因为无法解析服务器名称。 WinRM 错误代码：如果设备的 Azure DNS 服务无法解析提供的群集或主机名，则可能出现 0x803381B9 "。

- 如果在群集上看到此错误，则为群集 FQDN。
- 对于群集中的主机，你可能还会看到此错误。 这表明设备可以连接到群集，但群集返回的主机名不是 Fqdn。 若要解决此错误，请通过添加 IP 地址和主机名的映射来更新设备上的 hosts 文件：
    1. 以管理员身份打开记事本。
    2. 打开 C:\Windows\System32\Drivers\etc\hosts 文件。
    3. 在行中添加 IP 地址和主机名。 对于出现此错误的每个主机或群集重复此操作。
    4. 保存然后关闭 hosts 文件。
    5. 使用设备管理应用检查设备是否可以连接到主机。 30分钟后，应在 Azure 门户中看到这些主机的最新信息。


## <a name="error-60001-unable-to-connect-to-server"></a>错误60001：无法连接到服务器 

- 确保从设备到服务器的连接
- 如果它是 linux 服务器，请使用以下步骤确保启用基于密码的身份验证：
    1. 登录到 linux 计算机，并使用命令 "vi/etc/ssh/sshd_config" 打开 ssh 配置文件
    2. 将 "PasswordAuthentication" 选项设置为 "是"。 保存文件。
    3. 通过运行 "service sshd restart" 重新启动 ssh 服务
- 如果它是 windows 服务器，请确保端口5985处于打开状态以允许进行远程 WMI 调用。
- 如果要发现 GCP linux 服务器并使用根用户，请使用以下命令更改根登录的默认设置
    1. 登录到 linux 计算机，并使用命令 "vi/etc/ssh/sshd_config" 打开 ssh 配置文件
    2. 将 "PermitRootLogin" 选项设置为 "是"。
    3. 通过运行 "service sshd restart" 重新启动 ssh 服务

## <a name="error-no-suitable-authentication-method-found"></a>错误：找不到合适的身份验证方法

使用以下步骤确保已在 linux 服务器上启用基于密码的身份验证：
    1. 登录到 linux 计算机，并使用命令 "vi/etc/ssh/sshd_config" 打开 ssh 配置文件
    2. 将 "PasswordAuthentication" 选项设置为 "是"。 保存文件。
    3. 通过运行 "service sshd restart" 重新启动 ssh 服务


## <a name="discovered-vms-not-in-portal"></a>未在门户中发现的 Vm

如果发现状态为 "发现正在进行"，但在门户中看不到 Vm，请等待几分钟：
- VMware VM 大约花费15分钟时间。
- 每添加一台主机进行 Hyper-v VM 发现约需两分钟。

如果等待但状态不会更改，请选择 "**服务器**" 选项卡上的 "**刷新**"。这应显示发现的服务器的计数 Azure Migrate：服务器评估和 Azure Migrate：服务器迁移。

如果这不起作用，并且你发现的是 VMware 服务器：

- 验证指定的 vCenter 帐户是否已正确设置权限，并且可以访问至少一个 VM。
- 如果 vCenter 帐户具有在 vCenter VM 文件夹级别授予访问权限，则 Azure Migrate 无法发现 VMware Vm。 [了解](set-discovery-scope.md) 有关范围发现的详细信息。

## <a name="vm-data-not-in-portal"></a>VM 数据不在门户中

如果已发现的 Vm 未显示在门户中，或者 VM 数据已过时，请等待几分钟。 已发现的 VM 配置数据中的更改会在门户中显示，最多需要30分钟。 应用程序数据更改可能需要几个小时才能显示。 如果此时间之后没有任何数据，请尝试刷新，如下所示

1. 在**服务器**  >  **Azure Migrate 服务器评估**中，选择 "**概述**"。
2. 在 " **管理**" 下，选择 **代理运行状况**。
3. 选择 " **刷新代理**"。
4. 等待刷新操作完成。 现在应会看到最新信息。

## <a name="deleted-vms-appear-in-portal"></a>已删除 Vm 显示在门户中

如果删除 Vm 并且它们仍显示在门户中，请等待30分钟。 如果它们仍出现，请按如上所述进行刷新。

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>我看不到物理服务器上的某些网络适配器的性能数据

如果物理服务器启用了 Hyper-v 虚拟化，则会发生这种情况。 由于产品缺口，会在发现的虚拟网络适配器上捕获网络吞吐量。

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>错误：上传的文件不是预期格式
某些工具具有区域设置，这些区域设置使用分号作为分隔符来创建 CSV 文件。 请更改设置以确保分隔符为逗号。

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>我导入了 CSV，但系统显示“正在发现”
如果 CSV 上传由于验证失败而失败，则会显示此状态。 尝试再次导入 CSV。 你可以下载上一次上传的错误报告，并遵循该文件中的修复指南来修复错误。 可以从“发现计算机”页的“导入详细信息”部分下载错误报告。

## <a name="do-not-see-application-details-even-after-updating-guest-credentials"></a>即使在更新来宾凭据后，也不会看到应用程序详细信息
应用程序发现每24小时运行一次。 如果希望立即查看详细信息，请按如下所示进行刷新。 这可能需要几分钟时间，具体取决于 no。 已发现的虚拟机。

1. 在**服务器**  >  **Azure Migrate 服务器评估**中，选择 "**概述**"。
2. 在 " **管理**" 下，选择 **代理运行状况**。
3. 选择 " **刷新代理**"。
4. 等待刷新操作完成。 现在应会看到最新信息。

## <a name="unable-to-export-application-inventory"></a>无法导出应用程序清单
确保从门户下载清单的用户对订阅具有参与者权限。

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>找不到合适的身份验证方法来完成身份验证 (publickey) 
基于密钥的身份验证将不起作用，请使用密码身份验证。

## <a name="common-app-discovery-errors"></a>常见的应用发现错误

Azure Migrate 使用 Azure Migrate：服务器评估支持应用程序、角色和功能的发现。 应用发现目前仅适用于 VMware。 [了解](how-to-discover-applications.md) 有关设置应用程序发现的要求和步骤的详细信息。

表中汇总了典型的应用发现错误。 

**错误** | **原因** | **操作**
--- | --- | ---
9000：无法检测到 VMware 工具状态。     |   VMWare 工具可能未安装或已损坏。    |   确保已在 VM 上安装并运行 VMware 工具。
9001：未安装 VMware 工具。     |   VMWare 工具可能未安装或已损坏。    |   确保已在 VM 上安装并运行 VMware 工具。
9002： VMware 工具未运行。   |   VMWare 工具可能未安装或已损坏。    |   确保已在 VM 上安装并运行 VMware 工具。
9003：来宾 VM 发现不支持操作系统类型。    |   服务器上运行的操作系统既不是 Windows 也不是 Linux。    |   受支持的操作系统类型仅为 Windows 和 Linux。 如果服务器确实为 Windows 或 Linux，请检查 vCenter Server 中指定的操作系统类型。
9004： VM 未在运行。     |   VM 已关机。  |   确保 VM 已通电。
9005：来宾 VM 发现不支持操作系统类型。    |   来宾 VM 发现不支持的操作系统类型。     |   受支持的操作系统类型仅为 Windows 和 Linux。
9006：从来宾下载元数据文件的 URL 为空。     |   如果发现代理未按预期工作，则可能会发生这种情况。    |   此问题应自动解决 in24 小时数。 如果问题持续出现，请联系 Microsoft 支持。
9007：找不到在来宾 VM 中运行发现任务的进程。   |   如果发现代理运行不正常，则可能会发生这种情况。   |   此问题应在24小时内自动解决。 如果问题持续出现，请联系 Microsoft 支持。
9008：无法检索来宾 VM 进程状态。   |   此问题可能是由于内部错误引起的。   |   此问题应在24小时内自动解决。 如果问题持续出现，请联系 Microsoft 支持。
9009： Windows UAC 阻止了在服务器上执行发现任务。  |   Windows 用户帐户控制 (服务器上的 UAC) 设置受到限制，并会阻止发现已安装的应用程序。  |   在服务器上的 "用户帐户控制" 设置中，将 UAC 设置配置为一个较低的两个级别之一。
9010： VM 已关闭电源。     |   VM 已关机。  |   确保 VM 已通电。
9011：在来宾 VM 文件系统中找不到发现的元数据文件。    |   此问题可能是由于内部错误引起的。   |   此问题应在24小时内自动解决。 如果问题持续出现，请联系 Microsoft 支持。
9012：发现的元数据文件为空。     |   此问题可能是由于内部错误引起的。   |   此问题应在24小时内自动解决。 如果问题持续出现，请联系 Microsoft 支持。
9013：为每个登录名创建了一个新的临时配置文件。    |   为 VMware VM 的每个登录名都创建一个新的临时配置文件。    |   若要解决此问题，请联系 Microsoft 支持部门。
9014：无法从来宾 VM 文件系统中检索元数据。     |   无连接到 ESXi 主机    |   确保设备可以连接到运行 VM 的 ESXi 主机上的端口443
9015： vCenter 用户帐户上未启用来宾操作角色   |   VCenter 用户帐户上未启用来宾操作角色。   |   确保在 vCenter 用户帐户上启用了来宾操作角色。
9016：无法发现，因为来宾操作代理已过时。   |   Vmware 工具未正确安装或不是最新版本。    |   请确保 VMware 工具安装正确并处于最新状态。
9017：在 VM 上找不到带有发现的元数据的文件。  |   此问题可能是由于内部错误引起的。   |   若要解决此问题，请联系 Microsoft 支持部门。
9018：未在来宾 Vm 中安装 PowerShell。  |   PowerShell 在来宾 VM 中不可用。    |   在来宾 VM 中安装 PowerShell。
9019：由于来宾 VM 操作失败，无法发现。     |   VM 上的 VMware 来宾操作失败。    |   请确保 VM 凭据有效，并且来宾 VM 凭据中提供的用户名采用 UPN 格式。
9020：文件创建权限被拒绝。    |   与用户或组策略关联的角色限制用户在文件夹中创建文件    |   检查来宾用户是否提供了对文件夹中的文件的 create 权限。 请参阅服务器评估中的 **通知** ，了解文件夹的名称。
9021：无法在系统临时路径中创建文件。     |   VMware 工具报告系统临时路径而不是用户临时路径。    |   将 vmware 工具10287版本 (NGC/VI 客户端格式) 升级。
9022：对 WMI 对象的访问被拒绝。    |   与用户或组策略关联的角色限制用户访问 WMI 对象。  |   请联系 Microsoft 支持。
9023：无法运行 PowerShell，因为 SystemRoot 环境变量值为空。    |   对于来宾 VM，SystemRoot 环境变量的值为空。     |   若要解决此问题，请联系 Microsoft 支持部门。
9024：无法发现，因为 TEMP 环境变量值为空。    |   对于来宾 VM，TEMP 环境变量的值为空。   |   请联系 Microsoft 支持。
9025：来宾 Vm 中的 PowerShell 已损坏。  |   PowerShell 在来宾 VM 中已损坏。    |   在来宾 VM 中重新安装 PowerShell，并验证是否可以在来宾 VM 上运行 PowerShell。
9026：无法在虚拟机上运行来宾操作。  |   VM 状态不允许在 VM 上运行来宾操作。   |   若要解决此问题，请联系 Microsoft 支持部门。
9027：来宾操作代理未在 VM 中运行。   |   无法联系在虚拟机中运行的来宾操作代理。    |   若要解决此问题，请联系 Microsoft 支持部门。
9028：无法创建文件，因为 VM 中的磁盘存储不足。     |   磁盘空间不足。   |   确保 VM 的磁盘存储中有足够的可用空间。
9029：在提供的来宾 VM 凭据上没有对 powershell 的访问权限。   |   对 Powershell 的访问权限不适用于用户。     |   确保在设备上添加的用户可以访问来宾 VM 上的 PowerShell。
9030：无法收集发现的元数据，因为 ESXi 主机已断开连接。     |   ESXi 主机处于断开连接状态。   |   确保运行 VM 的 ESXi 主机已连接。
9031：无法收集发现的元数据，因为 ESXi 主机未响应。   |   远程主机处于无效状态。    |   确保运行 VM 的 ESXi 主机正在运行并已连接。
9032：由于内部错误，无法发现。   |   此问题可能是由于内部错误引起的。   |   若要解决此问题，请联系 Microsoft 支持部门。
9033：无法发现，因为 VM 用户名包含无效字符。     |   在用户名中检测到无效字符。   |   请再次提供 VM 凭据，确保不存在无效字符。
9034：提供的用户名不是 UPN 格式。    |   用户名不是 UPN 格式。  |   确保用户名 (UPN) 格式为用户主体名称。
9035：无法发现，因为 Powershell 语言模式未设置为 "完全语言"。  |   来宾 VM 中的 Powershell 的语言模式未设置为 "完全语言"。   |   确保将 PowerShell 语言模式设置为 "完整语言"。
9037：数据收集暂时暂停，因为 VM 响应时间太高。    |   发现的 VM 的响应时间太长     |   无需采取措施。 将在24小时内尝试进行一次重试，以便进行应用程序发现，使用3小时进行依赖关系分析 (无代理) 。
10000：不支持操作系统类型。   |   服务器上运行的操作系统既不是 Windows 也不是 Linux。    |   受支持的操作系统类型仅为 Windows 和 Linux。
10001：在设备上找不到服务器发现脚本。    |   发现未按预期方式工作。   |   若要解决此问题，请联系 Microsoft 支持部门。
10002：发现任务未按时完成。     |   发现代理未按预期方式工作。     |   此问题应在24小时内自动解决。 如果问题持续出现，请联系 Microsoft 支持。
10003：执行发现任务的进程已退出并出现错误。    |   执行发现任务的进程已退出并出现错误。  |   此问题应在24小时内自动解决。 如果问题仍然存在，请联系 Microsoft 支持部门。
10004：没有为来宾操作系统类型提供凭据。  |   Azure Migrate 设备中未提供用于访问此 OS 类型的计算机的凭据。    |   为设备上的计算机添加凭据
10005：提供的凭据无效。   |   为设备提供的用于访问服务器的凭据不正确。  |   更新设备中提供的凭据，并确保可以使用凭据访问服务器。
10006：凭据存储不支持来宾 OS 类型。  |   服务器上运行的操作系统既不是 Windows 也不是 Linux。    |   受支持的操作系统类型仅为 Windows 和 Linux。
10007：无法处理发现的元数据。    |   尝试反序列化 JSON 时出错。    |   若要解决此问题，请联系 Microsoft 支持部门。
10008：无法在服务器上创建文件。    |  此问题可能是由于内部错误引起的。    |   若要解决此问题，请联系 Microsoft 支持部门。
10009：无法将发现的元数据写入服务器上的文件。  |   此问题可能是由于内部错误引起的。   |   若要解决此问题，请联系 Microsoft 支持部门。




## <a name="next-steps"></a>后续步骤
为 [VMware](how-to-set-up-appliance-vmware.md)、 [hyper-v](how-to-set-up-appliance-hyper-v.md)或 [物理服务器](how-to-set-up-appliance-physical.md)设置设备。
