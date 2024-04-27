---
title: AEM Dispatcher 發行說明
description: Adobe Experience Manager Dispatcher專屬的發行說明。
topic-tags: release-notes
content-type: reference
products: SG_EXPERIENCEMANAGER/6.4
exl-id: b55c7a34-d57b-4d45-bd83-29890f1524de
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: tm+mt
source-wordcount: '1048'
ht-degree: 53%

---

# AEM Dispatcher 發行說明{#aem-dispatcher-release-notes}

## 發行資訊 {#release-information}

|  |  |
|--- |--- |
| 產品 | Adobe Experience Manager (AEM) Dispatcher |
| 版本 | 4.3.7 |
| 類型 | 次要版本 |
| 日期 | 2024 年 3 月 27 日 |
| 下載 URL | <ul><li>[Apache 2.4](#apache)</li><li>[Microsoft® Internet Information Services (IIS)](#iis)</li></ul> |
| 相容性 | AEM 6.1 或更高版本 |

## 系統需求和先決條件 {#system-requirements-and-prerequisites}

另請參閱 [支援平台](https://experienceleague.adobe.com/en/docs/experience-manager-64/deploying/introduction/technical-requirements) 以取得需求與先決條件的詳細資訊。

Adobe建議您使用最新版AEM Dispatcher以受益於最新功能、最新錯誤修正和最佳效能。

## 安裝指示 {#installation-instructions}

如需詳細指示，請參閱[安裝 Dispatcher](dispatcher-install.md)。

## 發行版記錄 {#release-history}

### 發行版本 4.3.7 (2024 年 3 月 27 日) {#march}

**改良功能**：

* DISP-1009 — 再次設定標頭長度。
* DISP-1013 — 新增適用於Linux®的openssl 3.0支援。
* DISP-1014 - response.location處理導致無效的重新導向。
* disp-1017 — 變更DTD定義。

### 發行版本 4.3.6 (2023 年 7 月 25 日) {#jyly}

**改良功能**：

* DISP-911 AEM-05 - X-Edge-Key可能會在disp_apache2.c中洩漏。
* DISP-937記錄所有選取器。
* DISP-998可設定在啟動時載入虛名url。

### 版本 4.3.5 (2022 年 4 月 4 日) {#apr}

**改良功能**：

* DISP-954 - 即便尚未過期也支援失效功能
* DISP-949 — 即便篩選條件封鎖了POST請求，Dispatcher也會傳回200而不是404。

### 版本 4.3.4 (2021 年 11 月 29 日) {#nov}

**錯誤修正**：

* DISP-833 - X-Forwarded-Host標題可能包含以逗號分隔的主機名稱清單。
* DISP-835 - DispatcherUseForwardedHost會在Host標頭最後出現時吞併它。

**改良功能**：

* DISP-874 — 建立Dispatcher設定，以透過標幟開啟或關閉DISP-818的實作 `DispatcherRestrictUncacheableContent`. 預設值為關閉。 當開啟時，它會移除 mod expires 為不可快取的內容所設定的任何快取標題。 這與版本 4.3.3 (預設為開啟) 中的行為不同，但與 4.3.3 之前的版本 (預設為關閉) 中的行為相同。 建議保留 `DispatcherRestrictUncacheableContent` 的預設值「關閉」，好讓瀏覽器快取具有更大的彈性。 從4.3.3版升級至4.3.4版時，若想保留與4.3.3版相同的行為，您必須明確設定 `DispatcherRestrictUncacheableContent` 至開啟。
* DISP-841 - Dispatcher 不遵守 504 回應代碼的 /serverStaleOnError
* DISP-874 — 建立Dispatcher設定，以開啟或關閉DISP-818的實作
* DISP-883 - 在 Dispatcher 中顯示 URL 請求分解的追蹤
* DISP-944 — 預先載入虛名URL

### 版本 4.3.3 (2019 年 10 月 18 日) {#october}

**錯誤修正**：

* DISP-739 - LogLevel Dispatcher： **level** 無法運作。
* DISP-749 - Alpine Linux® Dispatcher當機，提供追蹤記錄層級。

**改良功能**：

* DISP-813 - 在 Dispatcher 中支援 openssl 1.1.x
* DISP-814 - 在快取清除期間發生 Apache 40x 錯誤
* DISP-818 - mod_expires 為不可快取的內容新增 Cache-Control 標題
* DISP-821 - 不會將記錄上下文儲存在 socket
* DISP-822 - Dispatcher應該使用 `ppoll` 而非 `pselect`
* DISP-824 - 安全 DispatcherUseForwardedHost
* DISP-825 - 當磁碟空間用完時記錄特殊訊息
* DISP-826 — 支援透過查詢字串重新擷取URI

**新功能**：

* DISP-703 - 陣列特有的快取命中比例
* DISP-827 - 用於測試的本機伺服器
* DISP-828 — 為Dispatcher建立測試Docker映像

### 版本 4.3.2 (2019 年 1 月 31 日) {#jan}

**錯誤修正**：

* DISP-734 - 如果未設定為處理常式，Dispatcher 會導致 insert_output_filter 損毀
* DISP-735 - REs無法在Alpine Linux上運作®
* DISP-740 — 預設會停用在macOS Mojave中載入Dispatcher的功能
* DISP-742 — 封鎖的要求可能會將資訊洩漏給受驗證檢查器保護的資源

**改良功能**：

* DISP-746 - dispatcher.any中未標籤的字串應該會產生警告

**新功能**：

* DISP-747 - 在 Apache 環境中提供請求資訊

### 版本 4.3.1 (2018 年 10 月 16 日) {#oct}

**錯誤修正**：

* DISP-656 - Dispatcher 提供錯誤的 ETag 標題
* DISP-694 - 當持續連線過期時隱藏警告
* DISP-714 - Cookie式工作階段管理不適用於IIS
* DISP-715 - Renderid Cookie 的安全標幟
* DISP-720 - 暫存檔案未關閉可能會導致資源耗盡 (開啟太多檔案)
* DISP-721 - 當 Apache 正常重新啟動子項時，Dispatcher 中斷 poll()
* DISP-722 - 以八進位模式 0600 建立快取檔案
* DISP-723 — 當轉譯器逾時設為0時，有隱含的10分鐘逾時（並重試）
* DISP-725 - 字串後面的尾隨字元自動被轉換為未命名值
* DISP-726 - 沒有任何陣列實際符合傳入主機時記錄警告
* DISP-727 - Dispatcher 會檢查空的快取檔案的請求內容長度
* DISP-730 — 嘗試透過Dispatcher存取標題檔案時傳回404錯誤
* DISP-731 - Dispatcher 容易受到記錄注入攻擊
* DISP-732 - Dispatcher應該移除URL中的連續「/」
* DISP-733 - Dispatcher 應該設定 (計算) Age 標題

**改良功能**：

* DISP-656 - Dispatcher 提供錯誤的 ETag 標題
* DISP-694 - 當持續連線過期時隱藏警告
* DISP-715 - Renderid Cookie 的安全標幟
* DISP-722 - 以八進位模式 0600 建立快取檔案
* DISP-726 - 沒有任何陣列實際符合傳入主機時記錄警告

### 版本 4.3.0 (2018 年 6 月 13 日) {#jun}

**錯誤修正**：

* DISP-682 - 錯誤地套用了數值記錄層級
* DISP-685 - 32位元Solaris™ SPARC®二進位檔案有__divdi3的未定義參照
* DISP-688 - Dispatcher未在404回應中傳回「X-Cache-Info」標頭
* DISP-690 - 無法快取 Last-Modified 標題
* DISP-691 - w3wp.exe 中有存取違規
* DISP-693 — 在Dispatcher下載頁面上更新Solaris™伺服器的架構詳細資料
* DISP-695 - Dispatcher 模組 4.2.3 中的 DispatcherLog 層級發生問題
* DISP-698 - Dispatcher TTL必須支援s-maxage和private指示詞
* DISP-700 — 模組無法在Alpine Linux上正常運作®
* DISP-704 - 包含 %2b 的瀏覽器請求以未編碼形式傳送給發佈者
* DISP-705 — 由於重複釋放或損毀(`fasttop`)
* DISP-706 — 在失效期間，Dispatcher追蹤回到可能導致無限回圈的參照符號連結
* DISP-709 - 封鎖某些虛名 URL 副檔名
* DISP-710 - Linux適用的組建®無法在Cent OS 6上使用

**改良功能**：

* DISP-652 - Dispatcher 提供錯誤的 Date 標題

## 實用資源 {#helpful-resources}

* [AEM Dispatcher 總覽](dispatcher.md)

## 下載 {#downloads}

### Apache 2.4 {#apache}

| 平台 | 架構 | OpenSSL支援 | 按一下以下載 |
|---|---|---|---|
| Linux® | i686 （32位元） | 無 | [`dispatcher-apache2.4-linux-i686-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.7.tar.gz) |
| Linux® | i686 （32位元） | 1.0 | [`dispatcher-apache2.4-linux-i686-ssl1.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.7.tar.gz) |
| Linux® | i686 （32位元） | 1.1 | [`dispatcher-apache2.4-linux-i686-ssl1.1-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.7.tar.gz) |
| Linux® | i686 （32位元） | 3.0 | [`dispatcher-apache2.4-linux-i686-ssl3.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl3.0-4.3.7.tar.gz) |
| Linux® | x86_64 （64位元） | 無 | [`dispatcher-apache2.4-linux-x86_64-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.7.tar.gz) |
| Linux® | x86_64 （64位元） | 1.0 | [`dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.7.tar.gz) |
| Linux® | x86_64 （64位元） | 1.1 | [`dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.7.tar.gz) |
| Linux® | x86_64 （64位元） | 3.0 | [`dispatcher-apache2.4-linux-x86_64-ssl3.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl3.0-4.3.7.tar.gz) |
| Linux® | aarch64 （64位元） | 無 | [`dispatcher-apache2.4-linux-aarch64-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-4.3.7.tar.gz) |
| Linux® | aarch64 （64位元） | 1.0 | [`dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.7.tar.gz) |
| Linux® | aarch64 （64位元） | 1.1 | [`dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.7.tar.gz) |
| Linux® | aarch64 （64位元） | 3.0 | [`dispatcher-apache2.4-linux-aarch64-ssl3.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl3.0-4.3.7.tar.gz) |
| macOS | arm64 （64位元） | 無 | [`dispatcher-apache2.4-darwin-arm64-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-arm64-4.3.7.tar.gz) |
| macOS | x86_64 （64位元） | 無 | [`dispatcher-apache2.4-darwin-x86_64-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.7.tar.gz) |

### IIS {#iis}

| 平台 | 架構 | OpenSSL支援 | 按一下以下載 |
|---|---|---|---|
| Windows | x86 （32位元） | 無 | [`dispatcher-iis-windows-x86-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.7.zip) |
| Windows | x86 （32位元） | 1.0 | [`dispatcher-iis-windows-x86-ssl1.0-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.7.zip) |
| Windows | x86 （32位元） | 1.1 | [`dispatcher-iis-windows-x86-ssl1.1-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.7.zip) |
| Windows | x64 （64位元） | 無 | [`dispatcher-iis-windows-x64-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.7.zip) |
| Windows | x64 （64位元） | 1.0 | [`dispatcher-iis-windows-x64-ssl1.0-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.7.zip) |
| Windows | x64 （64位元） | 1.1 | [`dispatcher-iis-windows-x64-ssl1.1-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.7.zip) |
| Windows | x64 （64位元） | 3.0 | [`dispatcher-iis-windows-x64-ssl3.0-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl3.0-4.3.7.zip) |