# Claude Code 設定ファイル

このファイルはClaude Codeがプロジェクトの構造と設定を理解するためのものです。

## プロジェクト概要（README.mdより）

このプロジェクトは新しいiOSアプリ開発のベーステンプレートです。

### 含まれる機能
- SwiftUI + UIKit 併用の実装パターン
- タブバーのカスタマイズ（色設定）
- 画像選択機能
- Hot Reload (Inject)
- Firebase統合準備
- .gitattributes (Swift言語認識)

### 新プロジェクト作成方法
1. `./setup_new_project.sh MyNewApp com.yourcompany.mynewapp`
2. Xcodeで開く
3. 開発開始

## プロジェクト構造

```
CLAUDE_TEMPLATE/
├── CLAUDE_TEMPLATE/
│   ├── CLAUDE_TEMPLATEApp.swift  # メインアプリファイル
│   ├── ContentView.swift         # メインビュー
│   ├── Assets.xcassets/         # アセット
│   └── Preview Content/         # プレビュー用アセット
├── CLAUDE_TEMPLATE.xcodeproj/   # Xcodeプロジェクト
├── CLAUDE.md                   # Claude Code設定ファイル
└── DerivedData/                # ビルドデータ（自動生成）
```

## シミュレーター管理

### 利用可能なシミュレーター一覧
```bash
# シミュレーター一覧表示
xcrun simctl list devices
```

### iPhone 16 Pro シミュレーター設定（iOS 18.5）
- Device ID: `YOUR_SIMULATOR_ID` (取得方法: `xcrun simctl list devices | grep "iPhone 16 Pro"`)
- iOS Version: `18.5`
- Bundle ID: `com.yourcompany.yourproject`

**注意**: 必ずiPhone 16 Pro (iOS 18.5)を使用してください。

### シミュレーター起動・停止
```bash
# シミュレーター起動
xcrun simctl boot YOUR_SIMULATOR_ID

# Simulatorアプリを開く
open -a Simulator

# シミュレーター停止
xcrun simctl shutdown YOUR_SIMULATOR_ID
```

### Hot Reload (Inject) 完全セットアップガイド

シミュレーターでアプリを起動したまま、コードの変更をリアルタイムで反映する完全な設定方法：

#### 📋 セットアップ前提条件
- InjectionIII アプリがインストール済み
- Xcode 16.2+ 
- iPhone 16 Pro iOS 18.5 シミュレーター

#### 🔧 Step 1: Claude Code での基本設定

**1-1. Inject パッケージ追加 (Claude Code)**
```bash
# プロジェクトファイルでInjectパッケージが追加済みであることを確認
# 既に https://github.com/krzysztofzablocki/Inject.git @ 1.5.2 が設定済み
```

**1-2. アプリファイルの設定確認 (Claude Code)**
```swift
// CLAUDE_TEMPLATEApp.swift - 既に設定済み
import SwiftUI
#if DEBUG
import Inject
#endif

@main
struct CLAUDE_TEMPLATEApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
            #if DEBUG
                .enableInjection()
            #endif
        }
    }
}
```

**1-3. 各ViewファイルのInject設定確認 (Claude Code)**
```swift
// 全てのViewファイルに以下が設定済み
import SwiftUI
#if DEBUG
import Inject
#endif

struct FeatureView: View {
    #if DEBUG
    @ObserveInjection var inject
    #endif
    
    var body: some View {
        // ビューコンテンツ
    }
}
```

#### 🛠️ Step 2: Xcode での必須設定

**⚠️ 重要: この設定はClaude Codeが自動的にしてくれます**

**2-1. Other Linker Flags の設定**
1. Xcodeでプロジェクトを開く
2. プロジェクトナビゲーターで `CLAUDE_TEMPLATE` プロジェクトを選択
3. `CLAUDE_TEMPLATE` ターゲットを選択
4. `Build Settings` タブを開く
5. `All` と `Combined` を選択
6. 検索バーで「Other Linker Flags」を検索
7. `Debug` 行で以下を追加:
   - `-Xlinker`
   - `-interposable`

**または Claude Code で自動設定 (推奨)**
```bash
# Claude Code がプロジェクトファイルを直接編集済み
# project.pbxproj の Debug セクションに以下が追加済み:
OTHER_LDFLAGS = (
    "-Xlinker",
    "-interposable",
);
```

#### 🚀 Step 3: 初回ビルドとアプリ起動 (Claude Code)

```bash
# 1. シミュレーター起動
xcrun simctl shutdown all
xcrun simctl boot YOUR_SIMULATOR_ID
open -a Simulator

# 2. クリーンビルド（リンカーフラグ反映のため）
xcodebuild -scheme CLAUDE_TEMPLATE -destination 'platform=iOS Simulator,id=YOUR_SIMULATOR_ID' clean build

# 3. アプリインストール・起動
xcrun simctl install YOUR_SIMULATOR_ID "/Users/○○○/Library/Developer/Xcode/DerivedData/CLAUDE_TEMPLATE-*/Build/Products/Debug-iphonesimulator/CLAUDE_TEMPLATE.app"
xcrun simctl launch YOUR_SIMULATOR_ID com.yourcompany.yourproject
```

#### 🔄 Step 4: Hot Reload 動作確認

**4-1. Xcode でプロジェクトを開く**
```bash
# Claude Code から Xcode を起動
open "/Users/○○○/CLAUDE_TEMPLATE/CLAUDE_TEMPLATE.xcodeproj"
```

**4-2. ファイル編集とテスト**
1. XcodeでFeatureView.swiftを開く
2. テキストを変更（例: "Hello, World!" → "ホットリロード成功!"）
3. ⌘+S で保存
4. シミュレーターで即座に変更が反映されることを確認

#### 📊 Hot Reload 成功時のログ例
```
💉 InjectionIII connected /Users/○○○/CLAUDE_TEMPLATE/CLAUDE_TEMPLATE.xcodeproj
💉 Watching files under the directory /Users/○○○/CLAUDE_TEMPLATE
💉 Compiling /Users/○○○/CLAUDE_TEMPLATE/CLAUDE_TEMPLATE/FeatureView.swift
💉 Loading .dylib ...
💉 Interposed 6 function references.
💉 Injected type #1 'CLAUDE_TEMPLATE.FeatureView'
```

#### 🚨 トラブルシューティング

**問題1: "Injection may have failed" 警告**
```
💉 ⚠️ Injection may have failed. Have you added -Xlinker -interposable
```
**解決策**: Step 2-1 の Other Linker Flags 設定を確認

**問題2: ビルドエラー "Cannot find 'ObserveInjection'"**
**解決策**: Inject パッケージが正しく追加されているかXcodeで確認

**問題3: 変更が反映されない**
**解決策**: 
1. アプリを完全に停止
2. クリーンビルド実行
3. 新しくインストール・起動

#### 💡 開発フロー（Hot Reload有効後）

```bash
# 1. 初回のみ：完全セットアップ（上記Step 1-4実行）

# 2. 日常の開発サイクル
# ⭐ シミュレーター起動したまま開発可能！
# - Xcodeでコード編集
# - ⌘+S で保存
# - シミュレーターで即座に確認
# - 再ビルド・再インストール不要！
```

#### 📝 注意事項
- **Debug ビルドでのみ**動作（Release では無効）
- **新しいファイル追加時**は再ビルドが必要
- **構造的変更**（新しいプロパティ追加等）は再ビルドが必要
- **シミュレーターは起動したまま**で開発継続可能
- **InjectionIII アプリ**は別途Mac App Storeからインストール必要

## ビルド・実行コマンド

### iPhone 16 Pro (iOS 18.5) シミュレーター用
```bash
# ビルド
xcodebuild -project CLAUDE_TEMPLATE.xcodeproj -scheme CLAUDE_TEMPLATE -destination 'platform=iOS Simulator,id=YOUR_SIMULATOR_ID' -derivedDataPath ./DerivedData build

# インストール・起動
xcrun simctl install YOUR_SIMULATOR_ID ./DerivedData/Build/Products/Debug-iphonesimulator/CLAUDE_TEMPLATE.app
xcrun simctl launch YOUR_SIMULATOR_ID com.yourcompany.yourproject

# アプリ停止
xcrun simctl terminate YOUR_SIMULATOR_ID com.yourcompany.yourproject
```

### 一連の開発フロー（iOS 18.5必須）
```bash
# 1. シミュレーター起動（iPhone 16 Pro iOS 18.5）
xcrun simctl boot YOUR_SIMULATOR_ID
open -a Simulator

# 2. ビルド
xcodebuild -project CLAUDE_TEMPLATE.xcodeproj -scheme CLAUDE_TEMPLATE -destination 'platform=iOS Simulator,id=YOUR_SIMULATOR_ID' -derivedDataPath ./DerivedData build

# 3. インストール・起動
xcrun simctl install YOUR_SIMULATOR_ID ./DerivedData/Build/Products/Debug-iphonesimulator/CLAUDE_TEMPLATE.app
xcrun simctl launch YOUR_SIMULATOR_ID com.yourcompany.yourproject

# 4. アプリ停止
xcrun simctl terminate YOUR_SIMULATOR_ID com.yourcompany.yourproject
```


## 依存関係

- iOS 18.5+ (必須: iPhone 16 Pro iOS 18.5シミュレーターを使用)
- Xcode 16.2+
- Swift 6.0+
- SwiftUI
- UIKit
- Swift Package Manager

### パッケージ管理

このプロジェクトでは**Swift Package Manager (SPM)**を使用して依存関係を管理します。

#### パッケージの追加方法

**⚠️ 重要: パッケージの追加は必ずXcodeで行ってください**

Claude Code は既存パッケージの設定や使用コードの実装は可能ですが、**新しいパッケージの追加はできません**。

1. **Xcodeでの追加（GUI）** - 🔴 **必須手順**
   - File → Add Package Dependencies...
   - パッケージURLを入力
   - バージョンを選択して追加
   - **Claude Code ではこの操作は不可能**

2. **Package.swiftでの管理**
   ```swift
   // Package.swift
   let package = Package(
       name: "CLAUDE_TEMPLATE",
       dependencies: [
           .package(url: "https://github.com/firebase/firebase-ios-sdk.git", from: "10.0.0"),
           .package(url: "https://github.com/krzysztofzablocki/Inject.git", from: "1.0.0"),
           .package(url: "https://github.com/googleads/swift-package-manager-google-mobile-ads.git", from: "11.0.0")
       ]
   )
   ```

3. **Claude Code での対応範囲**
   ```swift
   // ✅ Claude Code で可能
   #if DEBUG
   import Inject  // パッケージ使用コードの追加
   #endif
   
   // ✅ 設定コードの実装
   @ObserveInjection var inject
   
   // ✅ project.pbxproj の編集（リンカーフラグ等）
   OTHER_LDFLAGS = ("-Xlinker", "-interposable");
   ```

4. **コマンドラインでの管理**
   ```bash
   # パッケージの解決
   swift package resolve
   
   # パッケージの更新
   swift package update
   
   # パッケージの表示
   swift package show-dependencies
   ```

#### 🔄 パッケージ追加の推奨ワークフロー

**Xcode + Claude Code の組み合わせ**で効率的にパッケージを管理：

1. **Xcode**: パッケージの追加・削除
   - GUI操作でパッケージ依存関係を管理
   - Swift Package Manager の解決とダウンロード
   
2. **Claude Code**: 使用コードの実装・設定・ビルド設定
   - import文の追加
   - パッケージ使用コードの実装
   - Build Settings の調整
   - project.pbxproj の直接編集

#### ⚠️ Claude Code の制限事項

**できないこと:**
- 新しいパッケージの追加・削除
- Package Dependencies の GUI 操作
- swift package resolve の実行

**できること:**
- 既存パッケージの使用コード実装
- プロジェクト設定ファイルの編集
- パッケージ使用に必要なビルド設定

#### 推奨パッケージ
- **Firebase SDK**: バックエンドサービス
- **Inject**: Hot Reload機能
- **Google Mobile Ads (AdMob)**: 広告収益化
- **SwiftUI Navigation**: 高度なナビゲーション
- **Alamofire**: ネットワーキング（必要に応じて）

## カスタマイズガイド

### SwiftUIアプリの基本構造
`CLAUDE_TEMPLATEApp.swift`でアプリの基本設定を行い、`ContentView.swift`でメインビューを実装：
```swift
// CLAUDE_TEMPLATEApp.swift
import SwiftUI

@main
struct CLAUDE_TEMPLATEApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

### ContentViewのカスタマイズ
`ContentView.swift`で画面の内容を変更：
```swift
// ContentView.swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        VStack {
            Image(systemName: "globe")
                .imageScale(.large)
                .foregroundStyle(.tint)
            Text("Hello, world!")
        }
        .padding()
    }
}
```
