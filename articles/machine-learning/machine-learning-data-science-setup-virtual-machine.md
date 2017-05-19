---
title: "将虚拟机设置为 IPython Notebook 服务器 | Microsoft Docs"
description: "使用适用于高级分析的 IPython 服务器设置 Azure 虚拟机以供在数据科学环境中使用。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 818617c1-048e-49c2-b941-f9a983f93998
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: xibingao;bradsev
ms.translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5fb930cb71fe008ba63d2068bb36643f40259e76
ms.contentlocale: zh-cn
ms.lasthandoff: 03/29/2017


---
# <a name="set-up-an-azure-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>将 Azure 虚拟机设置为用于高级分析的 IPython Notebook 服务器
本主题介绍如何针对高级分析预配和配置 Azure 虚拟机以便可用作数据科学环境的一部分。 Windows 虚拟机使用支持工具（例如 IPython Notebook、Azure 存储资源管理器、AzCopy 以及其他可用于高级分析项目的实用程序）进行配置。 例如，Azure 存储资源管理器和 AzCopy 提供了从本地计算机将数据上载到 Azure blob 存储或从 blob 存储下载到本地计算机的便捷方法。

## <a name="create-vm"></a>步骤 1：创建通用 Azure 虚拟机
如果已具有 Azure 虚拟机，而且只想要在其上设置 IPython Notebook 服务器，可以跳过此步骤，转到[步骤 2：将 IPython Notebook 终结点添加到现有虚拟机](#add-endpoint)。

开始在 Azure 上创建虚拟机之前，需要确定处理其项目数据所需的虚拟机大小。 与较大虚拟机相比，较小虚拟机的内存更少并且 CPU 内核更少，不过其成本也较低。 有关虚拟机类型和价格列表，请参阅<a href="http://azure.microsoft.com/pricing/details/virtual-machines/" target="_blank">虚拟机定价</a>页

1. 登录到 <a href="https://manage.windowsazure.com" target="_blank">Azure 经典门户</a>，然后单击左下角的“新建”。 将弹出一个窗口。 依次选择“计算” -> “虚拟机” -> “从库”。
   
    ![创建工作区][24]
2. 选择以下映像之一：
   
   * Windows Server 2012 R2 Datacenter
   * Windows Server Essentials 体验 (Windows Server 2012 R2)
     
     然后单击右下角向右箭头，转到下一配置页。
     
     ![创建工作区][25]
3. 输入要创建的虚拟机名称，基于虚拟机将要处理的数据大小和希望虚拟机有多强大（内存大小和计算内核数目）选择虚拟机大小（默认值：A3），输入虚拟机的用户名和密码。 然后，单击向右箭头转到下一个配置页。
   
    ![创建工作区][26]
4. 选择“区域/地缘组/虚拟网络”，其中包含计划用于此虚拟机的“存储帐户”，然后选择该存储帐户。 通过输入终结点名称（此处为“IPython”），在“终结点”字段的底部添加终结点。 可以选择任何字符串作为终结点的“名称”，并选择任何介于 0 和 65536 之间的**可用**整数作为“公用端口”。 “专用端口”必须是 **9999**。 应**避免**使用已分配给 Internet 服务的公用端口。 <a href="http://www.chebucto.ns.ca/~rakerman/port-table.html" target="_blank">用于 Internet 服务的端口</a>提供已分配且应避免的端口列表。
   
    ![创建工作区][27]
5. 单击复选标记以启动虚拟机预配过程。
   
    ![创建工作区][28]

可能需要 15-25 分钟才能完成虚拟机预配过程。 创建虚拟机后，此虚拟机的状态应显示为“正在运行”。

![创建工作区][29]

## <a name="add-endpoint"></a>步骤 2：将 IPython Notebook 的终结点添加到现有虚拟机
如果已按照步骤 1 中的说明创建虚拟机，则已经添加 IPython Notebook 的终结点，可跳过此步骤。

如果虚拟机已存在，并且需要添加 IPython Notebook 的终结点（它将在下面的步骤 3 中进行安装），则首先登录到 Azure 经典门户、选择虚拟机，然后添加 IPython Notebook 服务器的终结点。 下图包含在将 Python Notebook 的终结点添加到 Windows 虚拟机后的门户屏幕截图。

![创建工作区][17]

## <a name="run-commands"></a>步骤 3：安装 IPython Notebook 和其他支持工具
创建虚拟机后，使用远程桌面协议 (RDP) 登录到 Windows 虚拟机。 有关说明，请参阅[如何登录到运行 Windows Server 的虚拟机](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 以**管理员身份**打开**命令提示符**（**而非 Powershell 命令窗口**），然后运行以下命令。

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

安装完成后，IPython Notebook 服务器将自动在 *C:\\Users\\\<user name\>\\Documents\\IPython Notebooks* 目录中启动。

当系统出现提示时，输入 IPython Notebook 的密码和虚拟机管理员的密码。 这样一来，IPython Notebook 便可以在虚拟机上作为服务运行。

## <a name="access"></a>步骤 4：从 Web 浏览器访问 IPython Notebook
若要访问 IPython Notebook 服务器，请打开 Web 浏览器，并在 URL 文本框中输入 *https://&#60;虚拟机 DNS 名称>:&#60;公用端口号>*。 此处 *&#60;公用端口号>* 应该是添加 IPython Notebook 终结点时所指定的端口号。

*&#60;虚拟机 DNS 名称>* 可以在 Azure 经典门户中找到。 登录到经典门户后，单击“虚拟机”，选择所创建的虚拟机，然后选择“仪表板”，随后 DNS 名称将如下所示：

![创建工作区][19]

将出现一条警告，指出*“此网站的安全证书有问题”*(Internet Explorer) 或*“你的连接不是专用的”*(Chrome)，如下图所示。 单击“继续浏览此网站(不推荐)”(Internet Explorer)，或者依次单击“高级”和“继续浏览 &#60;*DNS 名称*> (不安全)”****(Chrome) 以继续。 然后，输入之前指定的密码来访问 IPython Notebook。

**Internet Explorer：**
![创建工作区][20]

**Chrome：**
![创建工作区][21]

登录到 IPython Notebook 后，目录 *DataScienceSamples* 将显示在浏览器上。 此目录包含 Microsoft 共享的示例 IPython Notebook，以帮助用户执行数据科学任务。 这些示例 IPython Notebook 是在 IPython Notebook 服务器设置期间从 [**GitHub 存储库**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)检出到虚拟机的。 Microsoft 会经常维护并更新此存储库。 用户可以访问 GitHub 存储库来获取最近更新的示例 IPython Notebook。
![创建工作区][18]

## <a name="upload"></a>步骤 5：将现有 IPython Notebook 从本地计算机上载到 IPython Notebook 服务器
IPython Notebook 提供一种简单的方式，让用户可以将其本地计算机上的现有 IPython Notebook 上载到虚拟机上的 IPython Notebook 服务器。 在通过 Web 浏览器登录到 IPython Notebook 后，单击 IPython Notebook 将上传到的**目录**。 然后，在“文件资源管理器”中选择要从本地计算机上载的 IPython Notebook .ipynb 文件，并将其拖放到 Web 浏览器上的 IPython Notebook 目录。 单击“上载”按钮，将 .ipynb 文件上载到 IPython Notebook 服务器。 其他用户就可以从其 Web 浏览器开始使用它。

![创建工作区][22]

![创建工作区][23]

## <a name="shutdown"></a>关闭并解除分配未使用的虚拟机
Azure 虚拟机定价为**只为自己使用的东西付费**。 若要确保未使用虚拟机时不会计费，当虚拟机未使用时，其必须处于“已停止(已解除分配)”状态。

> [!NOTE]
> 如果从 VM 关闭虚拟机（使用 Windows 电源选项），则 VM 已停止，但仍处于分配状态。 若要确保不继续被计费，请始终从 [Azure 经典门户](http://manage.windowsazure.com/)停止虚拟机。 也可以通过 Powershell 停止 VM，方法是调用 **ShutdownRoleOperation** 并使“PostShutdownAction”等于“StoppedDeallocated”。
> 
> 

若要关闭并解除分配虚拟机：

1. 使用帐户登录到 [Azure 经典门户](http://manage.windowsazure.com/)。  
2. 从左侧导航栏选择“虚拟机”。
3. 在虚拟机列表中，单击虚拟机的名称，然后转到“仪表板”页。
4. 在页面底部，单击“关闭”。

![VM 关闭][15]

虚拟机将解除分配，但不会删除。 可以随时从 Azure 经典门户重新启动虚拟机。

## <a name="your-azure-vm-is-ready-to-use-whats-next"></a>Azure VM 可随时使用：后续步骤是什么？
现在，虚拟机可随时在数据科学练习中使用。 虚拟机还可以用作 IPython Notebook 服务器来浏览和处理数据，以及其他可与 Azure 机器学习和团队数据科学过程一起执行的任务。

团队数据科学过程中的后续步骤映射到[学习路径](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)中，可能包括将数据移动到 HDInsight、处理并在其中进行采样的步骤，以便为使用 Azure 机器学习来了解数据做好准备。

[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png

