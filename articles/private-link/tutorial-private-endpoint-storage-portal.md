---
title: 教程：使用 Azure 专用终结点连接到存储帐户
titleSuffix: Azure Private Link
description: 开始使用 Azure 专用终结点以私密方式连接到存储帐户。
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: 69bee753c2134b6eebe9c5df0a554c965208ad7c
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91366204"
---
# <a name="tutorial-connect-to-a-storage-account-using-an-azure-private-endpoint"></a>教程：使用 Azure 专用终结点连接到存储帐户

Azure 专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络和 Bastion 主机。
> * 创建虚拟机。
> * 创建采用专用终结点的存储帐户。
> * 测试到存储帐户专用终结点的连接。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-virtual-network-and-bastion-host"></a>创建虚拟网络和堡垒主机

在本部分中，你将创建虚拟网络、子网和堡垒主机。 

堡垒主机将用于安全地连接到虚拟机，以测试专用终结点。

1. 在屏幕的左上方选择“创建资源”>“网络”>“虚拟网络”，或者在搜索框中搜索“虚拟网络”。  

2. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

    | **设置**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **项目详细信息**  |                                                                 |
    | 订阅     | 选择 Azure 订阅                                  |
    | 资源组   | 选择“myResourceGroup” |
    | **实例详细信息** |                                                                 |
    | 名称             | 输入“myVNet”                                    |
    | 区域           | 选择“美国东部” |

3. 选择“IP 地址”选项卡  ，或选择页面底部的“下一步:  IP 地址”按钮。

4. 在“IP 地址”  选项卡上，输入以下信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | IPv4 地址空间 | 输入“10.1.0.0/16” |

5. 在“子网名称”下，选择词语“默认”。

6. 在“编辑子网”中输入以下信息： 

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | 子网名称 | 输入 **mySubnet** |
    | 子网地址范围 | 输入“10.1.0.0/24” |

7. 选择“保存” 。

8. 选择“安全”**** 选项卡。

9. 在“BastionHost”下，选择“启用” 。 输入此信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | Bastion 名称 | 输入“myBastionHost” |
    | AzureBastionSubnet 地址空间 | 输入“10.1.1.0/24” |
    | 公共 IP 地址 | 选择“新建”。 </br> 对于“名称”，请输入“myBastionIP” 。 </br> 选择“确定”。 |


8. 选择“查看 + 创建”选项卡，或选择“查看 + 创建”按钮。

9. 选择“创建”。

## <a name="create-a-virtual-machine"></a>创建虚拟机

在本部分中，你将创建将用来测试专用终结点的虚拟机。


1. 在门户的左上方，选择“创建资源” > “计算” > “虚拟机”，或者在搜索框中搜索“虚拟机”。   
   
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroup” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入“myVM” |
    | 区域 | 选择“美国东部” |
    | 可用性选项 | 选择“无需基础结构冗余” |
    | 映像 | 选择“Windows Server 2019 Datacenter - Gen1” |
    | Azure Spot 实例 | 请选择“否” |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
4. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | myVNet |
    | 子网 | **mySubnet** |
    | 公共 IP | 选择“无”。 |
    | NIC 网络安全组 | **基本**|
    | 公共入站端口 | 选择“无”。 |
   
5. 选择“查看 + 创建”。 
  
6. 检查设置，然后选择“创建”。

## <a name="create-storage-account-with-a-private-endpoint"></a>创建采用专用终结点的存储帐户

在本部分中，你将创建存储帐户并配置专用终结点。

1. 在左侧菜单中，选择“创建资源” > “存储” > “存储帐户”，或者在搜索框中搜索“存储帐户”。   

2. 在“创建存储帐户”的“基本信息”选项卡中，输入或选择以下信息：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroup” |
    | **实例详细信息** |  |
    | 存储帐户名称 | 输入 **mystorageaccount**。 如果该名称不可用，请输入一个唯一的名称。 |
    | 位置 | 选择“美国东部” |
    | 性能 | 保留默认值“标准” |
    | 帐户类型 | 保留默认值“存储(常规用途 v2)” |
    | 复制| 保留默认值“读取访问异地冗余存储 (RA-GRS)” |
   
3. 选择“网络”选项卡，或选择“下一步: 网络”按钮。

4. 在“网络”选项卡中，在“连接方法”下，选择“专用终结点”。

5. 在“专用终结点”中，选择“+ 添加”。

6. 在“创建专用终结点”中，输入或选择以下信息：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroup” |
    | 位置 | 选择“美国东部” |
    | “属性” | 输入 **myPrivateEndpoint** |
    | 存储子资源 | 保留默认值“blob” |
    | **联网** |  |
    | 虚拟网络 | 选择 myVNet |
    | 子网 | 选择“mySubnet” |
    | **专用 DNS 集成** |
    | 与专用 DNS 区域集成 | 保留默认值“是” |
    | 专用 DNS 区域 | 保留默认值“(新建) privatelink.blob.core.windows.net” |

7. 选择“确定”。

8. 选择“查看 + 创建”。

9. 选择“创建”。

10. 在左侧导航窗格中选择“资源组”。

11. 选择“myResourceGroup”。

12. 选择你在之前的步骤中创建的存储帐户。

13. 在存储帐户的“设置”部分中，选择“访问密钥”。 

14. 对于“key1”，选择**连接字符串**上的“复制”。

## <a name="test-connectivity-to-private-endpoint"></a>测试到专用终结点的连接

在本部分中，你将使用在上一步骤中创建的虚拟机通过专用终结点连接到存储帐户。

1. 在左侧导航窗格中选择“资源组”。

2. 选择“myResourceGroup”。

3. 选择“myVM”。

4. 在 **myVM**的“概述”页上，选择“连接”，然后选择“堡垒”。

5. 选择蓝色的“使用堡垒”按钮。

6. 输入在创建虚拟机期间输入的用户名和密码。

7. 连接后，在服务器上打开 Windows PowerShell。

8. 输入 `nslookup <storage-account-name>.blob.core.windows.net`。 将 **\<storage-account-name>** 替换为你在前面步骤中创建的存储帐户的名称。  你将收到类似于以下所示内容的消息：

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mystorageaccount8675.privatelink.blob.core.windows.net
    Address:  10.1.0.5
    Aliases:  mystorageaccount8675.blob.core.windows.net
    ```

    将为存储帐户名称返回专用 IP 地址 **10.1.0.5**。  此地址位于你之前创建的虚拟网络的子网中。

9. 在虚拟机上安装 [Microsoft Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)。

10. 安装“Microsoft Azure 存储资源管理器”后选择“完成”。   保持选中此复选框以打开应用程序。

11. 在“连接到 Azure 存储”屏幕中，选择“使用连接字符串”。

12. 选择“**下一页**”。

13. 在“显示名称”中输入来自之前步骤的存储帐户名称。

14. 在“连接字符串”下的框中，粘贴在前面步骤中复制的存储帐户的连接字符串。

15. 选择“**下一页**”。

16. 在“连接摘要”中验证设置是否正确。  

17. 选择“连接”  。

18. 关闭到 **myVM** 的连接。

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除虚拟网络、虚拟机和存储帐户：

1. 从左侧菜单中，选择“资源组”。

2. 选择“myResourceGroup”。

3. 选择“删除资源组”****。

4. 在“键入资源组名称”中输入“myResourceGroup”。 

5. 选择“删除” 。

## <a name="next-steps"></a>后续步骤

了解如何创建专用链接服务：
> [!div class="nextstepaction"]
> [创建专用链接服务](create-private-link-service-portal.md)
