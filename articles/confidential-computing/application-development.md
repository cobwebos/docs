---
title: Azure 机密计算开发工具
description: 使用工具和库为机密计算开发应用程序
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 6bb3b8dbc7887419f7901a52b56c25f60c869abb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994855"
---
# <a name="application-development-on-intel-sgx"></a>Intel SGX 上的应用程序开发 


机密计算基础结构需要特定工具和软件。 本页专门讨论了在 Intel SGX 上运行的 Azure 机密计算虚拟机与应用程序开发相关的概念。 阅读本页之前，请 [阅读引入 INTEL SGX 虚拟机和 enclaves](confidential-computing-enclaves.md)。 

若要利用领地和独立环境的强大功能，需要使用支持机密计算的工具。 有多种工具支持领地应用程序开发。 例如，可以使用以下开源框架： 

- [开放式 Enclave 软件开发工具包 (OE SDK) ](#oe-sdk)
- [机密联盟框架 (CCF)](#ccf)

## <a name="overview"></a>概述

使用领地构建的应用程序按两种方式分区：

1. “不受信任的”组件（宿主）
1. “受信任的”组件（领地）


![应用开发](media/application-development/oe-sdk.png)


宿主是运行领地应用程序的不受信任的环境。 宿主无法访问部署在它上面的领地代码。 

领地是应用程序代码及其缓存数据/内存运行的位置。 安全计算应在领地中进行，以确保机密和敏感数据保持受保护状态。 


在应用程序设计过程中，识别并确定应用程序的哪个部分需要在领地中运行非常重要。 你选择放入受信任组件的代码将与应用程序其余组件中的数据相互隔离。 初始化领地并将代码加载到内存后，无法从不受信任的组件读取或更改该代码。 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Open Enclave 软件开发工具包 (OE SDK) <a id="oe-sdk"></a>

若要编写在领地中运行的代码，请使用提供商支持的库或框架。 [开放领地 SDK](https://github.com/openenclave/openenclave) (OE SDK) 是一个开源 SDK，可以在支持机密计算的不同硬件上实现抽象化。 

OE SDK 旨在充当任何 CSP 的任何硬件上的单个抽象层。 可以在 Azure 机密计算虚拟机的基础上使用 OE SDK，以创建并运行基于领地的应用程序。

## <a name="confidential-consortium-framework-ccf"></a>机密联合会框架 (CCF) <a id="ccf"></a>

[CCF](https://github.com/Microsoft/CCF)是一个分布式节点网络，其中每个节点都运行其自己的 enclaves。 可信节点网络允许运行分布式分类帐。 分类帐为要使用的协议提供安全、可靠的组件。 

![CCF 节点](media/application-development/ccf.png)

此开源框架实现了高范围、更细化的机密性以及区块链的联盟管理。 对于每个使用 TEEs 的节点，可以确保安全达成共识和事务处理。


## <a name="next-steps"></a>后续步骤 
- [DCsv2-Series 虚拟机部署机密计算](quick-create-portal.md)
- [下载并安装 OE SDK 并开始开发应用程序](https://github.com/openenclave/openenclave)