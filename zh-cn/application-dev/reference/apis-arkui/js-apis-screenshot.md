# @ohos.screenshot (屏幕截图)

本模块提供屏幕截图的能力。

>  **说明：**
>
> - 本模块首批接口从API version 12开始支持。后续版本的新增接口，采用上角标单独标记接口的起始版本。

## 导入模块

```ts
import screenshot from '@ohos.screenshot';
```

## Rect

表示截取图像的区域。

**系统能力：** SystemCapability.WindowManager.WindowManager.Core

| 名称 | 类型   | 必填 | 说明                                                         |
| ------ | ------ | ---- | ------------------------------------------------------------ |
| left   | number | 是   | 表示截取图像区域的左边界，单位为px，该参数应为整数。 |
| top    | number | 是   | 表示截取图像区域的上边界，单位为px，该参数应为整数。 |
| width  | number | 是   | 表示截取图像区域的宽度，单位为px，该参数应为整数。 |
| height | number | 是   | 表示截取图像区域的高度，单位为px，该参数应为整数。 |

## PickInfo

截取图像的信息。

**系统能力：** SystemCapability.WindowManager.WindowManager.Core


| 名称                 | 类型          | 必填 | 说明                                                         |
| -------------------- | ------------- | ---- | ------------------------------------------------------------ |
| pickRect             | [Rect](#rect) | 是   | 表示截取图像的区域。                       |
| pixelMap             | &lt;[image.PixelMap](../apis-image-kit/js-apis-image.md#pixelmap7)&gt;  | 是   | 表示截取的图像PixelMap对象 |

## screenshot.pick

pick(): Promise&lt;PickInfo&gt;

获取屏幕截图。

**系统能力：** SystemCapability.WindowManager.WindowManager.Core

**返回值：**

| 类型                          | 说明                                            |
| ----------------------------- | ----------------------------------------------- |
| Promise&lt;[PickInfo](#pickinfo)&gt; | Promise对象。返回一个PickInfo对象。 |

**示例：**

```ts
import { BusinessError } from '@ohos.base';

try {
  let promise = screenshot.pick();
  promise.then((pickInfo: screenshot.PickInfo) => {
    console.log('pick Pixel bytes number: ' + pickInfo.pixelMap.getPixelBytesNumber());
    console.log('pick Rect: ' + pickInfo.pickRect);
    pickInfo.pixelMap.release(); // PixelMap使用完后及时释放内存
  }).catch((err: BusinessError) => {
    console.log('Failed to pick. Code: ' + JSON.stringify(err));
  });
} catch (exception) {
  console.error('Failed to pick Code: ' + JSON.stringify(exception));
};
```