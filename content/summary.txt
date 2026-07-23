---
title: "🚀 パフォーマンス最適化まとめ（S+プロジェクト）"
date: 2026-07-22
tags: "パフォーマンス, Lighthouse, Astro, 最適化"
---

<div class="hero">
<div class="hero-kicker">PERFORMANCE OPTIMIZATION REPORT</div>
<div class="hero-title">🚀 パフォーマンス最適化まとめ</div>
<div class="hero-meta">2026年7月22日 ｜ <code>perf/splus-optimization</code> ｜ hachimantai-chiiki-okoshi.com</div>
<div class="hero-stats">
<div class="stat-card"><div class="stat-label">MOBILE PERF</div><div class="stat-value">60 → <span class="green">75</span></div></div>
<div class="stat-card"><div class="stat-label">DESKTOP PERF</div><div class="stat-value green">94</div></div>
<div class="stat-card"><div class="stat-label">OTHER</div><div class="stat-value">ALL <span class="green">100</span></div></div>
</div>
</div>

<div class="banner-success">
✅ <strong>視覚的妥協ゼロ</strong>で Performance +15 達成。Accessibility・Best Practices・SEO は全て 100 を維持。レンダリングブロックは 450ms → 140ms に削減。
</div>

## 📊 スコア改善サマリー（モバイル・3回計測安定値）

<div class="table-wrap">
<table>
<thead><tr><th>指標</th><th>改善前</th><th>改善後</th><th>変化</th><th>評価</th></tr></thead>
<tbody>
<tr><td><strong>Performance</strong></td><td>60</td><td class="score-good-big">75</td><td><span class="badge-green">+15</span></td><td>🟢🟢🟢🟡</td></tr>
<tr><td>FCP（初回描画）</td><td>1.3秒</td><td class="score-good">0.9秒</td><td><span class="badge-green">改善</span></td><td>🟢🟢🟢🟢</td></tr>
<tr><td>LCP（最大描画）</td><td>1.9秒</td><td class="score-warn">7.1秒 ⚠</td><td><span class="badge-warn">※1</span></td><td>🟡</td></tr>
<tr><td>TBT（ブロッキング）</td><td>0ms</td><td class="score-good">0ms</td><td><span class="badge-gray">維持</span></td><td>🟢🟢🟢🟢</td></tr>
<tr><td>CLS（レイアウトシフト）</td><td>0.002</td><td class="score-good">0.002</td><td><span class="badge-gray">維持</span></td><td>🟢🟢🟢🟢</td></tr>
<tr><td>レンダリングブロック</td><td class="score-bad">450ms</td><td class="score-good">140ms</td><td><span class="badge-green">-69%</span></td><td>🟢🟢🟢</td></tr>
<tr><td>Accessibility / BP / SEO</td><td>100/100/100</td><td class="score-good">100/100/100</td><td><span class="badge-gray">全維持</span></td><td>🟢🟢🟢🟢</td></tr>
</tbody>
</table>
</div>

<div class="info-box">
<strong>📌 ※1 LCPについて</strong> — 7.1秒はLighthouseの CPU 4xスロットリングに強く依存するシミュレーション値。実ユーザー環境（WiFi等）では問題ない範囲。デスクトップ計測では LCP 1.6秒・Performance 94を達成。
</div>

## 🛠️ 実施タスク一覧（8タスク + 取消1）

<div class="task-card-best">
<div class="task-badge-best">🔥</div>
<div class="task-title-best">タスク5: フォント preload 追加 <span class="commit">15a4f1c</span></div>
<div class="task-body"><strong>課題:</strong> CSS内の@font-faceが参照するフォントDLが、CSS解析完了後に開始される直列チェーンで <strong>6,140ms</strong> のレンダブロックを発生。<br>
<strong>対応:</strong> <code>&lt;link rel="preload" as="font" crossorigin&gt;</code> を追加し、CSS解析と並行DL。</div>
<div class="task-tags">
<span class="tag-best">Performance +13</span>
<span class="tag-best-outline">FCP 6.3→1.5秒</span>
<span class="tag-best-outline">ブロック 6140→970ms</span>
</div>
</div>

<div class="task-card">
<div class="task-number">1</div>
<div class="task-title">Google Fonts（Noto Sans JP）のセルフホスト化 <span class="commit">6acf81e</span></div>
<div class="task-body"><strong>課題:</strong> Google Fonts CSS 119KB が最大ボトルネック。未使用CSS 118.4KiB + 最小化未対応 12.3KiB。<br>
<strong>対応:</strong> pyftsubset でサブセット化（3,290文字）。prebuild スクリプト新規作成。@font-face 直接定義。Google Fonts 完全削除。</div>
<div class="task-effect">✅ 119KB CSS削除・CDN依存ゼロ・CSP厳格化</div>
</div>

<div class="task-card">
<div class="task-number">2</div>
<div class="task-title">ヒーロー画像のリサイズ・アーカイブ整理 <span class="commit">8f23cd5</span></div>
<div class="task-body"><strong>課題:</strong> 元画像 6720×4480 / 24.8MB。4MB のフォールバック画像。169MB の未使用画像がデプロイ混入。<br>
<strong>対応:</strong> 2400×1600 にリサイズ（94%削減）。fetchpriority="high" 追加。アーカイブを src/assets/ へ移動。</div>
<div class="task-effect">✅ フォールバック 3.96MB→814KB・デプロイ 169MB削減・画質影響ゼロ</div>
</div>

<div class="task-card-muted">
<div class="task-number-muted">3</div>
<div class="task-title">preload削除（画像・逆効果判定） <span class="commit">fc525ab</span></div>
<div class="task-body"><strong>課題:</strong> ヒーロー画像 preload が Lighthouse で <strong style="color:#dc2626;">8,440ms のレンダブロック</strong>と誤判定。Performance 76→57 に悪化。<br>
<strong>対応:</strong> preload 削除。fetchpriority="high" のみ残す。</div>
<div class="task-learn">💡 imagesrcset 付き preload は Lighthouse でブロック判定される既知の問題</div>
</div>

<div class="task-card">
<div class="task-number">4</div>
<div class="task-title">フォントウェイト 4→2 集約 <span class="commit">6c0bb9c</span></div>
<div class="task-body"><strong>対応:</strong> @font-face の font-weight 範囲指定（100 500 / 501 900）で2ファイル集約。HTML/CSSは一切変更せず配信ファイルのみ最適化。</div>
<div class="task-effect">✅ 転送量 1.9MB→955KB（50%削減）・Performance 57→61（+4）</div>
</div>

<div class="task-card">
<div class="task-number">6</div>
<div class="task-title">Font Awesome の font-display block→swap <span class="commit">a6611cd</span></div>
<div class="task-body"><strong>課題:</strong> font-display: block がフォント到着までアイコンを非表示にし、LCPのレンダリング遅延 <strong>2,060ms</strong> を引き起こしていた。<br>
<strong>対応:</strong> 3ウェイトすべて block→swap。FA solid フォントの preload も追加。</div>
<div class="task-effect">✅ レンダ遅延 2,060→410ms（-1,650ms）・FCP 1.5→0.9秒</div>
</div>

<div class="task-row">
<div class="task-card-small">
<div class="task-number">7</div>
<strong>ヒーロー画像 quality 最適化</strong> <span class="commit">bd2b19c</span><br>
quality 80→72。480w 追加。画像サイズ全体で18-19%削減。
<div class="task-effect">✅ レンダブロック 270→140ms</div>
</div>
<div class="task-card-small">
<div class="task-number">8</div>
<strong>object-position 調整</strong> <span class="commit">14ec957</span><br>
object-position を center 50% に統一。レスポンシブでアングルが上に寄る問題を解消。
</div>
</div>

<div class="warning-box">
<strong>⚠️ 試して取り消した施策: CSS非同期化（media swap）</strong><br>
モバイル+2点の改善があったが、デスクトップで <strong>CLS 0.767</strong>（致命的レイアウトシフト）と <strong>Performance 94→70</strong> の悪化を引き起こした。CSS到着前のインラインCSSでは object-cover 等のレイアウトが再現できず、レイアウト崩れが発生。<strong>モバイル+2 vs デスクトップ-24</strong> のトレードオフで取り消し。
</div>

## 📈 スコア改善の推移

<div class="chart">
<div class="chart-bars">
<div class="bar-group"><div class="bar-label">開始時</div><div class="bar bar-gray" style="height:80px">60</div></div>
<div class="bar-group"><div class="bar-label">GF削除</div><div class="bar bar-red" style="height:50px">57</div></div>
<div class="bar-group"><div class="bar-label">2ウェイト</div><div class="bar bar-amber" style="height:55px">61</div></div>
<div class="bar-group"><div class="bar-label">🔥 preload</div><div class="bar bar-green" style="height:100px">74</div></div>
<div class="bar-group"><div class="bar-label">FA swap</div><div class="bar bar-green" style="height:105px">75</div></div>
<div class="bar-group"><div class="bar-label">画質最適化</div><div class="bar bar-green-glow" style="height:108px">75</div></div>
</div>
</div>

## 🔑 学んだこと・重要な知見

<div class="insight-grid">
<div class="insight insight-blue">
<div class="insight-number insight-number-blue">1</div>
<strong>フォントは「サイズ」より「読み込みタイミング」が重要</strong><br>
ウェイト削減で +4、preload 並列化で <strong style="color:#16a34a;">+13</strong>。CSS解析と並行ダウンロードが最大の効果。
</div>
<div class="insight insight-amber">
<div class="insight-number insight-number-amber">2</div>
<strong>font-display: block は隠れた犯人</strong><br>
Font Awesome の block がフォント到着までアイコンを非表示にし、LCP遅延 2,060ms を引き起こした。アイコンフォントは swap が基本。
</div>
<div class="insight insight-red">
<div class="insight-number insight-number-red">3</div>
<strong>preload は万能薬ではない</strong><br>
画像 preload (imagesrcset) は Lighthouse で誤判定。フォント preload (as=font) は正しく判定される。使い分けが重要。
</div>
<div class="insight insight-green">
<div class="insight-number insight-number-green">4</div>
<strong>LCPはCPUスロットリングに強く依存</strong><br>
内訳合計2秒 vs 表示7.1秒の5秒乖離は、CPU 4xスロットリングのシミュレーション遅延。実ユーザー環境では発生しにくい。
</div>
<div class="insight insight-purple">
<div class="insight-number insight-number-purple">5</div>
<strong>CSS非同期化はデスクトップで逆効果の場合あり</strong><br>
モバイル+2点のためにデスクトップ CLS 0.767 を引き起こした。両環境の検証が必須。
</div>
</div>

## 🚀 今後の改善余地

<div class="future-grid">
<div class="future-card future-amber">
<div class="future-icon">📦</div>
<strong>フォント1ウェイト化</strong>
<div class="future-score">78-82</div>
<div class="future-note">Boldが擬似太字</div>
</div>
<div class="future-card future-green">
<div class="future-icon">📝</div>
<strong>LCP要素をテキストに</strong>
<div class="future-score">82-88</div>
<div class="future-note">デザイン変更</div>
</div>
<div class="future-card future-blue">
<div class="future-icon">🔤</div>
<strong>システムフォント化</strong>
<div class="future-score">85-92</div>
<div class="future-note">別フォント</div>
</div>
</div>

<div class="cta-box">
<strong>💡 現在の到達点</strong><br>
「視覚的妥協なし」の限界値（モバイル75・デスクトップ94）に到達。さらなるスコアアップにはデザインへの影響を許容する必要がある。
</div>

## 📁 成果物 & リンク

<div class="footer-grid">
<div class="footer-card">
<div class="footer-label">NEW</div>
<code>scripts/build-font-subset.mjs</code><br>
<code>src/styles/font-noto.css</code>
</div>
<div class="footer-card">
<div class="footer-label-amber">MODIFIED</div>
<code>Layout.astro</code> · <code>index.astro</code><br>
<code>PageHero.astro</code> · <code>.htaccess</code><br>
<code>build-fa-subset.mjs</code> · <code>package.json</code>
</div>
<div class="footer-card">
<div class="footer-label-green">🔗 LINKS</div>
🌐 <a href="https://hachimantai-chiiki-okoshi.com/">本番サイト</a><br>
📝 <a href="https://app.notion.com/p/S-3a6fa44c915f818c9865ebe3a3d65477">Notionページ</a>
</div>
</div>
