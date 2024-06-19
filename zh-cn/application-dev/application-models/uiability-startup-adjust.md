# 应用间显式跳转切换link跳转适配指导


## 概述

在API12及以后版本，系统框架增加了应用间显式跳转的限制，应用间的跳转需要通过link跳转的方式实现。

### 启动其他应用的UIAbility

1. 将待跳转的应用安装到设备，在其对应UIAbility的[module.json5配置文件](../quick-start/module-configuration-file.md)中配置skills标签的entities字段、actions字段和uri字段：
- "actions"列表中包含"ohos.want.action.viewData"。
- "entities"列表中包含"entity.system.browsable"。
- "uris"列表中包含"scheme"为"https"且"domainVerify"为true的元素。uri的匹配规则参考[uri匹配](explicit-implicit-want-mappings.md#uri匹配规则), domainVerify为true代表开启域名检查，通过applinking匹配该应用时需经过配置的域名校验后才能匹配到。applinking域名配置具体可参考AppLinking。

```json
{
  "module": {
    "abilities": [
      {
        "name": "EntryAbility",
        "srcEntry": "./ets/entryability/EntryAbility.ts",
        "icon": "$media:icon",
        "label": "$string:EntryAbility_label",
        "startWindowIcon": "$media:icon",
        "startWindowBackground": "$color:start_window_background",
        "skills": [
          {
            "entities": [
              "entity.system.browsable"
            ],
            "actions": [
              "ohos.want.action.viewData"
            ],
            "uris": [
              {
                "scheme": "https",
                "host": "www.example.com",
              }
            ],
           "domainVerify": true
          }
        ]
      }
    ]
  }
}
```

2. 调用方通过[openLink](../reference/apis-ability-kit/js-apis-inner-application-uiAbilityContext.md#uiabilitycontextopenlink12)接口执行跳转，在接口入参需要传入转换后的link和配置[options](../reference/apis-ability-kit/js-apis-app-ability-openLinkOptions.md), 不再传入bundleName、moduleName和abilityName。系统会根据传入的link匹配到符合skill配置的应用。<br>
<br>
- 当options中的appLinkingOnly为true时，匹配到的应用会经过应用市场域名检查（需联网）返回域名校验检查的唯一匹配项或未匹配结果。
- 当options中的appLinkingOnly为false时，会优先尝试以AppLinking的方式拉起，如果没有匹配的应用则改为使用DeepLinking的方式拉起目标应用。
<br>具体请参考AppLinking开发指南。

```ts
import { common } from '@kit.AbilityKit';
import OpenLinkOptions from '@ohos.app.ability.OpenLinkOptions';
import { BusinessError } from '@ohos.base';
import hilog from '@ohos.hilog';

const TAG: string = '[UIAbilityComponentsOpenLink]';
const DOMAIN_NUMBER: number = 0xFF00;

@Entry
@Component
struct Index {
  build() {
    Button('start link', { type: ButtonType.Capsule, stateEffect: true })
      .width('87%')
      .height('5%')
      .margin({ bottom: '12vp' })
      .onClick(() => {
        let context: common.UIAbilityContext = getContext(this) as common.UIAbilityContext;
        // let want: Want = {
        //   bundleName: "com.test.example",
        //   moduleName: "entry",
        //   abilityName: "EntryAbility"
        // };
        // try {
        //   context.startAbility(want)
        //     .then(() => {
        //       hilog.info(DOMAIN_NUMBER, TAG, 'startAbility success.');
        //     }).catch((err: BusinessError) => {
        //       hilog.error(DOMAIN_NUMBER, TAG, `startAbility failed. Code is ${err.code}, message is ${err.message}`);
        //     })
        // } catch (paramError) {
        //   hilog.error(DOMAIN_NUMBER, TAG, `Failed to startAbility. Code is ${paramError.code}, message is ${paramError.message}`);
        // }
        let link: string = "https://www.example.com";
        let openLinkOptions: OpenLinkOptions = {
          // 匹配的abilities选项是否需要通过AppLinking域名校验，匹配到唯一配置过的应用ability
          appLinkingOnly: true,
          // 同want中的parameter，用于传递的参数
          parameters: {demo_key: "demo_value"}
        };

        try {
          context.openLink(link, openLinkOptions)
            .then(() => {
              hilog.info(DOMAIN_NUMBER, TAG, 'open link success.');
            }).catch((err: BusinessError) => {
              hilog.error(DOMAIN_NUMBER, TAG, `open link failed. Code is ${err.code}, message is ${err.message}`);
            })
        } catch (paramError) {
          hilog.error(DOMAIN_NUMBER, TAG, `Failed to start link. Code is ${paramError.code}, message is ${paramError.message}`);
        }
      })
  }
}
```

### 启动其他应用的UIAbility并获取返回结果

1. 将待跳转的应用安装到设备，在其对应UIAbility的[module.json5配置文件](../quick-start/module-configuration-file.md)中配置skills标签的entities字段、actions字段和uri字段：
- "actions"列表中包含"ohos.want.action.viewData"。
- "entities"列表中包含"entity.system.browsable"。
- "uris"列表中包含"scheme"为"https"且"autoVerify"为true的元素。uri的匹配规则参考[uri匹配](explicit-implicit-want-mappings.md#uri匹配规则), domainVerify为true代表开启域名检查，通过applinking匹配该应用时需经过配置的域名校验后才能匹配到。applinking域名配置具体可参考App Linking。

```json
{
  "module": {
    "abilities": [
      {
        "name": "EntryAbility",
        "srcEntry": "./ets/entryability/EntryAbility.ts",
        "icon": "$media:icon",
        "label": "$string:EntryAbility_label",
        "startWindowIcon": "$media:icon",
        "startWindowBackground": "$color:start_window_background",
        "skills": [
          {
            "entities": [
              "entity.system.browsable"
            ],
            "actions": [
              "ohos.want.action.viewData"
            ],
            "uris": [
              {
                "scheme": "https",
                "host": "www.example.com",
              }
            ],
           "domainVerify": true
          }
        ]
      }
    ]
  }
}
```

2. 调用方通过[openLink](../reference/apis-ability-kit/js-apis-inner-application-uiAbilityContext.md#uiabilitycontextopenlink12)接口执行跳转，在接口入参需要传入转换后的link和配置[options](../reference/apis-ability-kit/js-apis-app-ability-openLinkOptions.md), 不再传入bundleName、moduleName和abilityName。系统会根据传入的link匹配到符合skills配置的应用。AbilityResult回调结果返回通过入参传入回调函数，在启动ability停止自身后返回给调用方的信息。启动成功和失败结果仍通过Promise返回。<br>
- 当options中的appLinkingOnly为true时，匹配到的应用会经过应用市场域名检查（需联网）返回域名校验检查的唯一匹配项或未匹配结果。
- 当options中的appLinkingOnly为false时，会优先尝试以AppLinking的方式拉起，如果没有匹配的应用则改为使用DeepLinking的方式拉起目标应用。
<br>具体请参考AppLinking开发指南。

```ts
import { common } from '@kit.AbilityKit';
import OpenLinkOptions from '@ohos.app.ability.OpenLinkOptions';
import { BusinessError } from '@ohos.base';
import hilog from '@ohos.hilog';

const TAG: string = '[UIAbilityComponentsOpenLink]';
const DOMAIN_NUMBER: number = 0xFF00;

@Entry
@Component
struct Index {
  build() {
    Button('start link', { type: ButtonType.Capsule, stateEffect: true })
      .width('87%')
      .height('5%')
      .margin({ bottom: '12vp' })
      .onClick(() => {
        let context: common.UIAbilityContext = getContext(this) as common.UIAbilityContext;
        // let want: Want = {
        //   bundleName: "com.test.example",
        //   moduleName: "entry",
        //   abilityName: "EntryAbility"
        // };
        // try {
        //   context.startAbilityForResult(want)
        //     .then((data) => {
        //       hilog.info(DOMAIN_NUMBER, TAG, 'startAbility success. data:' + JSON.stringify(data));
        //     }).catch((err: BusinessError) => {
        //       hilog.error(DOMAIN_NUMBER, TAG, `startAbility failed. Code is ${err.code}, message is ${err.message}`);
        //     })
        // } catch (paramError) {
        //   hilog.error(DOMAIN_NUMBER, TAG, `Failed to startAbility. Code is ${paramError.code}, message is ${paramError.message}`);
        // }
        let link: string = "https://www.example.com";
        let openLinkOptions: OpenLinkOptions = {
          // 匹配的abilities选项是否需要通过AppLinking域名校验，匹配到唯一配置过的应用ability
          appLinkingOnly: true,
          // 同want中的parameter，用于传递的参数
          parameters: {demo_key: "demo_value"}
        };

        try {
          context.openLink(link, openLinkOptions, (err, data) => {
            // AbilityResult callback回调，仅在被拉起ability死亡时触发
            hilog.info(DOMAIN_NUMBER, TAG, 'open link success. Callback result:' + JSON.stringify(data));
          }).then(() => {
            hilog.info(DOMAIN_NUMBER, TAG, 'open link success.');
          }).catch((err: BusinessError) => {
            hilog.error(DOMAIN_NUMBER, TAG, `open link failed. Code is ${err.code}, message is ${err.message}`);
          })
        } catch (paramError) {
          hilog.error(DOMAIN_NUMBER, TAG, `Failed to start link. Code is ${paramError.code}, message is ${paramError.message}`);
        }
      })
  }
}
```