# Docker CE(Community Edition)の導入と設定
[参考にした記事を見る](https://qiita.com/shimitei/items/11c072383195e5830b6e)

## Homebrewをインストールしよう
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

#### Homebrew Caskをインストール
    brew tap caskroom/cask

#### VirtualBoxのインストール
    brew cask install virtualbox

#### Vagrantのインストール
    brew cask install vagrant


## CentOS7環境を作成しよう
[参考にした記事を見る](https://qiita.com/sudachi808/items/3614fd90f9025973de4b)
#### VagrantにBoxを追加する
    vagrant box add centos/7

#### VirtualBoxを選択する
    Enter your choice: 3

#### VagrantにBoxを追加されたか確認する
    $ vagrant box list
    centos/7 (virtualbox, 2004.01)

#### 作業ディレクトリの作成
    mkdir ~/centos7　&& cd ~/centos7/

#### Vagrantの初期設定
    vagrant init centos/7

#### Vagrantを起動
    vagrant up

#### Vagrantへログイン
    vagrant ssh


## CentOSにDockerを導入しよう
[参考にした記事を見る](https://docs.docker.com/install/linux/docker-ce/centos/)

#### 必要なパッケージをインストール
    sudo yum install -y device-mapper-persistent-data lvm2

#### DockerCE repoを追加する
　   sudo yum-config-manager \
        --add-repo \
        https://download.docker.com/linux/centos/docker-ce.repo

#### DockerCE パッケージ とcontainerd.ioをインストール
    sudo yum install -y docker-ce-18.09.5 docker-ce-cli-18.09.5 containerd.io

#### Dockerサービス起動と有効化にする
    sudo systemctl start docker
    sudo systemctl enable docker

#### dockerグループを追加する
    sudo groupadd docker

####  dockerグループにユーザ-追加をしdockerコマンドの権限を与える
    sudo usermod -a -G docker vagrant

#### インストール完了したかをコンテナを起動して、確認しよう
    docker run hello-world


## Storage Driverを選択する
[参考にした記事を見る](https://docs.docker.com/storage/storagedriver/select-storage-driver/)

　Storage Driverとは？
　- コンテナーの内部ストレージを処理するプラグインの利用可能なドライバー
　- 各レイヤーが互いにやり取りできるようにします

現在は、初期設定のDriver(Ubuntu)は overlay2が使われており,
CentOS/RedHatは時々Driverがdevicemapperになっています。

#### Storage Driver　を確認する
    docker info | grep "Storage"

もし、Storage Driver: devicemapperと表示されましたら、次のステップにお進みください。

#### daemon flagを使い、storage driverを設定する
    sudo vi /usr/lib/systemd/system/docker.service

#### ExecStartの行を編集する　
    ExecStart=/usr/bin/dockerd --storage-driver devicemapper ...

#### systemdを更新し、dockerを再起動する
    sudo systemctl daemon-reload
    sudo systemctl restart docker

#### daemon configuration fileを作成する
    sudo vi /etc/docker/daemon.json

#### daemon configuration fileにstrage-driverをdevicemapperに設定する
    {
        "storage-driver": "devicemapper"
    }

#### dockerを更新し、storage-driverが反映されたか確認しよう
    sudo systemctl restart docker
    sudo systemctl status docker
