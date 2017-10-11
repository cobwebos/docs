---
title: "创建 StorSimple 支持包 | Microsoft Docs"
description: "了解如何创建、解密和编辑 StorSimple 设备的支持包。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: eac76f5f-5db1-4c92-af8c-54053b91e66c
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 32d20e7a8adcfc646c592213fe7395b87a93c985
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="create-and-manage-a-storsimple-support-package"></a>创建和管理 StorSimple 支持包
## <a name="overview"></a>概述
StorSimple 支持包是一种易于使用的机制，用于收集所有相关日志，协助 Microsoft 支持部门排除 StorSimple 设备问题。 收集的日志会进行加密和压缩。

本教程提供创建和管理支持包的分步说明。

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>在 Azure 经典门户中创建并上传支持包
可以在 Azure 经典门户中通过服务的“维护”页创建支持包并将其上传到 Microsoft 支持站点。

> [!NOTE]
> 上传需要支持密钥。 支持工程师应该在电子邮件中提供了该密码。
> 
> 

创建经过加密和压缩的支持包（.cab 文件）并将其上传到支持站点。 然后，支持工程师可以从用于解决问题的支持站点检索此包。

请在经典门户中执行以下步骤，创建支持包。

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>在 Azure 经典门户中创建支持包
1. 选择“设备” > “维护”。
2. 在“支持包”部分，选择“创建并上传支持包”。
3. 在“创建并上传支持包”对话框中，执行以下操作：
   
    ![创建支持包](./media/storsimple-create-manage-support-package/IC740923.png)
   
   * 在“支持密钥”文本框中，输入密钥。 Microsoft 支持工程师会通过电子邮件发送该密钥。
   * 选中此复选框即表示同意自动将支持包上传到 Microsoft 支持站点。
   * 单击选中图标  ![勾选图标](./media/storsimple-create-manage-support-package/IC740895.png)。

## <a name="manually-create-a-support-package"></a>手动创建支持包
某些情况下，需要通过 Windows PowerShell for StorSimple 手动创建支持包。 例如：

* 与 Microsoft 支持部门共享日志文件之前，需要从其中删除敏感信息。
* 因连接问题而无法上传该包。

可以通过电子邮件与 Microsoft 支持部门共享手动创建的支持包。 执行以下步骤，在 Windows PowerShell for StorSimple 中创建支持包。

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>在 Windows PowerShell for StorSimple 中创建支持包
1. 若要在用于连接到 StorSimple 设备的远程计算机上以管理员身份启动 Windows PowerShell 会话，请输入以下命令：
   
    `Start PowerShell`
2. 在 Windows PowerShell 会话中，连接到设备的 SSAdmin 控制台：
   
   * 在命令提示符处，输入：
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   * 在打开的对话框中，输入设备管理员密码。 默认密码为：
     
      `Password1`
     
      ![“PowerShell 凭据”对话框](./media/storsimple-create-manage-support-package/IC740962.png)
   * 选择“确定”。
   * 在命令提示符处，输入：
     
      `Enter-PSSession $MS`
3. 在打开的会话中，输入合适的命令。
   
   * 对于受密码保护的网络共享，请输入：
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`
     
       系统会提示用户输入密码、网络共享文件夹的路径、加密密码（因为支持包已加密）。 然后会在指定的文件夹中创建支持包。
   * 对于不受密码保护的共享，不需 `-Credential` 参数。 输入以下内容：
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`
     
       为指定的网络共享文件夹中的两个控制器创建支持包。 该支持包是加密的压缩文件，可以发送到 Microsoft 支持部门进行故障排除。 有关详细信息，请参阅[联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)。

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Export-HcsSupportPackage cmdlet 参数
可将以下参数用于 Export-HcsSupportPackage cmdlet。

| 参数 | 必需/可选 | 说明 |
| --- | --- | --- |
| `-Path` |必选 |用于提供在其中放置支持包的网络共享文件夹的位置。 |
| `-EncryptionPassphrase` |必选 |用于提供一个密码，以便加密支持包。 |
| `-Credential` |可选 |用于提供网络共享文件夹的访问凭据。 |
| `-Force` |可选 |用于跳过加密密码确认步骤。 |
| `-PackageTag` |可选 |用于在“路径”下指定一个目录，以便放置支持包。 默认值为 [设备名称]-[当前日期和时间:yyyy-MM-dd-HH-mm-ss]。 |
| `-Scope` |可选 |指定为“群集”（默认值），以便为两个控制器创建支持包。 如果只需为当前控制器创建包，请指定“控制器”。 |

## <a name="edit-a-support-package"></a>编辑支持包
生成支持包之后，可能需要对包进行编辑，删除敏感信息。 敏感信息可能包括日志文件中的卷名称、设备 IP 地址和备份名称。

> [!IMPORTANT]
> 只能编辑通过 Windows PowerShell for StorSimple 生成的支持包。 无法编辑通过 StorSimple Manager 服务在 Azure 经典门户中创建的包。
> 
> 

要编辑支持包以便将其上传到 Microsoft 支持站点，请首先解密该支持包，编辑文件，然后重新对其加密。 执行以下步骤。

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>在 Windows PowerShell for StorSimple 中编辑支持包
1. 如前所述生成支持包，详见：[在 Windows PowerShell for StorSimple 中创建支持包](#to-create-a-support-package-in-windows-powershell-for-storsimple)。
2. [下载脚本](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65)到客户端本地。
3. 导入 Windows PowerShell 模块。 指定已将脚本下载到其中的本地文件夹的路径。 若要导入模块，请输入：
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. 所有文件均为已经过压缩和加密的 *.aes* 文件。 若要解压缩和解密文件，请输入：
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    请注意，现在所有文件都显示实际的文件扩展名。
   
    ![编辑支持包](./media/storsimple-create-manage-support-package/IC750706.png)
5. 当系统提示输入加密密码时，请输入在创建支持包时使用过的密码。
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. 浏览到包含日志文件的文件夹。 这些日志文件现在已解压缩和解密，因此会显示原始的文件扩展名。 修改这些文件，删除任何特定于客户的信息，例如卷名称和设备 IP 地址，并保存文件。
7. 关闭要使用 gzip 压缩并使用 AES-256 加密的文件。 这是为了确保通过网络传输支持包的速度和安全性。 若要压缩和加密文件，请输入以下命令：
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![编辑支持包](./media/storsimple-create-manage-support-package/IC750707.png)
8. 出现提示时，为已修改的支持包提供加密密码。
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. 记下新密码，当 Microsoft 支持部门要求提供密码时，即可将密码与之共享。

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>示例：在受密码保护的共享中编辑支持包中的文件
以下示例介绍如何解密、编辑和重新加密支持包。

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>后续步骤
* 了解如何[使用支持包和设备日志排查设备部署问题](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting)。
* 了解如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。

