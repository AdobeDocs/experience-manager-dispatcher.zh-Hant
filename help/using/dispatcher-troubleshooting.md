---
title: 疑難排解Dispatcher問題
description: 瞭解Dispatcher問題的疑難解答。
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
exl-id: 29f338ab-5d25-48a4-9309-058e0cc94cff
source-git-commit: c41b4026a64f9c90318e12de5397eb4c116056d9
workflow-type: tm+mt
source-wordcount: '472'
ht-degree: 93%

---

# 疑難排解Dispatcher問題 {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Dispatcher 版本與 AEM 無關。但是，Dispatcher 文件嵌入在 AEM 文件中。請始終使用文件中嵌入的 Dispatcher 文件獲取最新版本 AEM。
>
>如果您點按 Dispatcher 文件連結，您可能會被重新導向至本頁。該連結嵌入於舊版 AEM 的文件中。

>[!NOTE]
>
>如需詳細資訊，請參閱<!-- URL is 404[Dispatcher Knowledge Base](https://helpx.adobe.com/experience-manager/kb/index/dispatcher.html), -->[Dispatcher排清問題疑難排解](https://experienceleague.adobe.com/search.html?lang=zh-Hant#q=troubleshooting%20dispatcher%20flushing%20issues&sort=relevancy&f:el_product=[Experience%20Manager])和[Dispatcher常見問題集](dispatcher-faq.md)。

## 檢查基本設定 {#check-the-basic-configuration}

與以往一樣，第一步是檢查基本資訊：

* [確認基本操作](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* 檢查 Web 伺服器和 Dispatcher 的所有日誌檔案。 必要時，請提高`loglevel` (用於 Dispatcher [記錄](/help/using/dispatcher-configuration.md#logging))。

* [檢查配置](/help/using/dispatcher-configuration.md)：

   * 您有多個 Dispatcher 嗎？

      * 您確定哪個Dispatcher正在處理您正在調查的網站/頁面嗎？

   * 您是否實施過篩選器？

      * 這些篩選器是否影響您正在調查的事項?

## IIS診斷工具 {#iis-diagnostic-tools}

IIS提供各種跟蹤工具，具體取決於實際版本：

* IIS 6 — 可以下載和配置IIS診斷工具
* IIS 7 — 跟蹤已完全整合

這些工具可幫助您監控活動。

<!-- Both URLs in this topic 404! >
## IIS and 404 Not Found {#iis-and-not-found}

When using IIS, you might experience `404 Not Found` being returned in various scenarios. If so, see the following Knowledge Base articles.

* [IIS 6/7: POST method returns 404](https://helpx.adobe.com/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6: Requests to URLs that contain the base path `/bin` return a `404 Not Found`](https://helpx.adobe.com/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html)

Also check that the Dispatcher cache root and the IIS document root are set to the same directory. -->

## 刪除工作流程模型的問題 {#problems-deleting-workflow-models}

**症狀**

在通過 Dispatcher 存取編寫執行個體時嘗試刪 AEM 除工作流模型時出現問題。

**重現問題的步驟：**

1. 登入到編寫執行個體 (確認正透過 Dispatcher 路由請求)。
1. 建立工作流程；例如，將「標題」設定為 workflowToDelete。
1. 確認已成功建立工作流程。
1. 選取並用滑鼠右鍵按一下工作流程，然後按一下「**刪除**」。

1. 按一下「**是**」確認。
1. 將出現一個顯示以下內容的錯誤訊息框：\
   `ERROR 'Could not delete workflow model!!`。

**解析度**

將以下標題添加到 `/clientheaders` 的 `dispatcher.any` 檔案：

* `x-http-method-override`
* `x-requested-with`

```
{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}
```

## 與mod_dir(Apache)的干涉 {#interference-with-mod-dir-apache}

此程序描述 Dispatcher 如何與 Apache Webserver 中的 `mod_dir` 互動，因為這可能導致各種潛在的意外效果：

### Apache 1.3 {#apache}

在 Apache 1.3 中，`mod_dir` 會處理 URL 對應到檔案系統中的目錄的每個請求。

它也會：

* 將請求重定向到現有 `index.html` 檔案
* 生成目錄清單

在啟用 Dispatcher 時，它會處理這類請求，處理方式是將自己登錄為內容類型 `httpd/unix-directory` 的處理常式。

### Apache 2.x {#apache-x}

在 Apache 2.x 中，情況不同。 一個模組可以處理請求的不同階段，如 URL 修正。 `mod_dir` 會處理此階段，處理方式是將請求 (當 URL 對應到目錄時) 重新導向至已附加 `/` 的 URL。

Dispatcher不會攔截 `mod_dir` 修正，但完全處理對重新導向的 URL 的請求 (即附加 `/` )。 如果遠端伺服器 (例如 AEM) 處理對 `/a_path` 的請求的方式不同於對 `/a_path/` 的請求 (當 `/a_path` 對應到現有目錄)，此程序可能會導致問題。

如果發生這種情況，您必須執行以下任一操作：

* 停用 `mod_dir` (針對 Dispatcher 處理的 `Directory` 或 `Location` 子樹狀結構)

* 使用 `DirectorySlash Off` 配置 `mod_dir` 不追加 `/`
