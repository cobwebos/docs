---
title: "远程连接到 StorSimple 设备 | Microsoft Docs"
description: "介绍如何配置设备进行远程管理，以及如何通过 HTTP 或 HTTPS 连接到 Windows PowerShell for StorSimple。"
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9414d9c93fe463910ffa6fce72aada6a0d720464
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>远程连接到 StorSimple 8000 系列设备

## <a name="overview"></a>概述

可以通过 Windows PowerShell 远程连接到设备。 采取这种方式连接时，不会看到菜单。 （仅当在设备上使用串行控制台进行连接时，才会看到菜单。）借助 Windows PowerShell 远程处理，可连接到特定的运行空间。 也可以指定显示语言。

有关使用 Windows PowerShell 远程处理来管理设备的详细信息，请转到 [Use Windows PowerShell for StorSimple to administer your StorSimple device](storsimple-8000-windows-powershell-administration.md)（使用 Windows PowerShell for StorSimple 管理 StorSimple 设备）。

本教程介绍如何配置设备进行远程管理，以及如何连接到 Windows PowerShell for StorSimple。 可以使用 HTTP 或 HTTPS 通过 Windows PowerShell 建立远程连接。 但是，在决定如何连接到用于 StorSimple 的 Windows PowerShell 时，请注意以下信息：

* 直接连接到设备串行控制台是安全的，但通过网络交换机连接到串行控制台并不安全。 通过网络交换机连接到设备串行控制台时，请警惕安全风险。
* 与在网络上通过串行控制台进行连接相比，通过 HTTP 会话进行连接可能具有更高的安全性。 虽然这不是最安全的方法，但在受信任的网络上是比较安全的。
* 通过使用自签名证书的 HTTPS 会话进行连接是最安全的选项（建议使用）。

可以远程连接到 Windows PowerShell 接口。 但在默认情况下，通过 Windows PowerShell 接口远程访问 StorSimple 设备处于未启用状态。 首先必须在设备上启用远程管理，然后在用于访问设备的客户端上启用远程管理。

本文中所述的步骤是在运行 Windows Server 2012 R2 的主机系统上执行的。

## <a name="connect-through-http"></a>通过 HTTP 连接

与通过 StorSimple 设备的串行控制台连接到 Windows PowerShell for StorSimple 相比，通过 HTTP 会话连接具有更高的安全性。 虽然这不是最安全的方法，但在受信任的网络上是比较安全的。

可以使用 Azure 门户或串行控制台来配置远程管理。 在下列过程中选择：

* [使用 Azure 门户通过 HTTP 启用远程管理](#use-the-azure-classic-portal-to-enable-remote-management-over-http)
* [使用串行控制台通过 HTTP 启用远程管理](#use-the-serial-console-to-enable-remote-management-over-http)

启用远程管理后，使用以下过程为远程连接准备客户端。

* [为远程连接准备客户端](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>使用 Azure 门户通过 HTTP 启用远程管理

在 Azure 门户中执行以下步骤，通过 HTTP 启用远程管理。

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>通过 Azure 门户启用远程管理

1. 转到 StorSimple Device Manager 服务。 选择“设备”，选择并单击要为其配置远程管理的设备。 转到“设备设置”>“安全性”。
2. 在“安全设置”边栏选项卡中，单击“远程管理”。
3. 在“远程管理”边栏选项卡中，将“启用远程管理”设置为“是”。
4. 现在可选择使用 HTTP 进行连接。 （默认为通过 HTTPS 连接。）请确保已选中 HTTP。
   
   > [!NOTE]
   > 只有受信任的网络才支持通过 HTTP 连接。
   
5. 单击“保存”，出现确认提示时，选择“是”。

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>使用串行控制台通过 HTTP 启用远程管理
在设备串行控制台上执行以下步骤以启用远程管理。

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>通过设备串行控制台启用远程管理
1. 在串行控制台菜单上，选择“选项 1”。 有关在设备上使用串行控制台的详细信息，请转到 [Connect to Windows PowerShell for StorSimple via device serial console](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)（通过设备串行控制台连接到 Windows PowerShell for StorSimple）。
2. 在提示符下键入：`Enable-HcsRemoteManagement –AllowHttp`
3. 会收到使用 HTTP 连接到设备的安全漏洞的相关通知。 出现提示时，键入 **Y** 以确认。
4. 键入以下内容来验证是否启用了 HTTP：`Get-HcsSystem`
5. 验证“RemoteManagementMode”字段是否显示为“HttpsAndHttpEnabled”。下图显示了 PuTTY 中的这些设置。
   
     ![已启用串行 HTTPS 和 HTTP](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>为远程连接准备客户端
在客户端上执行以下步骤以启用远程管理。

#### <a name="to-prepare-the-client-for-remote-connection"></a>为远程连接准备客户端
1. 以管理员身份启动 Windows PowerShell 会话。 如果使用 Windows 10 客户端，默认情况下，Windows 远程管理服务设置为手动启动。 可能需要通过键入以下命令启动该服务：

    `Start-Service WinRM`
    
2. 键入以下命令将 StorSimple 设备的 IP 地址添加到客户端受信任的主机列表中：
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     将 <*device_ip*> 替换为设备的 IP 地址；例如： 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. 键入以下命令将设备凭据保存在变量中： 
   
    ```
    $cred = Get-Credential
    ```
    
4. 在显示的对话框中：
   
   1. 按此格式键入用户名：*device_ip\SSAdmin*。
   2. 键入在使用安装向导配置设备时设置的设备管理员密码。 默认密码为 *Password1*。
5. 通过键入此命令在设备上启动 Windows PowerShell 会话：
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > 若要创建用于 StorSimple 虚拟设备的 Windows PowerShell 会话，请追加 `–Port` 参数并指定为 StorSimple 虚拟设备在远程处理中配置的公用端口。
   
   
此时，应该创建了到设备的远程 Windows PowerShell 会话。
   
![使用 HTTP 的 PowerShell 远程处理](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>通过 HTTPS 连接

通过 HTTPS 会话连接到 Windows PowerShell for StorSimple 是远程连接到 Microsoft Azure StorSimple 设备最安全的方法（建议使用）。 以下过程介绍如何设置串行控制台和客户端计算机，以便使用 HTTPS 连接到 Windows PowerShell for StorSimple。

可以使用 Azure 门户或串行控制台来配置远程管理。 在下列过程中选择：

* [使用 Azure 门户通过 HTTPS 启用远程管理](#use-the-azure-classic-portal-to-enable-remote-management-over-https)
* [使用串行控制台通过 HTTPS 启用远程管理](#use-the-serial-console-to-enable-remote-management-over-https)

启用远程管理后，使用以下过程为远程管理准备主机，以及从远程主机连接到设备。

* [为远程管理准备主机](#prepare-the-host-for-remote-management)
* [从远程主机连接到设备](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>使用 Azure 门户通过 HTTPS 启用远程管理

在 Azure 门户中执行以下步骤，通过 HTTPS 启用远程管理。

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>在 Azure 门户中通过 HTTPS 启用远程管理

1. 转到 StorSimple Device Manager 服务。 选择“设备”，选择并单击要为其配置远程管理的设备。 转到“设备设置”>“安全性”。
2. 在“安全设置”边栏选项卡中，单击“远程管理”。
3. 将“启用远程管理”设置为“是”。
4. 现在可以选择使用 HTTPS 进行连接。 （默认为通过 HTTPS 连接。）请确保已选中 HTTPS。
5. 依次单击“...”、“下载远程管理证书”。 指定保存此文件的位置。 需要在用于连接到设备的客户端或主机计算机上安装此证书。
6. 单击“保存”，出现确认提示时，单击“是”。

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>使用串行控制台通过 HTTPS 启用远程管理

在设备串行控制台上执行以下步骤以启用远程管理。

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>通过设备串行控制台启用远程管理
1. 在串行控制台菜单上，选择“选项 1”。 有关在设备上使用串行控制台的详细信息，请转到 [Connect to Windows PowerShell for StorSimple via device serial console](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)（通过设备串行控制台连接到 Windows PowerShell for StorSimple）。
2. 在提示符下键入：
   
     `Enable-HcsRemoteManagement`
   
    这应在设备上启用 HTTPS。
3. 通过键入以下内容验证是否已启用 HTTPS： 
   
     `Get-HcsSystem`
   
    请确保“RemoteManagementMode”字段显示为“HttpsEnabled”。下图显示了 PuTTY 中的这些设置。
   
     ![已启用串行 HTTPS](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. 从 `Get-HcsSystem` 的输出中，复制设备的序列号，并将其保存供稍后使用。
   
   > [!NOTE]
   > 序列号映射到证书中的 CN 名。
   
5. 键入以下内容，以获取远程管理证书： 
   
     `Get-HcsRemoteManagementCert`
   
    将显示与下面类似的证书。
   
    ![获取远程管理证书](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. 将证书中从 **-----BEGIN CERTIFICATE-----** 到 **-----END CERTIFICATE-----** 的信息复制到如记事本等文本编辑器中，并将其另存为 .cer 文件。 （在准备主机时，需要将此文件复制到远程主机。）
   
   > [!NOTE]
   > 若要生成新的证书，请使用 `Set-HcsRemoteManagementCert` cmdlet。
   
### <a name="prepare-the-host-for-remote-management"></a>为远程管理准备主机

若要为使用 HTTPS 会话的远程连接准备主机计算机，请执行以下过程：

* [将 .cer 文件导入到客户端或远程主机的根存储中](#to-import-the-certificate-on-the-remote-host)。
* [将设备序列号添加到远程主机上的 hosts 文件中](#to-add-device-serial-numbers-to-the-remote-host)。

下面描述了上述每个过程。

#### <a name="to-import-the-certificate-on-the-remote-host"></a>在远程主机上导入证书
1. 右键单击 .cer 文件并选择“安装证书”。 这会启动证书导入向导。
   
    ![证书导入向导 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. 对于“存储位置”，选择“本地计算机”，并单击“下一步”。
3. 选择“将所有证书放入下列存储”，并单击“浏览”。 导航到远程主机的根存储，并单击“下一步”。
   
    ![证书导入向导 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. 单击“完成” 。 将显示一条提示已成功导入的消息。
   
    ![证书导入向导 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>将设备序列号添加到远程主机
1. 以管理员身份启动记事本，并打开位于 \Windows\System32\Drivers\etc 的主机文件。
2. 将以下三项添加到主机文件中：**DATA 0 IP 地址**、**控制器 0 固定 IP 地址**和**控制器 1 固定 IP 地址**。
3. 输入之前保存的设备序列号。 将此设备序列号映射到 IP 地址，如下图所示。 对于控制器 0 和控制器 1，请在序列号末尾追加 **Controller0** 和 **Controller1**（CN 名）。
   
    ![将 CN 名添加到主机文件中](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. 保存主机文件。

### <a name="connect-to-the-device-from-the-remote-host"></a>从远程主机连接到设备

使用 Windows PowerShell 和 SSL 从远程主机或客户端在设备上输入 SSAdmin 会话。 SSAdmin 会话映射到设备的[串行控制台](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)菜单中的选项 1。

在想要从中建立远程 Windows PowerShell 连接的计算机上执行以下过程。

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>通过使用 Windows PowerShell 和 SSL 在设备上输入 SSAdmin 会话
1. 以管理员身份启动 Windows PowerShell 会话。 如果使用 Windows 10 客户端，默认情况下，Windows 远程管理服务设置为手动启动。 可能需要通过键入以下命令启动该服务：

    `Start-Service WinRM`

2. 通过键入以下内容将设备 IP 地址添加到客户端受信任的主机中：
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    其中，<*device_ip*> 是设备的 IP 地址；例如： 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. 若要创建新凭据，请键入：
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    其中，<*IP of target device*> 是设备的 DATA 0 IP 地址；如前面主机文件的图片中所示的 **10.126.173.90**。 此外，请提供设备的管理员密码。
4. 通过键入以下内容创建会话：
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    对于 cmdlet 中的 -ComputerName 参数，请提供 <*目标设备的序列号*>。 已在远程主机上将此序列号映射到 hosts 文件中 DATA 0 的 IP 地址；如下图中所示的 **SHX0991003G44MT**。
5. 键入：
   
     `Enter-PSSession $session`
6. 等待几分钟后，会在 SSL 上通过 HTTPS 连接到设备。 会看到一条指示已连接到设备的消息。
   
    ![使用 HTTPS 和 SSL 的 PowerShell 远程处理](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>后续步骤

* 了解有关如何[使用 Windows PowerShell 管理 StorSimple 设备](storsimple-8000-windows-powershell-administration.md)的详细信息。
* 了解有关如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)的详细信息。

