# golang-mysql56
nginx, golang, mysql5.6 compose

## 環境

- マシン: MacBook Air Mid 2011
- CPU: 2 GHz Intel Core i7
- メモリ: 8 GB
- ストレージ: SSD 512 GB

- OS: macOS Sierra 10.12.2

- Docker for Mac バージョン: 1.12.5
  - Docker for Mac には virtualbox は不要ですが、
  - ハードウェアが MMU Virtulizationをサポートしている必要あり。  
  [Docker for Mac vs. Docker Toolbox](https://docs.docker.com/docker-for-mac/docker-toolbox/)
- Amazonlinuxイメージ: Amazon Linux AMI release 2016.09
- docker-sync バージョン: -- 


### MMU Virtulizationをサポートしているか確認

```
$ sysctl kern.hv_support
kern.hv_support: 1
```
1であれば、サポートしている。

## Docker for Macをインストール

公式
https://docs.docker.com/docker-for-mac/

「Get Docker for Mac (stable)」のリンクからDockerアプリをダウンロードし、
Docker.dmg (111.2 MB) をダブルクリックしてインストールする。

以下のコマンドでターミナルでインストールすることも可能です。
```
$ brew cask install docker
```

ターミナル
Docker for Macのインストール後の確認

バージョン
```
$ docker version
Client:
 Version:      1.12.5
 API version:  1.24
 Go version:   go1.6.4
 Git commit:   7392c3b
 Built:        Fri Dec 16 06:14:34 2016
 OS/Arch:      darwin/amd64

Server:
 Version:      1.12.5
 API version:  1.24
 Go version:   go1.6.4
 Git commit:   7392c3b
 Built:        Fri Dec 16 06:14:34 2016
 OS/Arch:      linux/amd64
```

情報
```
$ docker info
Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 0
Server Version: 1.12.5
Storage Driver: aufs
 Root Dir: /var/lib/docker/aufs
 Backing Filesystem: extfs
 Dirs: 0
 Dirperm1 Supported: true
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host null overlay
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Security Options: seccomp
Kernel Version: 4.4.39-moby
Operating System: Alpine Linux v3.4
OSType: linux
Architecture: x86_64
CPUs: 2
Total Memory: 1.951 GiB
Name: moby
ID: BCQW:6DGK:CRJ6:NOGH:RC5R:MRKG:LVHP:ZVTD:C3HP:2CJ7:UYDG:MEK5
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): true
 File Descriptors: 16
 Goroutines: 27
 System Time: 2017-01-08T22:46:00.704794215Z
 EventsListeners: 1
Registry: https://index.docker.io/v1/
WARNING: No kernel memory limit support
Insecure Registries:
 127.0.0.0/8
```

## Dockerイメージの検索

[Dockerhubレポジトリ](https://hub.docker.com/r/_/amazonlinux/)  
https://hub.docker.com/r/_/amazonlinux/

```
$ docker search amazonlinux
NAME                         DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
amazonlinux                  Amazon Linux is an execution environment f...   61        [OK]
marcosnils/amazonlinux       Mirror of the official Amazon Linux Contai...   2
cjonesdev/amazonlinux-lamp   Creates a LAMP stack image using the offic...   1                    [OK]
ann3/amazonlinuximage        pulled amazon linux image from ECS, instal...   1
   :
```

## オフィシャルの「amazonlinux」のDockerイメージをダウンロード
```
$ docker pull amazonlinux
Using default tag: latest
latest: Pulling from library/amazonlinux
c9141092a50d: Downloading [===>          ] 12.42 MB/91.78 MB
   :

c9141092a50d: Pull complete
Digest: sha256:2010c88ac1e7c118d61793eec71dcfe0e276d72b38dd86bd3e49da1f8c48bf54
Status: Downloaded newer image for amazonlinux:latest
```

## ダウンロードしたDockerイメージの確認
```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
amazonlinux         latest              8ae6f52035b5        12 days ago         292.3 MB
```

[Dockerコマンドリファレンス](https://gist.github.com/hotta/69b476ae6662c5ff67f0)

## ローカル作業ディレクトリ作成

名前は何でもよいですが、「ec2」 にします。
```
$ mkdir -p $HOME/docker/ec2
$ cd $HOME/docker/ec2
```

## Amazonlinuxコンテナの設定ファイル作成
```
$ vi docker-compose.yml
--------------------------------------------------
version: '2'
services:
  ec2:
    image: amazonlinux
    command: tail -f /dev/null
    container_name: ec2
--------------------------------------------------
```

- Compose ファイルの形式は、
バージョン１（旧フォーマット、ボリュームやネットワークをサポートしていません）、
バージョン２（最新版）と２つのバージョンが存在します。
詳しい情報は
[バージョン](http://docs.docker.jp/compose/compose-file.html#compose-file-versioning) 
に関するドキュメントをご覧ください。

- tail -f /dev/null は、コンテナが起動し続けるようにするためのおまじない。  
[参考サイト](http://stangler.hatenablog.com/entry/2016/11/17/165803)

## Amazonlinuxコンテナの起動
```
$ docker-compose up -d
Creating network "ec2_default" with the default driver
Creating ec2
```

## Amazonlinuxコンテナの確認
```
$ docker ps
CONTAINER ID        IMAGE               COMMAND               CREATED             STATUS              PORTS               NAMES
2430dfc4f400        amazonlinux         "tail -f /dev/null"   14 seconds ago      Up 12 seconds                           ec2
```
UPとなっているので起動しています。


## Amazonlinuxコンテナに切り替え
```
$ docker exec -it ec2 bash
```
環境が切り替わり、コンテナの中に入りました。


## Amazonlinuxコンテナのバージョン確認
```
# cat /etc/system-release
ßAmazon Linux AMI release 2016.09
```

