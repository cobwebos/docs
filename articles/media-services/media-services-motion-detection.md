<properties
	pageTitle="使用 Azure 媒体分析检测动作"
	description="Azure 媒体动作检测器媒体处理器 (MP) 可让你在冗长且平淡的视频中有效识别出你感兴趣的部分。"
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.date="04/14/2016"   
	wacn.date=""/>
 
# 使用 Azure 媒体分析检测动作

##概述

**Azure 媒体动作检测器**媒体处理器 (MP) 可让你在冗长且平淡的视频中有效识别出你感兴趣的部分。可以对静态相机数据片段使用动作检测，以识别视频中有动作的部分。它会生成 JSON 文件，其中包含带时间戳的元数据，以及发生事件的边界区域。

此技术面向安全视频提要，它可以将动作分类为相关事件和误报（例如阴影或光源变化）。这样，你就可以在无需查看无止境的不相关事件的情况下，从相机输出生成安全警报，并从长时间的监控视频中提取感兴趣的片段。

**Azure 媒体动作检测器** MP 目前以预览版提供。

本主题提供有关 **Azure 媒体动作检测器**的详细信息，并演示如何通过适用于 .NET 的媒体服务 SDK 使用它。

##动作检测器输入文件

视频文件。目前支持以下格式：MP4、MOV 和 WMV。

##动作检测器输出文件

动作检测作业将在输出资产中返回 JSON 文件，该文件描述视频中的动作警报和类别。该文件将包含有关在视频中检测到的动作的时间和持续时间的信息。

目前动作检测仅支持普通的动作类别，此类别在输出中称为 **type 2**。

X、Y 坐标和大小以介于 0.0 和 1.0 之间的规范化浮点数列出。将此数值乘以视频的高度和宽度分辨率可获取检测到的动作的区域边框。

每个输出拆分成片段，再细分成间隔，以定义视频中的数据。片段长度并不需要相等，在没有检测到任何动作的情况下，长度可能非常长。

一旦固定背景视频（例如监控视频）中出现运动对象，动作检测器 API 将提供指示器。动作检测器经过训练可减少误报（例如光源和阴影变化）。当前算法限制包括夜视视频、半透明对象和小对象。

###<a id="output_elements"></a>输出 JSON 文件中的元素

下表描述了输出 JSON 文件的元素。

元素|说明
---|---
版本|这是指视频 API 的版本。
时间刻度|视频每秒的“刻度”数。
Offset|时间戳的时间偏移量（以“刻度”为单位）。在版本 1.0 的视频 API 中，此属性始终为 0。在我们将来支持的方案中，此值可能会更改。
Framerate|视频的每秒帧数。
Width, Height|表示视频的宽度和高度（以像素为单位）。
开始|开始时间戳（以“刻度”为单位）。
持续时间|事件的长度（以“刻度”为单位）。
时间间隔|事件中每个条目的间隔（以“刻度”为单位）。
事件|每个事件片段包含在该持续时间内检测到的动作。
类型|在当前版本中，对于一般动作，该属性始终为“2”。此标签可让视频 API 在将来的版本中灵活地为动作分类。
RegionID|如上所述，在此版本中此属性始终为 0。此标签可让视频 API 在将来的版本中灵活地查找各区域中的动作。
区域|表示你关注的动作在视频中的区域。在当前版本的视频 API 中，你法指定区域，视频的整个曲面就是要检测动作的区域。<br/>- ID表示区域 – 此版本中只有一个 ID，即 ID 0。<br/>- 矩形表示你关注的动作所在区域的形状。在此版本中，它始终是矩形。<br/>- 区域具有以 X、Y、宽度及高度表示的维。X 和 Y 坐标表示规范化 0.0 到 1.0 比例中的区域的左上角 XY 坐标。宽度和高度表示规范化 0.0 到 1.0 比例中的区域的大小。在当前版本中，X、Y、宽度和高度始终固定为 0,0 和 1,1。<br/>- 片段。元数据划分成称为“片段”的不同段。每个片段包含开始时间、持续时间、间隔数字和事件。没有事件的片段表示在该开始时间和持续时间内没有检测到任何动作。
括号|每个括号表示事件中的单个间隔。如果该间隔显示空括号，则表示没有检测到动作。
 

##任务配置（预设）

在使用 **Azure 媒体动作检测器**创建任务时，必须指定配置预设。目前，无法在 Azure 媒体动作检测器配置预设中设置任何选项。下面是最起码要提供的配置预设。

	{"version":"1.0"}

##示例视频和动作检测器输出

###包含实际动作的示例

[包含实际动作的示例](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.chinacloudapi.cn%2Fd54876c6-89a5-41de-b1f4-f45b6e10a94f%2FGarage.ism%2Fmanifest)

###JSON 输出

	 {
	 "version": "1.0",
	 "timescale": 60000,
	 "offset": 0,
	 "framerate": 30,
	 "width": 1920,
	 "height": 1080,
	 "regions": [
	   {
	     "id": 0,
	     "type": "rectangle",
	     "x": 0,
	     "y": 0,
	     "width": 1,
	     "height": 1
	   }
	 ],
	 "fragments": [
	   {
	     "start": 0,
	     "duration": 68510
	   },
	   {
	     "start": 68510,
	     "duration": 969999,
	     "interval": 969999,
	     "events": [
	       [
	         {
	           "type": 2,
	           "regionId": 0
	         }
	       ]
	     ]
	   },
	   {
	     "start": 1038509,
	     "duration": 41489
	   }
	 ]
	}

###包含误报的示例

[包含误报的示例（光源变化）：](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.chinacloudapi.cn%2Ffdc6656b-1c10-4f3f-aa7c-07ba073c1615%2FLivingRoomLight.ism%2Fmanifest&tech=flash)

###JSON 输出

	{
	    "version": "1.0",
	    "timescale": 30000,
	    "offset": 0,
	    "framerate": 29.97,
	    "width": 1920,
	    "height": 1080,
	    "regions": [
	    {
	        "id": 0,
	        "type": "rectangle",
	        "x": 0,
	        "y": 0,
	        "width": 1,
	        "height": 1
	    }
	    ],
	    "fragments": [
	    {
	        "start": 0,
	        "duration": 320320
	    }
	    ]
	}


##限制

- 支持的输入视频格式包括 MP4、MOV 和 WMV。
- 动作检测已针对固定背景视频优化。算法专注于降低误报，例如光源变化和阴影。
- 某些动作可能因技术难题而无法检测到，例如夜视视频、半透明对象和小对象。


## 代码示例

以下程序演示如何：

1. 创建资产并将媒体文件上传到资产。
1. 使用基于包含以下 json 预设的配置文件的视频动作检测任务创建一个作业。 
					
		{
		    "version": "1.0"
		}

1. 下载输出 JSON 文件。
		 
        using System;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace VideoMotionDetection
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
		
		        static void Main(string[] args)
		        {
		
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            // Run the VideoMotionDetection job.
		            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
		                                        @"C:\supportFiles\VideoMotionDetection\config.json");
		
		            // Download the job output asset.
		            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
		        }
		
		        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
		        {
		            // Create an asset and upload the input media file to storage.
		            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
		                "My Video Motion Detection Input Asset",
		                AssetCreationOptions.None);
		
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My Video Motion Detection Job");
		
		            // Get a reference to Azure Media Motion Detector.
		            string MediaProcessorName = "Azure Media Motion Detector";
		
		            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
		
		            // Read configuration from the specified file.
		            string configuration = File.ReadAllText(configurationFile);
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input asset.
		            task.InputAssets.Add(asset);
		
		            // Add an output asset to contain the results of the job.
		            task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
		
		            // Use the following event handler to check job progress.  
		            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
		
		            // Launch the job.
		            job.Submit();
		
		            // Check job execution and wait for job to finish.
		            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
		
		            progressJobTask.Wait();
		
		            // If job state is Error, the event handling
		            // method for job progress should log errors.  Here we check
		            // for error state and exit if needed.
		            if (job.State == JobState.Error)
		            {
		                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
		                Console.WriteLine(string.Format("Error: {0}. {1}",
		                                                error.Code,
		                                                error.Message));
		                return null;
		            }
		
		            return job.OutputMediaAssets[0];
		        }
		
		        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
		        {
		            IAsset asset = _context.Assets.Create(assetName, options);
		
		            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
		            assetFile.Upload(filePath);
		
		            return asset;
		        }
		
		        static void DownloadAsset(IAsset asset, string outputDirectory)
		        {
		            foreach (IAssetFile file in asset.AssetFiles)
		            {
		                file.Download(Path.Combine(outputDirectory, file.Name));
		            }
		        }
		
		        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		        {
		            var processor = _context.MediaProcessors
		                .Where(p => p.Name == mediaProcessorName)
		                .ToList()
		                .OrderBy(p => new Version(p.Version))
		                .LastOrDefault();
		
		            if (processor == null)
		                throw new ArgumentException(string.Format("Unknown media processor",
		                                                           mediaProcessorName));
		
		            return processor;
		        }
		
		        static private void StateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine("Job state changed event:");
		            Console.WriteLine("  Previous state: " + e.PreviousState);
		            Console.WriteLine("  Current state: " + e.CurrentState);
		
		            switch (e.CurrentState)
		            {
		                case JobState.Finished:
		                    Console.WriteLine();
		                    Console.WriteLine("Job is finished.");
		                    Console.WriteLine();
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
		                    // LogJobStop(job.Id);
		                    break;
		                default:
		                    break;
		            }
		        }
		
		    }
        }



##相关链接

[Azure Media Services Analytics Overview（Azure 媒体服务分析概述）](/documentation/articles/media-services-analytics-overview)

[Azure Media Analytics demos（Azure 媒体分析演示）](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

<!---HONumber=Mooncake_0509_2016-->