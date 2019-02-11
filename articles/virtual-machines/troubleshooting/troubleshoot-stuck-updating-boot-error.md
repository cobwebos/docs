---
title: Azure VM 启动在更新 Windows 时停滞 | Microsoft Docs
description: 了解如何解决 Azure VM 启动在更新 Windows 时停滞的问题。
services: virtual-machines-windows
documentationCenter: ''
authors: genli
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: d56e96ca1fbc96261f6f526c792b0a53c74718ef
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063654"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Azure VM 启动在更新 Windows 时停滞

本文可帮助你解决虚拟机 (VM) 在启动过程中停滞在 Windows 更新阶段的问题。 

> [!NOTE] 
> Azure 具有用于创建和处理资源的两个不同部署模型：[资源管理器部署模型和经典部署模型](../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用 Resource Manager 部署模型。 建议为新部署使用此模型，而不是使用经典部署模型。

 ## <a name="symptom"></a>症状

 Windows VM 不启动。 检查[启动诊断](../troubleshooting/boot-diagnostics.md)窗口中的屏幕截图时，看到启动停滞在更新进程。 下面是可能收到的消息示例：

- 正在安装 Windows ##% 请不要关闭电脑。 你的电脑将重启几次 这可能需要一点时间
- 使电脑保持开机状态 直到操作完成。 正在安装第 # 个更新 共 # 个... 
- 无法完成更新 正在撤销更改 请不要关闭计算机
- 配置 Windows 更新失败 正在还原更改 请不要关闭计算机
- 应用第 #### 个(共 #### 个)更新操作时出错 <错误代码> (\Regist...)
- 应用第 #### 个(共 #### 个)更新操作时出现灾难性错误 <错误代码> ($$...)


## <a name="solution"></a>解决方案

根据要安装或回退的更新数量，更新过程可能需要一段时间。 将 VM 保持这种状态 8 小时。 如果该时间段后 VM 仍处于此状态，请从 Azure 门户中重启 VM，然后查看是否可以正常启动。 如果此步骤不起作用，请尝试以下解决方案。

### <a name="remove-the-update-that-causes-the-problem"></a>删除导致问题的更新

1. 拍摄受影响的 VM 的 OS 磁盘的快照作为备份。 有关详细信息，请参阅[拍摄磁盘快照](../windows/snapshot-copy-managed-disk.md)。 
2. [将 OS 磁盘附加到恢复 VM](troubleshoot-recovery-disks-portal-windows.md)。
3. 将 OS 磁盘附加到恢复 VM 上后，运行 **diskmgmt.msc** 以打开磁盘管理，并确保所附磁盘已**联机**。 记下分配给保存 \windows 文件夹的附加 OS 磁盘的驱动器号。 如果磁盘已加密，先解密磁盘，然后再继续此文档中的下一步。

4. 打开权限提升的命令提示符实例（“以管理员身份运行”）。 运行以下命令获取附加 OS 磁盘上的更新包列表：

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    例如，如果附加 OS 磁盘是驱动器 F，则运行以下命令：

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. 打开 C:\temp\Patch_level.txt 文件，然后从下往上浏览。 查找处于“安装挂起”或“卸载挂起”状态的更新。  以下是更新状态的示例：

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. 删除导致问题的更新：
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    示例： 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > DISM 工具需要一些时间来处理取消安装，具体取决于包的大小。 通常情况下，该过程在 16 分钟内完成。

7. [分离 OS 磁盘并重新创建 VM](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk)。 然后检查是否解决了问题。