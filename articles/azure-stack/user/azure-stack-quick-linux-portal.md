---
title: Azure Stack 快速入门 - 创建 VM 门户
description: Azure Stack 快速入门 - 使用门户创建 Linux VM
services: azure-stack
cloud: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 2ea07f04d4c566c0add39d75cad3d3a4ed81c6c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>快速入门： 使用 Azure 堆栈门户创建 Linux 服务器虚拟机

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

你可以通过 Azure 堆栈门户创建 Ubuntu Server 16.04 LTS 虚拟机。 按照这篇文章创建和使用虚拟机中的步骤。 这篇文章还为你提供的步骤：

* 连接到远程客户端的虚拟机。
* 安装 NGINX web 服务器。
* 清理你的资源。

## <a name="prerequisites"></a>必备组件

* **Azure Stack Marketplace 中的 Linux 映像**

   默认情况下，Azure Stack Marketplace 不包含 Linux 映像。 你可以创建 Linux 服务器虚拟机之前，请确保 Azure 堆栈运算符提供**Ubuntu Server 16.04 LTS**所需的映像。 运算符可以使用中所述的步骤[从 Azure 应用商店项下载到 Azure 堆栈](../azure-stack-download-azure-marketplace-item.md)文章。

* **可以访问 SSH 客户端**

   如果你使用的 Azure 堆栈开发工具包 (ASDK)，你可能没有对 SSH 客户端的访问。 如果你需要的客户端，有几个包中包括的 SSH 客户端。 例如，PuTTY 包括 SSH 客户端和 SSH 密钥生成器 (puttygen.exe)。 有关可用的包的详细信息，请阅读以下 Azure 文章：[如何使用 SSH 密钥与在 Azure 上的 Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients)。

   本快速入门教程使用 PuTTY，用于生成 SSH 密钥并连接到 Linux 服务器虚拟机。 若要下载并安装 PuTTY，请转到 [http://www.putty.org/](http://www.putty.org)。

## <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对

你需要的 SSH 密钥对，若要完成本文中的所有步骤。 如果你有现有的 SSH 密钥对，则可以跳过此步骤。

1. 导航到 PuTTY 安装文件夹（默认位置为 ```C:\Program Files\PuTTY```）并运行 ```puttygen.exe```。
2. 在“PuTTY 密钥生成器”窗口中，确保“要生成的密钥类型”已设置为 **RSA**，并且“所生成密钥中的位数”已设置为 **2048**。 当准备好后时，单击**生成**。

   ![PuTTY 密钥生成器配置](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. 若要生成密钥，将鼠标光标随机 PuTTY 密钥生成器窗口中的某个移动。
4. 在密钥生成完成后，单击**保存公钥**，然后单击**保存私钥**将你的密钥保存到文件。

   ![PuTTY 密钥生成器结果](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>登录到 Azure Stack 门户

登录到 Azure Stack 门户。 Azure Stack 门户的地址取决于连接到的 Azure Stack 产品：

* 对于 Azure Stack 开发工具包 (ASDK)，请转到：https://portal.local.azurestack.external。
* 对于 Azure Stack 集成系统，请转到 Azure Stack 运营商提供的 URL。

## <a name="create-the-virtual-machine"></a>创建虚拟机

1. 在 Azure Stack 门户的左上角单击“创建资源”。

2. 选择“计算”，然后选择“Ubuntu Server 16.04 LTS”。
3. 单击“创建”。

4. 键入虚拟机信息。 对于“身份验证类型”，请选择“SSH 公钥”。 粘贴的 SSH 公钥，保存，然后单击**确定**。

   >[!NOTE]
 请确保它们密钥为删除的所有前导或尾随空格。

   ![基础知识面板-配置虚拟机](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. 为虚拟机选择“D1_V2”。

   ![Size 面板-选择虚拟机大小](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. 在“设置”页上保留默认值，然后单击“确定”。

7. 在“摘要”页上，单击“确定”开始部署虚拟机。

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

1. 在虚拟机页上，单击“连接”。 此时将显示你需要连接到虚拟机的 SSH 连接字符串。

   ![连接虚拟机](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. 打开 PuTTY。
3. 上**PuTTY 配置**屏幕将使用**类别**向上或向下滚动的窗口。 向下滚动到**SSH**，展开**SSH**，然后单击**身份验证**。单击**浏览**和选取你保存的私钥文件。

   ![选择 PuTTY 私钥](media/azure-stack-quick-linux-portal/Putty03.PNG)

4. 在上滚**类别**窗口，，然后单击**会话**。
5. 在**主机名 （或 IP 地址）**框中，粘贴 Azure 堆栈门户中显示的连接字符串。 在本示例中，该字符串为 ```asadmin@192.168.102.34```。

   ![PuTTY 配置连接字符串](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. 单击**打开**以打开虚拟机的会话。

   ![Linux 会话](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>安装 NGINX web 服务器

使用以下 bash 命令来更新包源并在虚拟机上安装最新的 NGINX 包。

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

当你完成安装 NGINX 时，关闭 SSH 会话并打开在 Azure 堆栈门户的虚拟机概述页。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

网络安全组 (NSG) 保护入站和出站流量的安全。 在 Azure 堆栈门户中创建虚拟机后，SSH 连接的端口 22 上创建的入站的规则。 因为此虚拟机承载的 web 服务器，NSG 规则将需要创建允许在端口 80 上的 web 流量。

1. 在虚拟机的“概述”页上，单击**资源组**的名称。
2. 选择虚拟机的**网络安全组**。 可以通过“类型”列来标识 NSG。
3. 在左侧菜单的“设置”下，单击“入站安全规则”。
4. 单击 **“添加”**。
5. 在“名称”中，键入 **http**。 请确保将“端口范围”设置为 80，将“操作”设置为“允许”。
6. 单击 **“确定”**

## <a name="view-the-nginx-welcome-page"></a>查看 NGINX 欢迎页

使用安装，NGINX 和端口 80 打开虚拟机上，你可以访问 web 服务器上使用虚拟机的公共 IP 地址。 （公共 IP 地址显示在虚拟机的概述页中）。

打开 Web 浏览器，并浏览到 ```http://<public IP address>```。

![NGINX web 服务器欢迎页](media/azure-stack-quick-linux-portal/linux-04.PNG)

## <a name="clean-up-resources"></a>清理资源

清理不不再需要的资源。 若要删除虚拟机和其资源，请选择虚拟机页上的资源组，然后单击**删除**。

## <a name="next-steps"></a>后续步骤

在此快速入门中，你将部署基本 Linux 服务器虚拟机与 web 服务器。 有关 Azure Stack 虚拟机的详细信息，请转到 [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)。
