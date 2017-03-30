---
title: "Azure AD Connect：启用设备写回 | Microsoft Docs"
description: "本文档详细说明如何使用 Azure AD Connect 启用设备写回功能"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 28b5da6098316f8fbe84966e0dac88f5b7d2cb1d
ms.openlocfilehash: bf7dd55be2b0e6ae43fc148ff36d8d2141a2f12b
ms.lasthandoff: 12/20/2016


---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect：启用设备写回
> [!NOTE]
> 设备写回需要 Azure AD Premium 订阅。
> 
> 

以下文档提供有关如何在 Azure AD Connect 中启用设备写回功能的信息。 设备写回用于以下方案：

* 对 ADFS（2012 R2 或更高版本）保护的应用程序（信赖方信任），启用基于设备的条件性访问。

这可以提供额外的安全性，确保只有受信任的设备才能访问应用程序。 有关条件性访问的详细信息，请参阅[使用条件性访问管理风险](../active-directory-conditional-access.md)和[使用 Azure Active Directory Device Registration 设置本地的条件性访问](https://msdn.microsoft.com/library/azure/dn788908.aspx)。

> [!IMPORTANT]
> <li>设备必须位于用户所在的同一个林中。 由于设备必须写回到单个林，此功能当前不支持具有多个用户林的部署。</li>
> <li>只能将一个设备注册配置对象添加到本地 Active Directory 林。 此功能与本地 Active Directory 同步到多个 Azure AD 目录的拓扑不兼容。</li>> 

## <a name="part-1-install-azure-ad-connect"></a>第 1 部分：安装 Azure AD Connect
1. 使用自定义或快速设置安装 Azure AD Connect。 Microsoft 建议在启用设备写回之前，首先让所有用户和组成功完成同步。

## <a name="part-2-prepare-active-directory"></a>第2 部分：准备 Active Directory
使用以下步骤来准备使用设备写回。

1. 从已安装 Azure AD Connect 的计算机上，以权限提升模式启动 PowerShell。
2. 如果尚未安装 Active Directory PowerShell 模块，请使用以下命令安装该模块：
   
   `Add-WindowsFeature RSAT-AD-PowerShell`
3. 如果尚未安装 Azure Active Directory PowerShell 模块，请从 [用于 Windows PowerShell 的 Azure Active Directory 模块（64 位版本）](http://go.microsoft.com/fwlink/p/?linkid=236297)下载并安装该模块。 此组件依赖于随 Azure AD Connect 一起安装的登录助手。
4. 使用企业管理员凭据运行以下命令，然后退出 PowerShell。
   
   `Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`
   
   `Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`

由于需要对配置命名空间进行更改，因此需要使用企业管理员凭据。 域管理员没有足够的权限。

![用于启用设备写回的 Powershell](./media/active-directory-aadconnect-feature-device-writeback/powershell.png)

说明:

* 该命令将在 CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn] 下创建并配置新的容器和对象（如果不存在）。
* 该命令将在 CN=RegisteredDevices,[domain-dn] 下创建并配置新的容器和对象（如果不存在）。 将在此容器中创建设备对象。
* 在 Azure AD 连接器帐户中设置必要的权限，以便管理 Active Directory 上的设备。
* 即使 Azure AD Connect 安装在多个林中，也只需要在一个林中运行。

参数：

* DomainName：将在其中创建设备对象的 Active Directory 域。 注意：给定的 Active Directory 林的所有设备都在单个域中创建。
* AdConnectorAccount：Azure AD Connect 使用此 Active Directory 帐户来管理目录中的对象。 这是 Azure AD Connect 同步用来连接 AD 的帐户。 如果 Azure AD Connect 是使用快速设置安装的，则该帐户是带有 MSOL_ 前缀的帐户。

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>第 3 部分：在 Azure AD Connect 中启用设备写回
使用以下过程在 Azure AD Connect 中启用设备写回。

1. 再次运行安装向导。 从“其他任务”页中选择自定义同步选项，然后单击“下一步”。
   ![自定义安装 - 自定义同步选项](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2. 在“可选功能”页中，设备写回不再灰显。 请注意，如果 Azure AD Connect 准备步骤未完成，“可选功能”页中的设备写回将会灰显。 选中设备写回对应的框并单击“下一步”。 如果该复选框仍已禁用，请参阅[疑难解答部分](#the-writeback-checkbox-is-still-disabled)。
   ![自定义安装 - 设备写回可选功能](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3. 在写回页中，会看到提供的域是默认的设备写回林。
   ![自定义安装 - 设备写回目标林](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4. 在向导中完成安装，不需要更改其他配置。 如果需要，请参阅 [Azure AD Connect 的自定义安装](active-directory-aadconnect-get-started-custom.md)。

## <a name="enable-conditional-access"></a>启用条件性访问
[使用 Azure Active Directory Device Registration 设置本地条件性访问](https://msdn.microsoft.com/library/azure/dn788908.aspx)中提供了有关启用此方案的详细说明。

## <a name="verify-devices-are-synchronized-to-active-directory"></a>验证设备是否已同步到 Active Directory
设备写回现在应在正常运行。 请注意，将设备对象写回到 Active Directory 最长可能需要 3 个小时。  若要验证设备是否已正确同步，请在同步规则完成之后执行以下操作：

1. 启动 Active Directory 管理中心。
2. 在要联合的域中展开 RegisteredDevices。
   ![Active Directory 管理中心 - 已注册的设备](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3. 其中将会列出当前已注册的设备。
   ![Active Directory 管理中心 - 已注册的设备列表](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="troubleshooting"></a>故障排除
### <a name="the-writeback-checkbox-is-still-disabled"></a>写回复选框仍处于禁用状态
如果未启用设备写回复选框，即使已遵循上述步骤，以下步骤还是会在启用此框之前引导你完成安装向导正在验证的程序。

首先：

* 确保至少有一个林具有 Windows Server 2012R2。 设备对象类型必须存在。
* 如果安装向导已在运行，则不会检测到任何更改。 在此情况下，请先完成安装向导，然后再试一次。
* 确保在初始化脚本中提供的帐户是 Active Directory 连接器实际使用的正确用户。 若要验证，请执行以下步骤：
  * 从“开始”菜单打开“同步服务”。
  * 打开“连接器”选项卡。
  * 查找类型为 Active Directory 域服务的连接器并选择它。
  * 在“操作”下面，选择“属性”。
  * 转到“连接到 Active Directory 林”。 检查此屏幕上指定的域和用户名是否与提供给脚本的帐户匹配。
    ![同步服务管理器中的连接器帐户](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

在 Active Directory 中验证配置：

* 检查设备注册服务是否位于配置命名上下文下面的以下位置：(CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration)。

![故障排除，配置命名空间中的 DeviceRegistrationService](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

* 搜索配置命名空间以验证是否只有一个配置对象。 如果存在多个对象，请删除重复项。

![排除故障，搜索重复的对象](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

* 对于设备注册服务对象，请确保 msDS-DeviceLocation 属性存在且具有值。 查找此位置，并确保它存在且 objectType 为 msDS-DeviceContainer。

![故障排除，msDS-DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![故障排除，RegisteredDevices 对象类](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

* 验证 Active Directory 连接器使用的帐户是否具有上一步骤所找到的“注册的设备”容器的所需权限。 这是此容器上所需的权限：

![故障排除，验证对容器的权限](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

* 验证 Active Directory 帐户是否具有对 CN=Device Registration Configuration,CN=Services,CN=Configuration 对象的权限。

![排除故障，验证对设备注册配置的权限](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>其他信息
* [使用条件性访问管理风险](../active-directory-conditional-access.md)
* [使用 Azure Active Directory Device Registration 设置本地条件性访问](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。


