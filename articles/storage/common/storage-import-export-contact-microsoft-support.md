---
title: 针对 Azure 导入/导出作业创建支持票证或案例 | Microsoft Docs
description: 了解如何针对导入/导出作业相关的问题记录支持请求。
services: storsimple
author: alkohli
ms.service: storage
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ee30e9bba35ad39606fbe201c49e4eed3e656c97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88548267"
---
# <a name="open-a-support-ticket-for-an-importexport-job"></a>针对导入/导出作业开具支持票证

如果在使用导入/导出服务时遇到任何问题，可以创建服务请求以获取技术支持。 本文介绍：

* 如何创建支持请求。
* 如何从门户内部管理支持请求生命周期。

## <a name="create-a-support-request"></a>创建支持请求

可以执行以下步骤来创建支持请求：

1. 转到导入/导出作业。 导航到 " **支持 + 故障排除** " 部分，然后选择 " **新建支持请求**"。
     
    ![基础](./media/storage-import-export-contact-microsoft-support/import-export-support1.png)
   
2. 在“新建支持请求”中选择“基本信息”。  在“基本信息”中执行以下步骤：
    
    1. 从“颁发类型”下拉列表中，选择“技术” 。
    2. 选择“订阅”。
    3. 在“服务”下，检查“我的服务”。  在下拉列表中，可以选择以下选项之一：“存储帐户管理”、“Blob”或“文件”。************ 
        - 如果选择了“存储帐户管理”，请选择“资源”和“支持计划”。************
            ![选择存储帐户管理](./media/storage-import-export-contact-microsoft-support/import-export-support3.png)
        - 如果选择了“Blob”，请选择“资源”、“容器名称”（可选）和“支持计划”。****************
            ![选择 Blob](./media/storage-import-export-contact-microsoft-support/import-export-support2.png)
        - 如果选择了“文件”，请选择“资源”、“文件共享名称”（可选）和“支持计划”。**************** ![选择文件](./media/storage-import-export-contact-microsoft-support/import-export-support4.png)
    4. 选择“下一步”。

3. 在“新建支持请求”中，选择“步骤 2: 问题”。  在“问题”中执行以下步骤：
    
    1. 选择“C - 轻微影响”作为“严重性”。  支持人员会根据需要更新严重性。
    2. 选择“数据迁移”作为“问题类型”。********
    3. 选择“导入 - 导出”作为“类别”。********
    4. 提供问题的**标题**和其他**详细信息**。
    5. 提供问题的开始日期和时间。
    6. 在“文件上传”中选择文件夹图标，浏览要上传的任何其他文件。
    7. 选中“共享诊断信息”。
    8. 选择“下一步”。

       ![问题](./media/storage-import-export-contact-microsoft-support/import-export-support5.png)

4. 在 " **新建支持请求**" 中，选择 **步骤3联系信息**。 在“联系信息”中执行以下步骤：

   1. 在“联系人选项”中，提供首选的联系方式（电话或电子邮件）和语言。 系统会根据订阅计划自动选择响应时间。
   2. 在“联系人信息”中提供姓名、电子邮件、可选联系人和国家/地区。 选中“保存联系人更改以便在将来的支持请求中使用”复选框。****
   3. 选择“创建”  。
   
       ![联系信息](./media/storage-import-export-contact-microsoft-support/import-export-support7.png)   

      Microsoft 支持将使用此信息与你联系，以获得进一步信息、诊断和解决方法。
      提交请求后，支持工程师将与你联系以继续处理你的请求。

## <a name="manage-a-support-request"></a>管理支持请求

创建支持请求之后，可以在门户内管理请求的生命周期。

#### <a name="to-manage-your-support-requests"></a>管理支持请求

1. 若要访问“帮助和支持”页，请导航到“浏览”>“帮助 + 支持”。

    ![管理支持请求](./media/storage-import-export-contact-microsoft-support/manage-support-ticket2.png)   

2. “帮助 + 支持”中会显示“最近的支持请求”表格。 

    ![管理支持请求](./media/storage-import-export-contact-microsoft-support/manage-support-ticket1.png) 

3. 选择并单击某个支持请求。 可以查看此请求的状态和详细信息。 如果要跟进此请求，请选择 " **+ 新消息** "。

    ![管理支持请求](./media/storage-import-export-contact-microsoft-support/manage-support-ticket3.png) 


## <a name="next-steps"></a>后续步骤

了解如何 [使用 Azure 导入/导出将数据传入和传出 Azure 存储](storage-import-export-service.md)。
