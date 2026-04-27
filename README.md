# Feel Log

> 毎日の気分を、やさしく残す Androidアプリ

1日1回、5段階の気分を色で記録する気分日記アプリです。過去日付の遡及入力、詳細気分タグ、メモ、写真、通知、カレンダー、継続日数、インサイト分析、バックアップまで揃った、個人利用向けの完成形を目指しています。

## アプリアイコン

5段階の気分色（<span style="color:#E53935">●</span> 最低 / <span style="color:#FB8C00">●</span> 悪い / <span style="color:#42A5F5">●</span> 普通 / <span style="color:#9CCC65">●</span> 良い / <span style="color:#43A047">●</span> 最高）を横一列に並べたデザイン。セージグリーンの背景で落ち着いた印象に。

ベクタードローアブルで実装してあるので、どの解像度でも鮮明に表示されます。Android 13+ ではモノクロ版（テーマアイコン）も自動で使われます。

## 主な機能

### 記録
- **5段階気分**: 色で直感的に選択、選択時に拡大アニメとHaptic feedback
- **詳細気分タグ**: 13個のビルトイン（嬉しい/ワクワク/感謝/リラックス/満足/疲れた/わからない/退屈/心配/怒り/ストレスがある/悲しい/必死）+ カスタムタグの追加・削除
- **ひとことメモ**: 自由記述
- **写真**: Android Photo Pickerで複数枚添付
- **過去日遡及入力**: DatePickerで任意の過去日の記録を追加・編集

### 見る
- **ホーム**: 今日のヒーローカード（未記録/記録済み自動切替）、継続日数カード（炎のパルスアニメ）、月区切りで過去エントリー一覧
- **カレンダー**: 気分色で塗られたヒートマップ、今日は枠線強調、未記録日タップで新規記録、記録済み日タップで詳細
- **詳細画面**: 気分色のグラデーションヘッダー、タグ/メモ/写真のセクション表示、写真はタップで全画面ページャー
- **分析**: 自動インサイト（平均気分/記録日数/最多気分/頻出タグ/最高の曜日/最低の曜日）、90日ミニヒートマップ、トレンド折線、5段階分布、曜日別平均、タグ分布

### 続ける
- **継続日数**: 今日分の猶予付き（今日未記録でも昨日までの連続を表示）
- **未入力表示** 未入力分の日数表示、そこから入力もできる
- **リマインダー通知**: 設定時刻に通知。すでに記録済みの日は通知しない
- **通知クイックアクション**: 通知内の5ボタンから気分を1タップで記録（アプリを開かず完結）

### データ管理
- **JSONエクスポート**: Storage Access Framework経由で任意の場所に保存
- **JSONインポート**: 既存データとマージ（日付重複はスキップ、タグは名前照合）
- **daylioインポート** daylioアプリからのインポート互換
- **カスタムタグ管理**: 個別削除
- **全データ削除**: 確認ダイアログつき

### その他
- **オンボーディング**: 初回起動時の3ページ紹介
- **テーマ切替**: 自動/ライト/ダーク
- **Edge-to-edge表示**: ステータス/ナビゲーションバー透過
- **画面遷移アニメ**: 横スライド + フェード

## 技術スタック

| 項目 | 内容 |
|---|---|
| 言語 | Kotlin 1.9.0 |
| ビルド | Gradle 8.9 / AGP 8.7.0 |
| 最小SDK | 31 (Android 12) |
| ターゲットSDK | 34 (Android 14) |
| UI | Jetpack Compose + Material3 |
| アーキテクチャ | Single Activity + MVVM (ViewModel + Flow) |
| 永続化 | Room 2.6.1 (KSP) |
| 設定 | DataStore Preferences 1.1.1 |
| ナビゲーション | Navigation Compose 2.7.7 |
| チャート | Vico 1.14.0 (compose-m3) |
| 画像 | Coil 2.6.0, Android Photo Picker |
| 通知 | AlarmManager (`setAlarmClock`) + WorkManager |
| バックアップ | SAF + org.json |

**依存関係を意図的に入れていないもの**: Hilt（サービスロケータで代替）、kapt（KSPのみ）、ダイナミックカラー（独自ブランドパレット優先）。

## 開発環境

- Android Studio (最新推奨)
- JDK 17 相当（Android Studio同梱のJBRを使用）
- Android SDK 35

`gradle.properties` の `org.gradle.java.home` は Android Studio の JBR パスを直指定しています。環境が異なる場合は書き換えてください。

## ビルドと実行

```bash
# デバッグビルド
./gradlew :app:assembleDebug

# 実機/エミュレータにインストール
./gradlew :app:installDebug

# テスト実行
./gradlew :app:testDebugUnitTest
```

Android Studio で `C:\Users\sato\AIprojects\feel-log-app` を開けば Gradle Sync 後すぐに Run できます。

## プロジェクト構成

```
app/src/main/java/com/example/feellogapp/
├─ FeelLogApp.kt              Application, サービスロケータ
├─ MainActivity.kt            NavHost ホスト, テーマ適用
├─ data/
│  ├─ db/                     Room (AppDatabase, DAO, Converters, Relation)
│  ├─ entity/                 Room entities (MoodEntry, MoodTag, CrossRef, Photo)
│  ├─ model/                  ドメインモデル (MoodLevel enum, MoodEntry, MoodTag)
│  ├─ prefs/                  DataStore (通知, テーマ, onboarding_seen)
│  └─ repo/                   Repository (Mood, Streak, Backup, Insights)
├─ ui/
│  ├─ theme/                  カラーパレット / タイポグラフィ / Shape / Spacing
│  ├─ common/                 MoodDot, MoodLevelPicker, MoodLegend, SectionHeader,
│  │                          InsightCard, PhotoThumbGrid, MoodChip
│  ├─ nav/                    FeelLogNav (NavHost + ボトムナビ + 遷移アニメ)
│  ├─ home/                   HomeListScreen + ViewModel
│  ├─ edit/                   EditEntryScreen + ViewModel
│  ├─ detail/                 DayDetailScreen + ViewModel (新規)
│  ├─ stats/                  StatsScreen + ViewModel (4種チャート + インサイト)
│  ├─ calendar/               CalendarScreen + ViewModel (ヒートマップ)
│  ├─ settings/               SettingsScreen + ViewModel (エクスポート/インポート)
│  └─ onboarding/             OnboardingScreen (3ページ)
└─ notify/
   ├─ NotificationChannels.kt
   ├─ ReminderScheduler.kt    AlarmManager.setAlarmClock
   ├─ ReminderReceiver.kt     通知発火 + クイックアクション付き
   ├─ QuickMoodReceiver.kt    通知からの即ログ処理
   └─ BootReceiver.kt         BOOT_COMPLETED で再スケジュール
```

## データモデル

- **MoodEntryEntity**: 1日1件（PK: `LocalDate.toEpochDay()` の Long）。同日再入力は編集になる
- **MoodTagEntity**: ビルトイン13件 + カスタムタグ。`isBuiltin=true` は削除不可
- **EntryTagCrossRef**: 多対多、FKカスケード削除
- **EntryPhotoEntity**: `content://` URI を保持、`takePersistableUriPermission` で永続化
- **継続日数**: `StreakCalculator.compute()` の純粋関数（キャッシュなし、μs単位）

## 通知の仕組み

デイリー通知は `AlarmManager.setAlarmClock` を採用（Dozeで遅延しない正確発火）。`ReminderReceiver` 側で当日エントリーの有無をチェックし、すでに記録済みなら通知をスキップ。通知の5つのアクションボタンから気分を即記録可能（`QuickMoodReceiver`）。`BootReceiver` が再起動後の再スケジュールを担います。

## バックアップ形式

エクスポートJSON例:

```json
{
  "version": 1,
  "exportedAt": 1710000000000,
  "tags": [
    {"id": 1, "name": "嬉しい", "isBuiltin": true, "sortOrder": 0},
    ...
  ],
  "entries": [
    {
      "date": 20200,
      "moodLevel": 4,
      "memo": "散歩が気持ちよかった",
      "createdAt": 1710000000000,
      "updatedAt": 1710000000000,
      "tagIds": [1, 4],
      "photos": ["content://..."]
    }
  ]
}
```

インポート時は既存タグを名前で照合、日付が重複するエントリーはスキップ。写真のURIは端末内コンテンツへの参照なので、同じ端末へのリストアでは復元されますが、別端末へ移した場合は写真参照が切れるため元画像が必要です。

## テスト

`StreakCalculator` の単体テスト（JVM）が `app/src/test/` に入っています。

```bash
./gradlew :app:testDebugUnitTest
```

## 今後のアイデア

- ウィジェット（今日の気分 + 継続日数表示）
- ロック（生体認証/PIN）
- 気分 + タグの相関インサイトの拡充
- 週次/月次サマリの通知
- 写真のアプリ内コピー（オプション）

## ライセンス

個人利用を想定した非公開プロジェクト。現時点でライセンスは設定していません。
