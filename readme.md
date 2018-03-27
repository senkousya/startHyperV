# 🔰Windows10ProfessionalでHyper-Vをさわってみる

Hyper-Vを利用するとWindows10上に仮想マシンを作成できるようになるらしい。
巷でよく耳にする仮想化技術ってやつですね。

よくわからないのでとりあえずさわってみる。

## 🔰MS Docsのドキュメント

今回、知りたいことそのものズバリなドキュメントがMSDocsにあったのでこちらを参照してやっていく。

[MSDocs - Hyper-V on Windows10 Virtualization Documentation](https://docs.microsoft.com/ja-jp/virtualization/#pivot=main&panel=windows)

## 🔰Hyper-Vってなんぞや

MS DocsのHyper-Vの概要が載っているページがあるので読む。　なとなくへーという感じ。

[MSDocs - Windows 10 の Hyper-V の概要](https://docs.microsoft.com/ja-jp/virtualization/hyper-v-on-windows/about/)

## 🔰今回お試しする環境

- Windows 10 Professional
- thinkpad x1 carbon 4th generation
- CPU:Intel core-i7 6500U
- Memory:8192 MB

## 🔰Windows10でHyper-Vを利用するためのシステム要件

MS Docsにシステム要件の記載があるので確認していく。

[Windows 10 Hyper-V のシステム要件](https://docs.microsoft.com/ja-jp/virtualization/hyper-v-on-windows/reference/hyper-v-requirements)

### 🔰ソフトウェア要件

今回、対象環境はWindows10 Professionalなのでソフトウェア要件はOK

### 🔰ハードウェア要件

- 第 2 レベルのアドレス変換 (SLAT) の 64 ビット プロセッサ
- VM モニター モード拡張機能 (Intel CPU の VT-c) の CPU サポート
- 最小 4 GB のメモリ。

とあるが、メモリがOKな事はわかるけれど他は良く分からない……と思っていたら、システム要件のページに**systeminfo**コマンドで調べられると書いてあった。

**Hyper-V**の所が全部**はい**になってれば大丈夫っぽい。

![](image/systeminfoHyperV.png)

### 🔰BIOS設定

下記の設定が有効になっている必要がある。

- 仮想化テクノロジ (マザーボードの製造元によってラベルが異なる場合があります)
- ハードウェアによるデータ実行防止

今回、仮想化テクノロジの部分について、BIOSの設定を確認した所無効になっていたので有効に変更。

ハードウェアによるデータ実行防止が有効かどうかは`wmic OS Get DataExecutionPrevention_Available`のコマンドで確認できるようなので確認->**True**だったので有効になっているOK。

![](image/wmic.png)

## 🔰windows10 professionalでHyper-Vをインストールする

MS Docsの下記ページにPowershellから登録、手動からの登録の手順が記載されている。

今回は手動でインストールしてみる。

- [MSDocs - Windows 10 上に Hyper-V をインストールする](https://docs.microsoft.com/ja-jp/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)

コントロールパネル -> windowsの機能の有効化または無効化 -> Hyper-Vにチェックを入れて有効化 -> OKを選択

※BIOSの仮想化テクノロジ設定が無効になっていると、Hyper-Vの項目がグレーアウトになって選択できないようです。  
![](image/activeHyperVstep001.png)

今すぐ再起動  
![](image/activeHyperVstep002.png)

再起動後、**Hyper-V マネージャ**を起動してみる。  

windowsボタンから**Hyper-V マネージャ**を選択して起動する。  
![](image/startHyperVmanagerStep001.png)

起動しました  
![](image/startHyperVmanagerStep002.png)

## 🔰Hyper-V による仮想マシンの作成 + ゲストOSのインストール

Hyper-Vで仮想マシンを作成して、サポートされているゲストOSのubuntuをインストールしてみる。

- [MSDocs - Hyper-V による仮想マシンの作成](https://docs.microsoft.com/ja-jp/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)
- [MSDocs - サポートされているゲストオペレーティングシステム](https://docs.microsoft.com/ja-jp/virtualization/hyper-v-on-windows/about/supported-guest-os)

### 🔰Ubuntu Server 16.04.4 LTSのイメージをダウンロード

今回は**Ubuntu Server 16.04.4 LTS**を仮想マシンにインストールしてみる。

まずは**Ubuntu Server 16.04.4 LTS**のイメージをダウンロード。

- [Download Ubuntu Server](https://www.ubuntu.com/download/server)

![](image/downloadUbuntuServer.png)

### 🔰仮想スイッチの作成

仮想マシンからもインタネットに接続させるためには仮想スイッチを作成する必要があるらしいので作成する。

仮想スイッチマネージャを選択  
![](image/makeVSStep001.png)

仮想スイッチの作製を選択  
![](image/makeVSStep002.png)

名前を入力して接続の種類は外部ネットワークを選択  
![](image/makeVSStep003.png)

はい  
![](image/makeVSStep004.png)

### 🔰仮想マシンの作成

新規を選択  
![](image/makeVMStep001.png)

仮想マシンを選択  
![](image/makeVMStep002.png)

次へ  
![](image/makeVMStep003.png)

名前を入力して次へを選択  
![](image/makeVMStep004.png)

第２世代を選択  
![](image/makeVMStep005.png)

とりあえず初期値の1024のまま次へ。  
![](image/makeVMStep006.png)

さきほど作成した仮想スイッチを選択  
![](image/makeVMStep007.png)

初期値のまま次へ  
![](image/makeVMStep008.png)

ダウンロードしたUbuntuServerのイメージを選択して次へ  
![](image/makeVMStep009.png)

条件を確認して完了を選択  
![](image/makeVMStep010.png)

### 🔰セキュアブートの設定

ぐぐるとUbuntuで第二世代だったりする場合、セキュアブートの設定を変更しないと起動しないらしい。

ちなみにこの設定を変更しないで試しに起動してみましたが、見事に起動失敗しました。

UbuntuServerの設定を選択  
![](image/settingSecureBootStep001.png)

セキュアブートのテンプレートを**Microsoft UEFI 証明機関**に変更
![](image/settingSecureBootStep002.png)

### 🔰仮想マシンの起動と接続

起動を選択  
![](image/startVMStep001.png)

接続を選択すると仮想マシンにvmconnect.exeを利用して接続を行います  

接続を選択  
![](image/startVMStep002.png)

## 🔰Ubuntu Server 16.04.4 LTSのインストール

仮想マシンに接続できたのでUbuntu Server 16.04.4 LTSのインストールを行う。

ubuntuServerとかよくわからないのでとりあえずデフォルト設定でガシガシインストールしていく。

Install Ubuntu Serverを選択  
![](image/installUbuntuServerStep001.png)

Japaneseを選択  
![](image/installUbuntuServerStep002.png)

はいを選択  
![](image/installUbuntuServerStep003.png)

日本を選択  
![](image/installUbuntuServerStep004.png)

アジアを選択  
![](image/installUbuntuServerStep005.png)

はいを選択  
![](image/installUbuntuServerStep006.png)

Japaneseを選択  
![](image/installUbuntuServerStep007.png)

Japaneseを選択  
![](image/installUbuntuServerStep008.png)

ホスト名を入力  
![](image/installUbuntuServerStep009.png)

ユーザ名を入力(フルネーム)  
![](image/installUbuntuServerStep010.png)

ユーザ名を入力  
![](image/installUbuntuServerStep011.png)

パスワードを入力  
![](image/installUbuntuServerStep012.png)

パスワードを再度入力  
![](image/installUbuntuServerStep013.png)

いいえを選択  
![](image/installUbuntuServerStep014.png)

はいを選択  
![](image/installUbuntuServerStep015.png)

ガイド - ディスク全体を使いLVMをセットアップするを選択  
![](image/installUbuntuServerStep016.png)

選択  
![](image/installUbuntuServerStep017.png)

はいを選択  
![](image/installUbuntuServerStep018.png)

初期値のまま続けるを選択  
![](image/installUbuntuServerStep019.png)

はいを選択  
![](image/installUbuntuServerStep020.png)

はいを選択  
![](image/installUbuntuServerStep021.png)

続けるを選択  
![](image/installUbuntuServerStep022.png)

自動的にアップデートしないを選択  
![](image/installUbuntuServerStep023.png)

standard system utilitiesを選択して続けるを選択  
![](image/installUbuntuServerStep024.png)

インストールの完了 続けるを選択  
![](image/installUbuntuServerStep025.png)

## 🔰UbuntuServerにログイン

インストールして再起動後、再度仮想マシンに接続  
![](image/logonUbuntuStep001.png)

ユーザ名とパスワード入力  
![](image/logonUbuntuStep002.png)

ログイン出来ました。なんか日本語が◆に化けてますが。  
![](image/logonUbuntuStep003.png)

日本語が化けてるのでとりえあず英語表示にしておきます。

`export LANG=en_US`でセッション中は英語表示になるようです。  
![](image/logonUbuntuStep004.png)

## 🔰UbuntuServerにopenSSHのインストール

vmconnect.exeを使って接続するのもかったるいのでubuntuServerにopenSSHをインストールしてsshで接続できるように設定する。

とりあえず`sudo su -`でroot  
![](image/installOpenSSHStep001.png)

`apt-get update`でパッケージリストを更新  
![](image/installOpenSSHStep002.png)

`apt-get install openssh-server`でopenSSHのインストール  
![](image/installOpenSSHStep003.png)

**/etc/ssh/sshd_config**の**PasswordAuthentication yes**を有効にする

vimで/etc/ssh/sshd_configを編集する  
![](image/installOpenSSHStep004.png)

PasswordAuthentication yesをyesにする  
![](image/installOpenSSHStep005.png)

`invoke-rc.d ssh rerload`でsshの設定を再読込します。
![](image/installOpenSSHStep006.png)

`ip address show`コマンドでゲストOS(ubuntuServer)のipアドレスを控えておく。

![](image/ip.png)

## 🔰Win32-OpenSSH + PowershellでSSHでゲストOSに接続

`ssh ユーザ名@接続先`で接続  
![](image/sshWithPowershelStep001.png)

パスワードを入力  
![](image/sshWithPowershelStep002.png)

接続できました  
![](image/sshWithPowershelStep003.png)

##　🔰powershellでVMを操作してみる

Hyper-Vを操作するPowershell用のモジュールがHyper-Vをインストールする際に導入されているので（Hyper-V 管理ツール)こちらを利用してみる。

ちなみに下記のコマンドを利用するには管理者で実行する必要があります。

![](image/PowershellHyperV.png)

[MSDocs - Hyper-VとPowershell](https://docs.microsoft.com/ja-jp/virtualization/hyper-v-on-windows/quick-start/try-hyper-v-powershell)

`Start-Process powershell -verb runas`なり右クリックから**管理者で実行**なりでPowershellで管理者で実行して下記のコマンドを実行する

### 🔰get-vm VMの状態を取得する

`get-vm`コマンドでVMの状態を表示する。  
![](image/getVM.png)

### 🔰start-vm VMを起動する

`get-vm`で取得した情報をパイプして`start-vm`で起動してみる。  
![](image/startVM.png)

### 🔰stop-vm VMを停止する

`get-vm`で取得した情報をパイプして`stop-vm`で起動してみる。  
![](image/stopVM.png)

## 🔰総評

仮想化もネットワークもubuntuもよくわからないが、とりあえず動くだけは動いたっぽい。

知らないことばかりでも何とか動いたって感じ。