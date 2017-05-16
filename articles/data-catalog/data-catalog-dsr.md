---
title: "Azure 数据目录支持的数据源 |Microsoft Docs"
description: "当前支持的数据源的规格。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstevens
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 05/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: d68ed62022834c9417d820a7a64e30b47f71a1bd
ms.contentlocale: zh-cn
ms.lasthandoff: 03/31/2017


---

# <a name="azure-data-catalog-supported-data-sources"></a>Azure 数据目录支持的数据源

若要发布元数据，可使用公共 API、单击一次的注册工具或直接手动将信息输入到数据目录 Web 门户。 以下网格汇总了当前目录支持的所有源以及每个源的发布功能。  还列出了外部数据工具（每个源都可从门户“open-in”体验启动它）。 第二个网格具有每个数据源连接属性的详细技术规格。


## <a name="list-of-supported-data-sources"></a>受支持的数据源列表

<table>

    <tr>
       <td><b>数据源对象</b></td>
       <td><b>API</b></td>
       <td><b>手动输入</b></td>
       <td><b>注册工具</b></td>
       <td><b>Open-In 工具</b></td>
       <td><b>说明</b></td>
    </tr>

    <tr>
      <td>Azure Data Lake Store 帐户</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure Data Lake Store 文件</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure 存储 Blob</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure 存储目录</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure 存储表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>HDFS 目录</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HDFS 文件</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle Database 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle Database 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>其他（通用资产）</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL 数据仓库表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL 数据仓库视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 维度</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 度量</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Reporting Services 报表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>浏览器</font></td>
      <td><font size=2>仅本机模式服务器。不支持 SharePoint 模式。</font></td>
    </tr>

    <tr>
      <td>SQL Server 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP Hana 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Db2 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Db2 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>文件系统文件</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ftp 目录</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ftp 文件</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http 报表</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http 终结点</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http 文件</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Odata 实体集</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Odata 函数</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Postgresql 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Postgresql 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP Hana 视图</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Salesforce 对象</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Sharepoint 列表 </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
  
    <tr>
      <td>Azure DocumentDB 集合</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>泛型 ODBC 表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>泛型 ODBC 视图</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

如果需要其他源的支持，请使用 [Azure 数据目录论坛](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)提交功能请求。


<br>
<br>
## <a name="data-source-reference-specification"></a>数据源引用规范
> [!NOTE]
> 下表中的“DSL 结构”列仅列出了 Azure 数据目录使用的“地址”属性包的连接属性。 也就是说，“地址”属性包可能包含 Azure 数据目录仍然保留但未使用的数据源的其他连接属性。
<table>
    <tr>
       <td><b>源类型</b></td>
       <td><b>资产类型</b></td>
       <td><b>对象类型</b></td>
       <td><b>DSL 结构<b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>容器</td>
      <td>Data Lake</td>
      <td>
        <font size=2> 协议：webhdfs <br>身份验证：{基本，oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>表</td>
      <td>目录，文件</td>
      <td>
        <font size=2> 协议：webhdfs <br>身份验证：{基本，oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure 存储空间</td>
      <td>容器</td>
      <td>容器</td>
      <td>
        <font size=2> 协议：azure-blob <br>身份验证：{azure-access-key} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 域 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帐户 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 容器 </font>
      </td>
    </tr>
    <tr>
      <td>Azure 存储空间</td>
      <td>表</td>
      <td>Blob，目录</td>
      <td>
        <font size=2> 协议：azure-blob <br>身份验证：{azure-access-key} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 域 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帐户 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 容器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 名称 </font>
      </td>
    </tr>
    <tr>
      <td>Azure 存储空间</td>
      <td>容器</td>
      <td>容器</td>
      <td>
        <font size=2> 协议：azure 表 <br>身份验证：{azure-access-key} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 域 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帐户 </font>
      </td>
    </tr>
    <tr>
      <td>Azure 存储空间</td>
      <td>表</td>
      <td>表</td>
      <td>
        <font size=2> 协议：azure 表 <br>身份验证：{azure-access-key} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 域 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帐户 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 名称 </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>容器</td>
      <td>虚拟群集</td>
      <td>
        <font size=2> 协议：cosmos <br>身份验证：{基本，windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>表</td>
      <td>流、流集、视图</td>
      <td>
        <font size=2> 协议：cosmos <br>身份验证：{基本，windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>容器</td>
      <td>站点</td>
      <td>
        <font size=2> 协议：http <br>身份验证：{无、基本、windows、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>报表</td>
      <td>报表、仪表板</td>
      <td>
        <font size=2> 协议：http <br>身份验证：{无、基本、windows、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：db2 <br>身份验证：{基本，windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：db2 <br>身份验证：{基本，windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 </font>
      </td>
    </tr>
    <tr>
      <td>文件系统</td>
      <td>表</td>
      <td>文件</td>
      <td>
        <font size=2> 协议：文件 <br>身份验证：{无、基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 路径 </font>
      </td>
    </tr>
    <tr>
      <td>Ftp</td>
      <td>表</td>
      <td>目录，文件</td>
      <td>
        <font size=2> 协议：ftp <br>身份验证：{无、基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop 分布式文件系统</td>
      <td>容器</td>
      <td>群集</td>
      <td>
        <font size=2> 协议：webhdfs <br>身份验证：{基本，oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop 分布式文件系统</td>
      <td>表</td>
      <td>目录，文件</td>
      <td>
        <font size=2> 协议：webhdfs <br>身份验证：{基本，oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：Hive <br>身份验证：{hdinsight、基本、用户名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>connectionProperties： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器协议：{hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：Hive <br>身份验证：{hdinsight、基本、用户名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>connectionProperties： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器协议：{hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>容器</td>
      <td>站点</td>
      <td>
        <font size=2> 协议：http <br>身份验证：{无、基本、windows、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>报表</td>
      <td>报表、仪表板</td>
      <td>
        <font size=2> 协议：http <br>身份验证：{无、基本、windows、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>表</td>
      <td>终结点、文件</td>
      <td>
        <font size=2> 协议：http <br>身份验证：{无、基本、windows、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：mysql <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：mysql <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>Odata</td>
      <td>容器</td>
      <td>实体容器</td>
      <td>
        <font size=2> 协议：odata <br>身份验证：{无、基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Odata</td>
      <td>表</td>
      <td>实体集、函数</td>
      <td>
        <font size=2> 协议：odata <br>身份验证：{无、基本、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 资源 </font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：oracle <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：oracle <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：postgresql <br>身份验证：{基本，windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：postgresql <br>身份验证：{基本，windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>容器</td>
      <td>站点</td>
      <td>
        <font size=2> 协议：http <br>身份验证：{无、基本、windows、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>报表</td>
      <td>报表、仪表板</td>
      <td>
        <font size=2> 协议：http <br>身份验证：{无、基本、windows、oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>表</td>
      <td>数据混合</td>
      <td>
        <font size=2> 协议：power-query <br>身份验证：{oauth} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>表</td>
      <td>对象</td>
      <td>
        <font size=2> 协议：salesforce-com <br>身份验证：{基本，windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 登录服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 类 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 项名称 </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>容器</td>
      <td>服务器</td>
      <td>
        <font size=2> 协议：sap-hana-sql <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>表</td>
      <td>查看</td>
      <td>
        <font size=2> 协议：sap-hana-sql <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>表</td>
      <td>列出</td>
      <td>
        <font size=2> 协议：sharepoint 列表 <br>身份验证：{基本，windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>SQL 数据仓库</td>
      <td>命令</td>
      <td>存储过程</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SQL 数据仓库</td>
      <td>TableValuedFunction</td>
      <td>Table-valued 函数</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SQL 数据仓库</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>SQL 数据仓库</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>命令</td>
      <td>存储过程</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Table-valued 函数</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：tds <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多维度</td>
      <td>容器</td>
      <td>模型</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多维度</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象类型：{KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多维度</td>
      <td>度量</td>
      <td>度量</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象类型：{度量值} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多维度</td>
      <td>表</td>
      <td>维度</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象类型：{维度} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表格</td>
      <td>容器</td>
      <td>模型</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表格</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象类型：{KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表格</td>
      <td>度量</td>
      <td>度量</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象类型：{度量值} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表格</td>
      <td>表</td>
      <td>表</td>
      <td>
        <font size=2> 协议：分析服务 <br>身份验证：{windows、基本、匿名、无} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象类型：{表} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>容器</td>
      <td>服务器</td>
      <td>
        <font size=2> 协议：报表服务 <br>身份验证：{windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 版本：{ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>报表</td>
      <td>报表</td>
      <td>
        <font size=2> 协议：报表服务 <br>身份验证：{windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 路径 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 版本：{ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：teradata <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：teradata <br>身份验证协议：{协议、windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 服务器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>容器</td>
      <td>模型</td>
      <td>
        <font size="2"> 协议：mssql-mds <br>身份验证：{windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 版本 </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>表</td>
      <td>实体</td>
      <td>
        <font size="2"> 协议：mssql-mds <br>身份验证：{windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 模型 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 版本 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 实体 </font>
      </td>
    </tr>
    <tr>
      <td>Azure DocumentDB</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2>协议：document-db <br>身份验证：{azure-access-key} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>Azure DocumentDB</td>
      <td>集合</td>
      <td>集合</td>
      <td>
        <font size=2>协议：document-db <br>身份验证：{azure-access-key} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 集合 </font>
      </td>
    </tr>
    <tr>
      <td>泛型 ODBC</td>
      <td>容器</td>
      <td>数据库</td>
      <td>
        <font size=2> 协议：odbc <br>身份验证：{基本，windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 选项 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 </font>
      </td>
    </tr>
    <tr>
      <td>泛型 ODBC</td>
      <td>表</td>
      <td>表、视图</td>
      <td>
        <font size=2> 协议：odbc <br>身份验证：{基本，windows} <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 选项 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 数据库 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对象 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 架构 </font>
      </td>
    </tr>
    <tr>
      <td>其他（以上均不是）</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2> 协议：通用资产 <br>地址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId </font>
      </td>
    </tr>
</table>

