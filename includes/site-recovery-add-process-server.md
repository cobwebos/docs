---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: c9a0d4387511bbfa033bcb90d9f83e1a7bb39719
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "67172655"
---
1. 启动 Azure Site Recovery UnifiedSetup.exe
2. 在**开始之前**，选择 **"添加其他进程服务器以扩展部署**"。

   ![添加进程服务器](./media/site-recovery-add-process-server/ps-page-1.png)

3. 在**配置服务器详细信息**中，指定配置服务器的 IP 地址和密码。

   ![添加进程服务器 2](./media/site-recovery-add-process-server/ps-page-2.png)
4. 在**Internet 设置**中，指定在配置服务器上运行的提供程序如何通过 Internet 连接到 Azure 站点恢复。

   ![添加进程服务器 3](./media/site-recovery-add-process-server/ps-page-3.png)

   * 如果要与计算机上当前设置的代理连接，请选择"**连接现有代理设置**"。
   * 如果希望提供程序直接连接，请选择 **"在没有代理的情况下直接连接**"。
   * 如果现有代理需要身份验证，或者要对提供程序连接使用自定义代理，请选择"**使用自定义代理设置连接**"。

     * 如果使用自定义代理，则需指定地址、端口和凭据。
     * 如果使用代理，应事先允许访问该服务 URL。

5. 在**先决条件检查**中，安装程序运行检查以确保安装可以运行。 如果出现有关**全局时间同步检查**的警告，请验证系统时钟上的时间（**日期和时间**设置）是否与时区相同。

     ![添加进程服务器 4](./media/site-recovery-add-process-server/ps-page-4.png)

6. 在 **"环境详细信息**"中，选择是否要复制 VMware VM。 如果要复制，则安装程序会检查 PowerCLI 6.0 是否已安装。

     ![添加进程服务器 5](./media/site-recovery-add-process-server/ps-page-5.png)

7. 在“安装位置”中，选择要安装二进制文件和存储缓存的位置。**** 所选驱动器必须至少有 5 GB 的可用磁盘空间，但我们建议选择至少有 600 GB 可用空间的缓存驱动器。
     ![添加进程服务器 5](./media/site-recovery-add-process-server/ps-page-6.png)

8. 在“网络选择”**** 中，指定侦听器（网络适配器和 SSL 端口），以便配置服务器在其上发送和接收复制数据。 端口 9443 是用于发送和接收复制流量的默认端口，但可以根据环境的要求修改此端口号。 除了端口 9443 以外，还要打开端口 443，Web 服务器将使用该端口协调复制操作。 请不要使用端口 443 来发送或接收复制流量。

     ![添加进程服务器 6](./media/site-recovery-add-process-server/ps-page-7.png)
9. 在**摘要**中，查看信息并单击"**安装**"。 安装完成后，将生成通行短语。 启用复制时需要用到它，因此请复制并将它保存在安全的位置。

     ![添加进程服务器 7](./media/site-recovery-add-process-server/ps-page-8.png)
