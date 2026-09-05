# uptime-kuma-docker

[Uptime Kuma](https://github.com/louislam/uptime-kuma) を Docker Compose で動かすための宣言。

```sh
docker compose pull
docker compose up -d
```

Kuma は `http://<host>:3001/` で待ち受ける（前段の reverse proxy を想定）。
SQLite の実体は `./data/`（git 管理外）。

## ⚠ タグは固定する

`image:` は**必ず具体的な版で固定する**。

- `:latest` は上流が **v1 に据え置いている**。`latest` を指していると、
  pull しているのに版が動かない状態に気づけない
- `:2` のような動くタグにすると、宣言を変えていないのにマイナーが上がる

**版を上げるのは、このリポジトリの変更として行う。**

## ⚠⚠ v1 → v2 の移行

v2 の初回起動は、既存の SQLite を新しい集計テーブルへ**自動マイグレーション**する。

- **中断厳禁。**中断したら `data/` のバックアップから戻してやり直し
- **ダウングレード手順は無い**（戻す ＝ バックアップから復元）
- **移行中は監視が止まる**（Web UI は進捗ページを返す）。所要時間は heartbeat の
  行数に比例するので、事前に `VACUUM` してから始めると縮む
- `rootless` イメージは使わない（v1 からの移行で問題が出ると上流が明記）
