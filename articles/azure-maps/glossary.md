---
title: Azure Maps 术语表 | Microsoft Docs
description: 与 Azure 地图、基于位置的服务和 GIS 关联的常用术语的词汇表。
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: c9d5c2efc14dec5beee0eaf81a4f628421ef3d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657038"
---
# <a name="glossary"></a>词汇表

下面的列表描述了与 Azure 地图服务一起使用的常见单词。

## <a name="a"></a>A

<a name="address-validation"></a> **地址验证**：验证是否存在某一地址的过程。

<a name="advanced-routing"></a>**高级路由**：使用道路路由数据执行提前操作的服务的集合;例如，计算可到达范围（异位）、距离矩阵和批处理路由请求。

<a name="aerial-imagery"></a> **航拍图像**：请参阅[卫星图像](#satellite-imagery)。 

<a name="along-a-route-search"></a>**沿路径搜索**：在指定的弯路时间或距离路径的距离内查找数据的空间查询。

<a name="altitude"></a> **海拔高度**：参考曲面上方点的高度或垂直高程。 海拔高度测量基于给定的参考基准，例如平均海平面。 另请参阅海拔。

<a name="ambiguous"></a> **不明确**：数据分类中的不确定状态，在针对给定属性可向对象恰当地分配两个或更多值时存在。 例如，在地理编码"CA"时，返回两个不明确的结果："加拿大"和"加利福尼亚"。 "CA"是"加拿大"和"加利福尼亚"的国家/地区代码。 

<a name="annotation"></a> **注释**：地图上显示的可向用户提供信息的文本或图形。 注释可以标识或描述特定的地图实体，提供关于地图上的区域的一般信息，或者提供关于地图本身的信息。

<a name="antimeridian"></a>**安定人**：也被称为第180<sup>子</sup>午线。 这是 -180 度和 180 度经度交汇的点。 这与全球主要子午线正好相反。

<a name="application-programming-interface-api"></a> **应用程序编程接口 (API)**：允许开发人员创建应用程序的规范。

<a name="api-key"></a>**API 密钥**：请参阅[共享密钥身份验证](#shared-key-authentication)。

<a name="area-of-interest-aoi"></a>**感兴趣区域 （AOI）：** 用于定义地图或数据库生产的焦点区域的范围。

<a name="asset-tracking"></a>**资产跟踪**：跟踪资产位置的过程，如人员、车辆或其他对象。

<a name="asynchronous-request"></a>**异步请求**：打开连接并向服务器发出请求的 HTTP 请求，该服务器返回异步请求的标识符，然后关闭连接。 服务器继续处理请求，用户可以使用标识符检查状态。 当请求完成处理后，用户可以下载响应。 这种类型的请求用于长时间运行的进程。

<a name="autocomplete"></a>**自动完成**：应用程序中预测用户键入的单词的其余部分的功能。 

<a name="autosuggest"></a>**自动建议**：应用程序中预测用户键入内容的逻辑可能性的功能。

<a name="azure-location-based-services-lbs"></a> **Azure Location Based Services (LBS)**：Azure Maps 预览版的名称。

<a name="azure-active-directory"></a>**Azure 活动目录 （Azure AD）：** Azure AD 是 Microsoft 基于云的标识和访问管理服务。 Azure 地图 Azure AD 集成当前可用于所有 Azure 地图 API 的预览版。 Azure AD 支持基于角色的访问控制 （RBAC），以允许对 Azure 地图资源的细粒度访问。 要了解有关 Azure 映射 Azure AD 集成的更多内容，请参阅[Azure 映射和 Azure AD](azure-maps-authentication.md) [以及管理 Azure 映射中的身份验证](how-to-manage-authentication.md)。

<a name="azure-maps-key"></a>**Azure 映射密钥**：请参阅[共享密钥身份验证](#shared-key-authentication)。

## <a name="b"></a>B

<a name="base-map"></a> **基本地图**：地图应用程序的一部分，显示背景参考信息，例如道路、地标和政治边界。

<a name="batch-request"></a> **批处理请求**：将多个请求组合到单个请求中的过程。

<a name="bearing"></a> **方位**：一个点相对于另一个点的水平方向。 这表示为相对于北的角度，顺时针方向从 0 度到 360 度。 

<a name="boundary"></a>**边界**：分隔相邻政治实体（如国家/地区、地区和属性）的线或多边形。 边界是可能或可能不遵循物理特征（例如河流、山脉或墙壁）的线。

<a name="bounds"></a> **边界**：请参阅[边界框](#bounding-box)。

<a name="bounding-box"></a> **边界框**：用于表示地图上的矩形区域的一组坐标。 

## <a name="c"></a>C

<a name="cadastre"></a> **Cadastre**：登记的土地和财产记录。 另请参阅 [Parcel](#parcel)。

<a name="camera"></a> **照相机**：在交互式地图控件的上下文中，照相机定义了地图视图。 摄像机的视口基于多个地图参数确定：中心、缩放级别、俯仰、轴承。 

<a name="centroid"></a> **质心**：功能的几何中心。 线的质心为其中点，而多边形的质心则为其中心区域。

<a name="choropleth-map"></a>**乔罗普勒斯地图**：一个主题地图，其中区域与统计变量的测量成比例进行底形。 此统计变量显示在地图上。 例如，根据美国每个州相对于其他所有州的人口，将美国每个州的边界着色。

<a name="concave-hull"></a> **凹包**：一种形状，表示可能的凹形几何体，它包含指定数据集中的所有形状。 生成的形状类似于用保鲜膜包裹数据然后加热它，从而导致点之间的大跨度向其他数据点塌陷。

<a name="consumption-model"></a> **消耗模型**：定义车辆消耗燃油或电力的速率的信息。 另请参阅[消耗模型文档](consumption-model.md)。

<a name="control"></a> **控件**：一个自包含或可重用的组件，由一个图形用户界面组成，用于定义界面的一组行为。 例如，地图控件通常是加载交互式地图的用户界面的一部分。

<a name="convex-hull"></a> **凸包**：凸包是一种形状，表示包含指定数据集中所有形状的最小凸几何。 生成的形状类似于在数据集周围缠绕弹性带。

<a name="coordinate"></a> **坐标**：由用于表示地图上位置的经度和纬度值组成。

<a name="coordinate-system"></a> **坐标系统**：用于定义二维或三维空间中点位置的参考框架。

<a name="country-code"></a>**国家/地区代码**：基于 ISO 标准的国家/地区的唯一标识符。 ISO2 是一个国家/地区的双字符代码（例如 US），ISO3 则表示三字符代码（例如 USA）。

<a name="country-subdivision"></a>**国家/地区细分**：国家/区域（通常称为州或省）的一级细分。

<a name="country-secondary-subdivision"></a>**国家/地区二级细分**：国家/地区的二级细分，通常称为县。

<a name="country-tertiary-subdivision"></a>**国家三级细分**：国家/地区的三级细分，通常为命名区域，如病房。

<a name="cross-street"></a> **十字路口**：两个或多个街道相交的点。

<a name="cylindrical-projection"></a> **柱面投影**：将点从球状体或球体转换为切线或割线圆柱体的投影。 然后圆柱从顶部切至底部并平展为一个平面。

## <a name="d"></a>D

<a name="datum"></a> **基准面**：测量系统的参考规范，表面上的坐标位置系统（水平基准面）或表面上方或下方的高度（垂直基准面）。

<a name="dbf-file"></a> **DBF 文件**：与形状文件 (SHP) 结合使用的数据库文件格式。

<a name="degree-minutes-seconds-dms"></a> **度分秒 (DMS)**：描述纬度和经度的度量单位。 1 度是一个圆的 1/360<sup></sup>。 1 度进一步分为 60 分钟，1 分钟分为 60 秒。

<a name="delaunay-triangulation"></a> **Delaunay 三角剖分**：一种在点数据集中创建连续的非重叠三角形网格的技术。 每个三角形的外接圆都不包含内部数据集中的点。

<a name="demographics"></a> **人口统计信息**：人口统计特征（如年龄、出生率和收入）。

<a name="destination"></a> **目的地**：某人前往的终点或位置。

<a name="digital-elevation-model-dem"></a> **数字高程模型 (DEM)**：与表面相关的高程值的数据集，使用公共基准以固定间隔在区域上捕获。 DEM 通常用于表示地形缓解。

<a name="dijkstra's-algorithm"></a> **Dijkstra 算法**：一种算法，用于检查网络的连接性，以找到两点之间的最短路径。

<a name="distance-matrix"></a> **距离矩阵**：包含一组起点和目的地之间的行程时间和距离信息的矩阵。 

## <a name="e"></a>E

<a name="elevation"></a>**高程**：点或物体在参考曲面或基准面上方或下方的垂直距离。 一般来说，参考表面是平均海平面。 高程一般是指地面的垂直高度。

<a name="envelope"></a> **包络线**：请参阅[边界框](#bounding-box)。

<a name="extended-postal-code"></a> **扩展邮政编码**：可能包含其他信息的邮政编码。 例如，在美国，邮政编码有五位数字。 但是，一个扩展的邮政编码，称为 zip+4，包括四个额外的数字。 这些附加数字用于标识五位交付区域内的地理段，例如城市街区、一组公寓或邮政信箱。 了解地理细分有助于高效邮件分拣和递送。

<a name="extent"></a> **程度**：请参阅[边界框](#bounding-box)。

## <a name="f"></a>F

<a name="federated-authentication"></a> **联合身份验证**：一种身份验证方法，允许在多个 Web 和移动应用中使用单一登录/身份验证机制。 

<a name="feature"></a> **功能**：将几何与其他元数据信息组合在一起的对象。 

<a name="feature-collection"></a> **功能集合**：功能对象的集合。

<a name="find-along-route"></a> **沿路由查找**：一种空间查询，用于查找在路由路径的指定绕行时间或距离内的数据。

<a name="find-nearby"></a> **在附近查找**：从一个点搜索固定直线距离（如乌鸦飞行）的空间查询。

<a name="fleet-management"></a> **车队管理**：商用车辆的管理，如汽车、卡车、轮船和飞机。 车队管理可以包括一系列功能，例如车辆融资、维护、远程信息处理（跟踪和诊断）以及驾驶员、速度、燃油以及运行状况和安全管理。 车队管理是业务中依赖运输的公司使用的流程。 这些公司希望尽量减少风险，降低整体运输和员工成本，同时确保遵守政府法规。

<a name="free-flow-speed"></a> **自由流动速度**：在理想条件下预期的自由流动速度。 通常是速度限制。

<a name="free-form-address"></a> **自由格式地址**：一个完整的地址，用单行文本表示。

<a name="fuzzy-search"></a> **模糊搜索**：其中包含可能是地址或兴趣点的自由格式文本字符串的搜索。 

## <a name="g"></a>G

<a name="geocode"></a> **地理代码**：已转换为可用于在地图上显示该位置的坐标的地址或位置。 

<a name="geocoding"></a> **地理编码**：也称为前向地理编码，是将位置数据的地址转换为坐标的过程。 

<a name="geodesic-path"></a> **测地路径**：曲面上两点之间的最短路径。 由于墨卡托投影，此路径将在 Azure Maps 上显示为曲线。

<a name="geofence"></a>**地理围栏**：在设备进入或存在区域时可用于触发事件的已定义地理区域。

<a name="geojson"></a> **GeoJSON**：是一种常见的基于 JSON 的文件格式，用于存储地理矢量数据，如点、线和多边形。 **注意**：Azure Maps 使用 GeoJSON 的扩展版本，如[此处所述](extend-geojson.md)。

<a name="geometry"></a> **几何**：表示空间对象，例如点、线或多边形。

<a name="geometrycollection"></a> **GeometryCollection**：几何对象的集合。

<a name="geopol"></a> **GeoPol**：指地缘政治敏感数据，例如有争议的边界和地名。

<a name="georeference"></a> **地理参考**：将地理数据或图像与已知坐标系对齐的过程。 此过程可能包括移位、旋转、缩放或倾斜数据。

<a name="georss"></a> **GeoRSS**：用于向 RSS 源添加空间数据的 XML 扩展。

<a name="gis"></a> **GIS**：“地理信息系统”的首字母缩写词。 用于描述测绘行业的常用术语。

<a name="gml"></a> **GML**：也称为地理标记语言。 用于存储空间数据的 XML 文件扩展名。

<a name="gps"></a> **GPS**：也称为全球定位系统，是用于确定地球上的设备位置的卫星系统。 轨道卫星发射信号，允许地球上任何地方的 GPS 接收器通过三边测量计算自己的位置。

<a name="gpx"></a> **GPX**：也称为 GPS eXchange 格式，是通常由 GPS 设备创建的 XML 文件格式。  

<a name="great-circle-distance"></a> **大圆距离**：球体表面上两点之间的最短距离。

<a name="greenwich-mean-time-gmt"></a> **格林威治标准时间 (GMT)**：在本初子午线上的时间，经过英国格林威治的皇家天文台。

<a name="guid"></a> **GUID**：全局唯一标识符。 用于唯一标识接口、类、类型库、组件类别或记录的字符串。

## <a name="h"></a>H

<a name="haversine-formula"></a> **半正矢公式**：用于计算球体上两点之间的大圆距离的常用方程。

<a name="hd-maps"></a> **HD 地图**：也称为高清晰度地图，由高保真道路网络信息组成，例如车道标记、标牌和自动驾驶所需的方向灯。

<a name="heading"></a> **标题**：指向或面对的方向。 另请参阅[方位](#heading)。

<a name="heatmap"></a> **热度地图**：一种数据可视化，其中一系列颜色表示特定区域中的点密度。 另请参阅“主题地图”。

<a name="hybrid-imagery"></a> **混合图像**：卫星或航拍图像，将道路数据和标签叠加在其上。

## <a name="i"></a>I

<a name="iana"></a> **IANA**：Internet 数字分配机构的首字母缩写词。 监督全球 IP 地址分配的非营利组织。

<a name="isochrone"></a> **等时线**：等时线定义某人可在指定时间内采用某种交通方式，从给定位置沿任何方向行进的区域。 另请参阅[可访问范围](#reachable-range)。

<a name="isodistance"></a> **Isodistance**：给定位置，等时线定义某人在指定距离内，采用某种交通方式，沿任何方向可行进的区域。 另请参阅[可访问范围](#reachable-range)。

## <a name="k"></a>K

<a name="kml"></a> **KML**：也称为锁眼标记语言，是用于存储地理矢量数据（如点、线和多边形）的常见 XML 文件格式。 

## <a name="l"></a>L

<a name="landsat"></a>**大地卫星**：由美国宇航局研制的多光谱、地球轨道卫星，收集陆地图像。 此图像用于许多行业，如农业、林业和制图。

<a name="latitude"></a> **纬度**：以度为单位从赤道向北或向南方向测量的角距离。

<a name="level-of-detail"></a>**详细级别**：请参阅缩放级别。

<a name="lidar"></a> **Lidar**：光探测和测距的缩写。 一种遥感技术，使用激光测量到反射表面的距离。

<a name="linear-interpolation"></a> **线性内插**：使用已知值之间的线性距离估计未知值。

<a name="linestring"></a> **LineString**：用于表示直线的几何图形。 也称为折线。 

<a name="localization"></a> **本地化**：对不同语言和区域性的支持。

<a name="logistics"></a> **物流**：以协调的方式移动人员、车辆、物资或资产的过程。

<a name="longitude"></a> **经度**：以度为单位从子午线向东或向西方向度量的角距离。

## <a name="m"></a>M

<a name="map-tile"></a> **地图图块**：矩形图像，表示地图画布的分区。 有关详细信息，请参阅[缩放级别和磁贴网格文档](zoom-levels-and-tile-grid.md)。

<a name="marker"></a> **标记**：也称为销或图钉，是一个表示在地图上的点位置的图标。

<a name="mercator-projection"></a> **墨卡托投影**：圆柱形地图投影，由于能够表示恒定航线（称为垂直线），作为与经线保持角度的直段，因此成为航海用途的标准地图投影。 与地球表面的真实布局相比，所有平面地图投影都会扭曲地图的形状或大小。 墨卡托投影会放大远离赤道的区域，因此接近两极时，较小的区域在地图上显得更大。 

<a name="multilinestring"></a> **MultiLineString**：表示 LineString 对象集合的几何体。 

<a name="multipoint"></a> **MultiPoint**：表示 Point 对象集合的几何体。

<a name="multipolygon"></a> **MultiPolygon**：表示 Polygon 对象集合的几何体。 例如，要显示夏威夷的边界，每个岛屿都将用多边形进行轮廓。 因此，夏威夷的边界将是一个多面形。

<a name="municipality"></a> **市政当局**：市或城镇。 

<a name="municipality-subdivision"></a> **市政当局细分**：市政当局的一个分区，例如附近或当地区域名称，例如“市中心”。

## <a name="n"></a>N

<a name="navigation-bar"></a> **导航栏**：地图上的一组控件，用于调整缩放级别、间距、旋转和切换基本地图图层。

<a name="nearby-search"></a> **邻近搜索**：从一个点搜索固定直线距离（如乌鸦飞行）的空间查询。

<a name="neutral-ground-truth"></a> **中性标定真实数据**：一种地图，使用其所代表区域的官方语言和本地脚本（如果可用）呈现标签。

## <a name="o"></a>O

<a name="origin"></a> **源**：用户所在的起点或位置。

## <a name="p"></a>P

<a name="panning"></a> **平移**：在保持恒定缩放级别的同时向任何方向移动地图的过程。

<a name="parcel"></a> **包**：土地或财产边界的图。

<a name="pitch"></a> **间距**：地图相对于垂直方向的倾斜量，其中 0 正垂直于地图。

<a name="point"></a> **点**：表示地图上单个位置的几何图形。 

<a name="points-of-interest-poi"></a> **兴趣点 (POI)** 企业、地标或共同兴趣位置。

<a name="polygon"></a> **多边形**：表示地图上某个区域的实体几何图形。 

<a name="polyline"></a> **折线**：用于表示行的几何图形。 也称为 LineString。 

<a name="position"></a> **位置**：点的经度、纬度和高度（x、y、z 坐标）。

<a name="post-code"></a> **发布代码**：请参阅[邮政编码](#postal-code)。

<a name="postal-code"></a>**邮政编码**：一系列字母或数字，或两者，以特定格式。 国家/地区的邮政部门使用邮政编码将地理区域划分为区域，以便简化邮件的递送。

<a name="primary-key"></a>**主键**：为 Azure 映射共享密钥身份验证提供的两个订阅密钥中的第一个。 请参阅[共享密钥身份验证](#shared-key-authentication)。

<a name="prime-meridian"></a> **本初子午线**：表示经度为 0 度的经线。 通常，当向西移动至 180 度时，经度值会减小，在向东方向行驶至 -180 度时，经度值会减小。 

<a name="prj"></a>**PRJ**：一个文本文件，该文件通常伴随着一个 Shapefile 文件，该文件包含有关数据集所包含的投影坐标系的信息。

<a name="projection"></a> **投影**：基于地图投影的投影坐标系，例如横向墨卡托、阿尔伯斯投影和罗宾逊投影。 这些投影可将地球球面的地图投影到二维笛卡尔坐标平面。 投影坐标系统有时称为地图投影。

## <a name="q"></a>Q

<a name="quadkey"></a> **Quadkey**：四叉树图块系统中平铺的 base-4 地址索引。 有关详细信息，请参阅[缩放级别和磁贴网格](zoom-levels-and-tile-grid.md)文档。

<a name="quadtree"></a> **四叉树**：一种数据结构，其中每个节点只有四个子节点。 Azure 地图中使用的切片系统使用四树结构，例如当用户放大一个级别时，每个地图磁贴分解为四个子磁贴。  有关详细信息，请参阅[缩放级别和磁贴网格](zoom-levels-and-tile-grid.md)文档。

<a name="queries-per-second-qps"></a> **每秒查询次数 (QPS)**：一秒钟内可以对服务或平台进行的查询或请求的数量。 

## <a name="r"></a>R

<a name="radial-search"></a> **径向搜索**：从一个点搜索固定直线距离（如乌鸦飞行）的空间查询。 

<a name="raster-data"></a> **光栅数据**：组织成行和列（或网格）的单元格（或像素）矩阵，其中每个单元格包含表示信息的值，例如温度。 光栅数据包括数字航空照片、卫星图像、数字图片和扫描地图。

<a name="raster-layer"></a> **光栅图层**：由光栅图像组成的图块层。

<a name="reachable-range"></a> **可到达范围**：可到达范围定义某人采用某种交通方式在指定时间或距离内，从某个位置向任何方向行进的区域。 另请参阅[等时线](#isochrone)和[等距离](#isodistance)。

<a name="remote-sensing"></a> **遥感**：从远处收集和解释传感器数据的过程。

<a name="rest-service"></a> **REST 服务**：首字母缩略词 REST 代表表述性状态转移。 REST 服务是一种基于 URL 的 Web 服务，它依赖于基本的 Web 技术进行通信，最常见的方法是 HTTP GET 和 POST 请求。 与传统的基于 SOAP 的服务相比，这些类型的服务更快更小。

<a name="reverse-geocode"></a> **反向地理编码**：获取坐标并确定地图上代表的地址的过程。

<a name="reproject"></a> **重新投影**：请参阅[转换](#transformation)。

<a name="rest-service"></a> **REST 服务**：代表表述性状态转移的首字母缩略词。 在分散的分布式环境中用于在对等机之间交换信息的体系结构。 REST 允许不同计算机上的程序独立于操作系统或平台进行通信。 服务可以将超文本传输协议 （HTTP） 请求发送到统一的资源定位器 （URL） 并返回数据。

<a name="route"></a> **路由**：两个或多个位置之间的路径，它还可能包括附加信息，例如沿着路线的路标的指令。

<a name="requests-per-second-rps"></a> **每秒的请求数 (RPS)**：请参阅[每秒查询次数 (QPS)](#queries-per-second-qps)。 

<a name="rss"></a> **RSS**：Really Simple Syndication、Resource Description Framework (RDF) Site Summary 或 Rich Site Summary 的缩写，具体取决于源。 一种简单的结构化 XML 格式，用于在不同网站之间共享内容。 RSS 文档包括关键元数据元素，例如作者、日期、标题、简要描述和超文本链接。 此信息有助于用户（或 RSS 发布者服务）决定哪些材料值得进一步调查。

## <a name="s"></a>S

<a name="satellite-imagery"></a> **卫星图像**：由垂直向下指向的飞机和卫星捕获的图像。

<a name="secondary-key"></a>**辅助密钥**：为 Azure 映射共享密钥身份验证提供的两个订阅密钥中的第二个。 请参阅[共享密钥身份验证](#shared-key-authentication)。

<a name="shapefile-shp"></a> **形状文件 (SHP)**：也称为 ESRI 形状文件，是一种矢量数据存储格式，用于存储地理特征的位置、形状和属性。 形状文件存储在一组相关文件中。

<a name="shared-key-authentication"></a>**共享密钥身份验证**：共享密钥身份验证依赖于将 Azure 映射帐户生成的密钥与每个请求传递到 Azure 映射。 这些密钥通常称为订阅密钥。 建议定期重新生成密钥以确保安全性。 提供两个键，以便您可以使用一个键维护连接，同时重新生成另一个键。 重新生成密钥时，必须将访问此帐户的所有应用程序更新为使用新密钥。 要了解有关 Azure 映射身份验证的更多内容，请参阅[Azure 映射和 Azure AD](azure-maps-authentication.md) [以及管理 Azure 映射中的身份验证](how-to-manage-authentication.md)。

<a name="software-development-kit-sdk"></a> **软件开发工具包 (SDK)**：一组文档、示例代码和示例应用，可帮助开发人员使用 API 构建应用。

<a name="spherical-mercator-projection"></a> **球形墨卡托投影**：请参阅 [Web 墨卡托](#web-mercator)。 

<a name="spatial-query"></a> **空间查询**：向执行空间操作的服务发出的请求。 如径向搜索，或沿路由搜索。

<a name="spatial-reference"></a> **空间引用**：用于精确定位地理实体的基于坐标的本地、区域或全球系统。 它定义用于将地图坐标与现实世界中的位置相关联的坐标系。 空间参考可确保可以集成来自不同图层或源的空间数据，以便进行精确查看或分析。 Azure Maps 使用 [EPSG:3857](https://epsg.io/3857) 坐标参考系统和 WGS 84，以输入几何数据。

<a name="sql-spatial"></a> **SQL 空间**：指 SQL Azure 和 SQL Server 2008 及更高版本中内置的空间功能。 此空间功能也可作为 .NET 库使用，可以独立于 SQL Server 使用。 有关详细信息，请参阅[空间数据 (SQL Server) 文档](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)。

<a name="subscription-key"></a>**订阅密钥**：请参阅[共享密钥身份验证](#shared-key-authentication)。

<a name="synchronous-request"></a> **同步请求**：HTTP 请求打开连接并等待响应。 浏览器限制可从页面发出的并发 HTTP 请求的数量。 如果同时发出多个长时间运行的同步请求，则可以达到此限制。 请求将延迟，直到其他请求之一完成。

## <a name="t"></a>T

<a name="telematics"></a> **远程信息处理**：通过电信设备发送、接收和存储信息，同时实现对远程对象的控制。 

<a name="temporal-data"></a> **临时数据**：专门指时间或日期的数据。 临时数据可能指离散事件，例如雷击、移动物体（例如火车）或重复观察结果（例如流量传感器计数）。

<a name="terrain"></a> **地形**：具有特定特征的土地区域，例如沙地或山地地形。

<a name="thematic-maps"></a> **主题地图**：主题地图是用于反映有关地理区域的主题的简单地图。 这种类型的地图的常见方案是根据某些数据指标对管理区域（如国家/地区）进行着色。

<a name="tile-layer"></a> **图块层**：通过将地图图块（矩形部分）组合成连续图层进行显示的图层。 图块分为两类：光栅图像图块和矢量图块。 栅格切片图层通常提前呈现，并且它们作为图像存储在服务器上。 栅格切片图层可能使用较大的存储空间。 矢量切片图层在客户端应用程序中几乎实时呈现。 因此，矢量切片图层的服务器端存储要求较小。

<a name="time-zone"></a> **时区**：地球上的一个区域，因为法律、商业和社会目的而遵守统一标准时间。 时区往往遵循国家/区域及其细分的边界。

<a name="transaction"></a> **事务**Azure Maps 使用事务许可模型，其中；

- 为每 15 个请求的地图或流量图块创建一个事务。
- 为 Azure 映射中对其中一个服务的 API 调用创建一个事务。 搜索和路由是 Azure 地图服务的示例。

<a name="transformation"></a> **转换**：在不同地理坐标系之间转换数据的过程。 例如，你的一些数据可能在英国捕获且基于 OSGB 1936 地理坐标系统。 Azure Maps 使用 WGS84 的 [EPSG:3857](https://epsg.io/3857) 坐标参考系统变体。 因此，为了正确显示数据，需要将其坐标从一个系统转换为另一个系统。

<a name="traveling-salesmen-problem-tsp"></a> **旅行商问题 (TSP)**：哈密尔顿线路问题，销售人员必须找到访问一系列拜访地点，然后返回起始地点的最有效方法。  

<a name="trilateration"></a>**三角化**：通过测量所有三个点之间的距离，确定地球表面上点相对于另外两个点的位置的过程。

<a name="turn-by-turn-navigation"></a> **逐向道路导航**：一种应用程序，用于在用户即将进行下一个操纵时，提供路线每个步骤的路线说明。

## <a name="v"></a>V

<a name="vector-data"></a> **矢量数据**：基于坐标的数据，表示为点、线或多边形。

<a name="vector-tile"></a> **矢量图块**：一种开放数据规范，用于使用与地图控件相同的图块系统存储地理空间矢量数据。 另请参阅[图块层](#tile-layer)。

<a name="vehicle-routing-problem-vrp"></a>**车辆布线问题 （VRP）**： 一类问题，其中计算车队一组有序路线，同时考虑为约束集。 这些约束可能包括交货时间窗口、多个路由容量和行驶时间限制。

<a name="voronoi-diagram"></a>**沃罗诺伊图**：空间分区为区域或单元格，围绕一组几何对象，通常点要素。 这些单元格或多边形必须满足 Delaunay 三角形的标准。 与该集中的其他对象相比，区域内的所有位置都更靠近其围绕的对象。 Voronoi 图通常用于描绘地理特征周围的影响区域。 

## <a name="w"></a>W

<a name="waypoint"></a> **航点**：航点是由经度和纬度定义的指定地理位置，用于导航目的。 通常用于表示某人导航路线的点。

<a name="waypoint-optimization"></a> **航点优化**：重新排序一组航点的过程，以尽可能减少通过所有提供的航点所需的行程时间或距离。 根据优化的复杂性，此优化通常称为[旅行销售人员问题](#traveling-salesmen-problem-tsp)或[车辆路由问题](#vehicle-routing-problem-vrp)。

<a name="web-map-service-wms"></a> **Web 地图服务 (WMS)**：WMS 是一种开放地理空间信息联盟 (OGC) 标准，用于定义基于图像的地图服务。 WMS 服务根据需要为地图内的特定区域提供地图图像。 图像包括预呈现的符号系统，若由服务定义，则可以其中一种命名样式呈现。

<a name="web-mercator"></a>**网络墨卡托**：也称为球面墨卡托投影。 它是 Mercator 投影的一个细微变体，主要用于基于 Web 的映射程序。 它使用与用于小比例地图的标准墨卡托投影相同的公式。 但是，Web Mercator 在所有比例下都使用球形公式，但大型墨卡托贴图通常使用投影的椭圆形式。 这种差异在全球尺度上是难以察觉的，但它导致局部区域地图与真正的椭圆墨卡托地图稍有偏差，比例相同。

<a name="wgs84"></a> **WGS84**：用于将空间坐标与地图表面上的位置相关联的一组常量。 WGS84 基准是大多数在线地图提供程序和 GPS 设备使用的标准基准。 Azure Maps 使用 WGS84 的 [EPSG:3857](https://epsg.io/3857) 坐标参考系统变体。

## <a name="z"></a>Z

<a name="z-coordinate"></a> **Z 坐标**：请参阅[海拔高度](#altitude)。 

<a name="zip-code"></a> **邮政编码**：请参阅[邮政编码](#postal-code)。

<a name="Zoom level"></a> **缩放级别**：指定详细程度以及可见的地图数量。 当一直缩放到 0 级时，整个世界地图通常可见。 但是，地图将显示有限的详细信息，如国家/地区名称、边界和海洋名称。 当放大到接近等级 17 时，地图将显示具有详细道路信息的几个城市街区的区域。 在 Azure 地图中，最高缩放级别为 22。 有关详细信息，请参阅[缩放级别和磁贴网格](zoom-levels-and-tile-grid.md)文档。

