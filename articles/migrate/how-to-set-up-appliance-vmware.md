---
title: 为 VMware 设置 Azure Migrate 设备
description: 了解如何设置 Azure Migrate 设备来评估和迁移 VMware Vm。
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 272481640db739a458a19a2c9383ff45b54974b5
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86112810"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>为 VMware Vm 设置设备

按照本文所述，使用[Azure Migrate： Server 评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具设置 Azure Migrate 设备进行评估，并使用[Azure Migrate： server 迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具进行无代理迁移。

[Azure Migrate 设备](migrate-appliance.md)是一种轻型设备，由 Azure Migrate：服务器评估和服务器迁移，用于发现本地 VMware vm、将 VM 元数据/性能数据发送到 Azure，以及在无代理迁移期间复制 VMware vm。

可以使用几种方法来部署设备：

- 使用下载的 OVA 模板在 VMware VM 上进行设置。 这是本文中所述的方法。
- 使用 PowerShell 安装程序脚本在 VMware VM 或物理计算机上进行设置。 如果无法使用 OVA 模板设置 VM，或者你使用的是 Azure 政府，则应使用[此方法](deploy-appliance-script.md)。

创建设备后，请检查它是否可以连接到 Azure Migrate:服务器评估，首次配置该设备，并将其注册到 Azure Migrate 项目。


## <a name="appliance-deployment-ova"></a>设备部署（.OVA）

若要使用 .OVA 模板设置设备，请执行以下操作：
- 下载 OVA 模板文件，并将其导入 vCenter Server。
- 创建设备，并检查它是否可以连接到 Azure Migrate 服务器评估。
- 完成设备的首次配置，并将其注册到 Azure Migrate 项目。

## <a name="download-the-ova-template"></a>下载 OVA 模板

1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器评估”中，单击“发现”。
2. 在“发现计算机” > “计算机是否已虚拟化?”中，单击“是，使用 VMWare vSphere 虚拟机监控程序”。  
3. 单击“下载”以下载 .OVA 模板文件。****

  ![用于下载 OVA 文件的选项](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>验证安全性

在部署 .OVA 文件之前请检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令，生成 .OVA 的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 对于最新的设备版本，生成的哈希应与这些[设置](./tutorial-assess-vmware.md#verify-security)相符。



## <a name="create-the-appliance-vm"></a>创建设备 VM

导入下载的文件，然后创建 VM。

1. 在 vSphere 客户端控制台中，单击 "**文件**" "  >  **部署 OVF 模板**"。
![用于部署 OVF 模板的菜单命令](./media/tutorial-assess-vmware/deploy-ovf.png)

2. 在“部署 OVF 模板向导”>“源”中，指定 OVA 文件的位置。
3. 在“名称”和“位置”中，为 VM 指定一个易记名称。  选择要在其中托管 VM 的库存对象。
5. 在“主机/群集”中，指定要在其上运行 VM 的主机或群集。
6. 在“存储”中，指定 VM 的存储目标。
7. 在“磁盘格式”中，指定磁盘类型和大小。
8. 在 "**网络映射**" 中，指定 VM 将连接到的网络。 该网络需要与 Internet 建立连接，这样才能向 Azure Migrate 服务器评估发送元数据。
9. 检查并确认设置，然后单击“完成”****。


## <a name="verify-appliance-access-to-azure"></a>验证设备的 Azure 访问权限

确保设备 VM 可以连接到[公有云](migrate-appliance.md#public-cloud-urls)和[政府云](migrate-appliance.md#government-cloud-urls)的 Azure URL。


## <a name="configure-the-appliance"></a>配置设备

首次设置设备。 如果使用脚本而不是 .OVA 模板部署设备，则过程中的前两个步骤不适用。

1. 在 vSphere 客户端控制台中，右键单击“VM”>“打开控制台”****。
2. 提供设备的语言、时区和密码。
3. 在可连接到该 VM 的任一计算机上打开浏览器，然后打开设备 Web 应用的 URL：**https://*设备名称或 IP 地址*:** 44368。

   或者，可以在设备桌面上单击应用快捷方式打开该应用。
4. 在 Web 应用 >“设置必备组件”中执行以下操作：
    - **许可证**：接受许可条款，并阅读第三方信息。
    - **连接**：应用将检查 VM 是否可访问 Internet。 如果 VM 使用代理：
        - 单击“代理设置”，并以 http://ProxyIPAddress 或 http://ProxyFQDN 格式指定代理地址和侦听端口。
        - 如果代理需要身份验证，请指定凭据。
        - 仅支持 HTTP 代理。
    - **时间同步**：将验证时间。 设备上的时间应与 Internet 时间同步，这样才能正常进行发现。
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
1. 在“指定 vCenter Server 详细信息”中，指定 vCenter Server 的名称 (FQDN) 或 IP 地址。**** 可以保留默认端口，或指定 vCenter Server 侦听的自定义端口。
2. 在“用户名”和“密码”中，指定设备用来发现 vCenter Server 上的 VM 的只读帐户凭据。******** 可以通过限制对 vCenter 帐户的访问，来限定发现范围。 [了解详细信息](set-discovery-scope.md)。
3. 单击“验证连接”，确保设备可以连接到 vCenter Server。****

### <a name="specify-vm-credentials"></a>指定 VM 凭据
为发现应用程序、角色和功能并可视化 VM 的依赖关系，可以提供可访问 VMware VM 的 VM 凭据。 可以分别为 Windows VM 和 Linux VM 添加一个凭据。 [详细了解](./migrate-support-matrix-vmware.md)所需的访问特权。

> [!NOTE]
> 此输入是可选的，但需要此输入才能发现应用程序和可视化无代理依赖关系。

1. 在“发现应用程序和 VM 依赖关系”上，单击“添加凭据”********。
2. 选择“操作系统”****。
3. 提供凭据的易记名称。
4. 在“用户名”和“密码”中，指定在 VM 上至少具有来宾访问权限的帐户********。
5. 单击“添加”。

指定 vCenter Server 和 VM 凭据（可选）后，单击“保存并开始发现”，开始发现本地环境****。

大约 15 分钟后，已发现的 VM 的元数据将显示在门户中。 发现已安装的应用程序、角色和功能需要一些时间，具体时间取决于待发现的 VM 数量。 如果是 500 个 VM，Azure Migrate 门户大约需要 1 小时才会显示应用程序清单。

## <a name="next-steps"></a>后续步骤

查看[VMware 评估](tutorial-assess-vmware.md)和[无代理迁移](tutorial-migrate-vmware.md)教程。
