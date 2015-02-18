vagrantとchefでCentOSにkvmがインストール済みの[railsチュートリアル](http://railstutorial.jp/)環境を作る
===

* rubyとkvmがインストールされたcentos6.5環境をセットアップ
* rails-tutorialの環境セットアップのための下準備までを行う

### 前提

* virtualboxインストール済み
* vagrantインストール済み
* vagrant omnibusプラグインインストール済み
※ (hostOS上に)ruby & gemインストール済み


### Berksfileインストール
```
ZENIKAMI-iMac:~ yas$ gem install berkshelf
```
### vagrant環境を作成

```
ZENIKAMI-iMac:~ yas$ vagrant box add https://github.com/2creatives/vagrant-centos/releases/download/v6.5.3/centos65-x86_64-20140116.box
ZENIKAMI-iMac:~ yas$ vagrant box list
centos65_x86_64     (virtualbox)
ZENIKAMI-iMac:~ yas$ git clone https://github.com/YasKamito/rails-tutorial.git
ZENIKAMI-iMac:~ yas$ cd vagrant-rails
ZENIKAMI-iMac:vagrant-rails yas$ vagrant init centos65_x86_64
```

### berkshelfを実行してレシピをインストールする

```
ZENIKAMI-iMac:vagrant-rails yas$ berks vendor vendor/cookbooks
```

* 以上で準備はできたのでここからはvagrantで仮想マシンを立ち上げる

```
ZENIKAMI-iMac:vagrant-rails yas$ vagrant up --provision
```

* 完成したらvagrantの仮想環境にsshで接続する

```
ZENIKAMI-iMac:vagrant-rails yas$ vagrant ssh
Last login: Tue Jan  6 21:02:53 2015 from 10.0.2.2
[vagrant@vagrant-centos65 ~]$

```

* rootパスワードはvagrant

```
[vagrant@vagrant-centos65 ~]$ su -
Password:
[root@vagrant-centos65 ~]#

```

* rubyが使えることを確認

```
[vagrant@vagrant-centos65 ~]$ ruby -v
ruby 1.9.3p327 (2012-11-10 revision 37606) [x86_64-linux]

```

* gitも

```
[vagrant@vagrant-centos65 ~]$ git --version
git version 1.7.1

```

* gemも

```
[vagrant@vagrant-centos65 ~]$ gem -v
2.4.5

```

* ここからrailsのインストールへ

---

### rvm最新化
```
[root@vagrant-centos65 ~]# rvm get head && rvm reload
Downloading https://get.rvm.io
Downloading https://raw.githubusercontent.com/wayneeseguin/rvm/master/binscripts/rvm-installer.asc
Verifying /usr/local/rvm/archives/rvm-installer.asc
gpg: directory `/root/.gnupg' created
gpg: new configuration file `/root/.gnupg/gpg.conf' created
gpg: WARNING: options in `/root/.gnupg/gpg.conf' are not yet active during this run
gpg: keyring `/root/.gnupg/pubring.gpg' created
gpg: Signature made Wed 07 Jan 2015 01:06:20 AM UTC using RSA key ID BF04FF17
gpg: Can't check signature: No public key
Warning, RVM 1.26.0 introduces signed releases and automated check of signatures when GPG software found.
Assuming you trust Michal Papis import the mpapis public key (downloading the signatures).

GPG signature verification failed for '/usr/local/rvm/archives/rvm-installer' - 'https://raw.githubusercontent.com/wayneeseguin/rvm/master/binscripts/rvm-installer.asc'!
try downloading the signatures:

    gpg2 --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3

or if it fails:

    command curl -sSL https://rvm.io/mpapis.asc | gpg2 --import -

the key can be compared with:

    https://rvm.io/mpapis.asc
    https://keybase.io/mpapis

-bash: return: _ret: numeric argument requiredi
```

* gpgの認証ワーニングが出たら、言われた通りにする

```
[root@vagrant-centos65 ~]# gpg2 --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
gpg: keyring `/root/.gnupg/secring.gpg' created
gpg: requesting key D39DC0E3 from hkp server keys.gnupg.net
gpg: /root/.gnupg/trustdb.gpg: trustdb created
gpg: key D39DC0E3: public key "Michal Papis (RVM signing) <mpapis@gmail.com>" imported
gpg: no ultimately trusted keys found
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
```

* 再度、最新化

```
[root@vagrant-centos65 ~]# rvm get head && rvm reload

```

### 必要なパッケージをインストール

```
[root@vagrant-centos65 ~]# rvm requirements
[root@vagrant-centos65 ~]# yum install libyaml-devel
[root@vagrant-centos65 ~]# yum install libtool libxslt libksba

```

* tutorialに合わせてRuby2.0をインストール

```
[root@vagrant-centos65 ~]# rvm install 2.0.0 --with-openssl-dir=$HOME/.rvm/usr
```

* gemset作成

```
[root@vagrant-centos65 ~]# rvm use 2.0.0@railstutorial_rails_4_0 --create --default
[root@vagrant-centos65 ~]# which gem
/usr/local/rvm/rubies/ruby-2.0.0-p598/bin/gem
```

* gemのバージョンアップ

```
[root@vagrant-centos65 ~]# gem update --system 2.0.3
```

* gemrcファイル作成

```
[root@vagrant-centos65 ~]# vi ~/.gemrc

---
install: --no-rdoc --no-ri
update:  --no-rdoc --no-ri
---
```

* rails インストール  
  注）時間かかる

```
[root@vagrant-centos65 ~]# gem install rails --version 4.0.5
[root@vagrant-centos65 ~]# rails -v
Rails 4.0.5
```

