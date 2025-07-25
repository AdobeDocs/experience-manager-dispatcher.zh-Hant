---
title: 透過Dispatcher使用SSL
description: 了解如何設定 Dispatcher 以便使用 SSL 連線與 AEM 通訊。
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
index: y
internal: n
snippet: y
exl-id: ec378409-ddb7-4917-981d-dbf2198aca98
source-git-commit: c41b4026a64f9c90318e12de5397eb4c116056d9
workflow-type: tm+mt
source-wordcount: '1305'
ht-degree: 91%

---

# 透過Dispatcher使用SSL {#using-ssl-with-dispatcher}

在 Dispatcher 與轉譯器電腦之間使用 SSL 連線：

* [單向 SSL](#use-ssl-when-dispatcher-connects-to-aem)
* [雙向 SSL](#configuring-mutual-ssl-between-dispatcher-and-aem)

>[!NOTE]
>
>與 SSL 憑證有關的作業會與協力廠商產品綁定。Adobe 白金級維護和支援合約中不涵蓋這些作業。

## Dispatcher連線至AEM時使用SSL {#use-ssl-when-dispatcher-connects-to-aem}

設定 Dispatcher 以便使用 SSL 連線與 AEM 或 CQ 轉譯器執行個體通訊。

在設定 Dispatcher 之前，請設定 AEM 或 CQ 使用 SSL。如需進一步詳細資訊，請參閱：

* [預設使用 SSL/TLS](https://experienceleague.adobe.com/zh-hant/docs/experience-manager-65/content/security/ssl-by-default)
* [在 AEM 中使用 SSL 精靈](https://experienceleague.adobe.com/zh-hant/docs/experience-manager-learn/foundation/security/use-the-ssl-wizard)

### SSL相關的請求標頭 {#ssl-related-request-headers}

當 Dispatcher 收到 HTTPS 請求時，Dispatcher 會在其傳送給 AEM 或 CQ 的後續請求中包含以下標頭：

* `X-Forwarded-SSL`
* `X-Forwarded-SSL-Cipher`
* `X-Forwarded-SSL-Keysize`
* `X-Forwarded-SSL-Session-ID`

透過帶有 `mod_ssl` 的 Apache-2.4 發送的請求包含與以下範例類似的標頭：

```shell
X-Forwarded-SSL: on
X-Forwarded-SSL-Cipher: DHE-RSA-AES256-SHA
X-Forwarded-SSL-Session-ID: 814825E8CD055B4C166C2EF6D75E1D0FE786FFB29DEB6DE1E239D5C771CB5B4D
```

### 設定Dispatcher使用SSL {#configuring-dispatcher-to-use-ssl}

若要設定 Dispatcher 來透過 SSL 與 AEM 或 CQ 連線，您的 [dispatcher.any](dispatcher-configuration.md) 檔案需要以下屬性：

* 處理 HTTPS 請求的虛擬主機。
* 虛擬主機的 `renders` 區段包含一個項目，此項目會識別使用 HTTPS 的 CQ 或 AEM 執行個體的主機名稱及連接埠。
* `renders` 項目包含名為 `secure` 的屬性，其值為 `1`。

注意：如果需要，請建立另一部虛擬主機來處理 HTTP 請求。

以下範例 `dispatcher.any` 檔案顯示用來使用 HTTPS 連線到在主機 `localhost` 和連接埠 `8443` 上執行的 CQ 執行個體的屬性值：

```
/farms
{
   /secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTPS requests
         "https://*"
      }
      /renders
      {
      /0001
         {
            # hostname or IP of the render
            /hostname "localhost"
            # port of the render
            /port "8443"
            # connect via HTTPS
            /secure "1"
         }
      }
     # the rest of the properties are omitted
   }

   /non-secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTP requests
         "http://*"
      }
      /renders
      {
         /0001
      {
         # hostname or IP of the render
         /hostname "localhost"
         # port of the render
         /port "4503"
      }
   }
    # the rest of the properties are omitted
}
```

## 設定Dispatcher與AEM之間的雙向SSL {#configuring-mutual-ssl-between-dispatcher-and-aem}

若要使用雙向 SSL，請設定 Dispatcher 與轉譯器電腦之間的連線 (通常是 AEM 或 CQ 發佈執行個體)：

* Dispatcher 會透過 SSL 連線到轉譯器執行個體。
* 轉譯器執行個體會驗證 Dispatcher 的憑證是否有效。
* Dispatcher 會驗證轉譯器執行個體憑證的 CA 是否值得信任。
* (選擇性) Dispatcher 會驗證轉譯器執行個體的憑證是否符合轉譯器執行個體的伺服器位址。

若要設定雙向 SSL，您需要由受信任的憑證授權單位 (CA) 簽署的憑證。自我簽署憑證是不夠的。您可以充當 CA 或使用第三方 CA 的服務來簽署您的憑證。若要設定雙向 SSL，您需要以下項目：

* 轉譯器執行個體和 Dispatcher 適用的已簽署憑證
* CA 憑證 (如果您充當 CA)
* 用來產生 CA、憑證和憑證請求的 OpenSSL 程式庫。

若要設定雙向 SSL，請執行以下步驟：

1. [安裝](dispatcher-install.md)您的平台適用的最新版 Dispatcher。使用支援 SSL 的 Dispatcher 二進位檔案 (SSL 位於檔案名稱中，例如 `dispatcher-apache2.4-linux-x86-64-ssl10-4.1.7.tar`)。
1. [建立或取得 CA 簽署的憑證](dispatcher-ssl.md#main-pars-title-3)，該憑證適用於 Dispatcher 和轉譯器執行個體。
1. [建立包含轉譯器憑證的金鑰儲存區](dispatcher-ssl.md#main-pars-title-6)並設定轉譯器的 HTTP 服務。
1. [設定 Dispatcher 網頁伺服器模組](dispatcher-ssl.md#main-pars-title-4)以供雙向 SSL 使用。

### 建立或取得CA簽署的憑證 {#creating-or-obtaining-ca-signed-certificates}

建立或取得 CA 簽署的憑證，該憑證用來驗證發佈執行個體和 Dispatcher。

#### 建立您的CA {#creating-your-ca}

如果您充當 CA，請使用 [OpenSSL](https://www.openssl.org/) 建立可簽署伺服器和用戶端憑證的憑證授權單位。（您必須已安裝OpenSSL程式庫。） 如果您使用第三方CA，請勿執行此程式。

1. 開啟終端機，並將目前目錄切換到包含 `CA.sh` 檔案的目錄，例如 `/usr/local/ssl/misc`。
1. 若要建立 CA，請輸入以下命令，然後在收到提示時提供值：

   ```shell
   ./CA.sh -newca
   ```

   >[!NOTE]
   >
   >`openssl.cnf` 檔案中的幾個屬性可控制 CA.sh 指令碼的行為。在您建立 CA 之前，視需要編輯此檔案。

#### 建立憑證 {#creating-the-certificates}

使用 OpenSSL 建立要傳送給第三方 CA 或透過您的 CA 簽署的憑證請求。

當您建立憑證時，OpenSSL 會使用一般名稱屬性來識別憑證持有者。對於轉譯器執行個體的憑證，如果您將 Dispatcher 設定為接受憑證，請使用執行個體電腦的主機名稱當作一般名稱。只有在符合發佈執行個體的主機名稱時才執行此程序。請參閱 [DispatcherCheckPeerCN](dispatcher-ssl.md#main-pars-title-11) 屬性。

1. 開啟終端機，並將目前目錄切換到包含您的 OpenSSL 程式庫的 CH.sh 檔案的目錄。
1. 輸入以下命令，然後在收到提示時提供值。必要時，請使用發佈執行個體的主機名稱當作一般名稱。主機名稱是轉譯器的 IP 位址的 DNS 可解析名稱：

   ```shell
   ./CA.sh -newreq
   ```

   如果您使用第三方 CA，請傳送 newreq.pem 檔案給 CA 進行簽署。如果您充當 CA，請繼續前往步驟 3。

1. 若要使用您 CA 的憑證簽署此憑證，請輸入以下命令：

   ```shell
   ./CA.sh -sign
   ```

   在包含您的 CA 管理檔案的目錄中建立兩個名為 `newcert.pem` 和 `newkey.pem` 的檔案。這兩個檔案分別是適用於轉譯電腦的公開憑證和私密金鑰。

1. 將 `newcert.pem` 重新命名為 `rendercert.pem`，並將 `newkey.pem` 重新命名為 `renderkey.pem`。
1. 重複步驟 2 和 3，為 Dispatcher 模組建立憑證和公開金鑰。務必使用 Dispatcher 執行個體專屬的一般名稱。
1. 將 `newcert.pem` 重新命名為 `dispcert.pem`，並將 `newkey.pem` 重新命名為 `dispkey.pem`。

### 在轉譯器電腦上設定SSL {#configuring-ssl-on-the-render-computer}

在轉譯器執行個體上使用 `rendercert.pem` 和 `renderkey.pem` 檔案設定 SSL。

#### 將轉譯器憑證轉換為JKS (Java™ KeyStore)格式 {#converting-the-render-certificate-to-jks-format}

使用以下命令，將轉譯器憑證 (亦即 PEM 檔案) 轉換為 PKCS#12 檔案。也請包含簽署了轉譯器憑證的 CA 的憑證：

1. 在終端機視窗中，將目前目錄切換到轉譯器憑證和私密金鑰的位置。
1. 若要將轉譯器憑證 (亦即 PEM 檔案) 轉換為 PKCS#12 檔案，請輸入以下命令。也請包含簽署了轉譯器憑證的 CA 的憑證：

   ```shell
   openssl pkcs12 -export -in rendercert.pem -inkey renderkey.pem  -certfile demoCA/cacert.pem -out rendercert.p12
   ```

1. 若要將 PKCS#12 檔案轉換為 Java™ KeyStore (JKS) 格式，請輸入以下命令：

   ```shell
   keytool -importkeystore -srckeystore servercert.p12 -srcstoretype pkcs12 -destkeystore render.keystore
   ```

1. Java™ Keystore 是使用預設別名所建立。如有需要，請變更別名：

   ```shell
   keytool -changealias -alias 1 -destalias jettyhttp -keystore render.keystore
   ```

#### 將CA憑證新增到轉譯器的信任存放區 {#adding-the-ca-cert-to-the-render-s-truststore}

如果您充當 CA，請將您的 CA 憑證匯入金鑰儲存區。然後，將執行轉譯器執行個體的 JVM 設定為信任該金鑰儲存區。

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2014-08-12T13:11:21.401-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The jetty http service has properties to specify trusted CA certificates for mutual SSL for 6.0. Whether they are operable is undetetermined. See https://issues.adobe.com/browse/DOC-4738.</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;"> </p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For 5.6.1, you would specify the system property javax.net.ssl.trustStore, using the path to cacerts as value.</p>

 -->

1. 使用文字編輯器開啟 cacert.pem 檔案，並移除在以下行前面的所有文字：

   `-----BEGIN CERTIFICATE-----`

1. 使用以下命令，將憑證匯入金鑰儲存區：

   ```shell
   keytool -import -keystore cacerts.keystore -alias myca -storepass password -file cacert.pem
   ```

1. 若要將執行轉譯器執行個體的 JVM 設定為信任該金鑰儲存區，請使用以下系統屬性：

   ```shell
   -Djavax.net.ssl.trustStore=<location of cacerts.keystore>
   ```

   例如，如果您使用 crx-quickstart/bin/quickstart 指令碼啟動您的發佈執行個體，您可以修改 CQ_JVM_OPTS 屬性：

   ```shell
   CQ_JVM_OPTS='-server -Xmx2048m -XX:MaxPermSize=512M -Djavax.net.ssl.trustStore=/usr/lib/cq6.0/publish/ssl/cacerts.keystore'
   ```

#### 設定轉譯器執行個體 {#configuring-the-render-instance}

若要設定轉譯器執行個體的 HTTP 服務為使用 SSL，請依照 *`Enable SSL on the Publish Instance`* 區段中的說明示來使用轉譯器憑證：

* AEM 6.2：[啟用透過 SSL 的 HTTP](https://experienceleague.adobe.com/zh-hant/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)
* AEM 6.1：[啟用透過 SSL 的 HTTP](https://experienceleague.adobe.com/zh-hant/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)
* AEM 舊版：請參閱[此頁面。](https://experienceleague.adobe.com/zh-hant/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)

### 為Dispatcher模組設定SSL {#configuring-ssl-for-the-dispatcher-module}

若要設定 Dispatcher 使用雙向 SSL，請準備 Dispatcher 憑證，然後設定網頁伺服器模組。

### 建立統一的Dispatcher憑證 {#creating-a-unified-dispatcher-certificate}

將 Dispatcher 憑證和未加密的私密金鑰合併到單一 PEM 檔案中。使用文字編輯器或 `cat` 命令建立類似於以下範例的檔案：

1. 開啟終端機，並將目前目錄切換到 dispkey.pem 檔案的位置。
1. 若要將私密金鑰解密，請輸入以下命令：

   ```shell
   openssl rsa -in dispkey.pem -out dispkey_unencrypted.pem
   ```

1. 使用文字編輯器或 `cat` 命令，將未加密的私密金鑰和憑證合併到類似於以下範例的單一檔案中：

   ```xml
   -----BEGIN RSA PRIVATE KEY-----
   MIICxjBABgkqhkiG9w0B...
   ...M2HWhDn5ywJsX
   -----END RSA PRIVATE KEY-----
   -----BEGIN CERTIFICATE-----
   MIIC3TCCAk...
   ...roZAs=
   -----END CERTIFICATE-----
   ```

### 指定要用於Dispatcher的憑證 {#specifying-the-certificate-to-use-for-dispatcher}

將以下屬性新增到 [Dispatcher 模組設定](dispatcher-install.md#main-pars-55-35-1022) (在 `httpd.conf` 中)：

* `DispatcherCertificateFile`：指向 Dispatcher 統一憑證檔案的路徑，該檔案包含公開憑證和未加密的私密金鑰。當 SSL 伺服器請求 Dispatcher 用戶端憑證時，就會使用這個檔案。
* `DispatcherCACertificateFile`：CA 憑證檔案的路徑。在 SSL 伺服器提供的 CA 未受到根授權單位信任時使用。
* `DispatcherCheckPeerCN`：是要啟用 (`On`) 還是停用 (`Off`) 對遠端伺服器憑證的主機名稱檢查。

以下程式碼為設定範例：

```xml
<IfModule disp_apache2.c>
  DispatcherConfig conf/dispatcher.any
  DispatcherLog    logs/dispatcher.log
  DispatcherLogLevel 3
  DispatcherNoServerHeader 0
  DispatcherDeclineRoot 0
  DispatcherUseProcessedURL 0
  DispatcherPassError 0
  DispatcherCertificateFile disp_unified.pem
  DispatcherCACertificateFile cacert.pem
  DispatcherCheckPeerCN On
</IfModule>
```
