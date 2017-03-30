---
title: "Azure 日志集成常见问题 |Microsoft 文档"
description: "此常见问题回答了关于 Azure 日志的问题。"
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 2d5201a335e96cc8595de834858750f6aac884a3
ms.lasthandoff: 03/17/2017


---
# <a name="azure-log-integration-frequently-asked-questions-faq"></a>Azure 日志集成常见问题 (FAQ)
此常见问题回答有关 Azure 日志集成的问题，这项服务使你能够将来自 Azure 资源原始日志集成到本地安全信息和事件管理 (SIEM) 系统中。 此集成为本地或云中的所有资产提供统一的仪表板，以便聚合、相互关联、分析和警示与应用程序相关的安全事件。

## <a name="is-the-azure-log-integration-software-free"></a>Azure 日志集成软件是否免费？
是的。 Azure 日志集成软件不产生任何费用。 

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs-from"></a>如何查看 Azure 日志集成从中提取 Azure VM 日志的存储帐户？
运行 **azlog source list** 命令。

## <a name="how-can-i-update-the-proxy-configuration"></a>如何更新代理配置？
如果代理设置不允许直接访问 Azure 存储，请打开 **c:\Program Files\Microsoft Azure Log Integration** 中的 **AZLOG.EXE.CONFIG** 文件。 更新文件，以将组织的代理地址包括在 **defaultProxy** 部分中。 完成更新后，并使用命令 **net stop azlog** 和 **net start azlog** 停止和启动该服务。

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>如何查看 Windows 事件中的订阅信息？
添加源时，将 **subscriptionid** 追加到友好名称后面。

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
事件 XML 具有如下所示的元数据，包括订阅 ID。

![事件 XML][1]

## <a name="error-messages"></a>错误消息
### <a name="when-running-command-azlog-createazureid-why-do-i-get-the-following-error"></a>运行 **azlog createazureid** 命令时，为什么收到以下错误？
错误：

  *无法创建 AAD 应用程序 - 租户 72f988bf-86f1-41af-91ab-2d7cd011db37 - 原因 = “禁止” - 消息 = “特权不足以完成此操作”。*

**Azlog createazureid** 尝试在 Azure 登录有权访问的订阅在所有 Azure AD 租户中创建服务主体。 如果 Azure 登录在 Azure AD 租户中只是来宾用户，那么该命令会失败，并显示“特权不足以完成此操作”。 请求租户管理员将你的帐户添加为租户中的用户。

### <a name="when-running-command-azlog-authorize-why-do-i-get-the-following-error"></a>运行 **azlog authorize** 命令时，为什么收到以下错误？
错误：

  *创建角色分配警告 - AuthorizationFailed：具有对象 ID“fe9e03e4-4dad-4328-910f-fd24a9660bd2”的客户端 janedo@microsoft.com 没有在“/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000”范围执行操作“Microsoft.Authorization/roleAssignments/write”的权限。*

**Azlog authorize** 命令会将读取器角色分配给 Azure AD 服务主体（使用 **Azlog createazureid** 创建）所提供的订阅。 如果 Azure 登录不是订阅的协同管理员或所有者，就会失败，并显示“授权失败”错误消息。 需要 Azure 基于角色访问控制 (RBAC) 的共同管理员或所有者来完成此操作。

## <a name="where-can-i-find-the-definition-of-the-properties-in-audit-log"></a>在哪里可以找到审核日志中的属性定义？
请参阅：

* [使用 Resource Manager 执行审核操作](../azure-resource-manager/resource-group-audit.md)
* [在 Azure Monitor REST API 中列出订阅的管理事件](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>哪里可以找到 Azure 安全中心警报的详细信息？
请参阅[管理和响应 Azure 安全中心中的安全警报](../security-center/security-center-managing-and-responding-alerts.md)。

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>如何修改使用 VM 诊断收集的内容？
有关如何获取、修改和设置 Windows *(WAD)* 配置中的详细信息，请参阅[使用 PowerShell 在运行 Windows 的虚拟机上启用 Azure 诊断](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 下面是一个示例：

### <a name="get-the-wad-config"></a>获取 WAD 配置
    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### <a name="modify-the-wad-config"></a>修改WAD 配置
在下面的示例中，只有从安全事件日志中收集的 EventID 4624 和 EventId 4625 的配置。 从系统事件日志中收集 Microsoft 反恶意软件事件。 有关使用 XPath 表达式的详细信息，请参阅 [使用的事件] (https://msdn.microsoft.com/library/windows/desktop/dd996910 (v = vs.85)。

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### <a name="set-the-wad-configuration"></a>设置 WAD 配置
    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

完成更改后，检查存储帐户，以确保收集正确的事件。

如果在安装和配置过程中遇到问题，请开具[支持请求](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request)，选择“日志集成”作为需要请求支持的服务。


<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png

