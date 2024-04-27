---
title: Dispatcher 安全性檢查清單
description: 在進入生產階段前應該完成的安全性檢查清單。
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
jcr-lastmodifiedby: remove-legacypath-6-1
index: y
internal: n
snippet: y
exl-id: 49009810-b5bf-41fd-b544-19dd0c06b013
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: tm+mt
source-wordcount: '591'
ht-degree: 51%

---

# Dispatcher 安全性檢查清單{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

Adobe建議您在進入生產階段前完成以下檢查清單。

>[!CAUTION]
>
>在上線之前，請先完成您的AEM版本的安全性檢查清單。 檢視對應的 [Adobe Experience Manager檔案](https://experienceleague.adobe.com/en/docs/experience-manager-65/content/security/security-checklist).

## 使用最新版的 Dispatcher {#use-the-latest-version-of-dispatcher}

安裝您的平台適用的最新版本。 請務必升級您的Dispatcher執行個體，以便使用最新版本來充分利用產品和安全性增強功能。 請參閱[安裝 Dispatcher](dispatcher-install.md)。

>[!NOTE]
>
>檢視Dispatcher記錄檔來檢查Dispatcher安裝的目前版本。
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>若要尋找記錄檔，請在中檢查Dispatcher設定 `httpd.conf`.

## 限制可以清除您的快取的用戶端 {#restrict-clients-that-can-flush-your-cache}

Adobe 建議您最好[限制可以清除您的快取的用戶端。](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)

## 啟用 HTTPS 以提供傳輸層安全性 {#enable-https-for-transport-layer-security}

Adobe建議在製作和發佈執行個體上啟用HTTPS傳輸層。

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

設定Dispatcher時，請儘可能限制外部存取。 請參閱 Dispatcher 文件中的[範例 /filter 區段](dispatcher-configuration.md#main-pars_184_1_title)。

## 確保對管理 URL 的存取權已被拒絕 {#make-sure-access-to-administrative-urls-is-denied}

務必使用篩選條件來封鎖對任何管理 URL (例如網頁主控台) 的外部存取。

另請參閱 [測試Dispatcher安全性](dispatcher-configuration.md#testing-dispatcher-security) 以取得必須封鎖的URL清單。

## 使用允許清單而非封鎖清單 {#use-allowlists-instead-of-blocklists}

允許清單是提供存取控制的較佳方式，因為其原本就會假定所有存取請求都應該被拒絕，除非這些請求被明確列為允許清單的一部分。 此模型對可能尚未在特定設定階段審查或考慮的新請求提供較嚴格的控制。

## 透過專用系統使用者執行 Dispatcher {#run-dispatcher-with-a-dedicated-system-user}

設定Dispatcher時，您應該確保網頁伺服器是由具有最低許可權的專用使用者執行。 建議僅授與Dispatcher快取資料夾的寫入許可權。

此外，IIS使用者必須依照以下方式設定其網站：

1. 在您網站的實體路徑設定中，請選取&#x200B;**以特定使用者身分連線**。
1. 設定使用者。

## 避免阻斷服務 (DoS) 攻擊 {#prevent-denial-of-service-dos-attacks}

阻斷服務 (DoS) 攻擊指的是嘗試讓電腦資源無法提供給其目標使用者使用。

Dispatcher層級有 [兩種設定方法可避免DoS攻擊](https://experienceleaguecommunities.adobe.com/t5/adobe-experience-manager/configure-aem-dispatcher-to-prevent-dos-attacks-aem-community/m-p/447780).

* 使用 mod_rewrite 模組 (例如 [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)) 執行 URL 驗證 (如果 URL 模式規則不是太複雜)。

* 防止Dispatcher使用快取帶有虛假副檔名的URL [篩選器](dispatcher-configuration.md#configuring-access-to-conten-tfilter).\
  例如，變更快取規則，將快取限制為預期的 mime 類型，例如：

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`

  可以看到用來[限制外部存取](#restrict-access)的設定檔範例，這包括 mime 類型的限制。

若要安全地在發佈執行個體上啟用完整功能，請設定篩選條件來避免存取以下節點：

* `/etc/`
* `/libs/`

然後設定篩選條件來允許存取以下節點路徑：

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` （JS、CSS和JSON）
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

AEM 提供了旨在避免跨網站請求偽造攻擊的[架構](https://experienceleague.adobe.com/en/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions#verification-steps)。 若要正確使用此架構，您必須在Dispatcher中允許清單CSRF權杖支援。
<!-- OLD URL ABOVE USED TO BE https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps -->
您可以執行下列動作來達成此目標：

1. 建立篩選條件以允許 `/libs/granite/csrf/token.json` 路徑；
1. 將 `CSRF-Token` 標頭新增到 Dispatcher 設定的 `clientheaders` 區段。

## 避免點擊劫持 {#prevent-clickjacking}

若要避免點選劫持，Adobe建議您設定網頁伺服器，以提供 `X-FRAME-OPTIONS` HTTP標頭設為 `SAMEORIGIN`.

如需點選劫持的詳細資訊，請參閱 [OWASP網站](https://owasp.org/www-community/attacks/Clickjacking).

## 執行滲透測試 {#perform-a-penetration-test}

Adobe建議您在進入生產階段前執行AEM基礎結構的滲透測試。
