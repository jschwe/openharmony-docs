# native_child_process.h


## 概述

支持创建Native子进程，并在父子进程间建立IPC通道。

**库：** libchild_process.so

**系统能力：** SystemCapability.Ability.AbilityRuntime.Core

**起始版本：** 12

**相关模块：**[ChildProcess](c-apis-ability-childprocess.md)


## 汇总

### 文件

| 名称                                                     | 描述                                                                                                 |
| ------------------------------------------------------ | -------------------------------------------------------------------------------------------------- |
| [native_child_process.h](native__child__process_8h.md) | 支持创建Native子进程，并在父子进程间建立IPC通道。<br>引用文件：<AbilityKit/native_child_process.h><br>库：libchild_process.so |

### 类型定义

| 名称                                                                                                                                                                             | 描述                |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------- |
| typedef enum Ability_NativeChildProcess_ErrCode [Ability_NativeChildProcess_ErrCode](c-apis-ability-childprocess.md#ability_nativechildprocess_errcode)                        | 定义Native子进程模块错误码。 |
| typedef void(\* [OH_Ability_OnNativeChildProcessStarted](c-apis-ability-childprocess.md#oh_ability_onnativechildprocessstarted)) (int errCode, OHIPCRemoteProxy \*remoteProxy) | 定义通知子进程启动结果的回调函数。 |


### 枚举

| 名称                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 描述                |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------- |
| [Ability_NativeChildProcess_ErrCode](c-apis-ability-childprocess.md#ability_nativechildprocess_errcode) {<br>    NCP_NO_ERROR = 0,<br>    NCP_ERR_INVALID_PARAM = 401,<br>    NCP_ERR_NOT_SUPPORTED = 801,<br>    NCP_ERR_INTERNAL = 16000050,<br>    NCP_ERR_BUSY = 16010001,<br>    NCP_ERR_TIMEOUT = 16010002,<br>    NCP_ERR_SERVICE_ERROR = 16010003,<br>    NCP_ERR_MULTI_PROCESS_DISABLED = 16010004,<br>    NCP_ERR_ALREADY_IN_CHILD = 16010005,<br>    NCP_ERR_MAX_CHILD_PROCESSES_REACHED = 16010006,<br>    NCP_ERR_LIB_LOADING_FAILED = 16010007,<br>    NCP_ERR_CONNECTION_FAILED = 16010008<br>} | 定义Native子进程模块错误码。 |


### 函数

| 名称                                                                                                                                                                                                                                                                     | 描述                                                                                    |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| int [OH_Ability_CreateNativeChildProcess](c-apis-ability-childprocess.md#oh_ability_createnativechildprocess) (const char \*libName, [OH_Ability_OnNativeChildProcessStarted](c-apis-ability-childprocess.md#oh_ability_onnativechildprocessstarted) onProcessStarted) | 创建子进程并加载参数中指定的动态链接库文件，进程启动结果通过回调参数异步通知，需注意回调通知为独立线程，回调函数实现需要注意线程同步，且不能执行高耗时操作避免长时间阻塞。 |