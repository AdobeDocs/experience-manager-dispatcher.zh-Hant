---
title: 設定 Adobe Experience Manager Dispatcher 以防止 CSRF 攻擊
description: 了解如何設定 Adobe Experience Manager Dispatcher 以防止跨網站請求偽造攻擊。
topic-tags: dispatcher
content-type: reference
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
source-git-commit: 0a1aa854ea286a30c3527be8fc7c0998726a663f
workflow-type: tm+mt
source-wordcount: '235'
ht-degree: 100%

---

# 設定 Adobe Experience Manager Dispatcher 以防止 CSRF 攻擊{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM (Adobe Experience Manager) 提供了旨在防止跨網站請求偽造攻擊的框架。為了正確地使用此框架，請對 Dispatcher 設定進行以下變更：

>[!NOTE]
>
>請確保根據現有配置更新以下示例中的規則編號。請記住，Dispatcher 會使用最後一個匹配規則來授予允許或拒絕，因此將規則放在現有清單的底部附近。

1. 在 `author-farm.any` 和 `publish-farm.any` 的 `/clientheaders` 區段中，將以下項目新增至清單底部：\
   `CSRF-Token`
1. 在 `author-farm.any` 和 `publish-farm.any` 的 /filters 區段中，或在 `publish-filters.any` 檔案中，新增以下行以透過 Dispatcher 允許 `/libs/granite/csrf/token.json` 的請求。\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`

1. 在 `publish-farm.any` 的 `/cache /rules` 區段下面，新增規則以阻止 Dispatcher 快取 `token.json` 檔案。 通常作者會繞過快取，因此您不必將規則添加到 `author-farm.any`。

   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

若要驗證設定確實有效，請在偵錯模式下觀察 dispatcher.log。這可以幫助您驗證 `token.json` 檔案以確保它未被快取且未被篩選器阻止。您應看到類似於：\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

您還可以驗證 Apache 中的請求是否成功 `access_log`。 「/libs/granite/csrf/token.json」的請求應傳回 HTTP 200 狀態代碼。
