连接采集卡、断开采集卡、扫描采集卡所连接相机、连接相机

## 1. SAPData

数据类型的父类。没必要创建实例。

```cpp
void Clear();	//数据单元清空为黑色
SapFormatType GetType();	//获取类型
```

## 2. SapAcquisition 

操作采集资源。将数据资源传输到另一个节点。

\#include <SapClassBasic.h>  

| --                                                           | --                                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| bool Create();                                               | 创建采集对象所需的所有低级Sapera资源。在调用SapTransfer::Create之前调用。 |
| BOOL Destroy();                                              | 摧毁获取对象所需的所有低级Sapera资源。在SapTransfer::Destroy之后调用。 |
| BOOL EnableImageFilter(BOOL enable = TRUE);                  | 启用或禁用采集图像过滤器                                     |
| BOOL EnableLut(BOOL enable = TRUE);                          | 启用或禁用采集查找表。<br/>当LUT被禁用时，它不影响获取图像。 |
| SapAcqCallback GetCallback();                                | 获取事件的当前应用程序回调方法。若不在构造中指定，初始值是NULL |
| int GetCamsel();<br/>BOOL SetCamSel(int camSel);             | 以index（从0开始）指定采集设备中获取图像的相机。<br/>初始值为0，调用Create()方法时自动指定初始值。<br/>不能在Create()调用之前调用，也不能在采集中调用（SapTransfer::IsGrabbing） |
| const char *GetConfigFile();<br/>BOOL SetConfigFile(const char *configFile); | 获取/设置采集配置文件（CCF）的名称。<br/>通常在SapAcquisition构造函数中设置此属性的初始值。如果您使用默认构造函数，则此值为NULL。<br/>只能在Create方法之前调用SetConfigFile。 |
| void *GetContext();                                          | 获取与采集事件关联的应用程序上下文。<br/>此属性的初始值为NULL，除非在构造函数中指定其他值。 |
| SapAcquisition::EventType GetEventType(); <br/>BOOL SetEventType(SapAcquisition::EventType eventType); | 获取/设置已注册的获取事件类型的组合。<br/>此属性的初始值为EventNone，除非在构造函数中指定其他值。<br/>只能在Create方法之前调用SetEventType |
| SapAcquisition::FlipMode GetFlipMode(); <br/>BOOL SetFlipMode(SapAcquisition::FlipMode flipMode);<br/>SapAcquisition::FlipNone——不翻转<br/>SapAcquisition::FlipHorizontal——水平翻转<br/>SapAcquisition::FlipVertical——垂直翻转 | 获取/设置获取的图像的翻转（即镜像）模式。<br/>此属性的初始值是FlipNone。<br/>只能在Create方法之后调用SetFlipMode |
| BOOL GetImageFilter(int filterIndex, SapBuffer *pKernel); <br/>BOOL SetImageFilter(int filterIndex, SapBuffer *pKernel); | 设置**图像过滤器**内核值。<br/>合适的kernel value，图像过滤器可以对图片进行平滑、边缘或峰值增强、位置变换。<br/>通过SapAcquisition::IsImageFilterAvailable检查设备是否支持图像过滤器。<br/> |
| BOOL GetImageFilterKernelSize(int filterIndex, ImageFilterKernelSize *pKernelSize); | 使用SapAcquisition:：GetImageFilterKernelSize函数检索**图像过滤器的大小**。<br/>pKernelSize——内核尺寸。可能值：SapAcquisition::ImageFilterSize1x1<br/>SapAcquisition::ImageFilterSize2x2<br/>... |
| const char* GetLabel();                                      | 获取采集资源的文本描述：**服务器名[资源名]**                 |
| SapLut *GetLut(int lutIndex = 0);                            | 获取**查找表**。<br/>采集设备上的所有可用LUT都是自动创建的并在调用Create方法时初始化。<br/>通过SapLut类操作LUT，使用ApplyLut()应用到LUT上。<br/>lutIndex参数的有效值是从0到GetNumLut方法返回的值减去1。<br/>如果当前采集设备不支持查找表，GetLut将返回NULL。 |
| int GetNumLut();                                             | 获取可用采集查找表的数量，其中值0表示当前采集设备没有LUT。<br/>调用Create方法之后，返回的值才有意义。 |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |



## 3. SapAcqCallbackInfo

充当一个容器，用于存储SapAcquisition类回调函数的所有参数。



## 4. SapAcqDevice 

\#include <SapClassBasic.h>  

**SapAcqDevice对象仅用于存储采集设备参数。**

提供从设备（如：Teledyne DALSA GigE Vision cameras）读/写的特性。

| ---                                                          | ---                                                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| SapAcqDevice(SapLocation location = SapLocation::ServerSystem, BOOL readOnly = FALSE); | SapLocation对象，指定采集设备所在的服务器，以及该服务器上采集设备的索引。<br/>readOnly=TRUE强制对设备进行只读访问。（只有当另一个程序已获取对同个设备的读写权限时，该选项才有用。）<br/> |
| SapAcqDevice(SapLocation location, const char *configFile);  | configFile—CCF文件(采集设备配置文件，包含所有采集参数，可以是CamExpert或自己的程序创建的。)<br/>当不需要配置文件时，将使用第一个构造函数。在这种情况下，默认使用采集设备的参数<br/> |
| BOOL Create();                                               | 在调用SapTransfer::Create前调用                              |
| BOOL Destroy();                                              | 在调用SapTransfer::Destroy之后调用                           |
| BOOL DeleteDeviceFile(const char *deviceFileName);           | 删除设备上的指定文件。<br/>要查找可用的设备文件名，请将GetFileCount()和GetFileNameByIndex() |
| BOOL DeleteDeviceFile(int deviceFileIndex);                  | 获取文件index，可通过GetFileIndexByName()                    |
| BOOL GetCategoryCount(int *categoryCount);                   | 返回目录的数目。<br/>等价于：GetFeatureCount()+GetFeatureInfo()+SapFeature::GetCategory() |
| BOOL GetCategoryFeatureCount(int categoryIndex, int *featureCount); | 返回指定目录下的feature数目。<br/>categoryIndex目录index，从0到GetCategoryCount()-1 |
| BOOL GetCategoryFeatureIndexes(int categoryIndex, int *indexList, int indexListSize); | 返回与类别中的每个feature相对应的索引数组。<br/>indexList——feature数组，32bit<br/>indexListSize——indexList数组的字节数。通过GetCategoryFeatureCount获得featureCount，乘以sizeof(int) |
| BOOL GetCategoryPath(int categoryIndex, char* path, int pathSize); | path——目录全名<br/>pathSize——存储path的char数组的长度        |
| const char* GetConfigFile();                                 | 获取Create()被调用时加载的采集配置文件CCF<br/>一般在构造函数中设置初始值，使用默认构造时返回NULL |
| BOOL SetConfigFile(const char* configFile);                  | 必须在调用Create()前调用                                     |
| const char* GetConfigName();                                 |                                                              |
| BOOL SetConfigName(const char* configName);                  | configName——写入CCF文件的配置的名称。字符串的长度必须不超过64个字符。 |
| BOOL GetEventCount(int *eventCount);                         | 返回采集设备支持的事件总数。                                 |
| BOOL GetEventNameByIndex(int eventIndex, char* eventName, int eventNameSize); | 事件的index（范围0到GetEventCount()-1）                      |
| BOOL GetEventIndexByName(const char* eventName, int* eventIndex); | 从事件名获取事件index。有关支持的事件列表，请参阅采集设备用户手册 |
| BOOL GetFeatureCount(int* featureCount);                     | 返回采集设备支持的feature数量<br/>只有在Create调用后才有效   |
| BOOL GetFeatureInfo(const char* featureName, SapFeature* feature); | 请注意，**在调用此方法之前，必须为SapFeature对象调用Create方法**<br/>Feature——SapFeature对象，存储feature信息 |
| BOOL GetFeatureInfo(int featureIndex, SapFeature* feature);  | featureIndex——从0到featureCount-1                            |
| BOOL GetFeatureNameByIndex(int featureIndex, char* featureName, int featureNameSize); | 根据feature的index显示feature名称。                          |
| bool GetFeatureValue(const char* featureName, INT32* featureValue); | 获取feature值                                                |
| bool GetFeatureValue(int featureIndex, INT32* featureValue); |                                                              |
| BOOL GetFileCount(int* fileCount);                           | 返回采集设备支持的文件数                                     |
| BOOL GetFileNameByIndex(int fileIndex, char* fileName, int fileNameSize); | 返回与指定索引关联的设备文件的名称                           |
| BOOL GetFileIndexByName(const char* fileName, int* fileIndex); | 返回与指定名称关联的设备文件的索引                           |
| BOOL GetFileProperty(int fileIndex, SapAcqDevice::FileProperty propertyType, UINT64* filePropertyValue); | ***获取文件属性值***<br/>propertyType—SapAcqDevice::FilePropertyAccessMode或SapAcqDevice::FilePropertySize |
| BOOL GetFileProperty(const char* fileName, SapAcqDevice::FileProperty propertyType, UINT64* filePropertyValue); | FilePropertyAccessMode的可能取值：<br/>SapAcqDevice::FileAccessModeNone <br/>SapAcqDevice::FileAccessModeReadOnly <br/>SapAcqDevice::FileAccessModeWriteOnly <br/>SapAcqDevice::FileAccessModeReadWrite |
| const char* GetLabel();                                      | 获取采集设备资源的文本描述。资源的名称为：ServerName[ResourceName]。 |
| const char* GetModeName();<br/>BOOL SetModeName(const char* modeName); | 获取/设置使用SaveFeatures方法保存设备功能时要使用的模式名称。<br/>公司名称+模式<br/>通过LoadFeatures加载CCF文件时，modeName参数自动装载 |
| BOOL GetReadOnly(); <br/>BOOL SetReadOnly(BOOL readOnly);    | 获取/设置类是否对设备具有只读访问权限。                      |
| UpdateFeatureMode GetUpdateFeatureMode();                    | 获取/设置feature写入设备的模式<br/>mode可能取值：<br/>SapAcqDevice::UpdateFeatureAuto或SapAcqDevice::UpdateFeatureManual<br/>前者使用SetFeatureValue修改feature时，会立即发送到设备。<br/>后者将每个feature都临时缓存，需要调用UpdateFeaturesToDevice将一次性全部发送到设备 |
| BOOL SetUpdateFeatureMode(UpdateFeatureMode mode);           |                                                              |
| BOOL IsCallbackRegistered(const char* eventName, BOOL* isRegistered);<br/> | 判断event上是否注册了回调函数。<br/>eventIndex范围0到GetEventCount()<br/>可在循环里使用，判断某个事件是否有需要解除注册的回调函数 |
| BOOL IsCallbackRegistered(int eventIndex, BOOL* isRegistered); |                                                              |
| BOOL IsEventAvailable(const char* eventName, BOOL* isAvailable); | 判断该event是否被当前采集设备支持                            |
| BOOL IsFeatureAvailable(const char *featureName, BOOL *isAvailable); | 判断feature是否被当前采集设备支持。<br/>当程序支持多种采集设备，每种都有不同的feature集合时，该函数很有用 |
| BOOL IsFeatureRecordingStarted();                            | 返回featureRecording是否开启。<br/>                          |
| SapAcqDevice::StartFeatureRecording                          |                                                              |
| SapAcqDevice::PauseFeatureRecording                          |                                                              |
| SapAcqDevice::StopFeatureRecording                           |                                                              |
| SapAcqDevice::ResumeFeatureRecording                         | 恢复feature recording                                        |
| BOOL IsFeatureRecordingStarted();                            | 判断feature recording是否已开始                              |
| BOOL IsFileAccessAvailable();                                | 采集设备的file是否可访问。<br/>若为false，则不可使用GetFileCount、GetFileNameByIndex、GetFileIndexByName、GetFileProperty、WriteFile，ReadFile和DeleteDeviceFile函数。 |
| BOOL LoadFeatures(const char* configFile);                   | 加载CCF文件（CamExpert或调用SaveFeatures生成）<br/>若当前object是read-only access不可调用。 |
| BOOL LoadFeatureRecording(const char *recordFileName);       | 从文本文件加载设备功能设置更改并应用功能设置更改。           |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |



















