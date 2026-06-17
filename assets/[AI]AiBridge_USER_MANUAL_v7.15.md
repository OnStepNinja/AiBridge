# AiBridge USER MANUAL v7.15

**バージョン**: v7.15
**作者**: Nishioka Sadahiko
**ライセンス**: MIT License（無償・商用利用可・改変可・再配布可）
**最終更新**: 2026-04-05

---

> **重要なお知らせ**
>
> AiBridge はコンパイル済みバイナリ（`.bin` ファイル）のみを配布しています。
> **ソースコードは公開していません。**
> Arduino IDE やライブラリのインストールは不要です。
> 書き込みには `esptool.py` または GUI ツール（ESP32 Flash Download Tool）を使用してください。
> 本ソフトウェアは無償で使用・再配布できます（バイナリ形式・MIT ライセンス）。

---

## 目次

**Part 1 — 概要・仕様**
1. [AiBridge とは](#1-aibridge-とは)
2. [製品情報](#2-製品情報)
3. [システムアーキテクチャ](#3-システムアーキテクチャ)

**Part 2 — ハードウェア**
4. [必要なもの](#4-必要なもの)
5. [GPIO ピン割り当て](#5-gpio-ピン割り当て)
6. [シリアルポート（UART）設定](#6-シリアルポートuart設定)
7. [ハードウェア配線](#7-ハードウェア配線)

**Part 3 — インストールとセットアップ**
8. [ファームウェアの書き込み](#8-ファームウェアの書き込み)
9. [初回セットアップ](#9-初回セットアップ)
10. [File Manager の使い方](#10-file-manager-の使い方)

**Part 4 — 操作・接続**
11. [AiBridge Console の使い方](#11-aibridge-console-の使い方)
12. [SkySafari・天文アプリとの接続（TCP）](#12-skysafari天文アプリとの接続tcp)
13. [ASCOM Alpaca 対応](#13-ascom-alpaca-対応)

**Part 5 — API 仕様**
14. [ネットワーク設定とプロトコル](#14-ネットワーク設定とプロトコル)
15. [API 仕様](#15-api-仕様)
16. [Web UI ページ一覧](#16-web-ui-ページ一覧)

**Part 6 — LUNA 連携・天文台統合**
17. [動的ダッシュボード（LUNA 連携）](#17-動的ダッシュボードluna-連携)
18. [LUNA AI ゲートウェイとの連携](#18-luna-ai-ゲートウェイとの連携)
19. [天文台統合ビジョン（ソラミちゃんシステム）](#19-天文台統合ビジョンソラミちゃんシステム)

**Part 7 — 付録**
20. [トラブルシューティング](#20-トラブルシューティング)
21. [技術仕様一覧](#21-技術仕様一覧)
22. [バージョン履歴](#22-バージョン履歴)
23. [起動ログ例](#23-起動ログ例)

---

# Part 1 — 概要・仕様

## 1. AiBridge とは

AiBridge は ESP32 搭載のコンパクトな望遠鏡制御インターフェースです。天文観測の分野における多様なコントロールプロトコルとハードウェア間の断絶を解消するために設計されました。従来の LX200 プロトコルと最新の ASCOM Alpaca 標準の両方を統合し、SkySafari・NINA・Stellarium などの最新制御アプリケーションが従来型望遠鏡マウントをシームレスに操作できる、戦略的に重要なブリッジとして機能します。

```
スマートフォン・PC・AI（Claude）
        │
        │ Wi-Fi（TCP / HTTP / Alpaca）
        │
   AiBridge（ESP32）
        │
        │ シリアル通信（LX200 プロトコル / 9600bps）
        │
   望遠鏡マウント（OnStep / NS-5000 等）
```

### できること

| 機能 | 説明 |
|------|------|
| **Web コンソール** | ブラウザから望遠鏡を操作・GoTo・状態確認 |
| **SkySafari 接続** | iOS / Android の天文アプリから TCP で直接制御 |
| **ASCOM Alpaca** | NINA・Sequence Generator 等の Windows 天文ソフトと連携 |
| **PHD2 オートガイド** | パルスガイド（PulseGuide）対応 |
| **気象センサー** | DHT22 で温度・湿度を Alpaca 経由で提供 |
| **スイッチ制御** | GPIO 4チャンネルを外部機器の ON/OFF に使用 |
| **動的ダッシュボード** | LUNA AI ゲートウェイと連携したカスタム画面表示 *(v7.15)* |
| **AP + STA 同時動作** | アクセスポイント兼用のため工事不要で即使用可能 |
| **MAC 自動 SSID** | ユニットごとに固有の AP SSID を自動生成 *(v7.15)* |

---

## 2. 製品情報

| 項目 | 詳細 |
|------|------|
| 製品名 | AiBridge - AI 対応望遠鏡インターフェース |
| バージョン | 7.15 |
| 著作権者 | Copyright (c) 2026 Nishioka Sadahiko / 西岡定彦 |
| ライセンス | MIT ライセンス |
| 配布形態 | バイナリのみ（ソースコードは非公開） |
| 連絡先 | nishioka.sst@gmail.com |
| 公式サイト | https://nskikaku.blog.fc2.com/ |
| GitHub | https://github.com/OnStepNinja |

---

## 3. システムアーキテクチャ

AiBridge は統合制御ハブとして機能し、Wi-Fi 経由でさまざまなクライアントからの接続を受け付け、望遠鏡マウントが解釈できるシリアルコマンドに変換します。

### 主要コンポーネント

1. **クライアントアプリケーション**: SkySafari・NINA・Stellarium・Claude AI（LUNA Observatory 経由）などが Wi-Fi 経由で接続。LX200（TCP）または ASCOM Alpaca（HTTP）を使用。

2. **AiBridge 本体**: ESP32 モジュール。Web サーバー（HTTP）・TCP サーバー（LX200）・UDP リスナー（Alpaca Discovery）が常時待機し、コマンドを解釈・変換。

3. **望遠鏡マウント**: OnStep などのシリアル通信対応の赤道儀または経緯台。UART 経由でシリアルコマンドを送信。

4. **外部デバイス**: 同一ネットワーク上の Alpaca 対応デバイス（フォーカサー・カメラ・ドーム等）のプロキシ制御も可能。

5. **LUNA Observatory (v7.15+)**: LUNA AI ゲートウェイが HTTP 経由で接続し、動的ダッシュボードや AI 主導の観測ワークフローを実行。

```
 クライアントアプリ              LUNA Observatory（AI）
（SkySafari / NINA / ─────────────── ↕ ─────────────────
  Stellarium / ブラウザ）              │
         │                            │
         │   Wi-Fi（TCP / HTTP / Alpaca）
         │                            │
         └──────── AiBridge（ESP32）──┘
                          │
                   UART（LX200 シリアル）
                          │
                  望遠鏡マウント（OnStep）
```

---

# Part 2 — ハードウェア

## 4. 必要なもの

### ハードウェア

| 品名 | 仕様 | 備考 |
|------|------|------|
| ESP32 開発ボード | WROOM-32 または互換品 | フラッシュ 4MB 以上 |
| LX200 互換望遠鏡マウント | OnStep / NS-5000 等 | RS-232C または TTL シリアル |
| USB-RS232C 変換（必要な場合） | 3.3V TTL 対応 | OnStep は TTL 直結可 |
| DHT22 温湿度センサー（任意） | GPIO5 に接続 | DHT11 でも可 |
| 小型タクタイルスイッチ（任意） | GPIO39 に接続 | File Manager 有効化用 |
| LED（任意） | GPIO25 に接続 | File Manager 状態表示用 |
| リレーモジュール（任意） | 4 チャンネル | GPIO27/14/12/13 で制御 |

### ソフトウェア（書き込み時のみ必要）

| ソフト | 用途 |
|--------|------|
| esptool.py または ESP32 Flash Download Tool | ファームウェア書き込み（初回のみ） |
| SkySafari / NINA 等（任意） | 天文制御アプリ |

---

## 5. GPIO ピン割り当て

`INPUT_PULLUP` 指定のピン（GPIO39・33・32）はファームウェアレベルで内部プルアップ済みのため、外部プルアップ抵抗は不要です。

| 定数名 | GPIO | 機能 | 方向 |
|--------|------|------|------|
| `FILE_MANAGER_LED_PIN` | 25 | File Manager 有効状態 LED | OUTPUT |
| `FILE_MANAGER_ENABLE_PIN` | 39 | File Manager 有効化ボタン | INPUT_PULLUP |
| `CUSTOM_UI_SELECT_PIN` | 33 | カスタム UI 選択 | INPUT_PULLUP |
| `SERIALONSTEP_SELECT_PIN` | 32 | OnStep 接続 UART 切替 | INPUT_PULLUP |
| `DHT_PIN` | 5 | DHT22/11 温湿度センサー | INPUT |
| `SWITCH_0_PIN` | 27 | Alpaca スイッチ出力 1 | OUTPUT |
| `SWITCH_1_PIN` | 14 | Alpaca スイッチ出力 2 | OUTPUT |
| `SWITCH_2_PIN` | 12 | Alpaca スイッチ出力 3 | OUTPUT |
| `SWITCH_3_PIN` | 13 | Alpaca スイッチ出力 4 | OUTPUT |
| `DEBUG_TX` | 19 | デバッグ用シリアル TX | OUTPUT |
| `DEBUG_RX` | 18 | デバッグ用シリアル RX | INPUT |

---

## 6. シリアルポート（UART）設定

### OnStep 接続ポート（SerialOnStep）

起動時の GPIO32 の入力レベルに基づいて使用する UART が自動切替されます。

| GPIO32 状態 | 使用 UART | TX ピン | RX ピン |
|------------|---------|--------|--------|
| HIGH（オープンまたは 3.3V） | UART0 | GPIO1 | GPIO3 |
| LOW（GND 接続） | UART2 | GPIO17 | GPIO16 |

- **ボーレート**: 9600 baud（固定）
- **フォーマット**: 8N1

### デバッグポート（SerialDebug）

- **UART**: UART1（TX: GPIO19、RX: GPIO18）
- **ボーレート**: 115200 baud

---

## 7. ハードウェア配線

### 7-1. 望遠鏡接続（シリアル）

```
【GPIO32 = HIGH（オープンまたは 3.3V 接続）→ UART0 使用】
  GPIO1 (TX) ─── 望遠鏡 RX
  GPIO3 (RX) ─── 望遠鏡 TX

【GPIO32 = LOW（GND に接続）→ UART2 使用】
  GPIO17 (TX) ─── 望遠鏡 RX
  GPIO16 (RX) ─── 望遠鏡 TX
```

> **OnStep ユーザーへ**: OnStep は TTL レベルのシリアルを直接使用できます。
> **NS-5000 等 RS-232C 機器**: RS-232C ↔ TTL レベル変換アダプターが必要です。

### 7-2. DHT22 センサー接続

```
DHT22                    ESP32
VCC  ─────────────────── 3.3V
GND  ─────────────────── GND
DATA ─── 10kΩ → 3.3V
     └───────────────── GPIO5
```

### 7-3. File Manager スイッチ・LED（任意）

```
【タクタイルスイッチ（GPIO39）】
  片端 ─── GPIO39
  他端 ─── GND
  （内部プルアップ使用。押すと LOW になり File Manager を有効化）

【LED（状態表示 GPIO25）】
  アノード  ─── 抵抗（220Ω）─── GPIO25
  カソード ─── GND
  （File Manager 有効中: 点灯）
```

### 7-4. カスタム UI 選択（GPIO33）

```
GPIO33 ─── GND に接続: 起動時に index2.html（カスタム UI）を表示
GPIO33 ─── オープン（HIGH）: 標準 AiBridge Console を表示
```

---

# Part 3 — インストールとセットアップ

## 8. ファームウェアの書き込み

AiBridge はコンパイル済みバイナリ（`.bin` ファイル）を配布しています。**Arduino IDE やソースコードは不要です。**

### 8-1. 必要なもの

| 品名 | 入手先 |
|------|--------|
| `AiBridge_v7.15_Standard.bin` | 配布ページからダウンロード |
| USB ケーブル（データ通信対応） | — |
| esptool.py | `pip install esptool` |

### 8-2. esptool.py を使った書き込み

```bash
pip install esptool

esptool.py --chip esp32 --port COMx --baud 921600 write_flash -z 0x0 AiBridge_v7.15_Standard.bin
```

| OS | ポート例 |
|----|---------|
| Windows | `COM3` など（デバイスマネージャーで確認） |
| macOS | `/dev/cu.SLAB_USBtoUART` |
| Linux | `/dev/ttyUSB0` |

### 8-3. GUI ツール（Windows）

[ESP32 Flash Download Tool](https://www.espressif.com/en/support/download/other-tools)（Espressif 公式）も利用できます。

| 設定項目 | 値 |
|---------|-----|
| アドレス | `0x0` |
| ファイル | `AiBridge_v7.15_Standard.bin` |
| モード | DIO |
| ボーレート | 921600 |

START ボタンをクリックして書き込み開始。

> ⚠️ **書き込み完了後、WiFi 設定の前に必ず SPIFFS をフォーマットしてください。**
> 手順は [セクション 10-3](#10-3-spiffs-フォーマット) を参照。

---

## 9. 初回セットアップ

### なぜ AP モードがあるのか

AiBridge は電源投入後、**必ず AP モードで起動**します。AP モードとは本体自身が WiFi アクセスポイントになる仕組みです。

1. **初回の WiFi 設定を行うため**
2. **DHCP で割り当てられた IP アドレスを確認するため**
3. **WiFi ルーターを変更したときに再設定できるようにするため**

AP は常時有効なので、どんな状態でも必ず本体に接続できます。

---

> ### ⚠️ 重要：WiFi 設定の前に必ず SPIFFS をフォーマットしてください
>
> 新規ファームウェア書き込み後、初めて WiFi 設定を行う前に、必ず SPIFFS をフォーマットしてください。
> フォーマットしないと古いデータが残り、WiFi 設定が正しく保存されない場合があります。
>
> **手順**: AP（`AiBridgeXXXX`）に接続 → `http://192.168.4.1` → File Manager → **Format SPIFFS** → 確認
>
> フォーマット後は本体が再起動します。その後 ① から設定を進めてください。

---

### ① AP に接続する

WiFi 設定で以下の SSID を選択します：

```
SSID    : AiBridgeXXXX  （XXXX は MAC アドレス下 4 桁 — 例: AiBridgeA1B2）
パスワード: なし（オープン）
```

> **AP SSID はデバイスごとにユニークです。** MAC アドレスから自動生成されるため、複数台でも SSID が重複しません。

ブラウザで以下を開きます：

```
http://192.168.4.1
```

### ② 家庭内 WiFi（STA）を設定する

1. AiBridge Console の **「File Manager」** ボタンをクリック
2. GPIO39 ボタンを押して File Manager を有効化（LED 点灯確認）
3. **WiFi Configuration** セクションに入力:
   - **SSID**: 家庭内 WiFi のネットワーク名
   - **Password**: WiFi のパスワード
   - **Location Name**: このデバイスの識別名（例: `Observatory`）
4. **「Save WiFi Config」** をクリック → 本体が再起動

### ③ STA IP アドレスを確認する

再起動後、再度 AP（`AiBridgeXXXX`）に接続して `http://192.168.4.1` を開くと：

```
STA Address: 192.168.x.xxx （DHCP 割り当て）
```

と表示されます。これが通常運用時のアクセス先 IP アドレスです。

> **💡 ヒント**: 設定完了後は、スマホ・PC を家庭内 WiFi に戻してください。
> 通常の運用は家庭内 WiFi 経由で行います。AP は常時有効なため、緊急時の接続手段としても使えます。

### Location Name（ロケーション名）について

Location Name は Alpaca Discovery 時にデバイスを識別するための名前です。

| 入力値 | Alpaca Discovery での表示 |
|--------|---------------------------|
| Bridge（デフォルト） | Ai-Bridge |
| Observatory | Ai-Observatory |
| MainScope | Ai-MainScope |
| GuideScope | Ai-GuideScope |

> **複数台使用時**: 必ず異なる Location Name を設定してください（例: `Mount1`、`Mount2`）。

---

## 10. File Manager の使い方

File Manager は SPIFFS（内蔵ファイルシステム）を管理する画面です。セキュリティのため、GPIO39 ボタンによる有効化が必要です。

### 10-1. File Manager の有効化

| 方法 | 操作 | 有効時間 |
|------|------|---------|
| **起動時** | GPIO39 を LOW にして電源 ON | 30 分間 |
| **運用中** | Console の「File Manager」ボタンを押した後、GPIO39 を押す | 10 分間 |

有効化中は GPIO25 の LED が点灯します。

### 10-2. WiFi 設定

File Manager の **WiFi Configuration** セクション:

| 項目 | 説明 |
|------|------|
| **SSID** | 家庭内 WiFi のネットワーク名 |
| **Password** | WiFi のパスワード |
| **Location Name** | デバイスの識別名（Alpaca Discovery に使用） |

入力後、**「Save WiFi Config」** をクリックすると `/aibridge_net_cfg.dat` に保存され、本体が再起動します。

### 10-3. SPIFFS フォーマット

**「Format SPIFFS」** ボタンで内蔵ファイルシステムを初期化します。

> ⚠️ フォーマットすると **全ての保存ファイル**（WiFi 設定・カスタム HTML 等）が削除されます。
> 初回書き込み後に 1 回だけ実行し、その後は通常使用しません。

### 10-4. ファイル管理

File Manager 画面で SPIFFS 内のファイルを確認・管理できます:

- ファイル一覧表示・使用容量確認
- PC からファイルをアップロード（カスタム HTML 等）
- ファイルの削除

### 10-5. カスタム UI（index2.html）

GPIO33 を LOW にして起動すると、標準 Console の代わりに `/index2.html` を表示します。
LUNA から配信したカスタムダッシュボード HTML をここに保存して利用できます。

---

# Part 4 — 操作・接続

## 11. AiBridge Console の使い方

ブラウザで `http://<IP>/` にアクセスすると AiBridge Console が開きます。

### 11-1. Network Information（ネットワーク情報）

起動直後に確認できる情報パネルです。

| 表示項目 | 内容 |
|---------|------|
| **Location Name** | Alpaca Discovery の識別名（`Ai-` + ロケーション名） |
| **AP SSID** | このデバイスの AP 名（MAC 自動生成） |
| **AP Address** | AP モード時の IP アドレス（192.168.4.1） |
| **STA Address** | 家庭内 WiFi 接続時の IP + 接続状態バッジ |
| **Command Ports** | TCP コマンドポート（9999 / 9998） |

接続状態バッジ:
- 🟢 **Connected** — 家庭内 WiFi 接続済み
- 🔴 **Not Connected** — 未接続（AP モードのみ稼働中）

### 11-2. ASCOM / Alpaca デバイス検出

ネットワーク上の Alpaca 対応機器を自動検出します。

1. **「Discover ASCOM / Alpaca Telescopes」** ボタンをクリック
2. 数秒待つと検出されたデバイスが一覧表示される
   - 表示例: `📡 Simulator Telescope #0 (192.168.3.100:80)`

> AiBridge 自身は一覧に表示されません（自己検出除外）。

### 11-3. Telescope Status（望遠鏡の状態）

| 表示項目 | 説明 |
|---------|------|
| **RA** | 現在の赤経（時:分:秒） |
| **DEC** | 現在の赤緯（±度:分:秒） |
| **Tracking** | 追尾状態（On / Off） |
| **Slewing** | スルー中かどうか |
| **Pier Side** | ピア側（East / West / Unknown） |

### 11-4. GoTo コマンド

RA・Dec を直接入力して望遠鏡を指定座標に移動させます。

1. **RA** 欄に赤経を入力（例: `05:34:32`）
2. **Dec** 欄に赤緯を入力（例: `+22:00:52`）
3. **「GoTo」** ボタンをクリック

> LX200 の `:MS#` コマンドを使用します。

### 11-5. LX200 コマンド送信

任意の LX200 コマンドを直接送信できます。

1. **Command** 欄にコマンドを入力（例: `GR`、`GD`、`Q`）
2. **「Send」** ボタンをクリック
3. 望遠鏡からの応答が表示される

> コマンド前後の `:` と `#` は自動付加されます。`GR` と入力すれば `:GR#` が送信されます。

### 11-6. Tracking Control（追尾制御）

| ボタン | 機能 |
|--------|------|
| **Tracking On** | 恒星時追尾を開始 |
| **Tracking Off** | 追尾を停止 |
| **Stop** | 即時スルー停止（`:Q#`） |

### 11-7. Switch Control（スイッチ制御）

GPIO に接続したリレー等を ON/OFF します。

| スイッチ | GPIO |
|---------|------|
| Switch 1 | GPIO27 |
| Switch 2 | GPIO14 |
| Switch 3 | GPIO12 |
| Switch 4 | GPIO13 |

### 11-8. Weather（気象情報）

DHT22 センサーが接続されている場合、温度・湿度をリアルタイム表示します。

- **Temperature**: 気温（℃）
- **Humidity**: 湿度（%）

センサーが未接続の場合は `N/A` と表示されます。

---

## 12. SkySafari・天文アプリとの接続（TCP）

AiBridge は LX200 プロトコルの TCP サーバーを 2 ポート同時待機します。

| ポート | 用途 |
|--------|------|
| **9999** | メインポート（SkySafari 等） |
| **9998** | サブポート（第 2 クライアント同時接続用） |

### 12-1. SkySafari の設定

1. SkySafari → **設定 → 望遠鏡 → セットアップ**
2. 以下を設定:

| 項目 | 設定値 |
|------|--------|
| スコープタイプ | Meade LX-200 GPS（または互換機種） |
| マウント | 機器に合わせて選択 |
| 接続方法 | **WiFi** |
| IP アドレス | AiBridge の STA アドレス（例: `192.168.3.154`） |
| ポート番号 | **9999** |

3. **「望遠鏡に接続」** をタップ

### 12-2. iOS（SkySafari）使用時の注意

iOS では WiFi の接続安定性のため、以下に注意してください:

- AiBridge と iOS 端末が **同じ WiFi ネットワーク** に接続されていることを確認
- SkySafari の接続が切れた場合は、アプリを再起動して再接続
- v7.14 以降: `setNoDelay(true)` と `WiFi.setSleep(false)` による iOS 接続安定化実装済み
- アイドル接続は 30 秒でタイムアウト（自動切断）

### 12-3. 2 ポート同時使用

SkySafari（ポート 9999）と別のアプリ（ポート 9998）を同時に接続できます。

```
SkySafari    ─── TCP:9999 ─── AiBridge ─── LX200 ─── 望遠鏡
PHD2 等      ─── TCP:9998 ─┘
```

### 12-4. iOS TCP 安定性対策（v7.14 以降）

v7.14 で実装した iOS 安定化対策は v7.15 に引き継がれています。

| 対策 | 目的 |
|------|------|
| `WiFi.setSleep(false)` | Modem-Sleep 無効化。ビーコン間隔での iOS TCP タイムアウトを防止 |
| `setNoDelay(true)` | Nagle アルゴリズム無効化。短い LX200 コマンドを即時送信 |
| 切断時 `client.stop()` | ソケットリソースを解放。iOS 再接続時のソケット枯渇を防止 |
| アイドルタイムアウト 30 秒 | iOS の「サイレント切断」を検出 |
| コマンド受信タイムアウト 100ms | iOS の TCP 省電力によるパケット遅延に対応 |

---

## 13. ASCOM Alpaca 対応

AiBridge は ASCOM Alpaca v1 プロトコルに対応しています。NINA・Sequence Generator Pro 等の Windows 天文ソフトから直接制御できます。

### 13-1. Alpaca Discovery

Alpaca 対応ソフトウェアからの UDP Discovery（ポート 32227）に自動応答します。
NINA 等で「デバイスを検索」を実行すると AiBridge が自動検出されます。

検出時の表示名: `Ai-` + Location Name（例: `Ai-Observatory`）

### 13-2. 対応デバイス

| デバイスタイプ | デバイス番号 | 内容 |
|---------------|-------------|------|
| **Telescope** | 0 | LX200 互換望遠鏡制御 |
| **ObservingConditions** | 0 | DHT22 温度・湿度 |
| **Switch** | 0 | GPIO 4チャンネル ON/OFF |

### 13-3. Telescope デバイス（主要プロパティ）

| プロパティ | 説明 |
|-----------|------|
| `RightAscension` | 現在の RA（10 進時間） |
| `Declination` | 現在の Dec（10 進度） |
| `Tracking` | 追尾状態（true/false） |
| `Slewing` | スルー中かどうか（true/false） |
| `SlewToCoordinatesAsync` | 座標指定スルー |
| `AbortSlew` | スルー中止 |
| `PulseGuide` | パルスガイド（PHD2 対応） |
| `CanPulseGuide` | true（パルスガイド対応） |

### 13-4. NINA での使用

1. NINA → **機器 → 望遠鏡 → Alpaca**
2. AiBridge が自動検出されたら選択して接続
3. 接続後、NINA から GoTo・追尾制御・パルスガイドが可能

### 13-5. 望遠鏡状態モニタリング（自動分類）

AiBridge は 3 秒ごとに座標を読み取り、移動速度から状態を自動判定します。

| 状態 | 速度比（恒星時速度比） | 説明 |
|------|---------------------|------|
| `Tracking` | × 0.4 未満 | 通常追尾中 |
| `Guiding` | × 0.4 〜 5.0 | オートガイド補正中 |
| `Slewing` | × 5.0 以上 | GoTo スルー中 |

---

# Part 5 — API 仕様

## 14. ネットワーク設定とプロトコル

### 14-1. Wi-Fi モード

AiBridge は `WIFI_AP_STA` モードで動作し、AP と STA の両機能を同時に提供します。

#### アクセスポイント（AP）モード

| 項目 | 値 |
|------|-----|
| **SSID** | `AiBridgeXXXX`（MAC 下 4 桁から自動生成） |
| **パスワード** | なし（オープンネットワーク） |
| **IP アドレス** | 192.168.4.1（固定） |

#### MAC アドレス自動 SSID 生成 *(v7.15 新機能)*

```
MAC アドレス: AA:BB:CC:DD:A1:B2
  → AP SSID: "AiBridgeA1B2"
```

- `ESP.getEfuseMac()` を使用（初期化前にゼロを返す `WiFi.softAPmacAddress()` は不使用）
- 複数台同時運用時も SSID の競合なし

#### ステーション（STA）モード

| 項目 | 値 |
|------|-----|
| **IP アドレス** | DHCP による自動割り当て |
| **設定方法** | File Manager で `aibridge_net_cfg.dat` を編集 |

### 14-2. 対応プロトコル

| プロトコル | ポート | 説明 |
|-----------|--------|------|
| LX200 TCP | 9999、9998 | 2 ポートで複数クライアント同時接続対応 |
| ASCOM Alpaca HTTP | 80（RESTful API） | NINA・Stellarium 等が直接接続 |
| Alpaca Discovery UDP | 32227 | デバイス自動検出 |

---

## 15. API 仕様

**ベース URL**: `http://<AiBridge IP>/`

---

### 15-1. OnStep ダイレクト制御 API

座標は HMS/DMS 形式（`HH:MM:SS`、`±DD:MM:SS`）を使用します。

#### GET /api/status

システム全体の状態を返します。

**レスポンス例:**
```json
{
  "version": "7.15",
  "location": "Bridge",
  "ra": "05:34:32",
  "dec": "+22:00:52",
  "tracking": true,
  "slewing": false,
  "temperature": 18.5,
  "humidity": 62.3,
  "wifi_ssid": "MyHomeNetwork",
  "wifi_ip": "192.168.3.154",
  "ap_ssid": "AiBridgeA1B2",
  "ap_ip": "192.168.4.1",
  "uptime": 3600
}
```

#### GET /api/ra / GET /api/dec

現在の赤経・赤緯をプレーンテキストで返します。

```
レスポンス: 05:34:32
```

#### GET /api/command

任意の LX200 コマンドを送信します。

| パラメータ | 型 | 必須 | 説明 |
|-----------|-----|------|------|
| `cmd` | string | ✅ | LX200 コマンド（`:` と `#` を除いた部分） |

**例:**
```
GET /api/command?cmd=GR   → :GR# を送信（RA 取得）
GET /api/command?cmd=Q    → :Q# を送信（停止）
```

**レスポンス例:**
```json
{
  "status": "success",
  "command": "GR",
  "response": "05:34:32#"
}
```

> **v7.15 変更点**: タイムアウトを 40ms → **100ms** に延長しました。

#### GET /api/goto

指定座標にスルーします。

| パラメータ | 型 | 必須 | 説明 |
|-----------|-----|------|------|
| `ra` | string | ✅ | 赤経（HH:MM:SS 形式） |
| `dec` | string | ✅ | 赤緯（±DD:MM:SS 形式） |

**例:** `GET /api/goto?ra=05:34:32&dec=+22:00:52`

**レスポンス例:**
```json
{
  "status": "success",
  "ra": "05:34:32",
  "dec": "+22:00:52"
}
```

#### GET /api/sync

現在位置を指定座標に同期します（`:CM#`）。

| パラメータ | 型 | 必須 | 説明 |
|-----------|-----|------|------|
| `ra` | string | ✅ | 赤経（HH:MM:SS 形式） |
| `dec` | string | ✅ | 赤緯（±DD:MM:SS 形式） |

#### GET /api/tracking

追尾の ON/OFF を制御します。

| パラメータ | 型 | 必須 | 説明 |
|-----------|-----|------|------|
| `state` | string | ✅ | `on` または `off` |

#### GET /api/stop

スルーを即時停止します（`:Q#`）。

**レスポンス例:** `{"status": "success"}`

#### GET /api/switch

GPIO スイッチを ON/OFF します。

| パラメータ | 型 | 必須 | 説明 |
|-----------|-----|------|------|
| `id` | integer | ✅ | スイッチ番号（0〜3） |
| `state` | string | ✅ | `on` または `off` |

**例:**
```
GET /api/switch?id=0&state=on    → GPIO27 を HIGH
GET /api/switch?id=2&state=off   → GPIO12 を LOW
```

**レスポンス例:**
```json
{"status": "success", "switch": 0, "state": "on"}
```

#### GET /api/weather

DHT22 センサーの計測値を返します。

**レスポンス例:**
```json
{"temperature": 18.5, "humidity": 62.3, "sensor": "DHT22"}
```

センサー未接続時:
```json
{"temperature": null, "humidity": null, "sensor": "N/A"}
```

---

### 15-2. 動的ダッシュボード UI API *(v7.15 新機能)*

| エンドポイント | メソッド | 機能 |
|-------------|--------|------|
| `/api/ui/upload` | POST | HTML を SPIFFS に `/index2.html` として保存 |
| `/api/ui/action` | POST | ブラウザがボタン押下を通知（`?action=xxx`） |
| `/api/ui/action` | GET | LUNA がボタン通知をポーリング取得（取得後クリア） |
| `/api/ui/state` | GET | 現在の通知状態確認（クリアしない） |

**POST /api/ui/upload パラメータ:**

| パラメータ | 型 | 説明 |
|-----------|-----|------|
| `filename` | string | 保存ファイル名（デフォルト: `index2.html`） |

**レスポンス例:**
```json
{"status": "success", "file": "/index2.html", "size": 1024}
```

**GET /api/ui/action レスポンス例:**
```json
// 通知あり
{"status": "success", "action": "goto_vega", "pending": true}
// 通知なし
{"status": "success", "action": "", "pending": false}
```

---

### 15-3. ファイル管理 API

> ⚠️ File Manager API は GPIO39 ボタンによる有効化が必要です。

| エンドポイント | メソッド | 機能 |
|-------------|--------|------|
| `/files` | GET | File Manager Web UI を表示 |
| `/list` | GET | SPIFFS のファイル一覧（JSON）を返す |
| `/upload` | POST | ファイルをアップロード（`multipart/form-data`） |
| `/delete` | DELETE | ファイルを削除（`?path=/ファイル名`） |
| `/format` | POST | SPIFFS をフォーマット（全ファイル削除） |

**GET /list レスポンス例:**
```json
[
  {"name": "/aibridge_net_cfg.dat", "size": 48},
  {"name": "/index2.html", "size": 2048}
]
```

---

### 15-4. ASCOM Alpaca デバイス API

> **注意**: 座標形式は Alpaca 標準に従い 10 進数（RA: 時間、Dec: 度）です。

#### Management API

| エンドポイント | メソッド | 機能 |
|-------------|--------|------|
| `/management/apiversions` | GET | 対応 API バージョン |
| `/management/v1/description` | GET | デバイス説明・製造者情報 |
| `/management/v1/configureddevices` | GET | 構成デバイス一覧（Telescope / ObservingConditions / Switch） |

**Discovery:** UDP ポート 32227 で自動応答

#### Telescope デバイス（`/api/v1/telescope/0/`）

| エンドポイント | メソッド | 機能 |
|-------------|--------|------|
| `connected` | GET/PUT | 接続状態 |
| `rightascension` | GET | 現在の RA（時間角） |
| `declination` | GET | 現在の Dec（度） |
| `tracking` | GET/PUT | 追尾状態 |
| `slewing` | GET | スルー状態 |
| `atpark` | GET | パーク状態 |
| `slewtocoordinatesasync` | PUT/GET | 非同期 GoTo（RA/Dec 指定） |
| `slewtocoordinates` | PUT | 同期 GoTo（完了まで待機） |
| `synctocoordinates` | PUT | 座標同期（`:CM#`） |
| `abortslew` | PUT/GET | スルー中断 |
| `canpulseguide` | GET | パルスガイド対応（true） |
| `pulseguide` | PUT | パルスガイド送信（PHD2 対応） |
| `ispulseguiding` | GET | パルスガイド中かどうか |
| `siderealtime` | GET | 地方恒星時 |
| `siteelevation` | GET/PUT | 観測地標高 |
| `sitelatitude` | GET/PUT | 観測地緯度 |
| `sitelongitude` | GET/PUT | 観測地経度 |

> `slewtocoordinatesasync` と `abortslew` は広いクライアント互換性のため PUT と GET の両方に対応。Alpaca 標準では PUT が正規。

#### ObservingConditions デバイス（`/api/v1/observingconditions/0/`）

| エンドポイント | メソッド | 機能 |
|-------------|--------|------|
| `connected` | GET/PUT | 接続状態 |
| `temperature` | GET | 気温（℃） |
| `humidity` | GET | 湿度（%） |
| `refresh` | PUT | センサー値を更新 |

> センサー未接続または読み取り失敗時は `-999.0` を返します。

#### Switch デバイス（`/api/v1/switch/0/`）

| エンドポイント | メソッド | 機能 |
|-------------|--------|------|
| `connected` | GET/PUT | 接続状態 |
| `maxswitch` | GET | スイッチ総数（4） |
| `getswitch` | GET | ON/OFF 状態取得（`?Id=0-3`） |
| `setswitch` | PUT | 状態設定（`Id`、`State`） |
| `getswitchname` | GET | スイッチ名取得 |
| `getswitchdescription` | GET | スイッチ説明取得 |

**スイッチ↔GPIO 対応表**

| Switch | GPIO |
|--------|------|
| Switch 0 | GPIO27 |
| Switch 1 | GPIO14 |
| Switch 2 | GPIO12 |
| Switch 3 | GPIO13 |

---

### 15-5. 外部 Alpaca デバイスプロキシ API

| エンドポイント | メソッド | 機能 |
|-------------|--------|------|
| `/api/external/discover` | GET | UDP ブロードキャストで全 Alpaca デバイスを検出（IP・ポート・モデル・種別） |
| `/api/external/alpaca` | GET | 任意の Alpaca デバイスの任意プロパティを中継（`target`・`device`・`property` 指定） |

---

### 15-6. エラーレスポンス形式

**独自 API 共通エラー:**
```json
{
  "status": "error",
  "message": "エラーの説明"
}
```

**Alpaca API エラー（Alpaca 仕様準拠）:**
```json
{
  "Value": null,
  "ErrorNumber": 1024,
  "ErrorMessage": "Not connected"
}
```

---

## 16. Web UI ページ一覧

| URL | 説明 |
|-----|------|
| `http://<IP>/` | AiBridge Console（メイン操作画面） |
| `http://<IP>/index2.html` | カスタムダッシュボード（GPIO33 LOW 時に自動表示） |
| `http://<IP>/files` | File Manager（GPIO39 有効化必要） |
| `http://<IP>/openapi.json` | OpenAPI 仕様書（JSON） |
| `http://<IP>/alpaca-api-spec.json` | Alpaca API 仕様書（JSON） |

---

# Part 6 — LUNA 連携・天文台統合

## 17. 動的ダッシュボード（LUNA 連携）

LUNA AI ゲートウェイと連携して、AiBridge のブラウザ画面を動的に変更できます。

### 17-1. しくみ

```
LUNA（Lua スクリプト）
    │
    ├── POST /api/ui/upload  → カスタム HTML を AiBridge SPIFFS に保存
    │
    └── GET  /api/ui/action  → ブラウザのボタン操作をポーリング取得
              ↑
        ブラウザで /index2.html を表示
        ユーザーがボタンを押す → POST /api/ui/action
```

### 17-2. GET `/api/ui/action` レスポンス例

```json
// ボタンが押された
{"status": "success", "action": "goto_vega", "pending": true}

// 通知なし
{"status": "success", "action": "", "pending": false}
```

### 17-3. LUNA Lua スクリプト例

```lua
-- AiBridge にカスタム HTML を配信する
local html = [[
<!DOCTYPE html><html><body>
<h2>Observatory Control</h2>
<button onclick="fetch('/api/ui/action?action=goto_vega',{method:'POST'})">
  ベガへ移動
</button>
</body></html>
]]
http.post("http://192.168.3.154/api/ui/upload", html, 3000)

-- ブラウザのボタン操作をポーリング
while true do
  local res = http.get("http://192.168.3.154/api/ui/action", 500)
  if res and res ~= "" then
    local data = json.parse(res)
    if data and data.action == "goto_vega" then
      -- ベガへの GoTo 処理
      serial.write(":SrXX:XX:XX#:SdXX:XX:XX#:MS#")
    end
  end
  hw.delay(500)
end
```

### 17-4. カスタム UI の表示

1. LUNA から `/api/ui/upload` で HTML を送信
2. ブラウザで `http://<AiBridge IP>/index2.html` を開く
   （または GPIO33 を LOW にして起動すれば自動表示）

---

## 18. LUNA AI ゲートウェイとの連携

### 18-1. システム構成

```
Claude Desktop
    │ MCP プロトコル
    └── LUNA（ESP32）
            │ HTTP（http.get / http.post）
            └── AiBridge（ESP32）
                    │ LX200 シリアル（9600bps）
                    └── 望遠鏡マウント
```

### 18-2. LUNA から AiBridge を制御する

LUNA の Lua スクリプトから AiBridge の REST API を呼び出せます:

```lua
-- RA/Dec を取得
local ra  = http.get("http://192.168.3.154/api/ra", 500)
local dec = http.get("http://192.168.3.154/api/dec", 500)
log.write("RA=" .. ra .. " Dec=" .. dec)

-- LX200 コマンドを送信
http.get("http://192.168.3.154/api/command?cmd=Q", 500)  -- 停止

-- GoTo 実行
http.get("http://192.168.3.154/api/goto?ra=18:36:56&dec=+38:47:01", 2000)
```

### 18-3. MCP Resource として公開

LUNA の MCP Resources 機能を使って、望遠鏡の現在位置を Claude が直接読める状態にできます:

```lua
-- /luna_resource_aibridge.lua
local ra  = http.get("http://192.168.3.154/api/ra",  500)
local dec = http.get("http://192.168.3.154/api/dec", 500)
return '{"ra":"' .. (ra or "N/A") .. '","dec":"' .. (dec or "N/A") .. '"}'
```

---

## 19. 天文台統合ビジョン（ソラミちゃんシステム）

### 19-1. コンセプト

v7.15 は、**Claude AI が天文台全体のオーケストレーターとなり、一般ユーザーに新しい天文体験を提供する**ためのプラットフォームの第一歩です。

```
【来館者の自然な問いかけ】
「今見えている星は何？」
「M31を見たい」
「今夜のおすすめ天体は？」
            ↓
【Claude AI（オーケストレーター）】
・天体情報の確認・計算
・装置の最適設定
・観測シーケンスの実行管理
・進捗・結果のレポート
            ↓
【LUNA Observatory + AiBridge】
望遠鏡・カメラ・ドーム・気象センサー等を統合制御
            ↓
【動的ダッシュボード（ブラウザ）】
リアルタイム状態表示・天体画像・インタラクション
```

### 19-2. 来館者ガイドシステムの例

**シナリオ:**

```
来館者: 「今見えている星は何ですか？」
        ↓ ブラウザのボタンから送信
Claude（LUNA 経由）:
  1. AiBridge から現在の RA/Dec を取得
  2. 恒星カタログで天体を特定
  3. その天体の情報をまとめて回答
  4. ダッシュボードに星図・説明を表示
        ↓
「おうし座のアルデバラン。赤い超巨星で...」と表示・読み上げ
```

### 19-3. 実装ロードマップ

| 段階 | 機能 | 技術 | 状態 |
|------|------|------|------|
| **v7.15** | テキスト Q&A + 望遠鏡リアルタイムダッシュボード | 動的ダッシュボード UI API | ✅ 完了 |
| **v7.16+** | 音声ナレーション出力 | OpenAI TTS MCP（Claude Desktop 側） | 📋 次期 |
| **v7.17+** | BGM・効果音による没入体験 | Audio Playback MCP + Epidemic Sound MCP | 📋 計画中 |
| **v8.0+** | 画像・動画・音声マルチメディア統合 | ファイルサーバー + LUNA マルチモーダル | 📋 将来 |

### 19-4. コンポーネント役割分担

| コンポーネント | 役割 |
|--------------|------|
| **AiBridge** | 望遠鏡位置・センサー情報を提供 |
| **LUNA Observatory** | 複数デバイス（カメラ・ドーム等）を統合 |
| **Claude AI** | 天文知識＋リアルタイムデータで回答・判断 |
| **動的ダッシュボード** | テキスト・画像・ボタンをブラウザに表示 |
| **音声出力（v7.16+）** | OpenAI TTS MCP による日本語ナレーション |

---

# Part 7 — 付録

## 20. トラブルシューティング

| 症状 | 原因 | 対処 |
|------|------|------|
| `AiBridgeXXXX` の SSID が見えない | 電源未投入・起動中 | LED 点灯を確認。USB 電源を確認 |
| AP に接続できない | パスワード設定 | v7.15 デフォルトはオープン（パスワードなし） |
| `http://192.168.4.1` が開かない | AP 接続していない | WiFi 設定で `AiBridgeXXXX` を選択 |
| STA アドレスが表示されない | WiFi 設定未完了・SSID/PW 誤り | File Manager で WiFi 設定を確認 |
| SkySafari が接続できない | IP アドレス・ポート誤り | STA アドレスとポート 9999 を確認 |
| SkySafari で RA/Dec が更新されない | 望遠鏡シリアル未接続 | 配線・ボーレート（9600bps）を確認 |
| Alpaca Discovery に表示されない | 別ネットワーク | PC と AiBridge が同じ WiFi か確認 |
| File Manager が開かない | GPIO39 ボタン未押し | ボタンを押してから File Manager を開く |
| WiFi 設定が保存されない | SPIFFS 未フォーマット | 初回のみ Format SPIFFS を実行 |
| `/api/command` のレスポンスが遅い・空 | タイムアウト不足 | v7.15 で 100ms に拡張済み。OnStep 側の応答速度を確認 |
| iOS SkySafari が頻繁に切断される | WiFi スリープ | v7.14 以降で改善済み（`WiFi.setSleep(false)`） |
| カスタム UI が表示されない | GPIO33 または index2.html 未設定 | GPIO33 を GND 接続・File Manager で HTML をアップロード |

---

## 21. 技術仕様一覧

| 項目 | 仕様 |
|------|------|
| **MCU** | ESP32-WROOM-32（Xtensa LX6 デュアルコア 240MHz） |
| **RAM** | 520KB SRAM |
| **フラッシュ** | 4MB（推奨） |
| **パーティション** | Huge APP 3MB / SPIFFS 1MB |
| **WiFi** | 802.11 b/g/n（2.4GHz）AP + STA 同時動作 |
| **AP IP** | 192.168.4.1（固定） |
| **AP SSID** | `AiBridgeXXXX`（MAC 下 4 桁・自動生成） |
| **AP パスワード** | なし（オープン） |
| **STA IP** | DHCP 自動割り当て |
| **TCP ポート** | 9999 / 9998（LX200） |
| **HTTP ポート** | 80 |
| **Alpaca Discovery** | UDP ポート 32227 |
| **シリアル（OnStep）** | UART0 または UART2（GPIO32 で選択）9600bps 8N1 |
| **シリアル（デバッグ）** | UART1（GPIO19/18）115200bps |
| **DHT センサー** | GPIO5（DHT22 / DHT11） |
| **スイッチ出力** | GPIO27 / 14 / 12 / 13（4 チャンネル） |
| **File Manager LED** | GPIO25 |
| **File Manager ボタン** | GPIO39（内部プルアップ） |
| **カスタム UI 選択** | GPIO33（LOW: index2.html、HIGH: 標準 Console） |
| **シリアル選択** | GPIO32（HIGH: UART0、LOW: UART2） |
| **動作電圧** | 3.3V ロジック / 5V USB 電源 |

---

## 22. バージョン履歴

| バージョン | 日付 | 主な変更点 |
|-----------|------|-----------|
| v7.13 | 2025-10 | ベース版 — LX200 + Alpaca + File Manager |
| v7.14 | 2026-03 | iOS TCP 安定性改善（Nagle 無効化・ソケット解放・アイドルタイムアウト・Modem-Sleep 無効化） |
| **v7.15** | **2026-04** | **MAC 自動 SSID・動的ダッシュボード UI API・/api/command タイムアウト 100ms** |

### v7.15 変更サマリー

| 変更点 | 詳細 |
|--------|------|
| AP SSID 自動生成 | MAC 下位 4 桁から `AiBridgeXXXX` を生成。ユニットごとにユニーク |
| AP パスワードデフォルト | オープン（パスワードなし）に変更。初回接続を容易に |
| 動的ダッシュボード UI API | 新規 4 エンドポイント: `/api/ui/upload`・`/api/ui/action`・`/api/ui/state` |
| `/api/command` タイムアウト | 40ms → **100ms** に延長し OnStep 応答を確実に取得 |
| 天文台統合ビジョン | LUNA Observatory 統合アーキテクチャを正式採用 |

---

## 23. 起動ログ例

AiBridge 起動時にシリアルモニター（115200bps）で以下のようなログが出力されます:

```
AiBridge v7.15 starting...
AP SSID: AiBridgeA1B2
AP IP: 192.168.4.1
Connecting to WiFi: MyHomeNetwork
WiFi connected. STA IP: 192.168.3.154
TCP server 1 started on port 9999
TCP server 2 started on port 9998
HTTP server started
Alpaca Discovery listening on UDP 32227
Ready.
```

---

*AiBridge 完全マニュアル v7.15*
*Copyright (c) 2026 Nishioka Sadahiko / 西岡定彦*
*MIT ライセンス — バイナリ配布のみ・ソースコードは非公開*
