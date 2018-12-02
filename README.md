# 忍者哈特利 ninja-hattori - 國軍回報彙整line機機人

### 每次放假都要被line和班長綁的喘不過氣來嗎?
### 每次班頭都要把班兵的回報內容一直整理很煩嗎?
### 何不叫個line機機人幫忙解決呢?
<p>line開放開發者使用messaging api的服務，可以新增機機人的頻道，將channelId、channelSecret、channelAccessToken
透過安全應用層https以post的方式和後端伺服器安全地連結，同時後端node.js套件npm中有一個linebot套件包，結合免費的Heroku---提供免費後端平台的服務PaaS，就能客製化自動彙整班兵回報的機機人

### 架構圖
![](https://github.com/billju/ninja-hattori/blob/master/images/flowchart.png)
### 步驟
1. 使用Line帳號登入[Line Developers](https://developers.line.biz/en/)頁面，並點選Messaging API選項
![](https://github.com/billju/ninja-hattori/blob/master/images/message-api.png)
2. 建立一個provider，等同於專案的管理群組，接著再建立一個channel，也就是機機人的帳號
![](https://github.com/billju/ninja-hattori/blob/master/images/message-provider.png)
3. 進入到channel之後就可以設定機機人的大頭貼和自我介紹，同時這個頁面還包括Webhook(應用程式連結)所需要的驗證資料channelId、channelSecret、channelAccessToken(需點選issue發行驗證碼)
![](https://github.com/billju/ninja-hattori/blob/master/images/line-channel.png)
4. 申請Heroku(網頁應用平台)帳號，下載Heroku CLI(command line interface)、git(版本控制軟體)，並且創建一個新app，上面會有settings的選項，打開後會有Config Vars，要在這裡輸入從line developer網頁取得的channelSecret、channelAccessToken
![](https://github.com/billju/ninja-hattori/blob/master/images/config-vars.png)
5. 下載[node.js](https://nodejs.org/en/)，開啟終端機(windows叫做命令提示字元)，輸入`npm --version`查看安裝版本
![](https://github.com/billju/ninja-hattori/blob/master/images/command-line.png)
6. 建立一個新資料夾，輸入`npm init`初始化之後會多一個package.json的檔案，輸入`npm install express linebot`
安裝express套件和linebot套件到資料夾中，這時會出現新的資料夾node_modedules
![](https://github.com/billju/ninja-hattori/blob/master/images/npm-install.png)
7. 再手動新建`.gitignore`的檔案，裡面輸入node_modedules，告訴git不用上傳這個檔案到後端，也就是套件資料夾
![](https://github.com/billju/ninja-hattori/blob/master/images/gitignore.png)
8. 新增index.js作為主程式，設定驗證資料，其餘內容完全可以客製化
![](https://github.com/billju/ninja-hattori/blob/master/images/index-js.png)
9. 設定班兵回報資料都存在report_status.json裡面，以回報第一員的作為標準自動設定成員名單(預設一班十六人)
![](https://github.com/billju/ninja-hattori/blob/master/images/report-status.png)
10. 接著到Heroku的deploy頁面，按照Deploy using Heroku Git的說明上傳檔案到平台
![](https://github.com/billju/ninja-hattori/blob/master/images/git-heroku.png)

### 使用方法
1. 輸入回報內容，重複輸入可以蓋掉前一個人的內容
2. 輸入「你逆」呼叫忍者哈特利回報現況
3. 輸入「重新回報」或是忍者哈特利說「在下已完成彙整 你逆」，就會清空所有資料
4. 這是忍術你逆術的帳號QRCode，開啟LINE掃描條碼即可加入
![](https://github.com/billju/ninja-hattori/blob/master/images/qr-code.png)

### 哈特利自介
在下乃忍者哈特利是也，來幫各位彙整回報內容
1. 輸入重複輸入可以蓋掉前一個人的內容
2. 輸入「你逆」呼叫在下回報現況
3. 出問題時請用指令「重新回報」
4. 在下說「在下已完成彙整 你逆」就會清空資料
5. 彙整到一半的資料在下也能處理

### 哈特利新發現
1. 將對話內容暫存於陣列之中，以陣列順序建立索引，會有伺服器重啟後洗掉暫存的問題
2. 建立文件系統，儲存成json檔案，改以物件名稱進行索引，每個關鍵對話都會寫入json檔，避免因Heroku為了保存免費使用時數(連續三十分鐘沒使用)進入休眠洗掉暫存
3. 新增一個對話多個紀錄的函數(multi-report)，能夠判斷內容中各個使用者的名稱(號碼)；除此之外發現班兵很愛亂打空行，所以新增了空白字串行的過濾器
