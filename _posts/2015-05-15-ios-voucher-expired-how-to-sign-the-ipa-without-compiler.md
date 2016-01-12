---
layout: post
title: "iOS憑證過期如何重簽ipa而不用重compiler"
date: 2015-05-15
categories: git github
---

最近遇到一個需求，要把別人做好但憑證己過期的ipa檔案，用我們自己的apple帳號重新簽署發行，原本以為不可行，原來還真的可以，記錄一下參考來源及步驟
本篇參考這篇文章的作法：[http://dev.mlsdigital.net/posts/how-to-resign-an-ios-app-from-external-developers/](http://dev.mlsdigital.net/posts/how-to-resign-an-ios-app-from-external-developers/ "How to Re-Sign an iOS App from an External Developer")

# 建立APP ID
首先如果你沒有App ID你必需申請一個新的app ID，因為我們拿別人的ipa檔所以它的app ID是綁在對方的帳號裡，為了要重新簽署，所以我們要新增一個自己的app ID,在apple developer portal下點選左邊的Identitifiers->App IDs功能選項進行到App ID的管理頁面，點選右上的"＋"來新增App ID

![iOS_App_IDs_-_Apple_Developer.png](/images/eqlmGc3LTfmWY6UVZsYo_iOS_App_IDs_Apple_Developer.png)
依指示輸入App ID的名稱

![Register_-_iOS_App_IDs_-_Apple_Developer.png](/images/Ryrv5xyITGuZ0iwvldge_Register_iOS_App_IDs_Apple_Developer.png)

App ID Suffix選擇`Explicit App ID`並輸入Bundle ID,Bundle ID不能跟原來的一模一樣哦，因為這個app ID必需是唯一的，名稱取一樣apple也不會給你過的

![Register_-_iOS_App_IDs_-_Apple_Developer 2.png](/images/gZOb0XUZTu2iHwVq2WuU_Register_iOS_App_IDs_Apple_Developer%202.png)

下方的App Services依你的需求來做選擇，如果需要推播就把Push Notification選項給勾起來，好了以後就可以點選下面的Continue來完成新增App ID

# 新增Provisioning Profiles
接下來用剛才新增的App ID來建立Provisioning Profile，點選portal左邊的Provisioning Profile選項進行到管理頁面，點選右上的"+"來新增Provisioning Porfile

![iOS_Provisioning_Profiles_-_Apple_Developer.png](/images/AoC4X3QFQfKoG6Fqs9IE_iOS_Provisioning_Profiles_Apple_Developer.png)

設定好你要的Profile發布方式，然後按`Continue`

![Add_-_iOS_Provisioning_Profiles_-_Apple_Developer.png](/images/HS24DGjYST67tJQ8pi7m_Add_iOS_Provisioning_Profiles_Apple_Developer.png)

接下來選取剛才新增的App ID，然後按`Continue`，完成新增Provisioning Profile

![Add_-_iOS_Provisioning_Profiles_-_Apple_Developer 2.png](/images/yU45QjT5TOSdSJDTf9S6_Add_iOS_Provisioning_Profiles_Apple_Developer%202.png)

新增完成後就可以點選Download來把Provisioning Profile下載回來，點二下安裝到Xcode裡

![iReSign_provisioning_profile.png](/images/d4AQtPwOR26fJaNGXkCw_iReSign_provisioning_profile.png)

# 建立Entitlements Plist
在你的mac本機裡，開啟你最愛的編輯器新增一個檔案，檔名為`entitlements.plist`，內容如下

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>application-identifier</key>
    <string>PREFIX.yourappBundleID</string>
    <key>aps-environment</key>
    <string>production</string>
    <key>get-task-allow</key>
    <false/>
    <key>keychain-access-groups</key>
    <array>
        <string>PREFIX.yourappBundleID</string>
    </array>
</dict>
</plist>
{% endhighlight %}

再回到Apple Developer portal裡的App IDs選項裡，找到你新增的那筆App ID，點開後可以看到PREFIX及BundleID的值，把這二個值取代掉entitlements.plist的PREFIX.yourappBundleID的值

# 執行iReSign app

 到GitHub下載iReSign程式
[iReSign](https://github.com/maciekish/iReSign "iReSign")
下載後直接點二下執行程式，依程式指示輸入你的ipa的路徑、Provisioning profile的路徑、entitlements.plist的路徑，如果Bundle ID有改的話，請把Change ID打勾並在左邊輸入新的Bundle ID(這裡要跟你上面申請新的Bundle ID一致)，下拉選單選擇你要執行簽署的apple帳號，設定完成後按下`ReSign!`
![iReSign_和_blog_和_blog.png](/images/lo0WSNbSUSyeTB69qUg5_iReSign_blog.png)

如果沒有問題的話就可以在原本ipa的路徑下找到被Resign過新的ipa檔案，可以直接把ipa放你的新的裝置上測試看看囉～
