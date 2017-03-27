---
title: "Azure 媒体服务输入元数据架构 | Microsoft Docs"
description: "本主题概述了 Azure 媒体服务输入元数据架构。"
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: d72848e2-4b65-4c84-94bc-e2a90a6e7f47
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2274f8b69e82edb2f3e24f23ac6f89dcd7dfcb17


---
# <a name="input-metadata"></a>输入元数据
编码作业与要在其上执行某些编码任务的输入资产（或资产）相关联。  完成任务后，会生成一个输出资产。  输出资产包含视频、音频、缩略图、清单等。输出资产还包含提供输入资产相关元数据的文件。 元数据 XML 文件的名称采用下列格式：&lt;asset_id&gt;_metadata.xml（例如，41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml），其中 &lt;asset_id&gt; 是输入资产的 AssetId 值。  

如果想要检查元数据文件，可以创建 **SAS** 定位器并将文件下载到本地计算机。 可以就如何创建 SAS 定位器并下载[使用媒体服务 .NET SDK 扩展](media-services-dotnet-get-started.md)的文件，找到相关示例。  

本主题讨论作为输入元数据 (&lt;asset_id&gt;_metadata.xml) 的基础的 XML 架构的元素和类型。  若要深入了解包含有关输出资产的元数据的文件，请参阅[输出元数据](media-services-output-metadata-schema.md)。  

> [!NOTE]
> 可以在本主题末尾找到[架构代码](media-services-input-metadata-schema.md#code)和 [XML 示例](media-services-input-metadata-schema.md#xml)。  
> 
> 

## <a name="a-nameassetfilesa-assetfiles-element-root-element"></a><a name="AssetFiles"></a>AssetFiles 元素（根元素）
包含用于编码作业的 [AssetFile 元素](media-services-input-metadata-schema.md#AssetFile)集合。  

请参阅本主题末尾处的 XML 示例：[XML 示例](media-services-input-metadata-schema.md#xml)。  

| Name | 说明 |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs="1" maxOccurs="unbounded" |单个子元素。 有关详细信息，请参阅 [AssetFile 元素](media-services-input-metadata-schema.md#AssetFile)。 |

## <a name="a-nameassetfilea-assetfile-element"></a><a name="AssetFile"></a>AssetFile 元素
 包含属性和描述资产文件的元素。  

 请参阅本主题末尾处的 XML 示例：[XML 示例](media-services-input-metadata-schema.md#xml)。  

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **Name**<br /><br /> 必选 |**xs:string** |资产文件名。 |
| **大小**<br /><br /> 必选 |**xs:long** |资产文件的大小（以字节为单位）。 |
| **持续时间**<br /><br /> 必选 |**xs:duration** |内容播放持续时间。 示例：持续时间 =“PT25M37.757S”。 |
| **NumberOfStreams**<br /><br /> 必选 |**xs:int** |资产文件中的流数。 |
| **FormatNames**<br /><br /> 必选 |**xs:string** |格式名。 |
| **FormatVerboseNames**<br /><br /> 必选 |**xs:string** |格式详细名称。 |
| **StartTime** |**xs:duration** |内容开始时间。 示例：StartTime =“PT2.669S”。 |
| **OverallBitRate** |**xs:int** |资产文件的平均比特率（以 kbps 为单位）。 |

> [!NOTE]
> 以下 4 个子元素必须出现在一个序列中。  
> 
> 

### <a name="child-elements"></a>子元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **节目**<br /><br /> minOccurs=“0” | |资产文件采用 MPEG-TS 格式时，所有[节目元素](media-services-input-metadata-schema.md#Programs)的集合。 |
| **VideoTracks**<br /><br /> minOccurs=“0” | |每个物理资产文件可包含交错成适当容器格式的零个或多个视频轨。 此元素包含所有 [VideoTracks 元素](media-services-input-metadata-schema.md#VideoTracks)（属于资产文件）的集合。 |
| **AudioTracks**<br /><br /> minOccurs=“0” | |每个物理资产文件可包含交错成适当容器格式的零个或多个音频轨。 此元素包含所有 [AudioTracks 元素](media-services-input-metadata-schema.md#AudioTracks)（属于资产文件）的集合。 |
| **元数据**<br /><br /> minOccurs=“0” maxOccurs =“unbounded” |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |资产文件的元数据表示为键\值字符串。 例如：<br /><br /> **&lt;元数据键 =“language” 值="eng" /&gt;** |

## <a name="a-nametracktypea-tracktype"></a><a name="TrackType"></a> TrackType
请参阅本主题末尾处的 XML 示例：[XML 示例](media-services-input-metadata-schema.md#xml)。  

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **Id**<br /><br /> 必选 |**xs:int** |此音频轨或视频轨从零开始的索引。<br /><br /> 这不一定是 MP4 文件中使用的 TrackID。 |
| **编解码器** |**xs:string** |视频轨编解码器字符串。 |
| **CodecLongName** |**xs:string** |音频或视频轨编解码器长名称。 |
| **TimeBase**<br /><br /> 必选 |**xs:string** |时间基数。 示例：TimeBase =“1/48000” |
| **NumberOfFrames** |**xs:int** |帧数（针对视频轨）。 |
| **StartTime** |**xs:duration** |轨道开始时间。 示例：StartTime =“PT2.669S” |
| **持续时间** |**xs:duration** |轨道持续时间。 示例：持续时间 =“PTSampleFormat M37.757S”。 |

> [!NOTE]
> 以下 2 个子元素必须出现在一个序列中。  
> 
> 

### <a name="child-elements"></a>子元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **处置**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |包含演示信息（例如，特定音频轨是否适用于视觉障碍人士）。 |
| **元数据**<br /><br /> minOccurs=“0” maxOccurs =“unbounded” |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |可用于保存各种信息的通用键/值字符串。 例如，键 =“language”，值 =“eng”。 |

## <a name="a-nameaudiotracktypea-audiotracktype-inherits-from-tracktype"></a><a name="AudioTrackType"></a> AudioTrackType（继承自 TrackType）
 **AudioTrackType** 是继承自 [TrackType](media-services-input-metadata-schema.md#TrackType) 的全局复杂类型。  

 此类型表示资产文件中的特定音频轨。  

 请参阅本主题末尾处的 XML 示例：[XML 示例](media-services-input-metadata-schema.md#xml)。  

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **SampleFormat** |**xs:string** |示例格式。 |
| **ChannelLayout** |**xs:string** |通道布局。 |
| **通道**<br /><br /> 必选 |**xs:int** |音频通道数（0 个或多个）。 |
| **SamplingRate**<br /><br /> 必选 |**xs:int** |音频采样速率（以采样数/秒或 Hz 为单位）。 |
| **Bitrate** |**xs:int** |由资产文件计算的平均音频比特率（以 bps 为单位）。 只对基本流有效负载进行计算，且不包括打包开销。 |
| **BitsPerSample** |**xs:int** |wFormatTag 格式类型的每个样本的位数。 |

## <a name="a-namevideotracktypea-videotracktype-inherits-from-tracktype"></a><a name="VideoTrackType"></a> VideoTrackType（继承自 TrackType）
**VideoTrackType** 是继承自 [TrackType](media-services-input-metadata-schema.md#TrackType) 的全局复杂类型。  

此类型表示资产文件中的特定视频轨。  

请参阅本主题末尾处的 XML 示例：[XML 示例](media-services-input-metadata-schema.md#xml)。  

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **FourCC**<br /><br /> 必选 |**xs:string** |视频编解码器 FourCC 代码。 |
| **配置文件** |**xs:string** |视频轨的配置文件。 |
| **级别** |**xs:string** |视频轨的级别。 |
| **PixelFormat** |**xs:string** |视频轨的像素格式。 |
| **宽度**<br /><br /> 必选 |**xs:int** |编码视频宽度（以像素为单位）。 |
| **高度**<br /><br /> 必选 |**xs:int** |编码视频高度（以像素为单位）。 |
| **DisplayAspectRatioNumerator**<br /><br /> 必选 |**xs:double** |视频显示纵横比分子。 |
| **DisplayAspectRatioDenominator**<br /><br /> 必选 |**xs:double** |视频显示纵横比分母。 |
| **DisplayAspectRatioDenominator**<br /><br /> 必选 |**xs:double** |视频采样纵横比分子。 |
| **SampleAspectRatioNumerator** |**xs:double** |视频采样纵横比分子。 |
| **SampleAspectRatioNumerator** |**xs:double** |视频采样纵横比分母。 |
| **FrameRate**<br /><br /> 必选 |**xs:decimal** |采用 .3f 格式测量的视频帧速率。 |
| **Bitrate** |**xs:int** |从资产文件计算的平均视频比特率（以千比特/秒为单位）。 只对基本流有效负载进行计算，且不包括打包开销。 |
| **MaxGOPBitrate** |**xs:int** |此视频轨道的最大 GOP 平均比特率，以千比特/秒为单位。 |
| **HasBFrames** |**xs:int** |B 帧的视频轨数量。 |

## <a name="a-namemetadatatypea-metadatatype"></a><a name="MetadataType"></a> MetadataType
**MetadataType** 是全局复杂类型，可对作为键/值字符串的资产文件的元数据进行描述。 例如，键 =“language”，值 =“eng”。  

请参阅本主题末尾处的 XML 示例：[XML 示例](media-services-input-metadata-schema.md#xml)。  

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **键**<br /><br /> 必选 |**xs:string** |键/值对中的键。 |
| **值**<br /><br /> 必选 |**xs:string** |键/值对中的值。 |

## <a name="a-nameprogramtypea-programtype"></a><a name="ProgramType"></a> ProgramType
**ProgramType** 是描述节目的全局复杂类型。  

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **ProgramId**<br /><br /> 必选 |**xs:int** |节目 ID |
| **NumberOfPrograms**<br /><br /> 必选 |**xs:int** |节目的数量。 |
| **PmtPid**<br /><br /> 必选 |**xs:int** |节目映射表 (PMT) 包含有关节目的信息。  有关详细信息，请参阅 [PMt](http://en.wikipedia.org/wiki/MPEG_transport_stream#PMT)。 |
| **PcrPid**<br /><br /> 必选 |**xs:int** |由解码器使用。 有关详细信息，请参阅 [PCR](http://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: long** |开始演示时间戳。 |
| **EndPTS** |**xs: long** |结束演示时间戳。 |

## <a name="a-namestreamdispositiontypea-streamdispositiontype"></a><a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** 是描述流的全局复杂类型。  

请参阅本主题末尾处的 XML 示例：[XML 示例](media-services-input-metadata-schema.md#xml)。  

### <a name="attributes"></a>属性
| Name | 类型 | 说明 |
| --- | --- | --- |
| **默认**<br /><br /> 必选 |**xs:int** |将此属性设置为 1，表示此为默认演示。 |
| **复录**<br /><br /> 必选 |**xs:int** |将此属性设置为 1，表示此为译制演示。 |
| **原始**<br /><br /> 必选 |**xs:int** |将此属性设置为 1，表示此为原始演示。 |
| **评论**<br /><br /> 必选 |**xs:int** |将此属性设置为 1，表示此轨道包含注释。 |
| **歌词**<br /><br /> 必选 |**xs:int** |将此属性设置为 1，表示此轨道包含歌词。 |
| **卡拉 Ok**<br /><br /> 必选 |**xs:int** |将此属性设置为 1，表示此为卡拉 OK 轨道（仅背景音乐，没有歌唱部分）。 |
| **强制**<br /><br /> 必选 |**xs:int** |将此属性设置为 1，表示此为强制演示。 |
| **HearingImpaired**<br /><br /> 必选 |**xs:int** |将此属性设置为 1，表示此轨道用于听力障碍人士。 |
| **VisualImpaired**<br /><br /> 必选 |**xs:int** |将此属性设置为 1，表示此轨道用于视觉障碍人士。 |
| **CleanEffects**<br /><br /> 必选 |**xs:int** |将此属性设置为 1，表示该轨道有清晰的效果。 |
| **AttachedPic**<br /><br /> 必选 |**xs:int** |将此属性设置为 1，表示该轨道包含图片。 |

## <a name="a-nameprogramsa-programs-element"></a><a name="Programs"></a> 节目元素
拥有多个**节目**元素的包装元素。  

### <a name="child-elements"></a>子元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **节目**<br /><br /> minOccurs=“0” maxOccurs =“unbounded” |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |对于 MPEG-TS 格式的资产文件，包含有关资产文件中节目的信息。 |

## <a name="a-namevideotracksa-videotracks-element"></a><a name="VideoTracks"></a>VideoTracks 元素
 拥有多个 **VideoTrack** 元素的包装元素。  

 请参阅本主题末尾处的 XML 示例：[XML 示例](media-services-input-metadata-schema.md#xml)。  

### <a name="child-elements"></a>子元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs=“0” maxOccurs =“unbounded” |[VideoTrackType（继承自 TrackType）](media-services-input-metadata-schema.md#VideoTrackType) |包含有关资产文件中视频轨的信息。 |

## <a name="a-nameaudiotracksa-audiotracks-element"></a><a name="AudioTracks"></a>AudioTracks 元素
 拥有多个 **AudioTrack** 元素的包装元素。  

 请参阅本主题末尾处的 XML 示例：[XML 示例](media-services-input-metadata-schema.md#xml)。  

### <a name="elements"></a>元素
| Name | 类型 | 说明 |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs=“0” maxOccurs =“unbounded” |[AudioTrackType（继承自 TrackType）](media-services-input-metadata-schema.md#AudioTrackType) |包含有关资产文件中音频轨的信息。 |

## <a name="a-namecodea-schema-code"></a><a name="code"></a> 架构代码
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               elementFormDefault="qualified">  

      <xs:complexType name="MetadataType">  
        <xs:attribute name="key"   type="xs:string" use="required"/>  
        <xs:attribute name="value" type="xs:string" use="required"/>  
      </xs:complexType>  

      <xs:complexType name="ProgramType">  
        <xs:attribute name="ProgramId" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Program Id</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Number of programs</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PmtPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pmt pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PcrPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pcr pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="StartPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>start pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="EndPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>end pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="StreamDispositionType">  
        <xs:attribute name="Default"          type="xs:int" use="required" />  
        <xs:attribute name="Dub"              type="xs:int" use="required" />  
        <xs:attribute name="Original"         type="xs:int" use="required" />  
        <xs:attribute name="Comment"          type="xs:int" use="required" />  
        <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
        <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
        <xs:attribute name="Forced"           type="xs:int" use="required" />  
        <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
        <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
        <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
        <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
      </xs:complexType>  

      <xs:complexType name="TrackType" abstract="true">  
        <xs:sequence>  
          <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
          <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
        </xs:sequence>  
        <xs:attribute name="Id" use="required">  
          <xs:annotation>  
            <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Codec" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec string</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="CodecLongName" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec long name</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="TimeBase"  type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfFrames">  
          <xs:annotation>  
            <xs:documentation>number of frames</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="StartTime" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Duration" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="VideoTrackType">  
        <xs:annotation>  
          <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="FourCC" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>video codec FourCC code</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Profile" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>profile</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Level" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>level</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="PixelFormat" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>Video track's pixel format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Width" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video width in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Height" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video height in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioNumerator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioDenominator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="FrameRate" use="required">  
              <xs:annotation>  
                <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:decimal">  
                  <xs:minInclusive value="0"/>  
                  <xs:fractionDigits value="3"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="MaxGOPBitrate">  
              <xs:annotation>  
                <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="HasBFrames" type="xs:int">  
              <xs:annotation>  
                <xs:documentation>video track number of B frames</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:complexType name="AudioTrackType">  
        <xs:annotation>  
          <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="SampleFormat"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>sample format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="ChannelLayout"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>channel layout</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Channels" use="required">  
              <xs:annotation>  
                <xs:documentation>number of audio channels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SamplingRate" use="required">  
              <xs:annotation>  
                <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="BitsPerSample">  
              <xs:annotation>  
                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:element name="AssetFiles">  
        <xs:annotation>  
          <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
        </xs:annotation>  
        <xs:complexType>  
          <xs:sequence>  
            <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
              <xs:annotation>  
                <xs:documentation>asset file</xs:documentation>  
              </xs:annotation>  
              <xs:complexType>  
                <xs:sequence>  
                  <xs:element name="Programs" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
                </xs:sequence>  
                <xs:attribute name="Name" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>the media asset file name</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="Size" use="required">  
                  <xs:annotation>  
                    <xs:documentation>size of file in bytes</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:long">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
                <xs:attribute name="Duration" type="xs:duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
                  <xs:annotation>  
                    <xs:documentation>number of streams in asset file</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatNames" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format verbose names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="StartTime" type="xs:duration">  
                  <xs:annotation>  
                    <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="OverallBitRate">  
                  <xs:annotation>  
                    <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:int">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  


## <a name="a-namexmla-xml-example"></a><a name="xml"></a>XML 示例
下面是输入元数据文件的示例。  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
      <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
        <VideoTracks>  
          <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
          </VideoTrack>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
          </AudioTrack>  
        </AudioTracks>  
        <Metadata key="major_brand" value="mp42" />  
        <Metadata key="minor_version" value="0" />  
        <Metadata key="compatible_brands" value="mp42mp41" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
        <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>后续步骤
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->


