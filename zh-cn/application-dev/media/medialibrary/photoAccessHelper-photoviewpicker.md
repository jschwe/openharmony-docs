# 使用Picker选择媒体库资源

用户有时需要分享图片、视频等用户文件，开发者可以通过特定接口拉起系统图库，用户自行选择待分享的资源，然后最终分享出去。此接口本身无需申请权限，目前适用于界面UIAbility，使用窗口组件触发。具体使用方式如下：

1. 导入选择器模块和文件管理模块。

   ```ts
   import photoAccessHelper from '@ohos.file.photoAccessHelper';
   import fs from '@ohos.file.fs';
   import { BusinessError } from '@ohos.base';
   ```

2. 创建图片-音频类型文件选择选项实例。

   ```ts
   const photoSelectOptions = new photoAccessHelper.PhotoSelectOptions();
   ```

3. 选择媒体文件类型和选择媒体文件的最大数目。
   以下示例以图片选择为例，媒体文件类型请参见[PhotoViewMIMETypes](../../reference/apis-media-library-kit/js-apis-photoAccessHelper.md#photoviewmimetypes)。

   ```ts
   photoSelectOptions.MIMEType = photoAccessHelper.PhotoViewMIMETypes.IMAGE_TYPE; // 过滤选择媒体文件类型为IMAGE
   photoSelectOptions.maxSelectNumber = 5; // 选择媒体文件的最大数目
   ```

4. 创建图库选择器实例，调用[PhotoViewPicker.select](../../reference/apis-media-library-kit/js-apis-photoAccessHelper.md#select)接口拉起图库界面进行文件选择。文件选择成功后，返回[PhotoSelectResult](../../reference/apis-media-library-kit/js-apis-photoAccessHelper.md#photoselectresult)结果集。

   ```ts
   let uris: Array<string> = [];
   const photoViewPicker = new photoAccessHelper.PhotoViewPicker();
   photoViewPicker.select(photoSelectOptions).then((photoSelectResult: photoAccessHelper.PhotoSelectResult) => {
     uris = photoSelectResult.photoUris;
     console.info('photoViewPicker.select to file succeed and uris are:' + uris);
   }).catch((err: BusinessError) => {
     console.error(`Invoke photoViewPicker.select failed, code is ${err.code}, message is ${err.message}`);
   })
   ```

   select返回的uri权限是只读权限，可以根据结果集中uri进行读取文件数据操作。注意不能在picker的回调里直接使用此uri进行打开文件操作，需要定义一个全局变量保存uri，使用类似一个按钮去触发打开文件。可参考[指定URI读取文件数据](#指定uri读取文件数据)。

   也可以通过返回的uri[获取图片或视频资源](#指定uri获取图片或视频资源)。

   如有获取元数据需求，可以通过[文件管理接口](../../reference/apis-core-file-kit/js-apis-file-fs.md)和[文件URI](../../reference/apis-core-file-kit/js-apis-file-fileuri.md)根据uri获取部分文件属性信息，比如文件大小、访问时间、修改时间、文件名、文件路径等。

## 指定URI读取文件数据

1. 待界面从图库返回后，再通过类似一个按钮调用其他函数，使用[fs.openSync](../../reference/apis-core-file-kit/js-apis-file-fs.md#fsopensync)接口，通过uri打开这个文件得到fd。这里需要注意接口权限参数是fs.OpenMode.READ_ONLY。

   ```ts
   let uri: string = '';
   let file = fs.openSync(uri, fs.OpenMode.READ_ONLY);
   console.info('file fd: ' + file.fd);
   ```

2. 通过fd使用[fs.readSync](../../reference/apis-core-file-kit/js-apis-file-fs.md#readsync)接口读取这个文件内的数据，读取完成后关闭fd。

   ```ts
   let buffer = new ArrayBuffer(4096);
   let readLen = fs.readSync(file.fd, buffer);
   console.info('readSync data to file succeed and buffer size is:' + readLen);
   fs.closeSync(file);
   ```

## 指定URI获取图片或视频资源

媒体库支持Picker选择文件URI后，根据指定URI获取图片或视频资源，下面以查询指定URI为'file://media/Photo/1/IMG_datetime_0001/displayName.jpg'为例。

```ts
import dataSharePredicates from '@ohos.data.dataSharePredicates';

const context = getContext(this);
let phAccessHelper = photoAccessHelper.getPhotoAccessHelper(context);

async function example() {
  let predicates: dataSharePredicates.DataSharePredicates = new dataSharePredicates.DataSharePredicates();
  let uri = 'file://media/Photo/1/IMG_datetime_0001/displayName.jpg' // 需保证此uri已存在。
  predicates.equalTo(photoAccessHelper.PhotoKeys.URI, uri.toString());
  let fetchOptions: photoAccessHelper.FetchOptions = {
    fetchColumns: [],
    predicates: predicates
  };

  try {
    let fetchResult: photoAccessHelper.FetchResult<photoAccessHelper.PhotoAsset> = await phAccessHelper.getAssets(fetchOptions);
    let photoAsset: photoAccessHelper.PhotoAsset = await fetchResult.getFirstObject();
    console.info('getAssets photoAsset.uri : ' + photoAsset.uri);
    fetchResult.close();
  } catch (err) {
    console.error('getAssets failed with err: ' + err);
  }
}
```