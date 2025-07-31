---
title: 將網站快取效能最佳化
description: 了解如何設計網站才能完全發揮快取的優點。
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
redirecttarget: https://helpx.adobe.com/tw/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
source-git-commit: c41b4026a64f9c90318e12de5397eb4c116056d9
workflow-type: ht
source-wordcount: '1128'
ht-degree: 100%

---


# 將網站快取效能最佳化 {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>Dispatcher 版本與 AEM 無關。如果您點按 Dispatcher 文件連結，您可能會被重新導向至本頁。該連結嵌入於舊版 AEM 的文件中。

Dispatcher 提供許多可用來將效能最佳化的內建機制。本節將說明如何設計網站，好讓快取的優點最大化。

>[!NOTE]
>
>記住 Dispatcher 會將快取儲存在標準網頁伺服器上可能會有所幫助。這表示您：
>
>* 可快取可以儲存為頁面並使用 URL 請求的所有內容
>* 無法儲存其他資料，例如 HTTP 標頭、Cookie、工作階段資料和表單資料。
>
>一般來說，許多快取策略涉及選取良好 URL，而不是依賴這些額外資料。

## 使用一致的頁面編碼 {#using-consistent-page-encoding}

不會快取 HTTP 請求標頭，所以如果您在標頭中儲存頁面編碼資訊，可能會發生問題。在此情況下，當 Dispatcher 從快取中提供頁面時，將會對此頁面使用網頁伺服器的預設編碼。有兩種方式可避免此問題：

* 如果您只使用一種編碼，請確定在網頁伺服器上使用的編碼與 AEM 網站的預設編碼相同。
* 若要設定編碼，可在 HTML `head` 區段中使用 `<META>` 標記，如以下範例所示：

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## 避免 URL 參數 {#avoid-url-parameters}

可能的話，請避免針對您想要快取的頁面使用 URL 參數。例如，如果您有圖片庫，則絕對不會快取以下 URL (除非 Dispatcher [已適當地設定](dispatcher-configuration.md#main-pars_title_24))：

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

不過，您可以將這些參數放到頁面 URL 中，如下所示：

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>此 URL 會呼叫與 gallery.html 相同的頁面和相同的範本。在範本定義中，您可以指定哪一個指令碼會轉譯頁面，或者針對所有頁面使用相同指令碼。

## 使用 URL 自訂 {#customize-by-url}

如果您允許使用者變更字體大小 (或是其他任何版面自訂內容)，請確定不同自訂內容會反映在 URL 中。

例如，不會快取 Cookie，所以如果您將字體大小儲存在 Cookie (或類似機制) 中，將不會為快取頁面保留字體大小。因此，Dispatcher 會隨機傳回任何字體大小的文件。

在 URL 中包含字體大小當作選擇器可避免這個問題：

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>對於大多數版面方面，也可以使用樣式表或用戶端指令碼，或是兩者皆用。其中一個或兩者都可以很好地與快取搭配運作。
>
>這方法也適用於列印版本，您可以在列印版本中使用類似以下的 URL：
>
>`www.myCompany.com/news/main.print.html`
>
>使用範本定義的指令碼萬用字元時，您可以指定轉譯列印頁面的個別指令碼。

## 使用作標題的影像檔案失效 {#invalidating-image-files-used-as-titles}

如果您將頁面標題或其他文字轉譯為圖片，可儲存檔案以便在頁面內容更新時將其刪除：

1. 將影像檔案放在與頁面相同的資料夾中。
1. 針對影像檔案使用以下命名格式：

   `<page file name>.<image file name>`

例如，您可以將 myPage.html 頁面的標題儲存在 myPage.title.gif 中。頁面更新時會自動刪除此檔案，所以對頁面標題所做的任何變更都會自動反映到快取中。

>[!NOTE]
>
>影像檔案不一定存在於 AEM 執行個體上。您可以使用可動態建立影像檔案的指令碼。然後 Dispatcher 會將檔案儲存在網頁伺服器上。

## 使用於導覽的影像檔案失效 {#invalidating-image-files-used-for-navigation}

如果您將圖片用於導覽項目，此方法基本上與標題相同，但是稍微複雜一點。將所有導覽影像與目標頁面一起儲存。如果您將兩張圖片用於一般和活躍情境，可以使用以下指令碼：

* 正常顯示頁面的指令碼。
* 處理 `.normal` 請求並傳回正常圖片的指令碼。
* 處理 `.active` 請求並傳回已啟用的圖片的指令碼。

請務必使用與頁面相同的命名識別碼來建立這些圖片，以確保內容更新會刪除這些圖片及頁面。

對於未修改的頁面，圖片會留在快取中，但是頁面本身會自動失效。

## 個人化 {#personalization}

Dispatcher 無法快取個人化資料，所以建議您將個人化限制在必要的地方。以下說明原因：

* 如果您使用可自由地自訂的起始頁，則每次使用者請求該頁面時都必須編寫它。
* 反之，如果您提供 10 個不同起始頁的選擇，您可以快取每一個起始頁，進而提高效能。

>[!NOTE]
>
>如果您將每個頁面個人化 (例如，將使用者的名稱放在標題列)，您就無法快取頁面，這可能會對效能產生重大影響。
>
>但是，如果必須的話，您可以執行以下操作：
>
>* 使用 iFrame 將頁面分割成對所有使用者都相同的部分，以及對使用者的所有頁面都相同的部分。然後您可以快取這兩個部分。
>* 使用用戶端 JavaScript 來顯示個人化資訊。不過，您必須確保當使用者關閉 JavaScript 時，該頁面還是會正常顯示。
>

## 黏性連線 {#sticky-connections}

[黏性連線](dispatcher.md#TheBenefitsofLoadBalancing)可確保同一個使用者的文件都會在相同伺服器上編寫。如果使用者離開此資料夾並於稍後返回，連線仍然保持不變。定義一個資料夾，以便保存所有需要網站的黏性連線的文件。試著不要將其他文件放在該資料夾中。如果您使用個人化頁面和工作階段資料，這樣做會影響負載平衡。

## MIME 類型 {#mime-types}

瀏覽器可透過兩種方式來判斷檔案類型：

1. 按副檔名 (例如，.html、.gif 和 .jpg)
1. 透過伺服器隨著檔案一起傳送的 MIME 類型。

對於大多數檔案，MIME 類型會隱含在副檔名中：

1. 按副檔名 (例如，.html、.gif 和 .jpg)
1. 透過伺服器隨著檔案一起傳送的 MIME 類型。

如果檔案名稱沒有副檔名，則會顯示為純文字。

MIME 類型是 HTTP 標頭的一部分，因此 Dispatcher 不會快取它。您的 AEM 應用程式可能會傳回無可識別之副檔名的檔案。如果檔案依賴 MIME 類型，則這些檔案可能無法正確顯示。

若要確保檔案可以正確地快取，請遵循以下準則：

* 確定檔案總是有適當的副檔名。
* 避免使用具有像是 download.jsp?file=2214 等 URL 的通用檔案服務指令碼。重寫指令碼，以便使用包含檔案規格的 URL以前面的範例來說，就是 `download.2214.pdf`。

