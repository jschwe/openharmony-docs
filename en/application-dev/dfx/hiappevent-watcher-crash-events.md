# Crash Event Overview

HiAppEvent provides APIs for subscribing to crash events.

- [Subscribing to Crash Events (ArkTS)](hiappevent-watcher-crash-events-arkts.md)
- [Subscribing to Crash Events (C/C++)](hiappevent-watcher-crash-events-ndk.md)

The **params** parameter in the event information is described as follows.

**params**

| Name   | Type  | Description                      |
| ------- | ------ | ------------------------- |
| time     | number | Event triggering time, in ms.|
| crash_type | string | Crash type, which can be JsError or NativeCrash.|
| foreground | boolean | Whether the application is running in the foreground.|
| bundle_version | string | Application version.|
| bundle_name | string | Application name.|
| pid | number | Process ID of the application.|
| uid | number | User ID of the application.|
| uuid | string | Error ID.|
| exception | object | Exception information. For details, see **exception**. <br>For details about the NativeCrash events, see **exception (NativeCrash)**.|
| hilog | string[] | Log information.|
| threads | object[] | Full thread call stack. For details, see **thread**. This field applies only to NativeCrash events.|
| external_log<sup>12+</sup> | string[] | Path of the error log file.|
| log_over_limit<sup>12+</sup> | boolean | Whether the size of generated log files and existing log files exceeds the upper limit (5 MB). The value **true** indicates that the upper limit is exceeded and logs fail to be written. The value **false** indicates that the upper limit is not exceeded.|

**exception**

| Name   | Type  | Description                      |
| ------- | ------ | ------------------------- |
| name | string | Exception type.|
| message | string | Exception cause.|
| stack | string | Exception call stack.|

**exception (NativeCrash)**

| Name   | Type  | Description                      |
| ------- | ------ | ------------------------- |
| message | string | Exception cause.|
| signal | object | Signal information. For details, see **signal**.|
| thread_name | string | Thread name.|
| tid | number | Thread ID.|
| frames | object[] | Thread call stack. For details, see **frame**.|

**signal**

| Name   | Type  | Description                      |
| ------- | ------ | ------------------------- |
| signo | number | Signal value.|
| code | number | Signal error code.|

**thread**

| Name   | Type  | Description                      |
| ------- | ------ | ------------------------- |
| thread_name | string | Thread name.|
| tid | number | Thread ID.|
| frames | object[] | Thread call stack. For details, see **frame**.|

**frame**

| Name   | Type  | Description                      |
| ------- | ------ | ------------------------- |
| symbol | string | Function name.|
| file | string | File name.|
| buildId | string | Unique file ID.|
| pc | string | PC register address.|
| offset | number | Function offset.|