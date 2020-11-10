# VM への SSH接続
* Git Bash 等を用いて、鍵ペアを生成する。id_rsa と　id_rsa.pub を、どのフォルダでもいいから作成する
```
# ssh-keygen -t rsa -b 4096 -C "tstpc6001@gmail.com"
（tstpc6001 の部分がユーザ名となる。好きなもので良いと思う）

「Enter file in which to save the key」に対しては、好きなファイル名で良い
（例）「/z/rsa/id_rsa」
```

* GCP へのアクセス
```
https://console.cloud.google.com/home/dashboard?project=proxy-test-265811&hl=ja&pli=1
```
* Always Free の制限の確認
```
https://cloud.google.com/free/docs/gcp-free-tier?hl=ja#free-tier-usage-limits
```
* GCE コンソールで、左側のリストから「VM インスタンス」を選ぶ  
* 画面上部の「編集」をクリック  
* 「SSHキーが○個あります」の「表示して編集する」をクリック  
* 「認証鍵全体を入力」の部分に、id_rsa.pub の内容を貼り付ける（ssh-rsa から ==tstpc6001@gmail.com まで）  
* 画面下部の「保存」をクリック

次は、RLogin の設定を行う  
* Server Address： GCE コンソールで、左側のリストから「VM インスタンス」を選び、ネットワークインターフェイスの「外部IP」に表示されているアドレスを入力  
* User Name： tstpc6001  
* SSH Identity Key：「id_rsa」を指定すれば良い（現在は、`__dev__/_GCP` に保存している）
* デフォルト文字セット： UTF-8  

---
# RLogin で SSH 接続後
```
$ sudo su -
# apt update
# apt upgrade
# apt install vim
```

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
# GCP のファイアウォールの調整
* VPC ネットワーク -> ファイアウォール
```
・ 上り　IP範囲 -> 自分の IP のみ　プロトコル/ポート all　アクション -> 許可
・ 上り　IP範囲 -> 0.0.0.0/0　　　プロトコル/ポート　all　アクション -> 拒否
```

---
# プロキシ（squid）の設定
```
# apt install squid
# systemctl status squid
```
* vim で http_access を検索し（ノーマルモードで /http_access + リターン。その後、「n」or「N」で検索）
```
以下のみに。それ以外は、コメントアウト
http_access allow all
```
* vim で http_port を検索し、http プロキシのポート番号を変更
```
http_port 3128 を変更
```

---
# 利用していないとき
* Compute Engine -> VMインスタンス　で、一番右端のメニューで「停止」しておく

---
# 使用料金の確認
* お支払い -> 予算とアラート、でアラートを設定しておく
* お支払い -> 概要、で使用量を確認しておく
