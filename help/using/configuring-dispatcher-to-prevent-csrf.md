---
title: 設定 Dispatcher 以防止 CSRF 攻擊
description: 瞭解如何配置AEMDispatcher以防止跨站點請求偽造攻擊。
topic-tags: dispatcher
content-type: reference
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: tm+mt
source-wordcount: '217'
ht-degree: 46%

---

# 設定 Dispatcher 以防止 CSRF 攻擊{#configuring-dispatcher-to-prevent-csrf-attacks}

提AEM供了旨在防止跨站點請求偽造攻擊的框架。 若要正確使用此架構，請對您的Dispatcher設定進行下列變更：

>[!NOTE]
>
>請確保根據現有配置更新以下示例中的規則編號。 請記住，排程程式使用最後一個匹配規則來授予允許或拒絕，因此將規則放在現有清單的底部附近。

1. 在 `/clientheaders` 部分 `author-farm.any` 和 `publish-farm.any`，新增下列專案至清單底部：\
   `CSRF-Token`
1. 在的/filters區段中 `author-farm.any` 和 `publish-farm.any` 或 `publish-filters.any` 檔案中，新增以下行以允許請求 `/libs/granite/csrf/token.json` 透過Dispatcher。\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. 在 `/cache /rules` 部分 `publish-farm.any`，新增規則以封鎖Dispatcher的快取 `token.json` 檔案。 通常作者會繞過快取，因此不需要將規則新增到 `author-farm.any`.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

要驗證配置是否正在工作，請在DEBUG模式下查看dispatcher.log以驗證token.json檔案是否未被快取且未被篩選器阻止。 您應看到類似於：\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

您也可以驗證Apache中的請求是否成功 `access_log`. 「/libs/granite/csrf/token.json」的請求應返回HTTP 200狀態代碼。
