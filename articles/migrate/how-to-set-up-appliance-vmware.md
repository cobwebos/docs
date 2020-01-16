---
title: 为 VMware 设置 Azure Migrate 设备
description: 了解如何设置 Azure Migrate 设备来评估和迁移 VMware Vm。
ms.topic: article
ms.date: 11/18/2019
ms.openlocfilehash: da451149d0420bc71a355fdf4f4fb122b196d5a0
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029059"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>为 VMware Vm 设置设备

本文介绍如何设置 Azure Migrate 设备，前提是使用 Azure Migrate 服务器评估工具评估 VMware Vm，或使用 Azure Migrate 服务器迁移工具将 VMware Vm 迁移到 Azure，并使用无代理迁移。

VMware VM 设备是一种轻型设备，Azure Migrate 服务器评估/迁移来执行以下操作：

- 发现本地 VMware VM。
- 将发现的 Vm 的元数据和性能数据发送到 Azure Migrate 服务器评估/迁移。

[了解](migrate-appliance.md)Azure Migrate 设备的详细信息。


## <a name="appliance-deployment-steps"></a>设备部署步骤

若要设置该设备，请执行以下操作：
- 下载 OVA 模板文件，并将其导入 vCenter Server。
- 创建设备，并检查它是否可以连接到 Azure Migrate 服务器评估。
- 完成设备的首次配置，并将其注册到 Azure Migrate 项目。

## <a name="download-the-ova-template"></a>下载 OVA 模板

1. 在 "**迁移目标** > **服务器** > **Azure Migrate：服务器评估**中，单击"**发现**"。
2. 在“发现计算机” > “计算机是否已虚拟化?”中，单击“是，使用 VMWare vSphere 虚拟机监控程序”。
3. 单击“下载”以下载 .OVA 模板文件。



### <a name="verify-security"></a>验证安全性

在部署 .OVA 文件之前请检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令，生成 .OVA 的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 对于最新设备版本，生成的哈希应与这些设置匹配。

  **算法** | **哈希值**
  --- | ---
  MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
  SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c


## <a name="create-the-appliance-vm"></a>创建设备 VM

导入下载的文件，然后创建 VM。

1. 在 vSphere 客户端控制台中，单击“文件” > “部署 OVF 模板”。
2. 在“部署 OVF 模板向导”>“源”中，指定 OVA 文件的位置。
3. 在“名称”和“位置”中，为 VM 指定一个易记名称。 选择要在其中托管 VM 的库存对象。
5. 在“主机/群集”中，指定要在其上运行 VM 的主机或群集。
6. 在“存储”中，指定 VM 的存储目标。
7. 在“磁盘格式”中，指定磁盘类型和大小。
8. 在“网络映射”中，指定 VM 要连接到的网络。 该网络需要与 Internet 建立连接，这样才能向 Azure Migrate 服务器评估发送元数据。
9. 检查并确认设置，然后单击“完成”。


### <a name="verify-appliance-access-to-azure"></a>验证设备的 Azure 访问权限

确保设备 VM 可以连接到 [Azure URL](migrate-appliance.md#url-access)。


## <a name="configure-the-appliance"></a>配置设备

首次设置设备。

1. 在 vSphere 客户端控制台中，右键单击“VM”>“打开控制台”。
2. 提供设备的语言、时区和密码。
3. 在任何可以连接到 VM 的计算机上打开浏览器，并打开设备 web 应用的 URL： **https://*设备名称或 IP 地址*： 44368**。

   或者，可以在设备桌面上单击应用快捷方式打开该应用。
4. 在 Web 应用 >“设置必备组件”中执行以下操作：
    - **许可证**：接受许可条款，并阅读第三方信息。
    - **连接**：应用检查 VM 是否可以访问 internet。 如果 VM 使用代理：
        - 单击“代理设置”，并以 http://ProxyIPAddress 或 http://ProxyFQDN 格式指定代理地址和侦听端口。
        - 如果代理需要身份验证，请指定凭据。
        - 仅支持 HTTP 代理。
    - **时间同步**：时间已验证。 设备上的时间应与 internet 时间同步，以使发现正常工作。
    - **安装更新**： Azure Migrate 检查是否安装了最新的设备更新。
    - **安装 VDDK**： Azure Migrate 检查是否安装了 VMWare VSphere 虚拟磁盘开发工具包（VDDK）。
        - Azure 迁移使用 VDDK 在迁移到 Azure 的过程中复制计算机。
        - 从 VMware 下载 VDDK 6.7，并将下载的 zip 内容解压缩到设备上的指定位置。

## <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 单击“登录”。 如果未显示该按钮，请确保已在浏览器中禁用弹出窗口阻止程序。
2. 在新的标签页中，使用 Azure 凭据登录。
    - 使用用户名和密码登录。
    - 不支持使用 PIN 登录。
3. 成功登录后，返回到 Web 应用。
2. 选择在其中创建了 Azure Migrate 项目的订阅。 然后选择该项目。
3. 指定设备的名称。 该名称应是字母数字，长度为 14 个或更少的字符。
4. 单击“注册”。


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>提供 vCenter Server 和 VM 凭据，开始连续发现

设备需连接 vCenter Server，以发现 VM 的配置和性能数据。

### <a name="specify-vcenter-server-details"></a>指定 vCenter Server 详细信息
1. 在“指定 vCenter Server 详细信息”中，指定 vCenter Server 的名称 (FQDN) 或 IP 地址。 可以保留默认端口，或指定 vCenter Server 侦听的自定义端口。
2. 在“用户名”和“密码”中，指定设备用来发现 vCenter Server 上的 VM 的只读帐户凭据。 可以通过相应地限制对 vCenter 帐户的访问，来限定发现范围；在[此处](tutorial-assess-vmware.md#set-the-scope-of-discovery)详细了解如何限定发现范围。
3. 单击“验证连接”，确保设备可以连接到 vCenter Server。

### <a name="specify-vm-credentials"></a>指定 VM 凭据
为发现应用程序、角色和功能并可视化 VM 的依赖关系，可以提供可访问 VMware VM 的 VM 凭据。 可以分别为 Windows VM 和 Linux VM 添加一个凭据。 [详细了解](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions)所需的访问特权。

> [!NOTE]
> 此输入是可选的，但需要此输入才能发现应用程序和可视化无代理依赖关系。

1. 在“发现应用程序和 VM 依赖关系”上，单击“添加凭据”。
2. 选择“操作系统”。
3. 提供凭据的易记名称。
4. 在“用户名”和“密码”中，指定在 VM 上至少具有来宾访问权限的帐户。
5. 单击“添加”。

指定 vCenter Server 和 VM 凭据（可选）后，单击“保存并开始发现”，开始发现本地环境。

大约 15 分钟后，已发现的 VM 的元数据将显示在门户中。 发现已安装的应用程序、角色和功能需要一些时间，具体时间取决于待发现的 VM 数量。 如果是 500 个 VM，Azure Migrate 门户大约需要 1 小时才会显示应用程序清单。

## <a name="next-steps"></a>后续步骤

查看[VMware 评估](tutorial-assess-vmware.md)和[无代理迁移](tutorial-migrate-vmware.md)教程。
