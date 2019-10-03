---
title: 一台设备的 Azure 迁移服务器评估/迁移为 HYPER-V Vm 设置 |Microsoft Docs
description: 介绍如何设置用于发现、 评估和使用 Azure 迁移服务器评估/迁移的 HYPER-V Vm 迁移到无代理的设备。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: c531fe49ebff6c021547c2d1c2f382bcd6c9caef
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811760"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>为 HYPER-V Vm 设置一台设备

本文介绍如何设置 Azure Migrate 设备，如果要使用 Azure Migrate Server 评估工具评估的 HYPER-V Vm，或将 VMware Vm 迁移到 Azure 中使用 Azure 迁移 Server 迁移工具。

HYPER-V VM 设备是轻量设备，通过 Azure 迁移服务器评估/迁移用于执行以下操作：

- 发现在本地 HYPER-V Vm。
- 发送发现的 Vm 到 Azure 迁移服务器评估/迁移元数据和性能数据。

[了解详细信息](migrate-appliance.md)有关 Azure Migrate 设备。


## <a name="appliance-deployment-steps"></a>设备的部署步骤

若要设置设备您：
- 从 Azure 门户下载压缩的 HYPER-V VHD。
- 创建设备，并检查它能够连接到 Azure Migrate Server 评估。 
- 将设备配置为第一次，并将其注册到 Azure Migrate 项目。

## <a name="download-the-vhd"></a>下载 VHD

下载设备的压缩的 VHD 模板。

1. 在中**迁移目标** > **服务器** > **Azure 迁移：Server 评估**，单击**发现**。
2. 在中**发现计算机** > **计算机虚拟化是否？** ，单击**是，使用 HYPER-V**。
3. 单击**下载**下载 VHD 文件。

    ![下载 VM](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>验证安全性

检查在部署之前安全，压缩后的文件。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令来为 VHD 生成的哈希
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  为设备版本 1.19.05.10，生成的哈希应匹配这些设置。

  **算法** | **哈希值**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="create-the-appliance-vm"></a>创建设备 VM

导入下载的文件，并创建 VM。

1. 提取到文件夹将托管设备 VM 的 HYPER-V 主机上的压缩的 VHD 文件。 提取这三个文件夹。
2. 打开 HYPER-V 管理器。 在中**操作**，单击**导入虚拟机**。

    ![将 VHD 部署](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. 在导入虚拟机向导 >**在开始之前**，单击**下一步**。
3. 在中**定位文件夹**，指定包含提取的 VHD 的文件夹。 然后单击“下一步”  。
1. 在中**选择虚拟机**，单击**下一步**。
2. 在中**选择导入类型**，单击**复制虚拟机 （创建新的唯一 ID）** 。 然后单击“下一步”  。
3. 在中**选择目标**，保留默认设置。 单击“下一步”  。
4. 在中**存储文件夹**，保留默认设置。 单击“下一步”  。
5. 在中**选择网络**，指定 VM 将使用的虚拟交换机。 此开关需要 internet 连接将数据发送到 Azure。
6. 在中**摘要**，查看设置。 然后单击“完成”  。
7. 中的 HYPER-V 管理器 >**虚拟机**，启动 VM。


### <a name="verify-appliance-access-to-azure"></a>验证设备到 Azure 的访问权限

请确保设备 VM 可以连接到[Azure Url](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)。

## <a name="configure-the-appliance"></a>配置设备

第一次设置设备。

1. 中的 HYPER-V 管理器 >**虚拟机**，右键单击 VM > **Connect**。
2. 为设备提供的语言、 时区和密码。
3. 打开浏览器可以连接到 VM，并打开设备的 web 应用的 URL 的任何计算机上： **https://*设备名称或 IP 地址*:44368**.

   或者，可以通过单击应用快捷方式从设备桌面打开应用。
1. 在 web 应用 >**设置先决条件**，执行以下操作：
    - **许可证**:接受许可条款，并阅读第三方信息。
    - **连接**:应用会检查 VM 具有 internet 访问权限。 如果 VM 使用代理：
        - 单击**代理设置**，并在窗体中指定的代理地址和侦听端口 http://ProxyIPAddress 或 http://ProxyFQDN 。
        - 如果代理需要身份验证，请指定凭据。
        - 仅支持 HTTP 代理。
    - **时间同步**:验证时间。 在设备上的时间应与 VM 发现正常工作的 internet 时间同步。
    - **安装更新**:Azure Migrate Server 评估检查设备具有最新的更新安装。

### <a name="register-the-appliance-with-azure-migrate"></a>使用 Azure Migrate 中注册设备

1. 单击**登录**。 如果未显示，请确保已禁用弹出窗口阻止程序在浏览器中。
2. 在新的选项卡上注册使用 Azure 凭据。 
    - 使用你的用户名和密码登录。
    - 不支持使用 PIN 登录。
3. 已成功登录后，请返回到 web 应用。
4. 选择在其中创建 Azure Migrate 项目的订阅。 然后选择的项目。
5. 指定设备的名称。 名称应包含 14 个字符的字母数字或更少。
6. 单击“注册”  。


### <a name="delegate-credentials-for-smb-vhds"></a>SMB Vhd 的委派凭据

如果您在中小型企业运行 Vhd，必须启用委派的凭据从设备到 HYPER-V 主机。 若要从设备执行此操作：

1. 在设备上的 VM，运行此命令。 HyperVHost1/HyperVHost2 是示例主机名称。

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. 或者，执行此操作在本地组策略编辑器在设备上：
    - 在中**本地计算机策略** > **计算机配置**，单击**管理模板** > **系统** > **凭据委派**。
    - 双击**允许委派新凭据**，然后选择**已启用**。
    - 在中**选项**，单击**显示**，并添加你想要使用发现到列表中，每个 HYPER-V 主机**wsman /** 作为前缀。
    - 在中**凭据委派**，双击**允许委派新的凭据用于仅 NTLM 服务器身份验证**。 同样，添加你想要使用发现到列表中，每个 HYPER-V 主机**wsman /** 作为前缀。

## <a name="start-continuous-discovery"></a>启动持续发现

从设备连接到 HYPER-V 主机或群集，并启动 VM 发现。

1. 在中**用户名**并**密码**，指定该设备将用来发现 Vm 的帐户凭据。 指定的凭据的友好名称，然后单击**保存详细信息**。
2. 单击**添加主机**，并指定的 HYPER-V 主机/群集详细信息。
3. 单击“验证”。  验证之后，可以发现每个主机/群集的 Vm 的数目是所示。
    - 如果验证失败的主机，通过将鼠标悬停在图标上查看的错误**状态**列。 解决问题，并再次验证。
    - 若要删除主机或群集，请选择 >**删除**。
    - 无法从群集中删除特定的主机。 仅可以删除整个群集。
    - 可以添加一个群集，即使没有与特定主机群集中的问题。
4. 验证之后，单击**保存并启动发现**若要开始发现过程。

这将启动发现。 花费大约 15 分钟，发现的 Vm 的元数据显示在 Azure 门户中。 

## <a name="verify-vms-in-the-portal"></a>在门户中验证 VM

发现完成后，可以验证 Vm 显示在门户中。

1. 打开 Azure Migrate 仪表板。
2. 在中**Azure 迁移的服务器** > **Azure 迁移：Server 评估**页上，单击图标，显示的计数**发现服务器**。 


## <a name="next-steps"></a>后续步骤

试用[HYPER-V 评估](tutorial-assess-hyper-v.md)使用 Azure Migrate Server 评估。