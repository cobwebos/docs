---
title: 一台设备的 Azure 迁移服务器评估/迁移为 VMware Vm 设置 |Microsoft Docs
description: 介绍如何设置用于发现、 评估和使用 Azure 迁移服务器评估/迁移 VMware Vm 迁移到无代理的设备。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: fe190381df346278e75a3e6fd9876b80c33bd86b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811721"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>为 VMware Vm 设置一台设备

本文介绍如何设置 Azure Migrate 设备，如果要使用 Azure Migrate Server 评估工具评估 VMware Vm 或使用 Azure 迁移 Server 迁移工具的无代理迁移 VMware Vm 迁移到 Azure。

VMware VM 设备是一个轻量设备，通过 Azure 迁移服务器评估/迁移用于执行以下操作：

- 发现本地 VMware VM。
- 发送发现的 Vm 到 Azure 迁移服务器评估/迁移元数据和性能数据。

[了解详细信息](migrate-appliance.md)有关 Azure Migrate 设备。


## <a name="appliance-deployment-steps"></a>设备的部署步骤

若要设置设备您：
- 下载.OVA 模板文件，并将其导入 vCenter 服务器。
- 创建设备，并检查它能够连接到 Azure Migrate Server 评估。 
- 将设备配置为第一次，并将其注册到 Azure Migrate 项目。

## <a name="download-the-ova-template"></a>下载 OVA 模板

1. 在中**迁移目标** > **服务器** > **Azure 迁移：Server 评估**，单击**发现**。
2. 在中**发现计算机** > **计算机虚拟化是否？** ，单击**是，使用 VMWare vSphere 虚拟机监控程序**。
3. 单击**下载**下载.OVA 模板文件。



### <a name="verify-security"></a>验证安全性

检查之前将其部署.OVA 文件是安全的。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令，以生成 ova 的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 为设备版本 1.0.0.5，生成的哈希应匹配这些设置。 

  **算法** | **哈希值**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>创建设备 VM

导入下载的文件，并创建 VM。

1. 在 vSphere 客户端控制台中，单击“文件”   > “部署 OVF 模板”  。
2. 在“部署 OVF 模板向导”>“源”  中，指定 OVA 文件的位置。
3. 在中**名称**并**位置**，指定虚拟机的友好名称。 选择将在其中托管 VM 的清单对象。
5. 在中**主机/群集**、 指定的主机或群集 VM 的运行。
6. 在中**存储**，指定 VM 的存储目标。
7. 在“磁盘格式”  中，指定磁盘类型和大小。
8. 在中**网络映射**，指定 VM 将连接到网络。 网络需要 internet 连接，将发送到 Azure Migrate Server 评估的元数据。
9. 检查并确认设置，然后单击“完成”  。


### <a name="verify-appliance-access-to-azure"></a>验证设备到 Azure 的访问权限

请确保设备 VM 可以连接到[Azure Url](migrate-support-matrix-vmware.md#assessment-url-access-requirements)。


## <a name="configure-the-appliance"></a>配置设备

第一次设置设备。

1. 在 vSphere 客户端控制台中，右键单击“VM”>“打开控制台”  。
2. 为设备提供的语言、 时区和密码。
3. 打开浏览器可以连接到 VM，并打开设备的 web 应用的 URL 的任何计算机上： **https://*设备名称或 IP 地址*:44368**.

   或者，可以通过单击应用快捷方式从设备桌面打开应用。
4. 在 web 应用 >**设置先决条件**，执行以下操作：
    - **许可证**:接受许可条款，并阅读第三方信息。
    - **连接**:应用会检查 VM 具有 internet 访问权限。 如果 VM 使用代理：
        - 单击**代理设置**，并在窗体中指定的代理地址和侦听端口 http://ProxyIPAddress 或 http://ProxyFQDN 。
        - 如果代理需要身份验证，请指定凭据。
        - 仅支持 HTTP 代理。
    - **时间同步**:验证时间。 在设备上的时间应与发现正常工作的 internet 时间同步。
    - **安装更新**:Azure Migrate 检查已安装最新的设备更新。
    - **安装 VDDK**:Azure Migrate 检查安装了 VMWare vSphere 虚拟磁盘开发工具包 (VDDK)。
        - Azure Migrates 使用 VDDK 来将计算机复制到 Azure 的迁移过程。
        - 从 VMware、 下载 VDDK 6.7 和下载的 zip 将内容提取到该设备上的指定位置。

## <a name="register-the-appliance-with-azure-migrate"></a>使用 Azure Migrate 中注册设备

1. 单击**登录**。 如果未显示，请确保已禁用弹出窗口阻止程序在浏览器中。
2. 在新的选项卡上注册使用 Azure 凭据。 
    - 使用你的用户名和密码登录。
    - 不支持使用 PIN 登录。
3. 已成功登录后，请返回到 web 应用。
2. 选择在其中创建 Azure Migrate 项目的订阅。 然后选择的项目。
3. 指定设备的名称。 名称应包含 14 个字符的字母数字或更少。
4. 单击“注册”  。


## <a name="start-continuous-discovery"></a>启动持续发现

现在，从设备连接到 vCenter 服务器，并启动 VM 发现。 

1. 在中**指定 vCenter Server 详细信息**，指定的名称 (FQDN) 或 vCenter 服务器的 IP 地址。 您可以保留默认端口，或指定 vCenter 服务器在其侦听的自定义端口。
2. 在中**用户名**并**密码**，指定该设备将用于发现 vCenter 服务器上的 Vm 的只读帐户凭据。 请确保帐户具有[针对发现所需权限](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions)。
3. 单击**验证连接**以确保设备可以连接到 vCenter 服务器。
4. 建立连接后，单击**保存并启动发现**。


这将启动发现。 花费大约 15 分钟，发现的 Vm 的元数据显示在门户中。 


## <a name="next-steps"></a>后续步骤

查看教程： [VMware 评估](tutorial-assess-vmware.md)并[无代理迁移](tutorial-migrate-vmware.md)。
