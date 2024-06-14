# 图像类型定义（系统接口）

>**说明：**
>
>本模块首批接口从API version 12开始支持，后续版本的新增接口，采用上角标单独标记接口的起始版本。
>
>本模块接口为系统接口。

## ImageAnalyzerConfig<sup>12+</sup>

图像AI分析配置项。

**原子化服务API：** 从API version 12开始，该接口支持在原子化服务中使用。

| 名称     | 类型                | 必填   | 说明                   |
| ------ | ----------------- | ---- | -------------------- |
| types | [ImageAnalyzerType[]](#imageanalyzertype12) | 是 | 图像AI分析类型。

## ImageAnalyzerType<sup>12+</sup>

图像AI分析类型。

| 名称     | 描述           |
| -------- | -------------- |
| SUBJECT | 主体识别功能。 |
| TEXT | 文字识别功能。 |
| OBJECT_LOOKUP | 对象查找功能。 |

## ImageAIOptions<sup>12+</sup>

图像AI分析选项。

**系统能力：** SystemCapability.ArkUI.ArkUI.Full

| 名称     | 类型                | 必填   | 说明                   |
| ------ | ----------------- | ---- | -------------------- |
| types | [ImageAnalyzerType[]](#imageanalyzertype12) | 否 | 图像AI分析类型。 |
| aiController | [ImageAnalyzerController](#imageanalyzercontroller12) | 否 | 图像AI分析控制器。 |

> **说明：**
>
> 该特性中的参数types优先级高于[ImageAnalyzerConfig](#imageanalyzerconfig12)中的参数types，两者同时设置时以该特性设置的值为准。
>
> 该特性依赖设备能力，且需要和对应组件的enableAnalyzer接口（例如[Image组件](ts-basic-components-image.md#enableanalyzer11)）搭配使用。

## ImageAnalyzerController<sup>12+</sup>

图像分析控制器，可以将此对象绑定至支持的组件，然后通过控制器来调用支持的方法。

### constructor<sup>12+</sup>

constructor()

构造函数。

### getImageAnalyzerSupportTypes<sup>12+</sup>

getImageAnalyzerSupportTypes(): ImageAnalyzerType[]

获取对应组件支持的分析类型。

**系统能力：** SystemCapability.ArkUI.ArkUI.Full

**返回值:**

| 类型     | 描述                      |
| ------ | ----------------------- |
| ImageAnalyzerType[] | 对应组件支持的分析类型。 |