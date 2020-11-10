# VM への SSH接続
* Git Bash 等を用いて、鍵ペアを生成する。id_rsa と　id_rsa.pub を、どのフォルダでもいいから作成する
```
# ssh-keygen -t rsa -b 4096 -C "tstpc6001@gmail.com"
（tstpc6001 の部分がユーザ名となる。好きなもので良いと思う）

「Enter file in which to save the key」に対しては、好きなファイル名で良い
（例）「/z/rsa/id_rsa」
```

* GCE コンソールで、左側のリストから「VM インスタンス」を選ぶ  
* 画面上部の「編集」をクリック  
* 「SSHキーが○個あります」の「表示して編集する」をクリック  
* 「認証鍵全体を入力」の部分に、id_rsa.pub の内容を貼り付ける（ssh-rsa から ==tstpc6001@gmail.com まで）  
* 画面下部の「保存」をクリック

次は、RLogin の設定を行う  
* Server Address： GCE コンソールで、左側のリストから「VM インスタンス」を選び、ネットワークインターフェイスの「外部IP」を入力  
* User Name： tstpc6001  
* SSH Identity Key：「id_rsa」を指定すれば良い  
* デフォルト文字セット： UTF-8  

---
# SSH ポート変更
```
$ sudo su -
# cd /etc/ssh
# ls
# cp sshd_config sshd_config.old
# ls -la
```
* sshd_config の Port を変更（先頭付近にあるから、すぐに分かるはず）  
* reboot  
* GCP のナビゲーションから、VPCネットワークを選択  
* 左側のリストから「ファイアウォール ルール」を選択  
* 上段から「ファイアウォール ルールを作成」を選択  
* とりあえず、自分の IP から、全てのポートを通過させるルールを作成  
* ssh と rdp に関するルールを削除  

ポートの開放状態の確認
```
# apt install nmap
# nmap localhost
```

---
# プロキシ（squid）の設定
```
# apt install squid
```
