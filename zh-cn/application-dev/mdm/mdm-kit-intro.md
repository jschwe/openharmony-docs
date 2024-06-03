# MDM Kit简介

## 业务介绍

移动设备管理（Mobile Device Management）是一种企业级的IT应用解决方案，用于管理并保护公司设备上的数据和应用程序。MDM可以通过集中管理、远程配置和监控来保障设备和数据的安全性和稳定性。它广泛应用于企业和政府机构，以确保员工和客户使用的设备和数受到保护，实现企业高效管理、安全使用设备。



## 实现原理

框架层和服务层提供了enterprise_device_management部件<!--RP1--><!--RP1End-->，enterprise_device_management部件提供了设备管理应用程序框架和基本设备管理能力<!--RP2--><!--RP2End-->。设备管理应用通过EnterpriseAdminExtensionAbility来调用MDM Kit中的接口，实现管理设备的意图。

![intro_arch.png](./figures/intro_arch.png)



## 约束与限制

- SDK版本为5.0.0（API 12）及以上。

- 仅支持Stage模型。

  <!--RP3--><!--RP3End-->