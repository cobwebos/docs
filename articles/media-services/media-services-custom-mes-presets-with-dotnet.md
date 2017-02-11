---
title: "使用 Media Encoder Standard 进行高级编码 | Microsoft Docs"
description: "本主题说明如何通过自定义媒体编码器标准任务预设执行高级编码。 本主题说明如何使用媒体服务 .NET SDK 创建编码任务和作业。 此外，还说明如何向编码作业提供自定义预设。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: ec95392f-d34a-4c22-a6df-5274eaac445b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e85bc8a1e9193474fa3c9ef3f19ead25f314ecac


---
# <a name="advanced-encoding-with-media-encoder-standard"></a>使用媒体编码器标准版进行高级编码
## <a name="overview"></a>概述
本主题演示如何使用媒体编码器标准版执行高级编码任务。 本主题演示[如何使用 .NET 创建编码任务和执行此任务的作业](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet)。 此外，还说明如何向编码任务提供自定义预设。 有关预设所用元素的说明，请参阅[此文档](https://msdn.microsoft.com/library/mt269962.aspx)。 

下面演示了执行以下编码任务的自定义预设：

* [生成缩略图](media-services-custom-mes-presets-with-dotnet.md#thumbnails)
* [剪裁视频（剪辑）](media-services-custom-mes-presets-with-dotnet.md#trim_video)
* [创建覆盖层](media-services-custom-mes-presets-with-dotnet.md#overlay)
* [在输入不包含音频时插入静音音轨](media-services-custom-mes-presets-with-dotnet.md#silent_audio)
* [禁用自动取消隔行扫描](media-services-custom-mes-presets-with-dotnet.md#deinterlacing)
* [仅音频预设](media-services-custom-mes-presets-with-dotnet.md#audio_only)

## <a name="a-idencodingwithdotnetaencoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>使用媒体服务 .NET SDK 进行编码
以下代码示例使用媒体服务 .NET SDK 执行下列任务：

* 创建编码作业。
* 获取对媒体编码器标准版编码器的引用。
* 加载自定义 XML 或 JSON 预设。 可以在某个文件中保存 XML 或 JSON（例如 [XML](media-services-custom-mes-presets-with-dotnet.md#xml) 或 [JSON](media-services-custom-mes-presets-with-dotnet.md#json)），然后使用以下代码加载该文件。
  
            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText(fileName);  
* 将编码任务添加到作业。 
* 指定要编码的输入资产。
* 创建将包含所编码资产的输出资产。
* 添加事件处理程序以检查作业进度。
* 提交作业。
  
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
  
        namespace CustomizeMESPresests
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
  
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
  
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
  
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
  
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
  
                    // Get an uploaded asset.
                    var asset = _context.Assets.FirstOrDefault();
  
                    // Encode and generate the output using custom presets.
                    EncodeToAdaptiveBitrateMP4Set(asset);
  
                    Console.ReadLine();
                }
  
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText("CustomPreset_JSON.json");

                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);

                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);

                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();

                    return job.OutputMediaAssets[0];
                }

                static public IAsset UploadMediaFilesFromFolder(string folderPath)
                {
                    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);

                    foreach (var af in asset.AssetFiles)
                    {
                        // The following code assumes 
                        // you have an input folder with one MP4 and one overlay image file.
                        if (af.Name.Contains(".mp4"))
                            af.IsPrimary = true;
                        else
                            af.IsPrimary = false;

                        af.Update();
                    }

                    return asset;
                }


                static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText(customPresetFileName);

                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);

                    // Specify the input assets to be encoded.
                    // This asset contains a source file and an overlay file.
                    task.InputAssets.Add(assetSource);

                    // Add an output asset to contain the results of the job. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);

                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();

                    return job.OutputMediaAssets[0];
                }


                private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:

                            // Cast sender as a job.
                            IJob job = (IJob)sender;

                            // Display or log error details as needed.
                            break;
                        default:
                            break;
                    }
                }


                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                    return processor;
                }

            }
        }


## <a name="a-idthumbnailsagenerate-thumbnails"></a><a id="thumbnails"></a>生成缩略图
本部分说明如何自定义生成缩略图的预设。 下面定义的预设包含有关如何将文件编码的信息，以及生成缩略图时所需的信息。 可使用[此处](https://msdn.microsoft.com/library/mt269960.aspx)所述的任何 MES 预设，并添加生成缩略图的代码。  

> [!NOTE]
> 如果要编码为单比特率视频，以下预设中的 **SceneChangeDetection** 设置只能设置为 true。 如果要编码为多比特率视频并将 **SceneChangeDetection** 设为 true，则编码器将返回错误。  
> 
> 

有关架构的信息，请参阅[此](https://msdn.microsoft.com/library/mt269962.aspx)主题。

请务必仔细阅读[注意事项](media-services-custom-mes-presets-with-dotnet.md#considerations)部分。

### <a name="a-idjsonajson-preset"></a><a id="json"></a>JSON 预设
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"

            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a name="a-idxmlaxml-preset"></a><a id="xml"></a>XML 预设
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

### <a name="considerations"></a>注意事项
请注意以下事项：

* 为 Start/Step/Range 使用的显式时间戳假设输入源的长度至少为 1 分钟。
* Jpg/Png/BmpImage 元素包含 Start、Step 和 Range 字符串属性 – 这些属性解释如下：
  
  * 帧数（如果为非负整数），例如： "Start": "120"；
  * 相对于源持续时间（如果以 % 后缀表示），例如： "Start": "15%"，或者
  * 时间戳（如果以 HH:MM:SS... 格式表示）。 例如 "Start" : "00:01:00"
    
    你可以随意混搭使用表示法。
    
    此外，Start 还支持特殊的宏 {Best}，它会尝试判断第一个“有意义”的内容帧。注意：（Start 设置为 {Best} 时，将忽略 Step 与 Range）
  * 默认值：Start:{Best}
* 需要显式提供每个图像格式的输出格式：Jpg/Png/BmpFormat。 MES 会将 JpgVideo（如果已指定）与 JpgFormat 进行匹配，依此类推。 OutputFormat 引入了新的图像编解码器特定宏 {Index}，需要为图像输出格式提供该宏一次（且只需一次）。

## <a name="a-idtrimvideoatrim-a-video-clipping"></a><a id="trim_video"></a>剪裁视频（剪切）
本部分说明如何修改编码器预设，以裁剪或修剪其输入为所谓的夹层文件或按需文件的输入视频。 也可以使用编码器来剪辑或剪裁从实时流捕获或存档的资产 – [此博客](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)提供了详细信息。

若要剪裁视频，可以使用[此处](https://msdn.microsoft.com/library/mt269960.aspx)所述的任何 MES 预设，并修改 **Sources** 元素（如下所示）。 StartTime 的值需与输入视频的绝对时间戳匹配。 例如，如果输入视频第一帧的时间戳为 12:00:10.000，则 StartTime 应大于或等于 12:00:10.000。 在以下示例中，假设输入视频的起始时间戳为零。 请注意，**Sources** 应位于架构顶部。 

### <a name="a-idjsonajson-preset"></a><a id="json"></a>JSON 预设
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    } 

### <a name="xml-preset"></a>XML 预设
若要剪裁视频，可以使用[此处](https://msdn.microsoft.com/library/mt269960.aspx)所述的任何 MES 预设，并修改 **Sources** 元素（如下所示）。

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

## <a name="a-idoverlayacreate-an-overlay"></a><a id="overlay"></a>创建覆盖层
媒体编码器标准允许你在现有视频上覆盖图像。 目前支持以下格式：png、jpg、gif 和 bmp。 下面定义的预设是视频覆盖层的基本示例。

除了定义预设文件外，你还必须让媒体服务知道资产中的哪个文件是覆盖层图像，哪个文件是你要在其上覆盖图像的源视频。 视频文件必须是**主**文件。 

上面的 .NET 示例定义了两个函数：**UploadMediaFilesFromFolder** 和 **EncodeWithOverlay**。 UploadMediaFilesFromFolder 函数从文件夹上载文件（例如 BigBuckBunny.mp4 和 Image001.png），并将 mp4 文件设置为资产中的主文件。 **EncodeWithOverlay** 函数使用传递给它的自定义预设文件（例如，下面的预设）来创建编码任务。 

> [!NOTE]
> 当前限制：
> 
> 不支持覆盖层不透明度设置。
> 
> 源视频文件和覆盖层文件必须位于同一个资产中。
> 
> 

### <a name="json-preset"></a>JSON 预设
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1,
                  "InputLoop": true
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Baseline",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "400",
              "Height": "400",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="xml-preset"></a>XML 预设
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>400</Width>
              <Height>400</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Baseline</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

## <a name="a-idsilentaudioainsert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>在输入不包含音频时插入静音曲目
默认情况下，如果你要向编码器发送仅包含视频而不包含音频的输入，输出资产将包含仅有视频数据的文件。 某些播放器可能无法处理此类输出流。 对于这种方案，你可以使用此设置来强制编码器将静音曲目添加到输出。

若要强制编码器在输入不包含音频时生成包含静音曲目的资产，请指定“InsertSilenceIfNoAudio”值。

可使用[此处](https://msdn.microsoft.com/library/mt269960.aspx)所述的任何 MES 预设，并进行以下修改：

### <a name="json-preset"></a>JSON 预设
    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

### <a name="xml-preset"></a>XML 预设
    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

## <a name="a-iddeinterlacingadisable-auto-de-interlacing"></a><a id="deinterlacing"></a>禁用自动取消隔行扫描
如果客户想要将隔行扫描内容自动取消隔行扫描，不需要执行任何操作。 当自动取消隔行扫描打开（默认设置）时，MES 将自动检测隔行扫描帧，并且只将标记为隔行扫描的帧取消隔行扫描。

你可以关闭自动取消隔行扫描， 但不建议这样做。

### <a name="json-preset"></a>JSON 预设
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

### <a name="xml-preset"></a>XML 预设
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


## <a name="a-idaudioonlyaaudio-only-presets"></a><a id="audio_only"></a>仅音频预设
本部分介绍两个仅音频 MES 预设：AAC 音频和 AAC 优质音频。

### <a name="aac-audio"></a>AAC 音频
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="aac-good-quality-audio"></a>AAC 优质音频
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>另请参阅
[媒体服务编码概述](media-services-encode-asset.md)




<!--HONumber=Nov16_HO3-->


