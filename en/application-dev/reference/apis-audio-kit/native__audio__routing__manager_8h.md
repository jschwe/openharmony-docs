# native_audio_routing_manager.h


## Overview

The **native_audio_routing_manager.h** file declares the functions related to an audio routing manager.

You can use the functions to create an audio routing manager, register and deregister a callback to listen for device connection status changes, and release devices.

**Library**: libohaudio.so

**System capability**: SystemCapability.Multimedia.Audio.Core

**Since**: 12

**Related module**: [OHAudio](_o_h_audio.md)


## Summary


### Types

| Name| Description| 
| -------- | -------- |
| typedef struct [OH_AudioRoutingManager](_o_h_audio.md#oh_audioroutingmanager) [OH_AudioRoutingManager](_o_h_audio.md#oh_audioroutingmanager) | Defines the struct of an audio routing manager, which is used for routing and device-related functions. | 
| typedef int32_t(\* [OH_AudioRoutingManager_OnDeviceChangedCallback](_o_h_audio.md#oh_audioroutingmanager_ondevicechangedcallback)) ([OH_AudioDevice_ChangeType](_o_h_audio.md#oh_audiodevice_changetype) type, [OH_AudioDeviceDescriptorArray](_o_h___audio_device_descriptor_array.md) \*audioDeviceDescriptorArray) | Defines a pointer to the callback function that returns the changed audio device descriptor (possibly multiple descriptors). | 


### Functions

| Name| Description| 
| -------- | -------- |
| [OH_AudioCommon_Result](_o_h_audio.md#oh_audiocommon_result) [OH_AudioManager_GetAudioRoutingManager](_o_h_audio.md#oh_audiomanager_getaudioroutingmanager) ([OH_AudioRoutingManager](_o_h_audio.md#oh_audioroutingmanager) \*\*audioRoutingManager) | Obtains the handle to an audio routing manager. The handle should be set as the first parameter in the routing-related functions. | 
| [OH_AudioCommon_Result](_o_h_audio.md#oh_audiocommon_result) [OH_AudioRoutingManager_GetDevices](_o_h_audio.md#oh_audioroutingmanager_getdevices) ([OH_AudioRoutingManager](_o_h_audio.md#oh_audioroutingmanager) \*audioRoutingManager, [OH_AudioDevice_Flag](_o_h_audio.md#oh_audiodevice_flag) deviceFlag, [OH_AudioDeviceDescriptorArray](_o_h___audio_device_descriptor_array.md) \*\*audioDeviceDescriptorArray) | Obtains available devices based on the device flag. | 
| [OH_AudioCommon_Result](_o_h_audio.md#oh_audiocommon_result) [OH_AudioRoutingManager_RegisterDeviceChangeCallback](_o_h_audio.md#oh_audioroutingmanager_registerdevicechangecallback) ([OH_AudioRoutingManager](_o_h_audio.md#oh_audioroutingmanager) \*audioRoutingManager, [OH_AudioDevice_Flag](_o_h_audio.md#oh_audiodevice_flag) deviceFlag, [OH_AudioRoutingManager_OnDeviceChangedCallback](_o_h_audio.md#oh_audioroutingmanager_ondevicechangedcallback) callback) | Registers a callback to listen for device changes of an audio routing manager. | 
| [OH_AudioCommon_Result](_o_h_audio.md#oh_audiocommon_result) [OH_AudioRoutingManager_UnregisterDeviceChangeCallback](_o_h_audio.md#oh_audioroutingmanager_unregisterdevicechangecallback) ([OH_AudioRoutingManager](_o_h_audio.md#oh_audioroutingmanager) \*audioRoutingManager, [OH_AudioRoutingManager_OnDeviceChangedCallback](_o_h_audio.md#oh_audioroutingmanager_ondevicechangedcallback) callback) | Unregisters the callback used to listen for device changes of an audio routing manager. | 
| [OH_AudioCommon_Result](_o_h_audio.md#oh_audiocommon_result) [OH_AudioRoutingManager_ReleaseDevices](_o_h_audio.md#oh_audioroutingmanager_releasedevices) ([OH_AudioRoutingManager](_o_h_audio.md#oh_audioroutingmanager) \*audioRoutingManager, [OH_AudioDeviceDescriptorArray](_o_h___audio_device_descriptor_array.md) \*audioDeviceDescriptorArray) | Releases audio devices available for an audio routing manager. | 