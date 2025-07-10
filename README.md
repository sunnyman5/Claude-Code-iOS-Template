# Claude Code iOS Template

iOSアプリ開発のためのベーステンプレートです。Claude CodeとHot Reload (Inject)の完全統合済み。

## 🚀 特徴

- ✅ SwiftUI + UIKit 併用パターン
- ✅ Hot Reload (Inject) 完全設定済み
- ✅ Firebase統合準備
- ✅ タブバーカスタマイズ
- ✅ 画像選択機能
- ✅ Claude Code 最適化設定

## 📋 必要な環境

- iOS 18.5+
- Xcode 16.2+
- Swift 6.0+
- iPhone 16 Pro iOS 18.5 シミュレーター
- InjectionIII アプリ（Mac App Store）

## 🔧 セットアップ

### 1. このテンプレートを使用

```bash
# リポジトリをクローン
git clone https://github.com/YOUR_USERNAME/claude-code-ios-template.git
cd claude-code-ios-template

# 新しいプロジェクトを作成
./setup_new_project.sh MyNewApp com.yourcompany.mynewapp
```

### 2. Claude Code設定

プロジェクトルートに`CLAUDE.md`を配置済みです。詳細な設定方法は以下をご確認ください：

📄 **[CLAUDE.md - 完全設定ガイド](./CLAUDE.md)**

### 3. Hot Reload設定

詳細な手順は以下をご確認ください：

📄 **[Hot Reload完全セットアップガイド](./docs/hot-reload-setup.md)**

## 📖 ドキュメント

- [CLAUDE.md](./CLAUDE.md) - Claude Code設定ファイル
- [Hot Reload設定](./docs/hot-reload-setup.md) - 詳細セットアップ手順
- [トラブルシューティング](./docs/troubleshooting.md) - よくある問題と解決策

## 🛠️ 使用方法

1. **初回セットアップ**: `CLAUDE.md`の手順に従ってプロジェクトを設定
2. **開発開始**: Xcodeでコード編集、⌘+S保存でHot Reload
3. **ビルド・実行**: Claude Codeコマンドでシミュレーター起動

## 🎯 Claude Code + Hot Reload の開発フロー

```bash
# 1. シミュレーター起動（初回のみ）
xcrun simctl boot YOUR_SIMULATOR_ID
open -a Simulator

# 2. アプリビルド・起動（初回のみ）
xcodebuild -scheme YOUR_PROJECT -destination 'platform=iOS Simulator,id=YOUR_SIMULATOR_ID' build
xcrun simctl install YOUR_SIMULATOR_ID ./DerivedData/Build/Products/Debug-iphonesimulator/YOUR_PROJECT.app

# 3. 開発サイクル（Hot Reload有効）
# - Xcodeでコード編集
# - ⌘+S で保存
# - シミュレーターで即座に反映確認
# - 再ビルド不要！
```

## 📦 含まれるパッケージ

- **Inject**: Hot Reload機能
- **Firebase SDK**: バックエンドサービス（準備済み）
- **Google Mobile Ads**: 広告収益化（準備済み）

## 🤝 貢献

Issue報告やPull Requestをお待ちしています！

## 📄 ライセンス

MIT License

## 🔗 関連記事

- [Zenn記事: Claude CodeでiOS開発を効率化](YOUR_ZENN_ARTICLE_URL)

---

⭐ このテンプレートが役に立ったらスターをお願いします！　
