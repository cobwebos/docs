---
title: "创建 .odc 文件以连接到 Azure Analysis Services 服务器 | Microsoft Docs"
description: "了解如何创建 Office 数据连接文件以连接到 Azure 中的 Analysis Services 服务器并从中获取数据。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/18/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 194910a3e4cb655b39a64d2540994d90d34a68e4
ms.openlocfilehash: 90a0cfb7c7cd7d3364ff42559dd7fdc7b2951abf
ms.contentlocale: zh-cn
ms.lasthandoff: 02/16/2017


---
<a id="create-an-office-data-connection-odc-file" class="xliff"></a>

# 创建 Office 数据连接 (.odc) 文件

本文中的信息描述了如何创建 .odc 文件以从 Excel 2016 版本号 16.0.7369.2117 或更早版本或 Excel 2013 连接到 Azure Analysis Services 服务器。 还需要更新的 [MSOLAP.7 提供程序](analysis-services-data-providers.md)。


1. 复制下面的示例 .odc 连接文件，并粘贴到文本编辑器中。 

2. 在 **odc:ConnectionString** 中，更改下列属性：

    *   在 **Data Source=asazure://region.asazure.windows.net/servername 中**，将 **region** 更改为 Analysis Services 服务器的区域，并将 servername 更改为服务器的名称。

    *   在 **Initial Catalog=database** 中，**将 database** 更改为你数据库的名称。

3. 在 **&lt;odc:CommandText>*Model*&lt;/odc:CommandText> **中，将* Model* 更改为模型或透视图的名称。 

4. 将具有 **.odc** 扩展名的文件保存到 C:\Users\\username\Documents\My Data Sources 文件夹。

5. 右键单击文件，然后单击“在 Excel 中打开”。 或者在 Excel 的“数据”功能区上，单击“现有连接”、选择你的文件，然后单击“打开”。



**示例 .odc 连接文件**
```
<html xmlns:o="urn:schemas-microsoft-com:office:office"
xmlns="http://www.w3.org/TR/REC-html40">

<head>
<meta http-equiv=Content-Type content="text/x-ms-odc; charset=utf-8">
<meta name=ProgId content=ODC.Cube>
<meta name=SourceType content=OLEDB>
<meta name=Catalog content="Database">
<meta name=Table content=Model>
<title>AzureAnalysisServicesConnection</title>
<xml id=docprops><o:DocumentProperties
  xmlns:o="urn:schemas-microsoft-com:office:office"
  xmlns="http://www.w3.org/TR/REC-html40">
  <o:Name>SampleAzureAnalysisServices</o:Name>
 </o:DocumentProperties>
</xml><xml id=msodc><odc:OfficeDataConnection
  xmlns:odc="urn:schemas-microsoft-com:office:odc"
  xmlns="http://www.w3.org/TR/REC-html40">
  <odc:Connection odc:Type="OLEDB">
   <odc:ConnectionString>Provider=MSOLAP.7;Data Source=asazure://<region>.asazure.windows.net/<servername>;Initial Catalog=<database>;</odc:ConnectionString>
   <odc:CommandType>Cube</odc:CommandType>
   <odc:CommandText>Model</odc:CommandText>
  </odc:Connection>
 </odc:OfficeDataConnection>
</xml>
<style>
<!--
    .ODCDataSource
    {
    behavior: url(dataconn.htc);
    }
-->
</style>
 
</head>

<body onload='init()' scroll=no leftmargin=0 topmargin=0 rightmargin=0 style='border: 0px'>
<table style='border: solid 1px threedface; height: 100%; width: 100%' cellpadding=0 cellspacing=0 width='100%'> 
  <tr> 
    <td id=tdName style='font-family:arial; font-size:medium; padding: 3px; background-color: threedface'> 
      &nbsp; 
    </td> 
     <td id=tdTableDropdown style='padding: 3px; background-color: threedface; vertical-align: top; padding-bottom: 3px'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td id=tdDesc colspan='2' style='border-bottom: 1px threedshadow solid; font-family: Arial; font-size: 1pt; padding: 2px; background-color: threedface'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td colspan='2' style='height: 100%; padding-bottom: 4px; border-top: 1px threedhighlight solid;'> 
      <div id='pt' style='height: 100%' class='ODCDataSource'></div> 
    </td> 
  </tr> 
</table> 

  
<script language='javascript'> 

function init() { 
  var sName, sDescription; 
  var i, j; 
  
  try { 
    sName = unescape(location.href) 
  
    i = sName.lastIndexOf(".") 
    if (i>=0) { sName = sName.substring(1, i); } 
  
    i = sName.lastIndexOf("/") 
    if (i>=0) { sName = sName.substring(i+1, sName.length); } 

    document.title = sName; 
    document.getElementById("tdName").innerText = sName; 

    sDescription = document.getElementById("docprops").innerHTML; 
  
    i = sDescription.indexOf("escription>") 
    if (i>=0) { j = sDescription.indexOf("escription>", i + 11); } 

    if (i>=0 && j >= 0) { 
      j = sDescription.lastIndexOf("</", j); 

      if (j>=0) { 
          sDescription = sDescription.substring(i+11, j); 
        if (sDescription != "") { 
            document.getElementById("tdDesc").style.fontSize="x-small"; 
          document.getElementById("tdDesc").innerHTML = sDescription; 
          } 
        } 
      } 
    } 
  catch(e) { 

    } 
  } 
</script> 

</body> 
 
</html>

```




