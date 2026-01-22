# Phase 1 實作計畫：首頁改版 + Lead Magnet + 預約流程

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** 完成林文杰博士官網 Phase 1 改版，包含首頁視覺升級、代謝年齡測驗 Lead Magnet、以及預約諮詢流程優化。

**Architecture:** 維持 Astro 靜態網站架構，新增可複用元件（SocialProof、VideoGallery、QuizCTA）。測驗系統使用前端狀態管理實現，結果頁面收集 Email 後導向 Surveycake 表單。

**Tech Stack:** Astro 5.x, TypeScript, CSS Variables, YouTube iframe embed

---

## Task 1: 更新全域 CSS 變數

**Files:**
- Modify: `src/styles/global.css:1-30`

**Step 1: 新增色彩與設計變數**

在 `:root` 區塊新增科學藍色與額外設計變數：

```css
:root {
	/* Warm & Health Color Palette */
	--accent: #d97706;
	/* Amber 600 - Warmth/Metabolism */
	--accent-dark: #92400e;
	/* Amber 800 */
	--accent-light: #fbbf24;
	/* Amber 400 - Hover states */
	--health-green: #15803d;
	/* Green 700 - Health/Nature */
	--health-green-light: #22c55e;
	/* Green 500 - Success states */
	--science-blue: #1e40af;
	/* Blue 800 - Scientific credibility */
	--science-blue-light: #3b82f6;
	/* Blue 500 - Data visualization */
	--black: 28, 25, 23;
	/* Warm Black */
	--gray: 120, 113, 108;
	/* Warm Gray */
	--gray-light: 245, 245, 244;
	/* Stone 100 */
	--gray-dark: 68, 64, 60;
	/* Stone 700 */
	--bg-warm: #fffbeb;
	/* Amber 50 - Very subtle warm background */
	--bg-science: #eff6ff;
	/* Blue 50 - Science section background */
	--box-shadow: 0 2px 6px rgba(40, 40, 40, 0.1);
	--box-shadow-hover: 0 8px 16px rgba(40, 40, 40, 0.15);
	--transition-fast: 0.2s ease;
	--transition-medium: 0.3s ease;
	--border-radius: 8px;
}
```

**Step 2: 執行開發伺服器確認無錯誤**

Run: `cd /Users/vista/Documents/My_AI_Projects/august-official-site && npm run dev`
Expected: 編譯成功，無 CSS 錯誤

**Step 3: Commit**

```bash
git add src/styles/global.css
git commit -m "style: add new color variables and design tokens"
```

---

## Task 2: 建立 SocialProof 元件

**Files:**
- Create: `src/components/SocialProof.astro`

**Step 1: 建立元件檔案**

```astro
---
interface Props {
  headline?: string;
}

const { headline = "已協助 50+ 位學員改善代謝健康" } = Astro.props;

const stats = [
  { value: "8.5", unit: "kg", label: "平均減重", note: "90天內" },
  { value: "85", unit: "%", label: "脂肪肝改善率", note: "3個月追蹤" },
  { value: "98", unit: "%", label: "學員滿意度", note: "問卷調查" },
];
---

<section class="social-proof">
  <p class="headline">{headline}</p>
  <div class="stats-grid">
    {stats.map((stat) => (
      <div class="stat-card">
        <div class="stat-value">
          {stat.value}<span class="stat-unit">{stat.unit}</span>
        </div>
        <div class="stat-label">{stat.label}</div>
        <div class="stat-note">{stat.note}</div>
      </div>
    ))}
  </div>
</section>

<style>
  .social-proof {
    background: var(--bg-science);
    padding: 3rem 1rem;
    text-align: center;
    border-top: 1px solid rgba(var(--gray-light), 1);
    border-bottom: 1px solid rgba(var(--gray-light), 1);
  }

  .headline {
    font-size: 1.1rem;
    color: rgb(var(--gray));
    margin-bottom: 2rem;
    text-transform: uppercase;
    letter-spacing: 0.05em;
  }

  .stats-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
    gap: 2rem;
    max-width: 800px;
    margin: 0 auto;
  }

  .stat-card {
    padding: 1.5rem;
  }

  .stat-value {
    font-size: 3rem;
    font-weight: 700;
    color: var(--science-blue);
    line-height: 1;
  }

  .stat-unit {
    font-size: 1.5rem;
    margin-left: 0.1em;
  }

  .stat-label {
    font-size: 1.1rem;
    font-weight: 600;
    color: rgb(var(--black));
    margin-top: 0.5rem;
  }

  .stat-note {
    font-size: 0.85rem;
    color: rgb(var(--gray));
    margin-top: 0.25rem;
  }
</style>
```

**Step 2: 執行開發伺服器確認元件無錯誤**

Run: `npm run dev`
Expected: 編譯成功

**Step 3: Commit**

```bash
git add src/components/SocialProof.astro
git commit -m "feat: add SocialProof component with stats display"
```

---

## Task 3: 建立 VideoGallery 元件

**Files:**
- Create: `src/components/VideoGallery.astro`

**Step 1: 建立元件檔案**

```astro
---
interface Video {
  id: string;
  title: string;
  thumbnail?: string;
}

interface Props {
  title?: string;
  videos?: Video[];
  youtubeChannel?: string;
}

const {
  title = "阿杰博士的代謝教室",
  videos = [
    { id: "VIDEO_ID_1", title: "什麼是代謝靈活性？" },
    { id: "VIDEO_ID_2", title: "168 斷食入門指南" },
    { id: "VIDEO_ID_3", title: "Zone 2 超慢跑完整教學" },
  ],
  youtubeChannel = "https://www.youtube.com/@AugustLin123"
} = Astro.props;
---

<section class="video-gallery">
  <h2 class="section-title">{title}</h2>
  <div class="video-grid">
    {videos.map((video) => (
      <a
        href={`https://www.youtube.com/watch?v=${video.id}`}
        target="_blank"
        rel="noopener noreferrer"
        class="video-card"
      >
        <div class="video-thumbnail">
          <img
            src={video.thumbnail || `https://img.youtube.com/vi/${video.id}/mqdefault.jpg`}
            alt={video.title}
            loading="lazy"
          />
          <div class="play-icon">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="48" height="48">
              <path d="M8 5v14l11-7z"/>
            </svg>
          </div>
        </div>
        <h3 class="video-title">{video.title}</h3>
      </a>
    ))}
  </div>
  <div class="channel-link">
    <a href={youtubeChannel} target="_blank" rel="noopener noreferrer">
      觀看更多影片 →
    </a>
  </div>
</section>

<style>
  .video-gallery {
    padding: 4rem 1rem;
    max-width: 960px;
    margin: 0 auto;
  }

  .section-title {
    text-align: center;
    color: var(--accent-dark);
    margin-bottom: 2rem;
  }

  .video-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 1.5rem;
  }

  .video-card {
    text-decoration: none;
    color: inherit;
    transition: transform var(--transition-fast);
  }

  .video-card:hover {
    transform: translateY(-4px);
  }

  .video-thumbnail {
    position: relative;
    aspect-ratio: 16 / 9;
    overflow: hidden;
    border-radius: var(--border-radius);
    background: rgb(var(--gray-light));
  }

  .video-thumbnail img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    transition: transform var(--transition-medium);
  }

  .video-card:hover .video-thumbnail img {
    transform: scale(1.05);
  }

  .play-icon {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 64px;
    height: 64px;
    background: rgba(0, 0, 0, 0.7);
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
    transition: background var(--transition-fast);
  }

  .video-card:hover .play-icon {
    background: var(--accent);
  }

  .video-title {
    font-size: 1rem;
    margin-top: 0.75rem;
    color: rgb(var(--black));
  }

  .channel-link {
    text-align: center;
    margin-top: 2rem;
  }

  .channel-link a {
    color: var(--accent);
    font-weight: 600;
    text-decoration: none;
  }

  .channel-link a:hover {
    text-decoration: underline;
  }
</style>
```

**Step 2: 執行開發伺服器確認元件無錯誤**

Run: `npm run dev`
Expected: 編譯成功

**Step 3: Commit**

```bash
git add src/components/VideoGallery.astro
git commit -m "feat: add VideoGallery component for YouTube videos"
```

---

## Task 4: 建立 QuizCTA 元件

**Files:**
- Create: `src/components/QuizCTA.astro`

**Step 1: 建立元件檔案**

```astro
---
interface Props {
  variant?: 'primary' | 'secondary' | 'banner';
  title?: string;
  description?: string;
  buttonText?: string;
}

const {
  variant = 'primary',
  title = "你的代謝年齡是幾歲？",
  description = "花 2 分鐘完成測驗，了解你的身體真實狀態",
  buttonText = "免費測驗"
} = Astro.props;
---

{variant === 'banner' ? (
  <section class="quiz-cta-banner">
    <div class="banner-content">
      <h2>{title}</h2>
      <p>{description}</p>
      <a href="/quiz" class="cta-btn primary">{buttonText}</a>
    </div>
  </section>
) : (
  <div class={`quiz-cta-inline ${variant}`}>
    <a href="/quiz" class={`cta-btn ${variant}`}>
      {buttonText}：{title}
    </a>
  </div>
)}

<style>
  /* Banner variant */
  .quiz-cta-banner {
    background: linear-gradient(135deg, var(--accent) 0%, var(--accent-dark) 100%);
    padding: 4rem 1rem;
    text-align: center;
  }

  .banner-content {
    max-width: 600px;
    margin: 0 auto;
  }

  .banner-content h2 {
    color: white;
    font-size: 2rem;
    margin-bottom: 1rem;
  }

  .banner-content p {
    color: rgba(255, 255, 255, 0.9);
    font-size: 1.1rem;
    margin-bottom: 2rem;
  }

  .quiz-cta-banner .cta-btn {
    background: white;
    color: var(--accent-dark);
    padding: 1rem 2.5rem;
    font-size: 1.1rem;
    font-weight: 700;
    text-decoration: none;
    display: inline-block;
    border-radius: var(--border-radius);
    transition: transform var(--transition-fast), box-shadow var(--transition-fast);
  }

  .quiz-cta-banner .cta-btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  }

  /* Inline variants */
  .quiz-cta-inline {
    display: inline-block;
  }

  .quiz-cta-inline .cta-btn {
    padding: 0.8rem 1.5rem;
    font-weight: 700;
    text-decoration: none;
    display: inline-block;
    border: 2px solid var(--accent);
    transition: all var(--transition-fast);
  }

  .quiz-cta-inline .cta-btn.primary {
    background: var(--accent);
    color: white;
  }

  .quiz-cta-inline .cta-btn.primary:hover {
    background: var(--accent-dark);
    border-color: var(--accent-dark);
    transform: translateY(-2px);
  }

  .quiz-cta-inline .cta-btn.secondary {
    background: transparent;
    color: var(--accent);
  }

  .quiz-cta-inline .cta-btn.secondary:hover {
    background: var(--accent);
    color: white;
    transform: translateY(-2px);
  }
</style>
```

**Step 2: 執行開發伺服器確認元件無錯誤**

Run: `npm run dev`
Expected: 編譯成功

**Step 3: Commit**

```bash
git add src/components/QuizCTA.astro
git commit -m "feat: add QuizCTA component with multiple variants"
```

---

## Task 5: 重新設計首頁

**Files:**
- Modify: `src/pages/index.astro`

**Step 1: 完整改寫首頁**

```astro
---
import BaseHead from '../components/BaseHead.astro';
import Footer from '../components/Footer.astro';
import Header from '../components/Header.astro';
import SocialProof from '../components/SocialProof.astro';
import VideoGallery from '../components/VideoGallery.astro';
import QuizCTA from '../components/QuizCTA.astro';
import { SITE_DESCRIPTION, SITE_TITLE } from '../consts';
---

<!doctype html>
<html lang="zh-Hant">
	<head>
		<BaseHead title={SITE_TITLE} description={SITE_DESCRIPTION} />
	</head>
	<body>
		<Header />
		<main>
			<!-- Hero Section -->
			<section class="hero-section">
				<div class="hero-content">
					<p class="hero-eyebrow">生物醫學博士的親身實證</p>
					<h1>從三高患者到代謝教練<br/>我用科學逆轉了自己，也能幫助你</h1>
					<p class="hero-desc">
						<span class="before">曾經的我：脂肪肝、慢性腎病、代謝失調</span>
						<span class="after">現在的我：逆轉三高，找回 20 歲的代謝活力</span>
					</p>
					<div class="cta-group">
						<a href="/quiz" class="btn primary-btn">免費測驗：你的代謝年齡是幾歲？</a>
						<a href="/about" class="btn secondary-btn">了解我的故事 →</a>
					</div>
				</div>
				<div class="hero-image">
					<img src="/profile.png" alt="Dr. August Lin (阿杰博士)" />
				</div>
			</section>

			<!-- Social Proof Section -->
			<SocialProof />

			<!-- Features Section -->
			<section class="features-section">
				<h2>代謝財富三支柱</h2>
				<div class="grid">
					<div class="card">
						<div class="card-icon">
							<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="32" height="32">
								<path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-1 17.93c-3.95-.49-7-3.85-7-7.93 0-.62.08-1.21.21-1.79L9 15v1c0 1.1.9 2 2 2v1.93zm6.9-2.54c-.26-.81-1-1.39-1.9-1.39h-1v-3c0-.55-.45-1-1-1H8v-2h2c.55 0 1-.45 1-1V7h2c1.1 0 2-.9 2-2v-.41c2.93 1.19 5 4.06 5 7.41 0 2.08-.8 3.97-2.1 5.39z"/>
							</svg>
						</div>
						<h3>代謝靈活性</h3>
						<p>訓練身體像油電混合車，自由切換燃糖與燃脂模式，擺脫對糖分的依賴。</p>
						<a href="/blog" class="card-link">深入了解 →</a>
					</div>
					<div class="card">
						<div class="card-icon">
							<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="32" height="32">
								<path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm-5 14H7v-2h7v2zm3-4H7v-2h10v2zm0-4H7V7h10v2z"/>
							</svg>
						</div>
						<h3>原子習慣</h3>
						<p>健康不是意志力的對抗，而是打造微小但持續的好習慣，讓改變自然發生。</p>
						<a href="/blog" class="card-link">深入了解 →</a>
					</div>
					<div class="card">
						<div class="card-icon">
							<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="32" height="32">
								<path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-2 15l-5-5 1.41-1.41L10 14.17l7.59-7.59L19 8l-9 9z"/>
							</svg>
						</div>
						<h3>身心合一</h3>
						<p>透過瑜伽與冥想，連結身體與心靈，在忙碌中找回內在的平靜與能量。</p>
						<a href="/blog" class="card-link">深入了解 →</a>
					</div>
				</div>
			</section>

			<!-- Video Gallery Section -->
			<VideoGallery />

			<!-- Bottom CTA Section -->
			<QuizCTA
				variant="banner"
				title="準備好逆轉你的代謝年齡了嗎？"
				description="免費測驗只需 2 分鐘，立即了解你的身體狀態與改善方向"
				buttonText="開始免費測驗"
			/>
		</main>
		<Footer />

		<style>
			/* Hero Section */
			.hero-section {
				display: flex;
				flex-direction: column-reverse;
				align-items: center;
				gap: 2rem;
				padding: 2rem 1rem 4rem;
				max-width: 1100px;
				margin: 0 auto;
			}

			@media(min-width: 768px) {
				.hero-section {
					flex-direction: row;
					align-items: center;
					padding: 4rem 2rem;
				}
			}

			.hero-content {
				flex: 1;
			}

			.hero-eyebrow {
				font-size: 0.9rem;
				text-transform: uppercase;
				letter-spacing: 0.1em;
				color: var(--health-green);
				font-weight: 600;
				margin-bottom: 1rem;
			}

			.hero-section h1 {
				font-size: 2.2rem;
				line-height: 1.2;
				color: rgb(var(--black));
				margin-bottom: 1.5rem;
			}

			@media(min-width: 768px) {
				.hero-section h1 {
					font-size: 2.8rem;
				}
			}

			.hero-desc {
				display: flex;
				flex-direction: column;
				gap: 0.5rem;
				margin-bottom: 2rem;
				font-size: 1.1rem;
			}

			.hero-desc .before {
				color: rgb(var(--gray));
			}

			.hero-desc .after {
				color: var(--health-green);
				font-weight: 600;
			}

			.hero-image {
				flex: 1;
				max-width: 400px;
			}

			.hero-image img {
				width: 100%;
				border-radius: var(--border-radius);
				box-shadow: var(--box-shadow);
			}

			.cta-group {
				display: flex;
				flex-direction: column;
				gap: 1rem;
			}

			@media(min-width: 480px) {
				.cta-group {
					flex-direction: row;
				}
			}

			.btn {
				padding: 1rem 1.5rem;
				text-decoration: none;
				font-weight: 700;
				text-align: center;
				border: 2px solid var(--accent);
				border-radius: var(--border-radius);
				transition: all var(--transition-fast);
			}

			.primary-btn {
				background: var(--accent);
				color: white;
			}

			.primary-btn:hover {
				background: var(--accent-dark);
				border-color: var(--accent-dark);
				transform: translateY(-2px);
			}

			.secondary-btn {
				background: transparent;
				color: var(--accent);
			}

			.secondary-btn:hover {
				background: var(--accent);
				color: white;
				transform: translateY(-2px);
			}

			/* Features Section */
			.features-section {
				padding: 4rem 1rem;
				max-width: 960px;
				margin: 0 auto;
			}

			.features-section h2 {
				text-align: center;
				color: var(--accent-dark);
				margin-bottom: 2rem;
			}

			.grid {
				display: grid;
				grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
				gap: 1.5rem;
			}

			.card {
				border: 1px solid rgba(var(--gray-light), 1);
				border-top: 4px solid var(--health-green);
				padding: 2rem;
				background: white;
				box-shadow: var(--box-shadow);
				border-radius: var(--border-radius);
				transition: all var(--transition-fast);
			}

			.card:hover {
				transform: translateY(-4px);
				box-shadow: var(--box-shadow-hover);
			}

			.card-icon {
				color: var(--health-green);
				margin-bottom: 1rem;
			}

			.card h3 {
				color: rgb(var(--black));
				margin-bottom: 0.75rem;
				font-size: 1.3rem;
			}

			.card p {
				color: rgb(var(--gray-dark));
				margin-bottom: 1rem;
				line-height: 1.6;
			}

			.card-link {
				color: var(--accent);
				font-weight: 600;
				text-decoration: none;
				font-size: 0.9rem;
			}

			.card-link:hover {
				text-decoration: underline;
			}
		</style>
	</body>
</html>
```

**Step 2: 執行開發伺服器檢視首頁**

Run: `npm run dev`
Expected: 首頁顯示新的 Hero、SocialProof、Features、VideoGallery、QuizCTA 區塊

**Step 3: Commit**

```bash
git add src/pages/index.astro
git commit -m "feat: redesign homepage with story-driven hero and new sections"
```

---

## Task 6: 建立測驗頁面結構

**Files:**
- Create: `src/pages/quiz/index.astro`

**Step 1: 建立測驗頁面**

首先建立資料夾：
```bash
mkdir -p src/pages/quiz
```

然後建立檔案：

```astro
---
import BaseHead from '../../components/BaseHead.astro';
import Header from '../../components/Header.astro';
import Footer from '../../components/Footer.astro';
import { SITE_TITLE } from '../../consts';
---

<!doctype html>
<html lang="zh-Hant">
	<head>
		<BaseHead
			title={`代謝年齡測驗 - ${SITE_TITLE}`}
			description="花 2 分鐘完成測驗，了解你的代謝年齡與改善方向"
		/>
	</head>
	<body>
		<Header />
		<main class="quiz-page">
			<div class="quiz-container">
				<div class="quiz-header">
					<h1>代謝年齡測驗</h1>
					<p>回答 7 個簡單問題，了解你的身體真實狀態</p>
				</div>

				<div id="quiz-app">
					<!-- Question slides -->
					<div class="quiz-questions">
						<!-- Q1 -->
						<div class="question-slide" data-question="1">
							<div class="progress-bar"><div class="progress" style="width: 14%"></div></div>
							<p class="question-number">問題 1 / 7</p>
							<h2>你的年齡區間？</h2>
							<div class="options">
								<button class="option-btn" data-value="30-40" data-score="0">30-40 歲</button>
								<button class="option-btn" data-value="40-50" data-score="5">40-50 歲</button>
								<button class="option-btn" data-value="50-60" data-score="10">50-60 歲</button>
								<button class="option-btn" data-value="60+" data-score="15">60 歲以上</button>
							</div>
						</div>

						<!-- Q2 -->
						<div class="question-slide" data-question="2" style="display: none;">
							<div class="progress-bar"><div class="progress" style="width: 28%"></div></div>
							<p class="question-number">問題 2 / 7</p>
							<h2>餐後是否容易感到昏沉想睡？</h2>
							<div class="options">
								<button class="option-btn" data-value="often" data-score="10">常常</button>
								<button class="option-btn" data-value="sometimes" data-score="5">偶爾</button>
								<button class="option-btn" data-value="rarely" data-score="0">很少</button>
							</div>
						</div>

						<!-- Q3 -->
						<div class="question-slide" data-question="3" style="display: none;">
							<div class="progress-bar"><div class="progress" style="width: 42%"></div></div>
							<p class="question-number">問題 3 / 7</p>
							<h2>是否有三高問題？（可複選）</h2>
							<div class="options multi-select">
								<button class="option-btn" data-value="blood-pressure" data-score="5">高血壓</button>
								<button class="option-btn" data-value="blood-sugar" data-score="5">高血糖</button>
								<button class="option-btn" data-value="blood-lipid" data-score="5">高血脂</button>
								<button class="option-btn" data-value="none" data-score="0">都沒有</button>
							</div>
							<button class="next-btn" style="display: none;">下一題 →</button>
						</div>

						<!-- Q4 -->
						<div class="question-slide" data-question="4" style="display: none;">
							<div class="progress-bar"><div class="progress" style="width: 56%"></div></div>
							<p class="question-number">問題 4 / 7</p>
							<h2>目前的運動頻率？</h2>
							<div class="options">
								<button class="option-btn" data-value="none" data-score="10">幾乎沒有</button>
								<button class="option-btn" data-value="1-2" data-score="5">每週 1-2 次</button>
								<button class="option-btn" data-value="3+" data-score="0">每週 3 次以上</button>
							</div>
						</div>

						<!-- Q5 -->
						<div class="question-slide" data-question="5" style="display: none;">
							<div class="progress-bar"><div class="progress" style="width: 70%"></div></div>
							<p class="question-number">問題 5 / 7</p>
							<h2>睡眠品質如何？</h2>
							<div class="options">
								<button class="option-btn" data-value="insomnia" data-score="10">常失眠</button>
								<button class="option-btn" data-value="light" data-score="5">淺眠易醒</button>
								<button class="option-btn" data-value="good" data-score="0">睡得不錯</button>
							</div>
						</div>

						<!-- Q6 -->
						<div class="question-slide" data-question="6" style="display: none;">
							<div class="progress-bar"><div class="progress" style="width: 84%"></div></div>
							<p class="question-number">問題 6 / 7</p>
							<h2>是否嘗試過減重但反覆失敗？</h2>
							<div class="options">
								<button class="option-btn" data-value="yes" data-score="5">是</button>
								<button class="option-btn" data-value="no" data-score="0">否</button>
							</div>
						</div>

						<!-- Q7 -->
						<div class="question-slide" data-question="7" style="display: none;">
							<div class="progress-bar"><div class="progress" style="width: 100%"></div></div>
							<p class="question-number">問題 7 / 7</p>
							<h2>最困擾你的健康問題是？</h2>
							<div class="options">
								<button class="option-btn" data-value="weight" data-score="0">體重</button>
								<button class="option-btn" data-value="fatigue" data-score="0">疲勞</button>
								<button class="option-btn" data-value="metabolic" data-score="0">三高</button>
								<button class="option-btn" data-value="sleep" data-score="0">睡眠</button>
								<button class="option-btn" data-value="other" data-score="0">其他</button>
							</div>
						</div>
					</div>

					<!-- Email capture -->
					<div class="email-capture" style="display: none;">
						<h2>測驗完成！</h2>
						<p>輸入 Email 查看你的代謝年齡報告</p>
						<form id="email-form">
							<input type="email" id="email-input" placeholder="your@email.com" required />
							<button type="submit" class="submit-btn">查看結果</button>
						</form>
						<p class="privacy-note">我們重視你的隱私，不會發送垃圾郵件</p>
					</div>
				</div>
			</div>
		</main>
		<Footer />

		<style>
			.quiz-page {
				min-height: 70vh;
				display: flex;
				align-items: center;
				justify-content: center;
				padding: 2rem 1rem;
			}

			.quiz-container {
				max-width: 600px;
				width: 100%;
			}

			.quiz-header {
				text-align: center;
				margin-bottom: 2rem;
			}

			.quiz-header h1 {
				color: var(--accent-dark);
				margin-bottom: 0.5rem;
			}

			.quiz-header p {
				color: rgb(var(--gray));
			}

			.question-slide {
				background: white;
				padding: 2rem;
				border-radius: var(--border-radius);
				box-shadow: var(--box-shadow);
			}

			.progress-bar {
				height: 6px;
				background: rgb(var(--gray-light));
				border-radius: 3px;
				margin-bottom: 1.5rem;
				overflow: hidden;
			}

			.progress {
				height: 100%;
				background: var(--health-green);
				transition: width 0.3s ease;
			}

			.question-number {
				font-size: 0.85rem;
				color: rgb(var(--gray));
				margin-bottom: 0.5rem;
			}

			.question-slide h2 {
				font-size: 1.4rem;
				color: rgb(var(--black));
				margin-bottom: 1.5rem;
			}

			.options {
				display: flex;
				flex-direction: column;
				gap: 0.75rem;
			}

			.option-btn {
				padding: 1rem 1.5rem;
				border: 2px solid rgb(var(--gray-light));
				background: white;
				border-radius: var(--border-radius);
				font-size: 1rem;
				cursor: pointer;
				transition: all var(--transition-fast);
				text-align: left;
			}

			.option-btn:hover {
				border-color: var(--accent);
				background: var(--bg-warm);
			}

			.option-btn.selected {
				border-color: var(--accent);
				background: var(--accent);
				color: white;
			}

			.multi-select .option-btn.selected {
				background: var(--health-green);
				border-color: var(--health-green);
			}

			.next-btn {
				margin-top: 1.5rem;
				padding: 1rem 2rem;
				background: var(--accent);
				color: white;
				border: none;
				border-radius: var(--border-radius);
				font-size: 1rem;
				font-weight: 600;
				cursor: pointer;
				width: 100%;
				transition: all var(--transition-fast);
			}

			.next-btn:hover {
				background: var(--accent-dark);
			}

			/* Email capture */
			.email-capture {
				background: white;
				padding: 2rem;
				border-radius: var(--border-radius);
				box-shadow: var(--box-shadow);
				text-align: center;
			}

			.email-capture h2 {
				color: var(--health-green);
				margin-bottom: 0.5rem;
			}

			.email-capture p {
				color: rgb(var(--gray));
				margin-bottom: 1.5rem;
			}

			#email-form {
				display: flex;
				flex-direction: column;
				gap: 1rem;
			}

			#email-input {
				padding: 1rem;
				border: 2px solid rgb(var(--gray-light));
				border-radius: var(--border-radius);
				font-size: 1rem;
			}

			#email-input:focus {
				outline: none;
				border-color: var(--accent);
			}

			.submit-btn {
				padding: 1rem;
				background: var(--accent);
				color: white;
				border: none;
				border-radius: var(--border-radius);
				font-size: 1rem;
				font-weight: 600;
				cursor: pointer;
				transition: all var(--transition-fast);
			}

			.submit-btn:hover {
				background: var(--accent-dark);
			}

			.privacy-note {
				font-size: 0.8rem;
				color: rgb(var(--gray));
				margin-top: 1rem;
				margin-bottom: 0;
			}
		</style>

		<script>
			// Quiz state
			let currentQuestion = 1;
			let totalScore = 0;
			let userAge = '40-50';
			let mainConcern = 'weight';
			const answers: Record<string, string | string[]> = {};

			// DOM elements
			const slides = document.querySelectorAll('.question-slide');
			const emailCapture = document.querySelector('.email-capture') as HTMLElement;
			const emailForm = document.getElementById('email-form') as HTMLFormElement;

			// Handle single-select options
			document.querySelectorAll('.options:not(.multi-select) .option-btn').forEach(btn => {
				btn.addEventListener('click', (e) => {
					const target = e.currentTarget as HTMLButtonElement;
					const slide = target.closest('.question-slide') as HTMLElement;
					const questionNum = parseInt(slide.dataset.question || '1');
					const score = parseInt(target.dataset.score || '0');
					const value = target.dataset.value || '';

					// Store answer
					answers[`q${questionNum}`] = value;
					totalScore += score;

					// Track specific answers
					if (questionNum === 1) userAge = value;
					if (questionNum === 7) mainConcern = value;

					// Visual feedback
					slide.querySelectorAll('.option-btn').forEach(b => b.classList.remove('selected'));
					target.classList.add('selected');

					// Go to next question after delay
					setTimeout(() => {
						goToNextQuestion();
					}, 300);
				});
			});

			// Handle multi-select options (Q3)
			const multiSelectOptions = document.querySelector('.multi-select');
			if (multiSelectOptions) {
				const nextBtn = multiSelectOptions.parentElement?.querySelector('.next-btn') as HTMLButtonElement;

				multiSelectOptions.querySelectorAll('.option-btn').forEach(btn => {
					btn.addEventListener('click', (e) => {
						const target = e.currentTarget as HTMLButtonElement;
						const value = target.dataset.value || '';

						if (value === 'none') {
							// Deselect all others
							multiSelectOptions.querySelectorAll('.option-btn').forEach(b => {
								b.classList.remove('selected');
							});
							target.classList.add('selected');
						} else {
							// Deselect "none" if selected
							multiSelectOptions.querySelector('[data-value="none"]')?.classList.remove('selected');
							target.classList.toggle('selected');
						}

						// Show next button if any selected
						const hasSelection = multiSelectOptions.querySelector('.selected');
						if (nextBtn) {
							nextBtn.style.display = hasSelection ? 'block' : 'none';
						}
					});
				});

				// Handle next button for multi-select
				nextBtn?.addEventListener('click', () => {
					const selected = multiSelectOptions.querySelectorAll('.selected');
					const values: string[] = [];
					let score = 0;

					selected.forEach(btn => {
						values.push((btn as HTMLButtonElement).dataset.value || '');
						score += parseInt((btn as HTMLButtonElement).dataset.score || '0');
					});

					answers['q3'] = values;
					totalScore += score;
					goToNextQuestion();
				});
			}

			function goToNextQuestion() {
				if (currentQuestion < 7) {
					slides[currentQuestion - 1].style.display = 'none';
					currentQuestion++;
					slides[currentQuestion - 1].style.display = 'block';
				} else {
					// Show email capture
					slides[currentQuestion - 1].style.display = 'none';
					if (emailCapture) {
						emailCapture.style.display = 'block';
					}
				}
			}

			// Handle email form submission
			emailForm?.addEventListener('submit', (e) => {
				e.preventDefault();
				const emailInput = document.getElementById('email-input') as HTMLInputElement;
				const email = emailInput.value;

				// Calculate metabolic age
				let baseAge = 40;
				if (userAge === '30-40') baseAge = 35;
				else if (userAge === '40-50') baseAge = 45;
				else if (userAge === '50-60') baseAge = 55;
				else if (userAge === '60+') baseAge = 65;

				const metabolicAge = baseAge + Math.floor(totalScore / 5);

				// Store data and redirect to results
				const resultData = {
					email,
					metabolicAge,
					actualAge: baseAge,
					totalScore,
					mainConcern,
					answers
				};

				// Store in sessionStorage for results page
				sessionStorage.setItem('quizResult', JSON.stringify(resultData));

				// Redirect to results page
				window.location.href = '/quiz/result';
			});
		</script>
	</body>
</html>
```

**Step 2: 執行開發伺服器測試測驗頁面**

Run: `npm run dev`
然後瀏覽 `http://localhost:4321/quiz`
Expected: 測驗頁面顯示，可以點選選項切換題目

**Step 3: Commit**

```bash
git add src/pages/quiz/index.astro
git commit -m "feat: add quiz page with 7-question metabolic age assessment"
```

---

## Task 7: 建立測驗結果頁面

**Files:**
- Create: `src/pages/quiz/result.astro`

**Step 1: 建立結果頁面**

```astro
---
import BaseHead from '../../components/BaseHead.astro';
import Header from '../../components/Header.astro';
import Footer from '../../components/Footer.astro';
import { SITE_TITLE } from '../../consts';
---

<!doctype html>
<html lang="zh-Hant">
	<head>
		<BaseHead
			title={`測驗結果 - ${SITE_TITLE}`}
			description="你的代謝年齡測驗結果"
		/>
	</head>
	<body>
		<Header />
		<main class="result-page">
			<div class="result-container">
				<!-- Loading state -->
				<div id="loading" class="loading">
					<p>計算你的代謝年齡中...</p>
				</div>

				<!-- Result content -->
				<div id="result-content" style="display: none;">
					<div class="result-card">
						<h1>你的代謝年齡</h1>
						<div class="age-display">
							<span id="metabolic-age" class="metabolic-age">--</span>
							<span class="age-unit">歲</span>
						</div>
						<p class="actual-age">實際年齡：<span id="actual-age">--</span> 歲</p>
						<div id="age-diff" class="age-diff"></div>
					</div>

					<div class="risk-section">
						<h2>你的主要代謝風險</h2>
						<ul id="risk-list" class="risk-list">
							<!-- Populated by JS -->
						</ul>
					</div>

					<div class="email-sent">
						<p>完整報告已寄送到 <strong id="user-email">your@email.com</strong></p>
					</div>

					<div class="video-recommend">
						<h2>推薦你先看這支影片</h2>
						<div class="video-embed">
							<iframe
								id="recommended-video"
								width="560"
								height="315"
								src="https://www.youtube.com/embed/VIDEO_ID"
								title="推薦影片"
								frameborder="0"
								allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
								allowfullscreen>
							</iframe>
						</div>
					</div>

					<div class="cta-section">
						<h2>想要逆轉代謝年齡？</h2>
						<p>預約免費諮詢，讓阿杰博士協助你打造專屬的代謝改善計畫</p>
						<a href="/booking" class="cta-btn">預約免費諮詢</a>
						<a href="/" class="back-link">← 返回首頁</a>
					</div>
				</div>

				<!-- No data state -->
				<div id="no-data" style="display: none;">
					<h1>尚未完成測驗</h1>
					<p>請先完成代謝年齡測驗</p>
					<a href="/quiz" class="cta-btn">開始測驗</a>
				</div>
			</div>
		</main>
		<Footer />

		<style>
			.result-page {
				padding: 2rem 1rem;
			}

			.result-container {
				max-width: 700px;
				margin: 0 auto;
			}

			.loading {
				text-align: center;
				padding: 4rem 2rem;
			}

			.result-card {
				background: white;
				padding: 3rem 2rem;
				border-radius: var(--border-radius);
				box-shadow: var(--box-shadow);
				text-align: center;
				margin-bottom: 2rem;
			}

			.result-card h1 {
				color: rgb(var(--gray));
				font-size: 1.2rem;
				text-transform: uppercase;
				letter-spacing: 0.1em;
				margin-bottom: 1rem;
			}

			.age-display {
				margin-bottom: 0.5rem;
			}

			.metabolic-age {
				font-size: 5rem;
				font-weight: 700;
				line-height: 1;
			}

			.age-unit {
				font-size: 2rem;
				color: rgb(var(--gray));
			}

			.actual-age {
				color: rgb(var(--gray));
				margin-bottom: 1rem;
			}

			.age-diff {
				font-size: 1.1rem;
				font-weight: 600;
				padding: 0.75rem 1.5rem;
				border-radius: var(--border-radius);
				display: inline-block;
			}

			.age-diff.warning {
				background: #fef3c7;
				color: #92400e;
			}

			.age-diff.danger {
				background: #fee2e2;
				color: #991b1b;
			}

			.age-diff.good {
				background: #dcfce7;
				color: #166534;
			}

			/* Risk section */
			.risk-section {
				background: white;
				padding: 2rem;
				border-radius: var(--border-radius);
				box-shadow: var(--box-shadow);
				margin-bottom: 2rem;
			}

			.risk-section h2 {
				font-size: 1.2rem;
				color: rgb(var(--black));
				margin-bottom: 1rem;
			}

			.risk-list {
				list-style: none;
				padding: 0;
				margin: 0;
			}

			.risk-list li {
				padding: 0.75rem 0;
				border-bottom: 1px solid rgb(var(--gray-light));
				color: rgb(var(--gray-dark));
			}

			.risk-list li:last-child {
				border-bottom: none;
			}

			.risk-list li::before {
				content: "⚠️ ";
			}

			/* Email sent */
			.email-sent {
				background: var(--bg-science);
				padding: 1rem 1.5rem;
				border-radius: var(--border-radius);
				text-align: center;
				margin-bottom: 2rem;
			}

			.email-sent p {
				margin: 0;
				color: var(--science-blue);
			}

			/* Video recommend */
			.video-recommend {
				margin-bottom: 2rem;
			}

			.video-recommend h2 {
				font-size: 1.2rem;
				margin-bottom: 1rem;
			}

			.video-embed {
				position: relative;
				padding-bottom: 56.25%;
				height: 0;
				overflow: hidden;
				border-radius: var(--border-radius);
			}

			.video-embed iframe {
				position: absolute;
				top: 0;
				left: 0;
				width: 100%;
				height: 100%;
			}

			/* CTA section */
			.cta-section {
				background: linear-gradient(135deg, var(--accent) 0%, var(--accent-dark) 100%);
				padding: 3rem 2rem;
				border-radius: var(--border-radius);
				text-align: center;
				color: white;
			}

			.cta-section h2 {
				color: white;
				margin-bottom: 0.5rem;
			}

			.cta-section p {
				opacity: 0.9;
				margin-bottom: 1.5rem;
			}

			.cta-btn {
				display: inline-block;
				padding: 1rem 2.5rem;
				background: white;
				color: var(--accent-dark);
				text-decoration: none;
				font-weight: 700;
				border-radius: var(--border-radius);
				transition: all var(--transition-fast);
			}

			.cta-btn:hover {
				transform: translateY(-2px);
				box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
			}

			.back-link {
				display: block;
				margin-top: 1rem;
				color: rgba(255, 255, 255, 0.8);
				text-decoration: none;
			}

			.back-link:hover {
				color: white;
			}

			/* No data state */
			#no-data {
				text-align: center;
				padding: 4rem 2rem;
			}

			#no-data h1 {
				color: rgb(var(--black));
				margin-bottom: 1rem;
			}

			#no-data p {
				color: rgb(var(--gray));
				margin-bottom: 2rem;
			}
		</style>

		<script>
			// Video recommendations based on concern
			const videoRecommendations: Record<string, string> = {
				weight: 'VIDEO_ID_WEIGHT',
				fatigue: 'VIDEO_ID_FATIGUE',
				metabolic: 'VIDEO_ID_METABOLIC',
				sleep: 'VIDEO_ID_SLEEP',
				other: 'VIDEO_ID_DEFAULT'
			};

			// Risk messages based on score ranges
			const riskMessages: Record<string, string[]> = {
				low: [
					'維持目前良好的生活習慣',
					'可考慮進階的代謝優化策略'
				],
				medium: [
					'餐後血糖波動較大（餐後嗜睡）',
					'運動量不足，線粒體功能待提升',
					'睡眠品質影響代謝恢復'
				],
				high: [
					'代謝靈活性明顯下降',
					'胰島素阻抗風險較高',
					'建議盡快進行專業代謝評估',
					'生活型態需要系統性調整'
				]
			};

			// Load result from sessionStorage
			document.addEventListener('DOMContentLoaded', () => {
				const loading = document.getElementById('loading') as HTMLElement;
				const resultContent = document.getElementById('result-content') as HTMLElement;
				const noData = document.getElementById('no-data') as HTMLElement;

				const resultDataStr = sessionStorage.getItem('quizResult');

				if (!resultDataStr) {
					loading.style.display = 'none';
					noData.style.display = 'block';
					return;
				}

				const resultData = JSON.parse(resultDataStr);

				// Simulate loading
				setTimeout(() => {
					loading.style.display = 'none';
					resultContent.style.display = 'block';

					// Display metabolic age
					const metabolicAgeEl = document.getElementById('metabolic-age') as HTMLElement;
					const actualAgeEl = document.getElementById('actual-age') as HTMLElement;
					const ageDiffEl = document.getElementById('age-diff') as HTMLElement;
					const userEmailEl = document.getElementById('user-email') as HTMLElement;
					const riskListEl = document.getElementById('risk-list') as HTMLElement;
					const videoEl = document.getElementById('recommended-video') as HTMLIFrameElement;

					metabolicAgeEl.textContent = resultData.metabolicAge.toString();
					actualAgeEl.textContent = resultData.actualAge.toString();
					userEmailEl.textContent = resultData.email;

					// Calculate and display age difference
					const diff = resultData.metabolicAge - resultData.actualAge;
					if (diff <= 5) {
						metabolicAgeEl.style.color = 'var(--health-green)';
						ageDiffEl.className = 'age-diff good';
						ageDiffEl.textContent = diff <= 0
							? '太棒了！代謝年齡與實際年齡相當'
							: `代謝年齡比實際年齡大 ${diff} 歲`;
					} else if (diff <= 15) {
						metabolicAgeEl.style.color = 'var(--accent)';
						ageDiffEl.className = 'age-diff warning';
						ageDiffEl.textContent = `⚠️ 代謝年齡比實際年齡大 ${diff} 歲`;
					} else {
						metabolicAgeEl.style.color = '#dc2626';
						ageDiffEl.className = 'age-diff danger';
						ageDiffEl.textContent = `🚨 代謝年齡比實際年齡大 ${diff} 歲`;
					}

					// Display risk messages
					let riskLevel = 'low';
					if (diff > 15) riskLevel = 'high';
					else if (diff > 5) riskLevel = 'medium';

					const risks = riskMessages[riskLevel];
					riskListEl.innerHTML = risks.map(r => `<li>${r}</li>`).join('');

					// Set recommended video
					const videoId = videoRecommendations[resultData.mainConcern] || videoRecommendations.other;
					videoEl.src = `https://www.youtube.com/embed/${videoId}`;

				}, 1500);
			});
		</script>
	</body>
</html>
```

**Step 2: 執行開發伺服器測試結果頁面**

Run: `npm run dev`
先完成測驗，然後自動跳轉到結果頁面
Expected: 結果頁面顯示代謝年齡、風險分析、推薦影片

**Step 3: Commit**

```bash
git add src/pages/quiz/result.astro
git commit -m "feat: add quiz result page with personalized recommendations"
```

---

## Task 8: 建立預約諮詢頁面

**Files:**
- Create: `src/pages/booking.astro`

**Step 1: 建立預約頁面**

```astro
---
import BaseHead from '../components/BaseHead.astro';
import Header from '../components/Header.astro';
import Footer from '../components/Footer.astro';
import { SITE_TITLE } from '../consts';

// Surveycake 或 Google 表單嵌入 URL
const FORM_EMBED_URL = "https://docs.google.com/forms/d/e/YOUR_FORM_ID/viewform?embedded=true";
---

<!doctype html>
<html lang="zh-Hant">
	<head>
		<BaseHead
			title={`預約諮詢 - ${SITE_TITLE}`}
			description="預約阿杰博士的免費代謝健康諮詢"
		/>
	</head>
	<body>
		<Header />
		<main class="booking-page">
			<div class="booking-container">
				<div class="booking-header">
					<h1>預約免費諮詢</h1>
					<p>填寫以下表單，阿杰博士會在 1-2 個工作天內與你聯繫確認諮詢時間</p>
				</div>

				<div class="booking-content">
					<div class="form-section">
						<h2>諮詢預約表單</h2>

						<!-- 選項 1: 嵌入 Google Form / Surveycake -->
						<!-- <iframe
							src={FORM_EMBED_URL}
							width="100%"
							height="800"
							frameborder="0"
							marginheight="0"
							marginwidth="0">
							載入中...
						</iframe> -->

						<!-- 選項 2: 自建表單（連結到 Google Form） -->
						<form id="booking-form" class="booking-form">
							<div class="form-group">
								<label for="name">姓名與稱呼 *</label>
								<input type="text" id="name" name="name" required placeholder="例：王小明 先生" />
							</div>

							<div class="form-group">
								<label for="email">Email *</label>
								<input type="email" id="email" name="email" required placeholder="your@email.com" />
							</div>

							<div class="form-group">
								<label>你目前最困擾的健康問題？（可複選）*</label>
								<div class="checkbox-group">
									<label class="checkbox-label">
										<input type="checkbox" name="concerns" value="weight" /> 體重管理
									</label>
									<label class="checkbox-label">
										<input type="checkbox" name="concerns" value="metabolic" /> 三高問題
									</label>
									<label class="checkbox-label">
										<input type="checkbox" name="concerns" value="fatigue" /> 慢性疲勞
									</label>
									<label class="checkbox-label">
										<input type="checkbox" name="concerns" value="sleep" /> 睡眠品質
									</label>
									<label class="checkbox-label">
										<input type="checkbox" name="concerns" value="other" /> 其他
									</label>
								</div>
							</div>

							<div class="form-group">
								<label for="treatment">是否有進行中的醫療治療？</label>
								<select id="treatment" name="treatment">
									<option value="no">否</option>
									<option value="yes">是（請在下方說明）</option>
								</select>
							</div>

							<div class="form-group" id="treatment-detail-group" style="display: none;">
								<label for="treatment-detail">請簡述目前的治療情況</label>
								<textarea id="treatment-detail" name="treatment-detail" rows="3" placeholder="例：目前服用降血壓藥物..."></textarea>
							</div>

							<div class="form-group">
								<label for="goal">你希望在 90 天後達成什麼目標？*</label>
								<textarea id="goal" name="goal" rows="3" required placeholder="例：減重 5 公斤、改善餐後嗜睡、降低血糖指數..."></textarea>
							</div>

							<div class="form-group">
								<label>方便諮詢的時段？（可複選）*</label>
								<div class="checkbox-group">
									<label class="checkbox-label">
										<input type="checkbox" name="time" value="weekday-morning" /> 平日上午
									</label>
									<label class="checkbox-label">
										<input type="checkbox" name="time" value="weekday-afternoon" /> 平日下午
									</label>
									<label class="checkbox-label">
										<input type="checkbox" name="time" value="weekday-evening" /> 平日晚上
									</label>
									<label class="checkbox-label">
										<input type="checkbox" name="time" value="weekend" /> 週末
									</label>
								</div>
							</div>

							<div class="form-group">
								<label for="source">你是如何認識阿杰博士的？</label>
								<select id="source" name="source">
									<option value="">請選擇</option>
									<option value="search">Google 搜尋</option>
									<option value="youtube">YouTube</option>
									<option value="instagram">Instagram</option>
									<option value="referral">朋友介紹</option>
									<option value="other">其他</option>
								</select>
							</div>

							<button type="submit" class="submit-btn">送出預約申請</button>
						</form>
					</div>

					<div class="info-section">
						<div class="info-card">
							<h3>諮詢包含什麼？</h3>
							<ul>
								<li>30 分鐘一對一視訊諮詢</li>
								<li>初步健康狀況評估</li>
								<li>個人化改善方向建議</li>
								<li>解答你的健康疑問</li>
							</ul>
						</div>

						<div class="info-card">
							<h3>諮詢前準備</h3>
							<ul>
								<li>近期健檢報告（如有）</li>
								<li>目前用藥清單（如有）</li>
								<li>想詢問的問題列表</li>
							</ul>
						</div>

						<div class="quiz-prompt">
							<p>還沒做過代謝年齡測驗？</p>
							<a href="/quiz" class="quiz-link">先做免費測驗 →</a>
						</div>
					</div>
				</div>
			</div>

			<!-- Success Modal -->
			<div id="success-modal" class="modal" style="display: none;">
				<div class="modal-content">
					<div class="modal-icon">✅</div>
					<h2>已收到你的預約申請！</h2>
					<p>阿杰博士會在 1-2 個工作天內，透過 Email 與你確認諮詢時間。</p>
					<div class="modal-video">
						<p>在等待期間，推薦你先觀看：</p>
						<a href="https://www.youtube.com/@AugustLin123" target="_blank" class="video-link">
							🎬 什麼是代謝靈活性？
						</a>
					</div>
					<a href="/" class="modal-btn">返回首頁</a>
				</div>
			</div>
		</main>
		<Footer />

		<style>
			.booking-page {
				padding: 2rem 1rem;
			}

			.booking-container {
				max-width: 1000px;
				margin: 0 auto;
			}

			.booking-header {
				text-align: center;
				margin-bottom: 3rem;
			}

			.booking-header h1 {
				color: var(--accent-dark);
				margin-bottom: 0.5rem;
			}

			.booking-header p {
				color: rgb(var(--gray));
			}

			.booking-content {
				display: grid;
				grid-template-columns: 1fr;
				gap: 2rem;
			}

			@media (min-width: 768px) {
				.booking-content {
					grid-template-columns: 2fr 1fr;
				}
			}

			/* Form styles */
			.form-section {
				background: white;
				padding: 2rem;
				border-radius: var(--border-radius);
				box-shadow: var(--box-shadow);
			}

			.form-section h2 {
				font-size: 1.3rem;
				margin-bottom: 1.5rem;
				color: rgb(var(--black));
			}

			.form-group {
				margin-bottom: 1.5rem;
			}

			.form-group label {
				display: block;
				margin-bottom: 0.5rem;
				font-weight: 600;
				color: rgb(var(--gray-dark));
			}

			.form-group input[type="text"],
			.form-group input[type="email"],
			.form-group select,
			.form-group textarea {
				width: 100%;
				padding: 0.75rem 1rem;
				border: 2px solid rgb(var(--gray-light));
				border-radius: var(--border-radius);
				font-size: 1rem;
				transition: border-color var(--transition-fast);
			}

			.form-group input:focus,
			.form-group select:focus,
			.form-group textarea:focus {
				outline: none;
				border-color: var(--accent);
			}

			.checkbox-group {
				display: flex;
				flex-wrap: wrap;
				gap: 0.75rem;
			}

			.checkbox-label {
				display: flex;
				align-items: center;
				gap: 0.5rem;
				padding: 0.5rem 1rem;
				border: 1px solid rgb(var(--gray-light));
				border-radius: var(--border-radius);
				cursor: pointer;
				transition: all var(--transition-fast);
				font-weight: normal;
			}

			.checkbox-label:hover {
				border-color: var(--accent);
			}

			.checkbox-label input:checked + .checkbox-label,
			.checkbox-label:has(input:checked) {
				background: var(--bg-warm);
				border-color: var(--accent);
			}

			.submit-btn {
				width: 100%;
				padding: 1rem;
				background: var(--accent);
				color: white;
				border: none;
				border-radius: var(--border-radius);
				font-size: 1.1rem;
				font-weight: 700;
				cursor: pointer;
				transition: all var(--transition-fast);
			}

			.submit-btn:hover {
				background: var(--accent-dark);
			}

			/* Info section */
			.info-section {
				display: flex;
				flex-direction: column;
				gap: 1.5rem;
			}

			.info-card {
				background: white;
				padding: 1.5rem;
				border-radius: var(--border-radius);
				box-shadow: var(--box-shadow);
			}

			.info-card h3 {
				font-size: 1.1rem;
				color: var(--accent-dark);
				margin-bottom: 1rem;
			}

			.info-card ul {
				list-style: none;
				padding: 0;
				margin: 0;
			}

			.info-card li {
				padding: 0.5rem 0;
				color: rgb(var(--gray-dark));
				border-bottom: 1px solid rgb(var(--gray-light));
			}

			.info-card li:last-child {
				border-bottom: none;
			}

			.info-card li::before {
				content: "✓ ";
				color: var(--health-green);
			}

			.quiz-prompt {
				background: var(--bg-science);
				padding: 1.5rem;
				border-radius: var(--border-radius);
				text-align: center;
			}

			.quiz-prompt p {
				margin-bottom: 0.5rem;
				color: rgb(var(--gray));
			}

			.quiz-link {
				color: var(--science-blue);
				font-weight: 600;
				text-decoration: none;
			}

			.quiz-link:hover {
				text-decoration: underline;
			}

			/* Modal */
			.modal {
				position: fixed;
				top: 0;
				left: 0;
				width: 100%;
				height: 100%;
				background: rgba(0, 0, 0, 0.5);
				display: flex;
				align-items: center;
				justify-content: center;
				z-index: 1000;
				padding: 1rem;
			}

			.modal-content {
				background: white;
				padding: 3rem 2rem;
				border-radius: var(--border-radius);
				max-width: 500px;
				width: 100%;
				text-align: center;
			}

			.modal-icon {
				font-size: 3rem;
				margin-bottom: 1rem;
			}

			.modal-content h2 {
				color: var(--health-green);
				margin-bottom: 1rem;
			}

			.modal-content p {
				color: rgb(var(--gray-dark));
				margin-bottom: 1.5rem;
			}

			.modal-video {
				background: var(--bg-warm);
				padding: 1rem;
				border-radius: var(--border-radius);
				margin-bottom: 1.5rem;
			}

			.modal-video p {
				margin-bottom: 0.5rem;
				font-size: 0.9rem;
			}

			.video-link {
				color: var(--accent);
				font-weight: 600;
				text-decoration: none;
			}

			.modal-btn {
				display: inline-block;
				padding: 0.75rem 2rem;
				background: var(--accent);
				color: white;
				text-decoration: none;
				border-radius: var(--border-radius);
				font-weight: 600;
			}
		</style>

		<script>
			// Show/hide treatment detail field
			const treatmentSelect = document.getElementById('treatment') as HTMLSelectElement;
			const treatmentDetailGroup = document.getElementById('treatment-detail-group') as HTMLElement;

			treatmentSelect?.addEventListener('change', () => {
				treatmentDetailGroup.style.display = treatmentSelect.value === 'yes' ? 'block' : 'none';
			});

			// Form submission
			const bookingForm = document.getElementById('booking-form') as HTMLFormElement;
			const successModal = document.getElementById('success-modal') as HTMLElement;

			bookingForm?.addEventListener('submit', (e) => {
				e.preventDefault();

				// Collect form data
				const formData = new FormData(bookingForm);
				const data: Record<string, any> = {};

				formData.forEach((value, key) => {
					if (data[key]) {
						if (Array.isArray(data[key])) {
							data[key].push(value);
						} else {
							data[key] = [data[key], value];
						}
					} else {
						data[key] = value;
					}
				});

				console.log('Booking form data:', data);

				// TODO: Send to Google Form / Surveycake / Backend
				// For now, just show success modal
				successModal.style.display = 'flex';
			});
		</script>
	</body>
</html>
```

**Step 2: 執行開發伺服器測試預約頁面**

Run: `npm run dev`
瀏覽 `http://localhost:4321/booking`
Expected: 預約表單頁面顯示，可以填寫並送出

**Step 3: Commit**

```bash
git add src/pages/booking.astro
git commit -m "feat: add booking page with consultation form"
```

---

## Task 9: 更新 Header 導航連結

**Files:**
- Modify: `src/components/Header.astro`

**Step 1: 更新社群連結為博士的實際連結**

```astro
---
import { SITE_TITLE } from '../consts';
import HeaderLink from './HeaderLink.astro';
---

<header>
	<nav>
		<h2><a href="/">{SITE_TITLE}</a></h2>
		<div class="internal-links">
			<HeaderLink href="/">首頁</HeaderLink>
			<HeaderLink href="/about">關於博士</HeaderLink>
			<HeaderLink href="/services">專業服務</HeaderLink>
			<HeaderLink href="/success-stories">成功案例</HeaderLink>
			<HeaderLink href="/blog">觀點文章</HeaderLink>
		</div>
		<div class="social-links">
			<a href="https://www.instagram.com/ah_j_journey/" target="_blank" rel="noopener noreferrer">
				<span class="sr-only">Follow on Instagram</span>
				<svg viewBox="0 0 24 24" aria-hidden="true" width="28" height="28">
					<path fill="currentColor" d="M12 2.163c3.204 0 3.584.012 4.85.07 3.252.148 4.771 1.691 4.919 4.919.058 1.265.069 1.645.069 4.849 0 3.205-.012 3.584-.069 4.849-.149 3.225-1.664 4.771-4.919 4.919-1.266.058-1.644.07-4.85.07-3.204 0-3.584-.012-4.849-.07-3.26-.149-4.771-1.699-4.919-4.92-.058-1.265-.07-1.644-.07-4.849 0-3.204.013-3.583.07-4.849.149-3.227 1.664-4.771 4.919-4.919 1.266-.057 1.645-.069 4.849-.069zm0-2.163c-3.259 0-3.667.014-4.947.072-4.358.2-6.78 2.618-6.98 6.98-.059 1.281-.073 1.689-.073 4.948 0 3.259.014 3.668.072 4.948.2 4.358 2.618 6.78 6.98 6.98 1.281.058 1.689.072 4.948.072 3.259 0 3.668-.014 4.948-.072 4.354-.2 6.782-2.618 6.979-6.98.059-1.28.073-1.689.073-4.948 0-3.259-.014-3.667-.072-4.947-.196-4.354-2.617-6.78-6.979-6.98-1.281-.059-1.69-.073-4.949-.073zm0 5.838c-3.403 0-6.162 2.759-6.162 6.162s2.759 6.163 6.162 6.163 6.162-2.759 6.162-6.163c0-3.403-2.759-6.162-6.162-6.162zm0 10.162c-2.209 0-4-1.79-4-4 0-2.209 1.791-4 4-4s4 1.791 4 4c0 2.21-1.791 4-4 4zm6.406-11.845c-.796 0-1.441.645-1.441 1.44s.645 1.44 1.441 1.44c.795 0 1.439-.645 1.439-1.44s-.644-1.44-1.439-1.44z"/>
				</svg>
			</a>
			<a href="https://www.youtube.com/@AugustLin123" target="_blank" rel="noopener noreferrer">
				<span class="sr-only">Subscribe on YouTube</span>
				<svg viewBox="0 0 24 24" aria-hidden="true" width="28" height="28">
					<path fill="currentColor" d="M23.498 6.186a3.016 3.016 0 0 0-2.122-2.136C19.505 3.545 12 3.545 12 3.545s-7.505 0-9.377.505A3.017 3.017 0 0 0 .502 6.186C0 8.07 0 12 0 12s0 3.93.502 5.814a3.016 3.016 0 0 0 2.122 2.136c1.871.505 9.376.505 9.376.505s7.505 0 9.377-.505a3.015 3.015 0 0 0 2.122-2.136C24 15.93 24 12 24 12s0-3.93-.502-5.814zM9.545 15.568V8.432L15.818 12l-6.273 3.568z"/>
				</svg>
			</a>
		</div>
	</nav>
</header>
<style>
	header {
		margin: 0;
		padding: 0 1em;
		background: white;
		box-shadow: 0 2px 8px rgba(var(--black), 5%);
		position: sticky;
		top: 0;
		z-index: 100;
	}
	h2 {
		margin: 0;
		font-size: 1em;
	}

	h2 a,
	h2 a.active {
		text-decoration: none;
		color: var(--accent-dark);
	}
	nav {
		display: flex;
		align-items: center;
		justify-content: space-between;
		max-width: 1100px;
		margin: 0 auto;
	}
	nav a {
		padding: 1em 0.5em;
		color: rgb(var(--gray-dark));
		border-bottom: 4px solid transparent;
		text-decoration: none;
		transition: color var(--transition-fast);
	}
	nav a:hover {
		color: var(--accent);
	}
	nav a.active {
		text-decoration: none;
		border-bottom-color: var(--accent);
	}
	.social-links,
	.social-links a {
		display: flex;
	}
	.social-links a {
		padding: 0.5em;
	}
	.social-links a:hover {
		color: var(--accent);
	}
	@media (max-width: 720px) {
		.social-links {
			display: none;
		}
		.internal-links {
			font-size: 0.9rem;
		}
	}
</style>
```

**Step 2: 執行開發伺服器確認導航**

Run: `npm run dev`
Expected: Header 顯示正確的社群連結（Instagram、YouTube）

**Step 3: Commit**

```bash
git add src/components/Header.astro
git commit -m "feat: update header with sticky nav and real social links"
```

---

## Task 10: 更新 Footer

**Files:**
- Modify: `src/components/Footer.astro`

**Step 1: 更新 Footer 內容**

```astro
---
const today = new Date();
---

<footer>
	<div class="footer-content">
		<div class="footer-brand">
			<p class="brand-name">Dr. August Lin | 代謝財富學</p>
			<p class="brand-tagline">用科學實證，解決身心健康的問題</p>
		</div>
		<div class="footer-links">
			<a href="/quiz">代謝年齡測驗</a>
			<a href="/booking">預約諮詢</a>
			<a href="/about">關於博士</a>
		</div>
		<div class="social-links">
			<a href="https://www.instagram.com/ah_j_journey/" target="_blank" rel="noopener noreferrer">
				<span class="sr-only">Instagram</span>
				<svg viewBox="0 0 24 24" aria-hidden="true" width="24" height="24">
					<path fill="currentColor" d="M12 2.163c3.204 0 3.584.012 4.85.07 3.252.148 4.771 1.691 4.919 4.919.058 1.265.069 1.645.069 4.849 0 3.205-.012 3.584-.069 4.849-.149 3.225-1.664 4.771-4.919 4.919-1.266.058-1.644.07-4.85.07-3.204 0-3.584-.012-4.849-.07-3.26-.149-4.771-1.699-4.919-4.92-.058-1.265-.07-1.644-.07-4.849 0-3.204.013-3.583.07-4.849.149-3.227 1.664-4.771 4.919-4.919 1.266-.057 1.645-.069 4.849-.069zm0-2.163c-3.259 0-3.667.014-4.947.072-4.358.2-6.78 2.618-6.98 6.98-.059 1.281-.073 1.689-.073 4.948 0 3.259.014 3.668.072 4.948.2 4.358 2.618 6.78 6.98 6.98 1.281.058 1.689.072 4.948.072 3.259 0 3.668-.014 4.948-.072 4.354-.2 6.782-2.618 6.979-6.98.059-1.28.073-1.689.073-4.948 0-3.259-.014-3.667-.072-4.947-.196-4.354-2.617-6.78-6.979-6.98-1.281-.059-1.69-.073-4.949-.073zm0 5.838c-3.403 0-6.162 2.759-6.162 6.162s2.759 6.163 6.162 6.163 6.162-2.759 6.162-6.163c0-3.403-2.759-6.162-6.162-6.162zm0 10.162c-2.209 0-4-1.79-4-4 0-2.209 1.791-4 4-4s4 1.791 4 4c0 2.21-1.791 4-4 4zm6.406-11.845c-.796 0-1.441.645-1.441 1.44s.645 1.44 1.441 1.44c.795 0 1.439-.645 1.439-1.44s-.644-1.44-1.439-1.44z"/>
				</svg>
			</a>
			<a href="https://www.youtube.com/@AugustLin123" target="_blank" rel="noopener noreferrer">
				<span class="sr-only">YouTube</span>
				<svg viewBox="0 0 24 24" aria-hidden="true" width="24" height="24">
					<path fill="currentColor" d="M23.498 6.186a3.016 3.016 0 0 0-2.122-2.136C19.505 3.545 12 3.545 12 3.545s-7.505 0-9.377.505A3.017 3.017 0 0 0 .502 6.186C0 8.07 0 12 0 12s0 3.93.502 5.814a3.016 3.016 0 0 0 2.122 2.136c1.871.505 9.376.505 9.376.505s7.505 0 9.377-.505a3.015 3.015 0 0 0 2.122-2.136C24 15.93 24 12 24 12s0-3.93-.502-5.814zM9.545 15.568V8.432L15.818 12l-6.273 3.568z"/>
				</svg>
			</a>
		</div>
	</div>
	<div class="footer-bottom">
		<p>&copy; {today.getFullYear()} Dr. August Lin. All rights reserved.</p>
	</div>
</footer>
<style>
	footer {
		background: rgb(var(--gray-dark));
		color: rgba(255, 255, 255, 0.8);
		padding: 3rem 1rem 1.5rem;
	}

	.footer-content {
		max-width: 1100px;
		margin: 0 auto;
		display: grid;
		grid-template-columns: 1fr;
		gap: 2rem;
		text-align: center;
	}

	@media (min-width: 768px) {
		.footer-content {
			grid-template-columns: 2fr 1fr 1fr;
			text-align: left;
		}
	}

	.brand-name {
		font-weight: 700;
		color: white;
		margin-bottom: 0.25rem;
	}

	.brand-tagline {
		font-size: 0.9rem;
		opacity: 0.8;
	}

	.footer-links {
		display: flex;
		flex-direction: column;
		gap: 0.5rem;
	}

	.footer-links a {
		color: rgba(255, 255, 255, 0.8);
		text-decoration: none;
		transition: color var(--transition-fast);
	}

	.footer-links a:hover {
		color: var(--accent-light);
	}

	.social-links {
		display: flex;
		gap: 1rem;
		justify-content: center;
	}

	@media (min-width: 768px) {
		.social-links {
			justify-content: flex-start;
		}
	}

	.social-links a {
		color: rgba(255, 255, 255, 0.8);
		transition: color var(--transition-fast);
	}

	.social-links a:hover {
		color: var(--accent-light);
	}

	.footer-bottom {
		max-width: 1100px;
		margin: 2rem auto 0;
		padding-top: 1.5rem;
		border-top: 1px solid rgba(255, 255, 255, 0.1);
		text-align: center;
	}

	.footer-bottom p {
		font-size: 0.85rem;
		opacity: 0.6;
		margin: 0;
	}
</style>
```

**Step 2: 執行開發伺服器確認 Footer**

Run: `npm run dev`
Expected: Footer 顯示新設計，包含品牌資訊、連結、社群圖示

**Step 3: Commit**

```bash
git add src/components/Footer.astro
git commit -m "feat: redesign footer with brand info and navigation"
```

---

## Task 11: 更新服務頁面 CTA

**Files:**
- Modify: `src/pages/services/consulting.astro`

**Step 1: 更新 CTA 連結與新增次要 CTA**

找到檔案中的 CTA 區塊，將 `cal.com/vista` 改為 `/booking`，並新增次要 CTA：

在 `lp-hero` section 中：
```html
<a href="/booking" class="cta-btn">預約免費代謝健康評估</a>
```

在底部 CTA section 中：
```html
<section class="section" style="text-align: center;">
    <h2 class="section-title">你的健康資產，值得最好的投資</h2>
    <p style="margin-bottom: 2rem; font-size: 1.2rem;">每天 15 分鐘的投入，換來未來 30 年的自由。<br/>立即預約 30 分鐘免費諮詢，讓我們聊聊你的健康目標。</p>
    <div class="cta-group">
        <a href="/booking" class="cta-btn">填寫預約表單，申請免費諮詢</a>
        <a href="/quiz" class="cta-btn secondary">還沒準備好？先做代謝年齡測驗 →</a>
    </div>
</section>
```

並在 `<style>` 區塊新增：
```css
.cta-group {
    display: flex;
    flex-direction: column;
    gap: 1rem;
    align-items: center;
}

@media (min-width: 480px) {
    .cta-group {
        flex-direction: row;
        justify-content: center;
    }
}

.cta-btn.secondary {
    background: transparent;
    border: 2px solid var(--accent);
    color: var(--accent);
}

.cta-btn.secondary:hover {
    background: var(--accent);
    color: white;
}
```

**Step 2: 執行開發伺服器確認服務頁面**

Run: `npm run dev`
瀏覽 `/services/consulting`
Expected: CTA 連結到 /booking，底部有雙 CTA

**Step 3: Commit**

```bash
git add src/pages/services/consulting.astro
git commit -m "feat: update consulting page CTAs to link to booking and quiz"
```

---

## Task 12: Build 測試與最終確認

**Step 1: 執行 Production Build**

Run: `cd /Users/vista/Documents/My_AI_Projects/august-official-site && npm run build`
Expected: 編譯成功，無錯誤

**Step 2: 執行 Preview**

Run: `npm run preview`
Expected: 可以在 `http://localhost:4321` 預覽完整網站

**Step 3: 最終 Commit**

```bash
git add -A
git commit -m "chore: phase 1 complete - homepage redesign, quiz, booking"
```

---

## 完成檢核清單

- [ ] 全域 CSS 變數更新
- [ ] SocialProof 元件建立
- [ ] VideoGallery 元件建立
- [ ] QuizCTA 元件建立
- [ ] 首頁改版完成
- [ ] 測驗頁面完成
- [ ] 測驗結果頁面完成
- [ ] 預約諮詢頁面完成
- [ ] Header 導航更新
- [ ] Footer 重新設計
- [ ] 服務頁面 CTA 更新
- [ ] Production Build 成功
