# 🎵 Web Music Score Editor & Player (VexFlow + Tone.js)

這是一個基於 TypeScript 開發的網頁互動寫譜與播放器。結合了 VexFlow 的專業樂譜渲染能力與 Tone.js 的強大網頁音效引擎，實現「所見即所得」的線上編曲體驗。

## ✨ 特色功能

- **動態樂譜渲染**：使用 VexFlow 在網頁上精準繪製標準五線譜、譜號與音符。
- **即時音訊播放**：整合 Tone.js 合成器，完美將視覺樂譜轉化為真實音訊。
- **TypeScript 驅動**：嚴格的型別定義（Type Safety），確保音符資料在渲染與播放間的一致性。
- **純前端實作**：無需後端支援，全面利用 Web Audio API 與 SVG 技術。

## 🛠 技術棧

- 開發語言: TypeScript
- 樂譜渲染: VexFlow (v4.x+)
- 音訊引擎: Tone.js (v14.x+)
- 建置工具: Vite / Webpack (推薦使用 Vite 進行快速開發)

## 📦 安裝與準備工作

1. 複製專案
   ```
   git clone https://github.com
   cd your-repo-name
   ```
   
2. 安裝依賴套件
   ```
   npm install
   # 或者使用 yarn / pnpm
   # yarn install
   # pnpm install
   ```
   
3. 安裝核心音樂庫
   ```
   npm install vexflow tone
   ```
   
## 🚀 核心架構與資料結構

本專案的核心邏輯在於**使用單一結構化資料（JSON）同時驅動視覺與聽覺**：
```
interface NoteData {
  key: string;      // VexFlow 格式 (例如: "c/4" 代表中央 C)
  toneKey: string;  // Tone.js 格式 (例如: "C4")
  duration: string; // VexFlow 拍子記號 (q = 四分音符, h = 二分音符)
  toneTime: string; // Tone.js 拍子記號 (4n = 四分音符, 2n = 二分音符)
}
```

## 💻 快速開始 (Quick Start)

### 1. HTML 結構 (`index.html`)
```
<div id="score-container"></div>
<button id="play-btn">▶️ 播放樂譜</button>
```

### 2. TypeScript 主邏輯 (`src/main.ts`)
```
import { Renderer, Stave, StaveNote, Voice, Formatter } from 'vexflow';
import * as Tone from 'tone';

// 1. 初始化曲譜資料
const myScore: NoteData[] = [
  { key: "c/4", toneKey: "C4", duration: "q", toneTime: "4n" },
  { key: "e/4", toneKey: "E4", duration: "q", toneTime: "4n" },
  { key: "g/4", toneKey: "G4", duration: "h", toneTime: "2n" }
];

// 2. 渲染樂譜 (VexFlow)
function renderScore(containerId: string, data: NoteData[]) {
  const div = document.getElementById(containerId)!;
  div.innerHTML = ''; // 清空舊畫布
  
  const renderer = new Renderer(div, Renderer.Backends.SVG);
  renderer.resize(500, 200);
  const context = renderer.getContext();
  
  const stave = new Stave(10, 40, 400).addClef("treble").addTimeSignature("4/4");
  stave.setContext(context).draw();

  const vexNotes = data.map(n => new StaveNote({ keys: [n.key], duration: n.duration }));
  const voice = new Voice({ num_beats: 4, beat_value: 4 }).addTickables(vexNotes);
  
  new Formatter().joinVoices([voice]).format([voice], 350);
  voice.draw(context, stave);
}

// 3. 播放音訊 (Tone.js)
async function playScore(data: NoteData[]) {
  await Tone.start(); // 解鎖瀏覽器音訊限制
  const synth = new Tone.Synth().toDestination();
  let currentTime = Tone.now();

  data.forEach(note => {
    synth.triggerAttackRelease(note.toneKey, note.toneTime, currentTime);
    currentTime += Tone.Time(note.toneTime).toSeconds();
  });
}

// 4. 綁定事件
document.addEventListener("DOMContentLoaded", () => {
  renderScore("score-container", myScore);
  document.getElementById("play-btn")?.addEventListener("click", () => playScore(myScore));
});
```

## 🗺 擴充計畫 (Roadmap)

- [ ] **互動式寫譜**：增加網頁虛擬鋼琴鍵盤，用家點擊即可將音符新增至五線譜中。
- [ ] **動態游標跟隨**：播放音樂時，網頁畫面上會有光標即時跟隨當前播放的音符。
- [ ] **多聲部與和弦**：支援多個音符同時發聲與繪製複音。
- [ ] **匯出功能**：支援將用家創作的樂譜匯出為 MIDI 檔案或 MusicXML。

## 📄 授權條款

本專案採用 [MIT License](LICENSE) 授權。
