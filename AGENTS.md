<!-- BEGIN:nextjs-agent-rules -->
# This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` before writing any code. Heed deprecation notices.
<!-- END:nextjs-agent-rules -->

# Karmagraph

## プロジェクト概要

Karmagraph は、コミュニティ内の「貸し借りポイント」を記録・可視化するWebアプリです。

詳しくは [docs/機能要件.md](./docs/機能要件.md) を参照してください。

## 要件・仕様の確認

実装前および設計変更時は必ず `/docs` を参照してください。

- 要件定義
- 画面仕様
- API仕様
- データ構造
- ユースケース
- デザインガイドライン

などのプロジェクト仕様は `/docs` を正とします。

## 開発方針

### ライブラリの利用

必要なライブラリは積極的に導入して構いません。

以下を優先してください。

- 十分にメンテナンスされている
- 広く利用されている
- TypeScript 対応
- Next.js と相性が良い

既存ライブラリで解決できる機能については、自作実装を避けてください。

例:

- フォーム → react-hook-form
- バリデーション → zod
- 日付処理 → date-fns
- 状態管理 → Zustand
- チャート → Recharts
- アニメーション → Motion

など

### shadcn/ui

UI コンポーネントは積極的に利用してください。

必要なコンポーネントは追加して構いません。

例:

```bash
pnpm dlx shadcn@latest add button
pnpm dlx shadcn@latest add dialog
pnpm dlx shadcn@latest add form
```

独自実装よりも shadcn/ui を優先してください。

## ディレクトリ構成

主要ディレクトリ:

```text
app/          # ルーティングおよびページ実装
components/   # 再利用可能な UI コンポーネント
constants/    # 定数管理
docs/         # プロジェクト仕様書
hooks/        # カスタムフック
lib/          # アプリケーション共通ロジック
mocks/        # モックデータ・モックサーバー
public/       # 静的ファイル
scripts/      # 開発用スクリプト
services/     # API通信や外部サービスとの連携
tests/        # テストコード
types/        # 型定義
utils/        # 副作用を持たないユーティリティ関数
```

## コンポーネント設計

適切な粒度で分割してください。

以下は避けてください。

- 巨大な page.tsx
- 巨大な component.tsx
- 1ファイルに複数責務が混在する実装

目安:

- 200〜300行を超える場合は分割を検討
- UI とロジックは可能な限り分離
- 再利用可能な処理は hooks または lib に切り出す

ただし過剰な抽象化は避けてください。

## コメント

コメントは基本的に日本語で記述してください。

複雑な実装の場合には、コードの意図や背景を説明するコメントを追加してください。
また、関数やクラスには JSDoc コメントを付与してください。

## コーディング規約

- TypeScript を使用する
- any の使用は極力避ける
- 型安全性を優先する
- Server Component を基本とする
- Client Component は必要時のみ使用する
- 型定義を明示する
- マジックナンバーを避ける
- ハードコード文字列を避ける

## 設計に関する注意点

主に、`services/` 下に置くプログラムは、以下の設計原則を意識してください。

- 単一の責務を持つサービスクラスを作成し、独立した関数を避ける
- クラスはコンストラクタで依存性を注入する。依存性以外の状態は持たない。
- クラスのメソッドはできる限り純粋関数であることを目指す。
- 以下のような副作用を持つ処理は専用クラスに分離して副作用を局所化する。
  - HTTP通信
  - DBアクセス
  - ファイル操作
  - 外部API呼び出し
- 設定値の注入は `utils/create-config.ts` の機能を利用する。

例:

```ts
import { createConfig, type DefaultConfig } from "@/utils/create-config";

export interface ExampleServiceConfig {
    configValue1: string;
    configValue2: number;
    configValue3?: "foo" | "bar";
}

export class ExampleService {
  // Optional config with default values
  private static readonly DEFAULTS: DefaultConfig<ExampleServiceConfig> = {
    configValue3: "foo"
  };

  private readonly config: Required<ExampleServiceConfig>;

  constructor(
    config: ExampleServiceConfig,
    private readonly dependency1: Dependency1,
    private readonly dependency2: Dependency2,
  ) {
    this.config = createConfig(config, ExampleService.DEFAULTS);
  }

  performAction(input: InputType): OutputType {
  
  }

  private helperMethod(data: DataType): ResultType {
  
  }
}
```

## 実装完了時の確認事項

タスク完了時は必ず以下を実行してください。

```bash
pnpm check

pnpm typecheck
```

両方が成功することを確認してください。

エラーが残った状態で完了扱いにしないでください。

## 品質基準

実装時は以下を意識してください。

- 可読性
- 保守性
- 型安全性
- 再利用性
- パフォーマンス

短期的な実装よりも長期的な保守性を優先してください。
