# Image Decoding (C/C++)

Image decoding refers to the process of decoding an archived image in a supported format into a [pixel map](image-overview.md) for image display or [processing](image-transformation.md). Currently, the following image formats are supported: JPEG, PNG, GIF, WebP, BMP, SVG, ICO, and DNG.

## How to Develop

Read [Image](../../reference/apis-image-kit/js-apis-image.md) for APIs related to image decoding.

### Adding Dependencies

Open the **src/main/cpp/CMakeLists.txt** file of the native project, add **libace_napi.z.so**, **libpixelmap_ndk.z.so**, **libimage_source_ndk.z.so**, **librawfile.z.so**, and **libhilog_ndk.z.so** (on which the native log APIs depend) to the **target_link_libraries** dependency.

```txt
target_link_libraries(entry PUBLIC libace_napi.z.so libhilog_ndk.z.so libpixelmap_ndk.z.so libimage_source_ndk.z.so librawfile.z.so)
```

### Adding API Mappings

Open the **src/main/cpp/hello.cpp** file and add the following API mappings to the **Init** function:

```c++
EXTERN_C_START
static napi_value Init(napi_env env, napi_value exports)
{
    napi_property_descriptor desc[] = {
        { "getSyncPixelMap", nullptr, getSyncPixelMap, nullptr, nullptr, nullptr, napi_default, nullptr },
    };

    napi_define_properties(env, exports, sizeof(desc) / sizeof(desc[0]), desc);
    return exports;
}
EXTERN_C_END
```

### Calling APIs on the JS Side

1.  Open **src\main\cpp\types\*libentry*\index.d.ts** (where **libentry** varies according to the project name), and import the following files:
 
    ```js
    import image from '@ohos.multimedia.image'
    import resourceManager from '@ohos.resourceManager'

    export const getSyncPixelMap: (resMgr: resourceManager.ResourceManager, src: string) => image.PixelMap;
    ```
2. Prepare an image resource file, named **example.jpg** in this example, and import it to **src\main\resources\rawfile\**.

3. Open **src\main\ets\pages\index.ets**, import ***libentry*.so** (where **libentry** varies according to the project name), call the native APIs, and pass in the JS resource object. The following is an example:

    ```js
    import testNapi from 'libentry.so'
    import image from '@ohos.multimedia.image'

    @Entry
    @Component
    struct Index {
      @State pixelMap : PixelMap | undefined = undefined;
      aboutToAppear() {
         this.pixelMap = testNapi.getSyncPixelMap(getContext(this).resourceManager, "example.jpg")
      }

      build() {
         Row() {
            Column() {
            Image(this.pixelMap)
               .width(100)
               .height(100)
            }
            .width('100%')
         }
         .height('100%')
      }
   }
    ```


### Calling the Native APIs

For details about the APIs, see [Image API Reference](../../reference/apis-image-kit/image.md).

Obtain the JS resource object from the **hello.cpp** file and convert it to a native resource object. Then you can call native APIs.

**Adding Reference Files**

   ```c++
      #include <multimedia/image_framework/image_source_mdk.h>
      #include <multimedia/image_framework/image_pixel_map_mdk.h>
      #include <rawfile/raw_file.h>
      #include <rawfile/raw_file_manager.h>
      #include <hilog/log.h>
      
      static napi_value getSyncPixelMap(napi_env env, napi_callback_info info)
      {
         size_t argc = 2;
         napi_value args[2] = {nullptr};

         napi_get_cb_info(env, info, &argc, args , nullptr, nullptr);
         
         napi_valuetype srcType;
         napi_typeof(env, args[0], &srcType);

         NativeResourceManager * mNativeResMgr = OH_ResourceManager_InitNativeResourceManager(env, args[0]);
         
         size_t strSize;
         char srcBuf[2048];
         napi_get_value_string_utf8(env, args[1], srcBuf, sizeof(srcBuf), &strSize);

         RawFile * rawFile = OH_ResourceManager_OpenRawFile(mNativeResMgr, srcBuf);
         if (rawFile != NULL) {
            long len = OH_ResourceManager_GetRawFileSize(rawFile);
            uint8_t * data = static_cast<uint8_t *>(malloc(len));
            int res = OH_ResourceManager_ReadRawFile(rawFile, data, len);

            OhosImageSource imageSource_c;
            imageSource_c.buffer = data;
            imageSource_c.bufferSize = len;

            OhosImageSourceOps ops{};
            napi_value imageSource;
            napi_value pixelMap;

            int32_t ret = OH_ImageSource_Create(env, &imageSource_c, &ops, &imageSource);

            ImageSourceNative * imageSourceNative_c = OH_ImageSource_InitNative(env, imageSource);
            OhosImageDecodingOps decodingOps{};
            OH_ImageSource_CreatePixelMap(imageSourceNative_c, &decodingOps, &pixelMap);

            // The following APIs are used for the GIF format.
            // napi_value pixelMapList;
            // OH_ImageSource_CreatePixelMapList(imageSourceNative_c, &decodingOps, &pixelMapList);
            // OhosImageSourceDelayTimeList list{};
            // OH_ImageSource_GetDelayTime(imageSourceNative_c, &list);
            // uint32_t count;
            // OH_ImageSource_GetFrameCount(imageSourceNative_c, &count);

            OhosImageSourceInfo info{};
            OH_ImageSource_GetImageInfo(imageSourceNative_c, 0, &info);
            OH_LOG_Print(LOG_APP, LOG_INFO, 0xFF00, "[decode]", "imageInfo width:%{public}d , height:%{public}d", info.size.width, info.size.height);
            
            OhosImageSourceProperty target;
            char exifKey_c[] = "ImageWidth";
            target.size = strlen(exifKey_c);
            target.value = exifKey_c;

            OhosImageSourceProperty dstValue;
            char dstValue_c[] = "2000";
            dstValue.size = strlen(dstValue_c);
            dstValue.value = dstValue_c;
            OH_ImageSource_ModifyImageProperty(imageSourceNative_c, &target, &dstValue);

            OhosImageSourceProperty response{};
            response.size = 20;
            response.value = static_cast<char *>(malloc(20));
            OH_ImageSource_GetImageProperty(imageSourceNative_c, &target, &response);
            OH_LOG_Print(LOG_APP, LOG_INFO, 0xFF00, "[decode]", "ImageProperty width after modify:%{public}s", response.value);

            OH_ImageSource_Release(imageSourceNative_c);
            OH_ResourceManager_CloseRawFile(rawFile);
            return pixelMap;
         }
         OH_ResourceManager_ReleaseNativeResourceManager(mNativeResMgr);
         return nullptr;
      }
   ```

The image framework supports incremental decoding. The method is as follows:
   ```c++
      #include <multimedia/image_framework/image_source_mdk.h>
      #include <multimedia/image_framework/image_pixel_map_mdk.h>
      #include <rawfile/raw_file.h>
      #include <rawfile/raw_file_manager.h>
      #include <hilog/log.h>
      
      static napi_value getSyncPixelMap(napi_env env, napi_callback_info info)
      {
         size_t argc = 2;
         napi_value args[2] = {nullptr};

         napi_get_cb_info(env, info, &argc, args , nullptr, nullptr);
         
         napi_valuetype srcType;
         napi_typeof(env, args[0], &srcType);

         NativeResourceManager * mNativeResMgr = OH_ResourceManager_InitNativeResourceManager(env, args[0]);
         
         size_t strSize;
         char srcBuf[2048];
         napi_get_value_string_utf8(env, args[1], srcBuf, sizeof(srcBuf), &strSize);

         RawFile * rawFile = OH_ResourceManager_OpenRawFile(mNativeResMgr, srcBuf);
         if (rawFile != NULL) {
            long len = OH_ResourceManager_GetRawFileSize(rawFile);
            if (len > 2048) {
               uint8_t * data = static_cast<uint8_t *>(malloc(len));
               int res = OH_ResourceManager_ReadRawFile(rawFile, data, len);
               
               uint8_t * holderdata = static_cast<uint8_t *>(malloc(len));

               OhosImageSource imageSource_c;
               imageSource_c.buffer = holderdata;
               imageSource_c.bufferSize = len;
               OhosImageSourceOps ops{};
               napi_value imageSource;
               OH_ImageSource_CreateIncremental(env, &imageSource_c, &ops, &imageSource);

               ImageSourceNative * imageSourceNative_c = OH_ImageSource_InitNative(env, imageSource);

               // The following simulates the segment loading scenario.
               OhosImageSourceUpdateData firstData{};
               firstData.buffer = data;
               firstData.bufferSize = len;
               firstData.isCompleted = false;
               firstData.offset = 0;
               firstData.updateLength = 2048;
               OH_ImageSource_UpdateData(imageSourceNative_c, &firstData);

               OhosImageSourceUpdateData secondData{};
               secondData.buffer = data;
               secondData.bufferSize = len;
               secondData.isCompleted = true;
               secondData.offset = 2048;
               secondData.updateLength = len - 2048;
               OH_ImageSource_UpdateData(imageSourceNative_c, &secondData);

               napi_value pixelMap;
               OhosImageDecodingOps decodingOps{};
               decodingOps.index = 0;
               OH_ImageSource_CreatePixelMap(imageSourceNative_c, &decodingOps, &pixelMap);

               OH_ImageSource_Release(imageSourceNative_c);
               OH_ResourceManager_CloseRawFile(rawFile);
               return pixelMap;
            } 
            uint8_t * data = static_cast<uint8_t *>(malloc(len));
            int res = OH_ResourceManager_ReadRawFile(rawFile, data, len);

            OhosImageSource imageSource_c;
            imageSource_c.buffer = data;
            imageSource_c.bufferSize = len;

            OhosImageSourceOps ops{};
            napi_value imageSource;
            napi_value pixelMap;

            int32_t ret = OH_ImageSource_Create(env, &imageSource_c, &ops, &imageSource);

            ImageSourceNative * imageSourceNative_c = OH_ImageSource_InitNative(env, imageSource);
            OhosImageDecodingOps decodingOps{};
            OH_ImageSource_CreatePixelMap(imageSourceNative_c, &decodingOps, &pixelMap);

            OH_ImageSource_Release(imageSourceNative_c);
            OH_ResourceManager_CloseRawFile(rawFile);
            return pixelMap;
         }
         OH_ResourceManager_ReleaseNativeResourceManager(mNativeResMgr);
         return nullptr;
      }
   ```
