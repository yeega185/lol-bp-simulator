# LOL Ban/Pick Simulator

## 專案概要
LOL Ban/Pick Simulator 是一個以 HTML 與原生 JavaScript 打造的英雄聯盟選角模擬器。介面採用 Tailwind CSS CDN 並結合電競風格的視覺設計，支援系列賽的 Ban/Pick 流程、戰隊與選手資料檢視，以及台港澳賽區常用的繁體中文英雄名稱。

專案提供兩個入口頁面：

- `index.html`：主模擬器頁面，包含系列賽流程、戰隊/選手資訊與完整的英雄挑選互動。
- `index_full_champs.html`：主要用於快速檢視全英雄列表的精簡版本，沿用相同的動態載入與互動邏輯。

## 整體架構
前端由單一頁面應用 (SPA-like) 組成，主要分為以下模組：

1. **UI Layout**：透過 Tailwind CSS 定義的格線與元件樣式，負責主畫面的結構 (Ban/Pick 卡片、系列歷史、選手資訊、英雄列表等)。
2. **狀態管理**：在 `<script>` 區塊內以原生 JavaScript 物件維護系列賽分數、當前局勢 (`currentBPState`)、已選英雄集合，以及系列歷史紀錄。
3. **資料載入流程**：
   - `loadChampionRoster()` 會在頁面啟動時向 Riot Data Dragon 取得最新版本與英雄列表，並以 zh_TW 在地化名稱排序。載入結果更新至 `champions` 狀態後觸發畫面重繪。
   - `loadPlayerStats()` 讀取本地 `data/player_stats.json`，根據賽區與戰隊輸出選手卡片與指標。
4. **互動邏輯**：多個事件監聽器控制 Ban/Pick 流程、搜尋與篩選、系列賽分數調整、Modal 提示等功能，確保在資料尚未載入完成之前禁用互動並顯示狀態訊息。
5. **渲染函式**：`renderChampions`、`renderDraftBoard`、`renderSeriesHistory` 等函式負責將狀態轉換成 DOM 元素，並在資料或流程更新時重新繪製畫面。

此架構讓整個應用能在純靜態環境中部署 (如 GitHub Pages)，同時維持即時的英雄資料與基礎的錯誤處理。

## 資料來源
專案的資料取得分為兩大類：

### 英雄資料
- **來源**：Riot Games Data Dragon API。
- **版本資訊**：請求 `https://ddragon.leagueoflegends.com/api/versions.json`，取得最新版號 (例如 `14.19.1`)。
- **英雄列表**：依版本向 `https://ddragon.leagueoflegends.com/cdn/<version>/data/zh_TW/champion.json` 發送請求，取得台港澳賽區使用的繁體中文英雄名稱與 ID。
- **使用方式**：讀取結果後以 `name` 欄位排序，並透過 `https://ddragon.leagueoflegends.com/cdn/<version>/img/champion/<ChampionId>.png` 顯示對應的英雄頭像。

### 戰隊與選手資料
- **來源**：儲存在倉庫的 `data/player_stats.json`。
- **格式**：外層為賽區陣列，每個賽區包含 `region`、`displayName`、`teams` 等欄位；隊伍則定義 `code`、`displayName`、`record`、`players` (內含 `name`、`role`、`signatureChamps`、`pickRate` 等欄位)。
- **更新方式**：手動編輯 JSON 後重新部署即可，前端在載入失敗時會顯示錯誤訊息並阻止後續互動。

透過上述設計，應用能自動維持最新的英雄名單，同時保留離線管理的賽區/選手資料，滿足台港澳使用者的需求。
