
# Azure Blob啟用SFTP功能(Preview)

###### tags:`Azure` `Storage Account` `Blob` `SFTP` `2022`

[toc]

## 1/ 基本介紹
* [Azure Blob 儲存體的 SSH 檔案傳輸通訊協定 (SFTP) 支援 (預覽)](https://docs.microsoft.com/zh-tw/azure/storage/blobs/secure-file-transfer-protocol-support)
    * Azure Blob Storage現在支援SSH 檔案傳輸通訊協定 (SFTP)。
    * 可以讓我們透過 SFTP 端點安全連線至 Blob Storage，允許我們可以利用 SFTP 進行檔案存取、檔案傳輸以及檔案管理。
    * 我們可以設定`本機使用者`身分識別進行驗證，透過port 22 連線至使用 SFTP 的儲存體帳戶。

## 2/ 建立儲存體帳戶

* 登入[Azure Portal](https://portal.azure.com/)，建立一個新的儲存體帳戶。

![](https://i.imgur.com/cO4Ql7O.png)

:::warning
:warning: **注意！** 

儲存體帳戶名稱僅能小寫英文字母搭配數字。
:::

* 啟用階層命名空間
    * 必須勾選`啟用階層命名空間`，才能夠勾選`啟用SFTP(預覽)`。

![](https://i.imgur.com/MLPP1ID.png)

* 後續設定皆使用預設選項即可。
:::info
:memo: **經過測試後，發現到如果要在FTP Client能夠刪除檔案的話，必須要將`啟用Blob的虛刪除`取消**。
:::
![](https://i.imgur.com/kvQUKcu.png)

## 3/ 新增本機使用者
* 成功建立儲存體帳戶後，`前往資源`，`點選SFTP(預覽)`->`新增本機使用者`。

![](https://i.imgur.com/qiDKnKA.png)

* 使用者名稱：輸入一個使用者名稱，ex:user1
* 驗證方法：可以使用兩種方式
    * `SSH密碼`：會在新增本機使用者後出現。
    * `SSH金鑰組`：有三種金鑰方式，我們選擇`產生新的金鑰組`，並且給他一個名字。

![](https://i.imgur.com/MVlAgWg.png)

* 設定容器權限
    * `新增容器`：容器就是我們要儲放檔案的空間。

![](https://i.imgur.com/Ty1psHG.png)


* 新增完容器後我們就可以來設定他的權限。
    * `權限`：可以設定這個使用者對於這個容器的權限，我們可以先選擇`全部`。
    * `首頁(登陸)目錄`：這邊可以設定這位使用者連線到這個容器的時，預設是在哪個目錄下。
        *    **經過測試後，使用者還是可以去到上層目錄，不會只限制在這邊設定的目錄底下**。

![](https://i.imgur.com/K9spXn9.png)

* 新增成功後
    * 會出現SSH密碼，建議先把他複製起來，因為一但關閉後就只能夠重新產生。
    * 下載SSH私密金鑰。

![](https://i.imgur.com/UBBfF9z.png =70%x)
![](https://i.imgur.com/E8DaNhn.png =70%x)

* 新增完的使用者會有連接字串，也建議先複製起來，後續我們在用FTP Client連線的時候會用到這些資訊。
* 以我的範例來說：
![](https://i.imgur.com/cTTMN6T.png)
    * 連接字串為：
    ```testblobsftp0729.<CONTAINER_NAME>.user1@testblobsftp0729.blob.core.windows.net```
    * 主機名稱為：
    ```testblobsftp0729.blob.core.windows.net```
    * 使用者名稱為：
    ```testblobsftp0729.<CONTAINER_NAME>.user1```
        * <CONTAINER_NAME>須置換為剛才建立的容器名稱**user1ctr**。
        * 因此完整的使用者名稱為```testblobsftp0729. user1ctr.user1```。
* 新增完使用者後，可以將其停用或者是刪除。

:::warning
:warning: **注意！** 
* [Azure Blob 儲存體中 SSH 檔案傳輸通訊協定 (SFTP) 支援的限制和已知問題 (預覽)](https://docs.microsoft.com/zh-tw/azure/storage/blobs/secure-file-transfer-protocol-known-issues)
    * 本機使用者是目前支援 SFTP 端點身分識別管理的唯一形式。
    * SFTP 端點不支援 Azure Active Directory (Azure AD)。
:::


## 4/ 容器中新增目錄及上傳檔案
* 點選`容器`->剛才建立的容器`user1ctr`。
    * 新增一些目錄和上傳一些檔案供後續測試。

![](https://i.imgur.com/kIb20fd.png)

## 5/ 使用FTP Client測試
* 我這邊使用FileZilla來做測試。
    * 點選`檔案`->`站台管理員`。
    * `新增站台`->主機輸入：```testblobsftp0729.blob.core.windows.net```。
    * `協定`選擇SFTP。
    * `登入型式`選擇一般。
    * `使用者`輸入```testblobsftp0729. user1ctr.user1```。
    * `密碼`輸入剛剛複製的SSH密碼，如果忘記可以重新產生![](https://i.imgur.com/bJB2yLy.png =30%x)
        * 密碼也可以使用金鑰檔案。
        * `登入型式`須改為`金鑰檔案`，並上傳從Azure上建立出來的SSH金鑰檔。
    * 以上資訊都資訊都填好後，點選連線。

![](https://i.imgur.com/b6SOTUI.png)

* 連線成功後，可以看到內容與Azure Blob上的內容相符。

![](https://i.imgur.com/1D5fE38.png)

* 我們也可以上傳檔案到FileZilla上看使否有同步到Azure。

![](https://i.imgur.com/KxnYefx.png)
* 測試成功。 

## 6/ 補充 
* 我們其實也可以透過Azure Blob上所提供的SAS來從Browser存取到檔案。
> [使用共用存取簽章 (SAS) 對 Azure 儲存體資源授與有限存取權](https://docs.microsoft.com/zh-tw/azure/storage/common/storage-sas-overview)
* 點選剛建立容器內的任一檔案
    * 點選`產生SAS`，並設定`權限`。
    * 往下點選`產生SAS權杖與URL`，將`Blob SAS URL`貼到Browser就可以看到文件的內容。

![](https://i.imgur.com/o3kMJOR.png)

![](https://i.imgur.com/MGmJQlD.png)

:::warning
:mag_right: **Discovery**
* 但我有發現到，透過SAS來訪問不同檔案類型會有不同的結果，瀏覽器可能會顯示文件的內容(如上範例)，或是不顯示文件內容直接自動開始下載檔案。

>這部分微軟的回覆是：
> * 瀏覽器在訪問文件後綴為.txt、.json等的文件時，默認操作會將文件内容直接顯示在螢幕上。
> * 而對其他類型的文件，瀏覽器會在訪問時自動開始下載。
> * 但這一默認操作可以被修改。如果希望自動下載後綴為.txt、.json等的文件，可以修改Azure Portal中的**CONTENT-DISPOSITION屬性**：
> > 1. 打開您想訪問的Blob文件，點擊Overview選項：
> > ![](https://i.imgur.com/3LslCFg.png)
> > 2. 下拉Overview選單，在CONTENT-DISPOSITION屬性一欄中輸入”attachment”並儲存:
> > ![](https://i.imgur.com/e2C5dMZ.png)
> > 3. 隨後按照正常步驟生成SAS token與URL，就能在訪問txt等文件類型時讓瀏覽器自動下載了。
> > 4. 如果您不想讓瀏覽器自動下載文件，而是顯示在瀏覽器中，則可以將對應的.jpg等後綴的文件中對應的CONTENT-DISPOSITION項修改為”Inline”，再產生SAS token訪問即可。但請注意，由於某些文件不支持在瀏覽器上顯示，而需要某些特定軟體打開，因此此方法僅對部分文件類型適用，例如.jpg等圖片類型文件。
:::

## 7/ Reference
* [Azure Blob 儲存體的 SSH 檔案傳輸通訊協定 (SFTP) 支援 (預覽)](https://docs.microsoft.com/zh-tw/azure/storage/blobs/secure-file-transfer-protocol-support)
* [Azure Blob 儲存體中 SSH 檔案傳輸通訊協定 (SFTP) 支援的限制和已知問題 (預覽)](https://docs.microsoft.com/zh-tw/azure/storage/blobs/secure-file-transfer-protocol-known-issues)
* [使用共用存取簽章 (SAS) 對 Azure 儲存體資源授與有限存取權](https://docs.microsoft.com/zh-tw/azure/storage/common/storage-sas-overview)