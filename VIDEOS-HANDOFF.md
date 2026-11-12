# 每課影片（🎵 兒歌繪本、📺 課本影片）維護說明

2026-11-12 建。給未來的 Christina 和 Claude 看，說明影片是怎麼接上每一課的、怎麼補、怎麼換。

## 1. 現況

- 49 個主題、約 330 部影片，138 課中 133 課有按鈕。
- 沒按鈕的 5 課（HL B4 L6、HL B5 L6、HL B6 L2、L3、補充）是文章式的課，沒有兒童影片可配，不用硬補。
- 全部資料都在 `index.html` 的三個物件裡，沒有別的檔案：

| 物件 | 內容 | 按鈕 |
|---|---|---|
| `TOPICS` | 主題 → 影片清單 | 🎵 主題名（紫色） |
| `TOPIC_OF` | 「冊代碼\|課名」→ 主題 id（可多個） | 同上，一課可以有好幾顆 |
| `PUB_VIDEO` | 「冊代碼\|課名」→ 出版社官方影片或播放清單 | 📺 課本影片／課本歌曲／教學補充（橘色） |

## 2. 按鈕怎麼運作

- 🎵 點了進站內的影片頁 `#主題/<id>/<冊代碼>/<課名>`，用 YouTube 嵌入播放器把該主題的影片串成一串自動連播（`embed/<第一部>?playlist=<其餘>`），底下縮圖可以點著跳，最後列「這些課也用得到」。
- 星期那個主題用的是 Christina 自己的 YouTube 播放清單（`yt:` 欄位），其他主題都是我們自己串的，**不需要她的 YouTube 帳號**。
- 📺 點了直接開 YouTube 新分頁。id 是 PL 開頭就開播放清單，否則開單支影片。
- 沒列在 `TOPIC_OF`／`PUB_VIDEO` 的課就不會有按鈕，不用另外關。

## 3. 怎麼改

**換掉一部影片**：在 `TOPICS` 找到那一行 `["YouTube id","片名","頻道","標籤"]`，整個換掉。標籤只是顯示用（兒歌／繪本／單字／對話／故事／拼讀／講解），隨便寫。

**加一個主題**：在 `TOPICS` 加一個 key（英文小寫），填 `e`（emoji）、`name`、`en`、`videos`；再到 `TOPIC_OF` 把用得到的課指過來。課名要跟 `BOOKS` 裡的一模一樣（`Lesson 1`、`Unit 1`、`補充`）。

**新冊加進來時**：`rebuild_books.py apply` 只會重寫 `BOOKS`，不會動這三個物件。新冊的課要手動到 `TOPIC_OF` 補對應（參考 `D:\工作\工具\audio_helper\batch_2026_10\主題對照表.md`），何嘉仁和康軒國中的課再到 `PUB_VIDEO` 補官方影片。

**改完一定要跑的檢查**（在 lesson-words 資料夾）：

```bash
node "D:/工作/工具/audio_helper/yt_tools/nobtn.js"
```

會印出主題數、影片數、`TOPIC_OF` 有沒有打錯課名或主題 id，以及哪些課還沒按鈕。

## 4. 怎麼找影片（不用 YouTube API）

腳本在 `D:\工作\工具\audio_helper\yt_tools\`，都是抓 YouTube 網頁裡的 `ytInitialData`，不用金鑰：

```bash
python -I yt_search.py "numbers song kids super simple" "Ten Black Dots read aloud"
```
印出 影片id | 片名 | 頻道 | 觀看數，每個關鍵字前 7 筆。

```bash
python -I yt_info.py xNw1SSz18Gg TdDypyS_5zE
```
查長度、片名、頻道、能不能播（OK）。**選好的影片一定要跑這個**，淘汰超過 10 分鐘的合輯和已下架的。

```bash
python -I yt_playlist.py PLD_3F4w1Rp_v4XBW79al8a14e1VVD86A5
python -I yt_handle.py knshjuniorenglish videos playlists
```
列播放清單內容；列某個頻道的影片和播放清單。私人播放清單看不到（會顯示「does not exist」）。

## 5. 挑片標準（照目前的做法）

1. 兒歌優先找官方頻道：Super Simple Songs、Noodle & Pals、Maple Leaf Learning、Dream English Kids、The Singing Walrus、Pinkfong、ELF Learning、Fun Kids English、English Singsing、Planet Pop。不會被下架，單字跟課本重疊高。
2. 繪本朗讀：Eric Carle 官方、Vooks、Penguin、Brightly 最穩；其他老師頻道挑觀看數高、放很久的（Buddy Son Storytime、Bri Reads、Miss Pam Reads、Read Aloud with Mr. Paul）。
3. 中高年級主題兒歌少，用 English Singsing 的 Kids Vocab（單字）、Easy Dialogue（對話短劇）、Theme（故事）補。
4. 每個主題 6–8 部，4 分鐘內為主，不放 10 分鐘以上的合輯。
5. Christina 自己 YouTube 上的公開清單可以直接抽：英語啟蒙52週（打招呼、家人、動物、水果、蔬菜、身體、心情、玩具）、週三英樂繪、字母繪本、Christmas Songs、想像力。她另有 10 個私人清單看不到。
6. 比較弱、以後可以再找的主題：量詞、頻率副詞、選舉、3C 科技、科目、飲料、中秋節（英文資源本來就少）。

## 6. 出版社官方影片

| 出版社 | 頻道 | 有什麼 | 已接上 |
|---|---|---|---|
| 何嘉仁 | HESS Super Fun 影音專區 @hess_superfun | Super Fun 1–10 每課一支外師教學影片（3–6 分） | SF B1/B3/B5/B7 全部課 |
| 康軒國小 | KNSH Boka | 只有兩首 WW 短歌、其餘是宣傳片 | WW B1 L3、L4 |
| 康軒國中 | 康軒國中英語教學補充 @knshjuniorenglish | B1–B6 每課一個播放清單（7–13 部補充影片） | KS B1/B3/B5 全部課 |
| 翰林國小 | 翰林國小英語 | 只有教材介紹 | 無 |
| 翰林國中 | 希平方 × 翰林出版 | 7上–9下每課 Dialogue 與 Reading 動畫（1–3 分）。2021-09／2022-02 上傳的是 108 課綱版；同一課有多支時挑最新的，標「(old)」「(103)」「(106 修訂)」的是舊課綱不要用 | HL B1–B6 每課（B2 L3–L5 只有 2020 版；B6 L3、L4 只有 Reading） |
| 佳音 | — | 沒有官方課文影片 | 無 |
| 南一國中 | 南一國中英語 | 課本音軌 | 沒收南一，無 |

何嘉仁 SF 2/4/6/8/9/10、康軒國中 B2/B4/B6 的影片都在，等那幾冊的字表做好再補 `PUB_VIDEO`。

## 7. Christina 的決定（不要再問）

- 入口只有課卡上的按鈕，**不做主題總覽分頁**，影片推薦網站也不放（家長不會按主題找）。
- 按鈕上不寫出版社名字，用「課本影片／課本歌曲／教學補充」。
- **不要**康軒國中教學趣的段考複習影片。
- 第二批以後的影片她沒逐一看過，說「有問題以後再說」，所以她回報哪一部不好就直接換。
- 她不會在 YouTube 存播放清單，所以用站內嵌入串播；要給她在 YouTube 開的連結可以用 `https://www.youtube.com/watch_videos?video_ids=id1,id2,...`（臨時清單，不能命名）。
