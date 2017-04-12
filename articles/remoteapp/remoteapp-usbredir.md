---
title: "如何在 Azure RemoteApp 中重定向 USB 设备？ | Microsoft Docs"
description: "了解如何在 Azure RemoteApp 中使用 USB 设备的重定向。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 191d98af-2f5a-4307-9042-aae0e4049f9f
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1be1e6f63c4a4786d23af57d454e7a2e3bb17ad0
ms.lasthandoff: 03/31/2017


---
# <a name="how-do-you-redirect-usb-devices-in-azure-remoteapp"></a>如何在 Azure RemoteApp 中重定向 USB 设备？
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

设备重定向允许用户将连接到其计算机或平板电脑的 USB 设备与 Azure RemoteApp 中的应用一起使用。 例如，如果通过 Azure RemoteApp 共享了 Skype，那么你的用户需要能够使用其摄像机设备。

进一步操作之前，请务必先阅读[在 Azure RemoteApp 中使用重定向](remoteapp-redirection.md)中的 USB 重定向信息。 但是，推荐的 nusbdevicestoredirect:s:* 并不适用于 USB 网络摄像机，并且可能也不适用于某些 USB 打印机或 USB 多功能设备。 出于设计和安全性方面的原因，Azure RemoteApp 管理员必须先通过设备类 GUID 或者通过设备实例 ID 启用重定向，然后你的用户才可以使用这些设备。

尽管本文讲的是网络摄像机重定向，但是你仍可以使用类似的方法来重定向无法通过 **nusbdevicestoredirect:s:*** 命令重定向的 USB 打印机和其他 USB 多功能设备。

## <a name="redirection-options-for-usb-devices"></a>适用于 USB 设备的重定向选项
Azure RemoteApp 重定向 USB 设备的机制与远程桌面服务重定向 USB 设备的机制非常相似。 使用基础技术可以为给定的设备选择正确的重定向方法，以使用 **usbdevicestoredirect:s:** 命令获取高级别重定向和 RemoteFX USB 设备重定向两者中的最佳者。 此命令有四个元素：

| 处理顺序 | 参数 | 说明 |
| --- | --- | --- |
| 1 |* |选择未按高级别重定向选取的所有设备。 注意︰按照设计，* 并不适用于 USB 网络摄像机。 |
| {Device class GUID} |选择与指定的设备安装程序类匹配的所有设备。 | |
| USB\InstanceID |选择为给定实例 ID 指定的 USB 设备。 | |
| 2 |-USB\Instance ID |移除指定设备的重定向设置。 |

## <a name="redirecting-a-usb-device-by-using-the-device-class-guid"></a>通过使用设备类 GUID 重定向 USB 设备
有两种方法可用来查找可用于重定向的设备类 GUID。 

第一个选项是使用[供供应商使用的系统定义的设备安装程序类](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx)。 选取与连接到本地计算机的设备最匹配的类。 对于数字相机来说，这可能是图像处理设备类或视频捕获设备类。 你需要使用设备类做一些试验，找到适合本地连接的 USB 设备（在本例中是网络摄像机）的正确类 GUID。

一种更好的方法（也就是第二个选项）是遵循以下步骤来找到特定设备类 GUID：

1. 打开“设备管理器”，找到将要重定向的设备并右键单击它，然后打开属性。
   ![打开“设备管理器”](./media/remoteapp-usbredir/ra-devicemanager.png)
2. 在“详细信息”选项卡上，选择属性“类 Guid”。 它显示的值是该类型设备的类 GUID。
   ![相机属性](./media/remoteapp-usbredir/ra-classguid.png)
3. 使用类 Guid 值来重定向与其匹配的设备。

例如：

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

你可以在同一个 cmdlet 中组合使用多个设备重定向。 例如：要重定向本地存储和 USB 网络摄像机，cmdlet 会如下所示：

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

当按类 GUID 设置设备重定向时，会重定向与指定集合中的类 GUID 匹配的所有设备。 例如，如果在本地网络上有多个计算机使用同一个 USB 网络摄像机，那么可以运行单个 cmdlet 来重定向所有网络摄像机。

## <a name="redirecting-a-usb-device-by-using-the-device-instance-id"></a>通过使用设备实例 ID 重定向 USB 设备
如果需要更精细的控制并且希望按设备控制重定向，则可以使用 **USB\InstanceID** 重定向参数。

此方法最困难的部分是查明 USB 设备实例 ID。 你需要访问计算机和特定 USB 设备。 然后，执行以下步骤：

1. 参照[如何在远程桌面会话中使用我的设备和资源？](http://windows.microsoft.com/en-us/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)中所述，在远程桌面会话中启用设备重定向
2. 打开远程桌面连接，然后单击“显示选项”。
3. 单击“另存为”，将当前的连接设置保存到某个 RDP 文件中。  
    ![将设置另存为 RDP 文件](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. 选择一个文件名和一个位置，例如 MyConnection.rdp 和 This PC\Documents，然后保存文件。
5. 使用文本编辑器打开文件 MyConnection.rdp，然后找到要重定向设备的实例 ID。

现在，请在下面的 cmdlet 中使用该实例 ID：

    Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB\<Device InstanceID value>"



### <a name="help-us-help-you"></a>请帮助我们改进，以便为你提供更好的帮助和支持
是否知道除了给这篇文章评级和在下面发表评论，还可以对文章本身进行更改？ 缺了什么？ 出现了错误？ 是否编写了令人困惑的内容？ 向上滚动并单击“在 GitHub 上编辑”  进行更改 - 更改的内容会发送给我们进行审阅，一旦签核，便可以在此处看到这些更改和改进。


