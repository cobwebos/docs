---
title: "Azure 堆栈快速入门-创建虚拟机门户"
description: "Azure 堆栈快速启动-创建 Linux VM 使用门户"
services: azure-stack
cloud: azure-stack
author: vhorne
manager: byronr
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a97372002052570ccad2bb8bc442eb0dd1ca76d9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-linux-virtual-machine-with-the-azure-stack-portal"></a>使用 Azure 堆栈门户创建 Linux 虚拟机

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

可以通过 Azure 堆栈门户创建 azure 堆栈的虚拟机。 此方法提供了一个基于浏览器的用户界面来创建和配置虚拟机和所有相关资源。 本快速入门演示如何快速创建 Linux 虚拟机并在其上安装 web 服务器。

## <a name="prerequisites"></a>必备组件

* **Azure 堆栈应用商店中的 Linux 映像**

   默认情况下，Azure 堆栈应用商店不包含 Linux 映像。 因此，你可以创建 Linux 虚拟机之前，请确保已下载 Azure 堆栈运算符**Ubuntu Server 16.04 LTS**使用中所述的步骤的映像[下载 Azure 到 Azure 的应用商店项堆栈](../azure-stack-download-azure-marketplace-item.md)主题。

* **SSH 客户端访问**

   如果你使用的 Azure 堆栈开发工具包 (ASDK)，你可能没有对 SSH 客户端访问你的环境中。 如果出现这种情况，你可以选择包括 SSH 客户端的多个包。 例如，你可以安装包括 SSH 客户端和 SSH 密钥生成器 (puttygen.exe) 的 PuTTY。 有关可能的选项的详细信息，请参阅以下相关 Azure 文章：[如何使用 SSH 密钥与在 Azure 上的 Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients)。

   本快速入门教程使用 PuTTY，用于生成 SSH 密钥并连接到 Linux 虚拟机。 若要下载并安装 PuTTY，请转到[http://www.putty.org/](http://www.putty.org)。

## <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对

你需要的 SSH 密钥对，若要完成本快速入门教程。 如果有现成的 SSH 密钥对，则可跳过此步骤。

1. 导航到 PuTTY 安装文件夹 (默认位置是```C:\Program Files\PuTTY```) 并运行```puttygen.exe```。
2. 在 PuTTY 密钥生成器窗口中，确保**要生成的密钥类型**设置为**RSA**，和**中生成的密钥的位数**设置为**2048**. 完成后，单击**生成**。

   ![puttygen.exe](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. 若要完成密钥生成过程，请在 PuTTY 密钥生成器窗口内移动鼠标光标。
4. 密钥生成完成后，单击**保存公钥**和**保存私钥**将保存到文件的公共和私有密钥。

   ![PuTTY 密钥](media/azure-stack-quick-linux-portal/Putty02.PNG)



## <a name="sign-in-to-the-azure-stack-portal"></a>登录到 Azure 堆栈门户

登录到 Azure 堆栈门户。 Azure 堆栈门户的地址取决于您连接到哪种 Azure 堆栈产品：

* 有关 Azure 堆栈开发工具包 (ASDK) 转到： https://portal.local.azurestack.external。
* 对于 Azure 堆栈集成系统，转至 Azure 堆栈运算符提供的 URL。

## <a name="create-the-virtual-machine"></a>创建虚拟机

1. 单击**新建**按钮位于 Azure 堆栈门户左上角。

2. 选择“计算”，然后选择“Ubuntu Server 16.04 LTS”。
3. 单击“创建” 。

4. 键入虚拟机信息。 对于“身份验证类型”，请选择“SSH 公钥”。 在你将粘贴你 SSH 公钥 （它以前保存到文件） 中，注意若要删除的所有前导或尾随空格。 完成后，单击“确定”。

   ![虚拟机基础知识](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. 选择**D1_V2**为虚拟机。

   ![虚拟机大小](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. 上**设置**页上，保留默认值，单击**确定**。

7. 上**摘要**页上，单击**确定**启动虚拟机部署。


## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

1. 单击**连接**在虚拟机页上。 此时将显示一个可以用于连接到虚拟机的 SSH 连接字符串。

   ![将虚拟机连接](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. 打开 PuTTY。
3. 上**PuTTY 配置**屏幕上，在**类别**，展开**SSH** ，然后单击**身份验证**。单击**浏览**，然后选择以前保存的私钥文件。

   ![PuTTY 私钥](media/azure-stack-quick-linux-portal/Putty03.PNG)
4. 下**类别**、 向上滚动并单击**会话**。
5. 在**主机名 （或 IP 地址）**框中，粘贴您前面看到的 Azure 堆栈门户中的连接字符串。 在此示例中，该字符串是```asadmin@192.168.102.34```。
 
   ![PuTTY 会话](media/azure-stack-quick-linux-portal/Putty04.PNG)
6. 单击**打开**以打开虚拟机的会话。

   ![Linus 会话](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-nginx"></a>安装 NGINX

使用以下 bash 脚本以更新包源和虚拟机上安装最新的 NGINX 程序包。 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完成后，退出 SSH 会话，在 Azure 堆栈门户中返回虚拟机的概述页。


## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80 

网络安全组 (NSG) 保护入站和出站流量的安全。 从 Azure 堆栈门户创建虚拟机时，SSH 连接的端口 22 上创建入站的规则。 因为此虚拟机承载的 web 服务器，需要为端口 80 创建 NSG 规则。

1. 虚拟机上**概述**页上，单击名称**资源组**。
2. 选择**网络安全组**为虚拟机。 可以通过“类型”列来标识 NSG。 
3. 在左侧菜单上，在**设置**，单击**入站安全规则**。
4. 单击“添加”。
5. 在“名称”中，键入 **http**。 请确保将“端口范围”设置为 80，将“操作”设置为“允许”。 
6. 单击 **“确定”**。


## <a name="view-the-nginx-welcome-page"></a>查看 NGINX 欢迎页

使用 NGINX 安装，和端口 80 在你的虚拟机上打开，web 服务器现在可以访问在虚拟机的公共 IP 地址。 可以在 Azure 堆栈门户的虚拟机的概述页上找到的公共 IP 地址。

打开 web 浏览器，并浏览到```http://<public IP address>```。

![NGINX 默认站点](media/azure-stack-quick-linux-portal/linux-04.PNG)


## <a name="clean-up-resources"></a>清理资源

不再需要资源组、虚拟机和所有相关的资源时，可将其删除。 为此，请从虚拟机页上选择资源组，然后单击**删除**。

## <a name="next-steps"></a>后续步骤

在此快速入门中，您已部署简单的 Linux 虚拟机，网络安全组规则，并安装 web 服务器。 若要了解有关 Azure 堆栈的虚拟机的详细信息，继续到[Azure 堆栈中的虚拟机的注意事项](azure-stack-vm-considerations.md)。

