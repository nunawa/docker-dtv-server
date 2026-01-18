# docker-dtv-server

![docker-dtv-serverを立ち上げた時のスクリーンショット](./screenshot.webp)

Dockerで構築するMirakurun + EDCB + KonomiTVなTV視聴・録画環境

## 技術スタック

- [Mirakurun](https://github.com/Chinachu/Mirakurun)
- [tsukumijima/libaribb25](https://github.com/tsukumijima/libaribb25)
- [stz2012/recpt1](https://github.com/stz2012/recpt1)
- [xtne6f/EDCB](https://github.com/xtne6f/EDCB)
- [EDCB_Material_WebUI](https://github.com/EMWUI/EDCB_Material_WebUI)
- [BonDriver_LinuxMirakc](https://github.com/matching/BonDriver_LinuxMirakc)
- [KonomiTV](https://github.com/tsukumijima/KonomiTV)
- [Docker](https://www.docker.com/)

## Getting Started

### 前提条件

- ホストPC上に以下のものが必要です：
  - Docker
  - px4_drvなどのチューナードライバ
- ホストOSはUbuntu 24.04 LTSを想定しています。

### インストール

Mirakurun用に`mirakurun/conf`ディレクトリ以下にchannels.yml、tuners.ymlを作成してください。
[ISDBScanner](https://github.com/tsukumijima/ISDBScanner)などで自動生成できます。

EDCB用に`EDCB/edcb`ディレクトリ以下にCommon.ini、EpgDataCap_Bon.ini、EpgTimerSrv.iniを作成してください。
EpgTimerSrv.iniの以下の項目はお使いのチューナーに合わせて変更してください。

```ini
[BonDriver_LinuxMirakc.so]
Count=4     # チューナー数
GetEpg=1
EPGCount=2  # EPG取得に使用するチューナー数
Priority=0
```

デフォルトの録画保存先ディレクトリは`EDCB/record`に設定されています。必要に応じてcompose.yamlのedcbサービスのボリューム設定を変更してください。

```yaml
      - type: bind
        source: "./EDCB/record" # ここを変更
        target: "/record"
```

KonomiTV用に`KonomiTV`ディレクトリ以下にconfig.yamlを作成してください。

設定が完了したら、以下のコマンドでDockerイメージをビルドし、コンテナを起動します：

```bash
docker compose up -d
```

最初にEDCBチャンネルスキャン用のコンテナが立ち上がります。チャンネルスキャンには3〜4分ほどかかりますので、完了するまで待ってからEDCBとKonomiTVにアクセスしてください。

チャンネルスキャンが必要ない場合は、次のコマンドでスキップできます：

```bash
docker compose up -d --no-deps mirakurun edcb konomitv
```

## 紹介記事

[Dockerで構築！ミニPC + PX-W3U4でお手軽自宅録画サーバー](https://zenn.dev/nunawa/articles/ecb9ef2e237532)

## ライセンス

docker-dtv-serverは、MITライセンスのもとで公開されています。
