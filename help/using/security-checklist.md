---
title: Dispatcher 安全性檢查清單
description: 瞭解在進入生產階段前應該完成的Dispatcher安全性檢查清單。
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
jcr-lastmodifiedby: remove-legacypath-6-1
index: y
internal: n
snippet: y
exl-id: 49009810-b5bf-41fd-b544-19dd0c06b013
source-git-commit: 0a1aa854ea286a30c3527be8fc7c0998726a663f
workflow-type: tm+mt
source-wordcount: '590'
ht-degree: 64%

---

# Dispatcher 安全性檢查清單{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

Adobe 建議您在進入生產階段前完成以下檢查清單。

>[!CAUTION]
>
>在上線之前，完成您的 AEM 版本的安全性檢查清單。 參閱相應的 [Adobe Experience Manager 文件](https://experienceleague.adobe.com/en/docs/experience-manager-65/content/security/security-checklist)。

## 使用最新版的 Dispatcher {#use-the-latest-version-of-dispatcher}

安裝您的平台適用的最新可用版本。 升級您的Dispatcher執行個體以使用最新版本以利用產品和安全性增強功能。 請參閱[安裝 Dispatcher](dispatcher-install.md)。

>[!NOTE]
>
>您可以檢視Dispatcher記錄檔來檢查Dispatcher安裝的目前版本。
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>若要尋找記錄檔，可在 `httpd.conf` 中檢查 Dispatcher 設定。

## 限制可以清除您的快取的用戶端 {#restrict-clients-that-can-flush-your-cache}

Adobe 建議您最好[限制可以清除您的快取的用戶端。](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)

## 啟用HTTPS以提供傳輸層安全性 {#enable-https-for-transport-layer-security}

Adobe建議您在製作和發佈執行個體上啟用HTTPS傳輸層。

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:41:28.841-0400

<p>Recommended to have SSL termination, front end SSL.</p> 
<p>Question is do we want to have SSL communication between dispatcher and AEM instances (publish and/or author).</p> 
<p>We might want to have two items:</p> 
<ul> 
 <li>MUST HTTPS clients -&gt; dispatcher / load balancer</li> 
 <li>NICE load balancer -&gt; dispatcher<br /> </li> 
 <li>NICE dispatcher -&gt; instances if sensitive information such as credit cards / or infrastructure requirements such as DMZ</li> 
</ul>

 -->

## 限制存取 {#restrict-access}

當您設定 Dispatcher 時，要盡可能限制外部存取。 請參閱 Dispatcher 文件中的[範例 /filter 區段](dispatcher-configuration.md#main-pars_184_1_title)。

## 確保對管理 URL 的存取權已被拒絕 {#make-sure-access-to-administrative-urls-is-denied}

務必使用篩選條件來封鎖對任何管理 URL (例如網頁主控台) 的外部存取。

如需必須封鎖的 URL 清單，可參閱[測試 Dispatcher 安全性](dispatcher-configuration.md#testing-dispatcher-security)。

## 使用允許清單而非封鎖清單 {#use-allowlists-instead-of-blocklists}

允許清單是提供存取控制的較佳方式，因為其原本就會假定所有存取請求都應該被拒絕，除非這些請求被明確列為允許清單的一部分。 此模型對可能尚未審查或在特定設定階段被考量的新請求提供較嚴格的控管。

## 透過專用系統使用者執行 Dispatcher {#run-dispatcher-with-a-dedicated-system-user}

設定Dispatcher時，請確定網頁伺服器是由具有最低許可權的專用使用者執行。 建議您只授與Dispatcher快取資料夾的寫入許可權。

此外，IIS 使用者必須依下面來設定他們的網站：

1. 在您網站的實體路徑設定中，選取 **以特定使用者身分連線**.
1. 設定使用者。

## 避免阻斷服務 (DoS) 攻擊 {#prevent-denial-of-service-dos-attacks}

阻斷服務 (DoS) 攻擊指的是嘗試讓電腦資源無法提供給其目標使用者使用。

在Dispatcher層級，有兩個設定方法可避免DoS攻擊： [篩選器](https://experienceleague.adobe.com/en/docs#/filter)

* 使用 mod_rewrite 模組 (例如 [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)) 執行 URL 驗證 (如果 URL 模式規則不是太複雜)。

* 使用[篩選條件](dispatcher-configuration.md#configuring-access-to-content-filter)避免 Dispatcher 快取帶有虛假副檔名的 URL。\
  例如，變更快取規則，將快取限制為預期的 mime 類型，例如：

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`

  您可以看到的設定檔範例 [限制外部存取](#restrict-access). 其中包含MIME型別的限制。

若要在發佈執行個體上啟用完整功能，請設定篩選條件來防止存取以下節點：

* `/etc/`
* `/libs/`

然後設定篩選條件來允許存取以下節點路徑：

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` (JS、CSS 和 JSON)
* `/libs/cq/security/userinfo.json` (CQ 使用者資訊)
* `/libs/granite/security/currentuser.json` (**資料不得快取**)

* `/libs/cq/i18n/*` (內部化)

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## 設定 Dispatcher 以避免 CSRF 攻擊 {#configure-dispatcher-to-prevent-csrf-attacks}

AEM 提供了旨在避免跨網站請求偽造攻擊的[架構](https://experienceleague.adobe.com/en/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions#verification-steps)。 若要正確使用此架構，請執行以下動作，在Dispatcher中允許清單CSRF權杖支援：

1. 建立篩選條件以允許 `/libs/granite/csrf/token.json` 路徑；
1. 將 `CSRF-Token` 標頭新增到 Dispatcher 設定的 `clientheaders` 區段。

## 避免點擊劫持 {#prevent-clickjacking}

若要避免點擊劫持，Adobe 建議您設定網頁伺服器，以提供設為 `SAMEORIGIN` 的 `X-FRAME-OPTIONS` HTTP 標頭。

如需點擊劫持的詳細資訊，可參閱 [OWASP 網站](https://owasp.org/www-community/attacks/Clickjacking)。

## 執行滲透測試 {#perform-a-penetration-test}

Adobe強烈建議您在進入生產階段前執行AEM基礎結構的滲透測試。

