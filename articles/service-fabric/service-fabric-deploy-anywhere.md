<properties
   pageTitle="在 Windows Server 和 Linux 上使用 Azure Service Fabric 随地部署 | Microsoft Azure"
   description="Service Fabric 群集会在 Windows Server 和 Linux 上运行，这意味着你将能够在可以运行 Windows Server 和 Linux 的任何位置部署和承载 Service Fabric 应用程序。"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunalds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="11/19/2015"
   wacn.date=""/>

# 使用 Service Fabric 在 Windows Server 和 Linux 上进行“随地部署”
随着添加了“随地部署”，Azure Service Fabric 允许在运行 Windows Server 或 Linux 的任何 VM 或计算机上创建 Service Fabric 群集。这意味着你能够在具有一组相互连接的 Windows Server 或 Linux 计算机的任何环境（无论是本地环境还是云提供商所提供的）中部署和运行 Service Fabric 应用程序。

 Service Fabric 提供了一个安装程序包以供你创建 Service Fabric 群集。随地部署功能的一个主要优点是在使用 Service Fabric 构建应用程序时不存在供应商锁定，因为是由你选择这些应用程序的运行位置。此功能还会使你有更大的潜能实现更广泛的客户群，因为客户对于想要在其中运行你的应用程序的环境可能具有不同的要求。例如，医疗保健和金融行业中的客户的需求可能与汽车或旅行行业中的客户不同。

此功能的技术预览版预计在 2016 年第 1 季度发布。

## 支持的操作系统
你将能够在运行以下这些操作系统的 VM 或计算机上创建群集：
* Windows Server 2012 R2
* Windows Server 2016
* Linux

## 支持的编程语言
你将能够使用以下这些编程语言来编写 Service Fabric 应用程序：
* C#
* Java

## 群集创建和配置
Service Fabric 会提供安装程序包，可从 Microsoft 下载中心进行下载。下载了此包之后，你便需要对配置文件进行更改，以指定群集的设置。编辑了群集设置之后，你会运行安装程序脚本，该脚本会创建跨群集设置中指定的计算机的群集。

我们在 2016 年第 1 季度发布此功能的预览时，会共享安装过程的确切详细信息。

## 云部署与本地部署
用于在本地创建 Service Fabric 群集的过程类似于在具有一组 VM 的任何所选云上创建群集的过程。用于设置 VM 的初始步骤由所使用的云或本地环境进行控制。具有一组在它们之间启用了网络连接的 VM 之后，随后用于设置 Service Fabric 包、编辑群集设置以及运行群集创建和管理脚本的步骤是类似的。这可在你选择面向新宿主环境时，确保你操作和管理 Service Fabric 群集的知识和经验可转移。

## 随地部署功能的优点
* 由于不存在供应商锁定，因此可以选择应用程序的运行位置。
* Service Fabric 应用程序在编写之后，可以在多个宿主环境中运行，只需进行最小程度的更改，甚至无需更改。
* 构建 Service Fabric 应用程序的知识可从一个宿主平台转移到其他平台。
* 运行 Service Fabric 群集的操作经验可从一个部署环境转移到其他环境。
* 广泛的客户范围不会受宿主环境约束的限制。
* 存在针对大范围中断的额外一层可靠性和保护，因为你可以在数据中心或云提供商中断时将服务转移到其他部署环境。

## Azure 上的 Service Fabric 托管群集相对于使用随地部署创建和管理的 Service Fabric 群集的优势
在 Azure 上运行 Service Fabric 群集相对于使用随地部署选项具有一些优势，因此，如果你对于群集的运行位置没有特定需求，则我们建议在 Azure 上运行它们。在 Azure 上，我们提供与其他 Azure 功能和服务的集成，这样可使群集的操作和管理更容易：

* **Azure 门户：**Azure 门户使群集易于创建和管理。
* **Azure Resource Manager：**使用 Azure Resource Manager 可以单元的形式方便地管理群集使用的所有资源，并简化了成本跟踪和计费。
* **诊断：**在 Azure 中，我们提供与 Azure 诊断和操作见解的集成。
* **自动缩放：**对于 Azure 上的群集，我们提供内置自动缩放功能。在使用随地部署功能的其他环境中，你必须构建自己的自动缩放功能或使用 Service Fabric 为缩放群集而公开的 API 来手动缩放。

<!---HONumber=Mooncake_0307_2016-->