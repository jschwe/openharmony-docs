# 图片编码native(C/C++)

图像打包类，用于创建以及释放ImagePacker实例。

## 开发步骤

### 添加链接库

在进行应用开发之前，开发者需要打开native工程的src/main/cpp/CMakeLists.txt，在target_link_libraries依赖中添libimage_packer.so 以及日志依赖libhilog_ndk.z.so。

```txt
target_link_libraries(entry PUBLIC libhilog_ndk.z.so libimage_packer.so)
```

### Native接口调用

具体接口说明请参考[API文档](../../reference/apis-image-kit/image.md)

在hello.cpp中实现C API接口调用逻辑，示例代码如下：

**编码接口使用示例**

在创建ImagePacker实例，指定打包参数后将ImageSource或Pixelmap图片源打包至文件或者缓冲区。

   ```c++

      #include <linux/kd.h>
      #include <string>

      #include "hilog/log.h"
      #include "multimedia/image_framework/image/image_packer_native.h"
      #include "multimedia/image_framework/image/pixelmap_native.h"
      #include "multimedia/image_framework/image/image_source_native.h"

      #undef LOG_DOMAIN
      #undef LOG_TAG
      #define LOG_DOMAIN 0x3200
      #define LOG_TAG "MY_TAG"

      Image_ErrorCode packToFileFromImageSourceTest(int fd)
      {
          //创建ImagePacker实例
          OH_ImagePackerNative *testPacker = nullptr;
          Image_ErrorCode errCode = OH_ImagePackerNative_Create(&testPacker);
          if (errCode != IMAGE_SUCCESS) {
              OH_LOG_ERROR(LOG_APP, "ImagePackerNativeCTest CreatePacker OH_ImagePackerNative_Create failed, errCode: %{public}d.", errCode);
              return errCode;
          }

          //创建ImageSource实例
          OH_ImageSourceNative* imageSource = nullptr;
          errCode = OH_ImageSourceNative_CreateFromFd(fd, &imageSource);
          if (errCode != IMAGE_SUCCESS) {
              OH_LOG_ERROR(LOG_APP, "ImagePackerNativeCTest OH_ImageSourceNative_CreateFromFd  failed, errCode: %{public}d.", errCode);
              return errCode;
          }

          //指定打包参数，将ImageSource图片源编码后直接打包进文件
          OH_PackingOptions *option = nullptr;
          OH_PackingOptions_Create(&option);
          char type[] = "image/jpeg";
          Image_MimeType image_MimeType = {type, strlen(type)};
          OH_PackingOptions_SetMimeType(option, &image_MimeType);
          errCode = OH_ImagePackerNative_PackToFileFromImageSource(testPacker, option, imageSource, fd);
          if (errCode != IMAGE_SUCCESS) {
              OH_LOG_ERROR(LOG_APP, "ImagePackerNativeCTest OH_ImagePackerNative_PackToFileFromImageSource failed, errCode: %{public}d.", errCode);
              return errCode;
          }

          //释放ImagePacker实例
          errCode = OH_ImagePackerNative_Release(testPacker);
          if (errCode != IMAGE_SUCCESS)
          {
              OH_LOG_ERROR(LOG_APP, "ImagePackerNativeCTest ReleasePacker OH_ImagePackerNative_Release failed, errCode: %{public}d.", errCode);
              return errCode;
          }
          //释放ImageSource实例
          errCode = OH_ImageSourceNative_Release(imageSource);
          if (errCode != IMAGE_SUCCESS)
          {
              OH_LOG_ERROR(LOG_APP, "ImagePackerNativeCTest ReleasePacker OH_ImageSourceNative_Release failed, errCode: %{public}d.", errCode);
              return errCode;
          }

          return IMAGE_SUCCESS;
      }

      Image_ErrorCode packToFileFromPixelmapTest(uint8_t *buffer, size_t buffSize, int fd)
      {
          //创建ImagePacker实例
          OH_ImagePackerNative *testPacker = nullptr;
          Image_ErrorCode errCode = OH_ImagePackerNative_Create(&testPacker);
          if (errCode != IMAGE_SUCCESS) {
              OH_LOG_ERROR(LOG_APP, "ImagePackerNativeCTest CreatePacker OH_ImagePackerNative_Create failed, errCode: %{public}d.", errCode);
              return errCode;
          }

          //创建Pixelmap实例
          OH_Pixelmap_InitializationOptions *createOpts;
          OH_PixelmapInitializationOptions_Create(&createOpts);
          OH_PixelmapInitializationOptions_SetWidth(createOpts, 6);
          OH_PixelmapInitializationOptions_SetHeight(createOpts, 4);
          OH_PixelmapInitializationOptions_SetPixelFormat(createOpts, 3);
          OH_PixelmapInitializationOptions_SetAlphaType(createOpts, 0);
          OH_PixelmapNative *pixelmap = nullptr;
          errCode = OH_PixelmapNative_CreatePixelmap(buffer, bufferSize, createOpts, &pixelmap);
          if (errCode != IMAGE_SUCCESS) {
              OH_LOG_ERROR(LOG_APP, "ImagePackerNativeCTest OH_PixelmapNative_CreatePixelmap  failed, errCode: %{public}d.", errCode);
              return errCode;
          }

          //指定打包参数，将PixelMap图片源编码后直接打包进文件
          OH_PackingOptions *option = nullptr;
          OH_PackingOptions_Create(&option);
          char type[] = "image/jpeg";
          Image_MimeType image_MimeType = {type, strlen(type)};
          OH_PackingOptions_SetMimeType(option, &image_MimeType);
          errCode = OH_ImagePackerNative_PackToFileFromPixelmap(testPacker, option, pixelmap, fd);
          if (errCode != IMAGE_SUCCESS) {
              OH_LOG_ERROR(LOG_APP, "ImagePackerNativeCTest OH_ImagePackerNative_PackToFileFromPixelmap  failed, errCode: %{public}d.", errCode);
              return errCode;
          }

          //释放ImagePacker实例
          errCode = OH_ImagePackerNative_Release(testPacker);
          if (errCode != IMAGE_SUCCESS)
          {
              OH_LOG_ERROR(LOG_APP, "ImagePackerNativeCTest ReleasePacker OH_ImagePackerNative_Release failed, errCode: %{public}d.", errCode);
              return errCode;
          }

          //释放Pixelmap实例
          errCode = OH_PixelmapNative_Release(pixelmap);
          if (errCode != IMAGE_SUCCESS)
          {
              OH_LOG_ERROR(LOG_APP, "ImagePackerNativeCTest ReleasePacker OH_PixelmapNative_Release failed, errCode: %{public}d.", errCode);
              return errCode;
          }

          return IMAGE_SUCCESS;
      }
   ```