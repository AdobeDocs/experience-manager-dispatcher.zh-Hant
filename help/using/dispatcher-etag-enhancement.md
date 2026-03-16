---
title: CDN重新驗證的Dispatcher ETag增強功能
description: AEM as a Cloud Service中INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT的可用性、支援狀態和行為。
source-git-commit: ac0fafd060643903735ff565072ef2c5bee970be
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 0%

---

# CDN重新驗證的Dispatcher ETag增強功能

## 概觀

`INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT`旗標可讓Dispatcher評估快取點選上的`If-None-Match`請求標頭。 當傳入的`If-None-Match`值與快取的`ETag`相符時，Dispatcher可以傳回`304 Not Modified`而非`200 OK`。

此行為旨在減少CDN與Dispatcher之間不必要的裝載傳輸，並提高條件式快取效率。

## 可用性

- Dispatcher版本： `2.0.264`
- AEM SDK組建： `aem-sdk-2026.2.24464.20260214T050318Z-260100`

## AEM as a Cloud Service支援

在AEM as a Cloud Service中，此功能受客戶支援使用。

客戶可以在Cloud Manager中設定`INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT`環境變數來啟用它。 Adobe也可在需要時代表客戶啟用。

啟用時，以及當CDN傳送`If-None-Match`且相關`ETag`存在於Dispatcher快取中時，CDN與Dispatcher之間應該會有較高的`304`回應率。 此增加是預期的結果。

## 設定範例（快取ETag標頭）

若要讓此增強功能生效，請確定Dispatcher會快取`ETag`回應標題，且您的網頁伺服器已設定為避免產生檔案系統型ETags。

範例`dispatcher.any`快取區段：

```text
/cache {
  /headers {
    "Cache-Control"
    "Content-Type"
    "Expires"
    "Last-Modified"
    "ETag"
  }
}
```

Dispatcher vhost內容中的Apache指示詞範例：

```apache
FileETag none
```

如需基準標頭快取指引，請參閱[快取HTTP回應標頭](dispatcher-configuration.md#caching-http-response-headers)。

## 驗證範例

啟用環境變數並部署設定變更後：

1. 要求一次以暖化快取並擷取傳回的`ETag`。
1. 使用`If-None-Match: <etag-value>`再次要求。
1. 確認Dispatcher針對快取點選重新驗證流程傳回`304 Not Modified`。

## 公開引用（相關行為）

如需在Dispatcher中標題快取和`ETag`處理方面面向客戶的基準線指引，請參考：

- [設定Dispatcher — 快取HTTP回應標頭](https://experienceleague.adobe.com/zh-hant/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration#caching-http-response-headers)

「此功能可在Dispatcher `2.0.264` (AEM SDK `2026.2.24464`)中使用。 啟用後，Dispatcher可針對快取的`ETag`值驗證`If-None-Match`，並在快取點選時傳回`304 Not Modified`。 AEM as a Cloud Service支援此功能，並可透過Cloud Manager環境設定加以啟用。」
