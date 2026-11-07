# elderly-hand-reaction-game
C:\hermes\bone-public\index.html
C:\hermes\bone-public\README.md
C:\hermes\bone-public\assets
C:\hermes\bone-public\audio
剪刀石頭布｜長輩手部反應訓練遊戲
系統流程、軟體架構與部署說明

公開網址：https://elderly-hand-reaction-game.vercel.app

1. 系統概述
這是一個以瀏覽器執行的長輩手部反應訓練遊戲。電腦會隨機出現剪刀、石頭或布，長輩需要觀察、思考並用手勢做出可以獲勝的動作。系統透過攝影機與 MediaPipe Hand Landmarker 辨識手部，並在畫面上繪製綠色手部骨架。

主要目標
訓練長輩的手指與手掌活動。
訓練簡單判斷與反應速度。
以大字體、高對比、滿版畫面降低操作負擔。
不保存或上傳攝影機影像與聲音。
2. 使用者遊戲流程

否

是

否

是

按下開始遊戲

允許瀏覽器使用攝影機

載入 MediaPipe 手部模型

電腦隨機出剪刀／石頭／布

長輩觀察並自己思考

用手勢做出答案

手勢穩定保持 2 秒?

記錄玩家手勢，不立即公布結果

等待本回合 7 秒倒數結束

公布答對／完成與得分

結果停留 2 秒

是否完成 5 回合?

顯示本局總分與重新開始

回合範例
電腦出：布
長輩出：剪刀

手勢保持 2 秒
↓
繼續等待倒數結束
↓
答對了！+100 分
↓
結果停留 2 秒
↓
顯示下一回合
3. 遊戲規則
項目	設定
每局回合數	5 回合
每回合反應時間	7 秒
手勢保持時間	2 秒
答對得分	+100 分
完成但答錯	+20 分
超時	顯示提醒並進入下一回合
結果公布時間	倒數結束後立即公布
下一回合	結果顯示 2 秒後開始
正確答案提示	不在答錯時顯示，讓長輩自己思考
4. 剪刀石頭布邏輯

電腦：石頭

玩家應出：布

電腦：布

玩家應出：剪刀

電腦：剪刀

玩家應出：石頭

系統只會在倒數結束時公布結果，避免長輩一做出手勢就立刻知道答案是否正確。

5. 系統架構

長輩玩家
攝影機前做手勢

Vercel HTTPS 靜態網站

瀏覽器遊戲 UI
大字體／滿版／提示／得分

遊戲狀態機
5 回合／7 秒倒數／結果等待

剪刀石頭布規則
答對 +100／完成 +20

getUserMedia
瀏覽器攝影機 API

MediaPipe Hand Landmarker
21 點手部座標／2 hands 設定

手勢分類器
石頭／布／剪刀

Canvas 2D
綠色關節點與骨架

Web Audio／SpeechSynthesis
音效與中文語音

無後端／無資料庫
不保存影像與聲音

6. 元件責任
元件	責任	主要技術
靜態網站部署	提供公開 HTTPS 網址與網頁檔案	Vercel Static Hosting
遊戲 UI	顯示電腦出拳、倒數、得分、回合與提示	HTML／CSS
回合控制器	管理開始、回合、等待結果與下一回合	JavaScript State Machine
攝影機模組	取得攝影機影像，並在不相容時自動 fallback	navigator.mediaDevices.getUserMedia
手部辨識	取得手部 21 個 landmarks	MediaPipe Hand Landmarker
手勢分類器	依手指伸展狀態判斷石頭、布、剪刀	JavaScript 幾何判斷
綠色骨架	繪製手腕、掌指與指節連接線	Canvas 2D
音效與語音	播放提示音與中文語音	Web Audio／SpeechSynthesis
資料儲存	目前不保存遊戲資料、影像或聲音	無資料庫／無後端
7. 回合狀態機

按開始／允許攝影機

模型載入完成

手勢穩定並保持 2 秒

7 秒超時

7 秒倒數結束

結果顯示 2 秒且未完成 5 回合

完成第 5 回合

重新開始

IDLE

CAMERA_LOADING

RESPONDING

WAITING_RESULT

RESULT

FINISHED

8. 資料流與判斷邏輯
瀏覽器透過 getUserMedia 取得攝影機串流。
MediaPipe Hand Landmarker 從影像中取得手部 21 點座標。
系統選取畫面中較清楚的手部進行遊戲判斷。
分析食指、中指、無名指與小指的伸展狀態。
四指大多伸展：判定為布。
食指與中指伸展、其他手指收起：判定為剪刀。
其他穩定握拳狀態：判定為石頭。
玩家手勢保持 2 秒後先記錄，不立刻公布結果。
7 秒倒數結束後，才比較電腦手勢與玩家手勢。
所有處理在使用者瀏覽器內完成，影像不傳送到 Vercel。
9. 長輩友善設計
使用大字體、大型按鈕與高對比色彩。
支援滿版模式。
滿版時得分與回合固定在右上角，動作提示放在中央下方，避免重疊。
綠色手部骨架讓長輩知道系統是否看到手掌。
手勢需要保持 2 秒，避免因短暫誤判立即計分。
結果等倒數結束才公布，讓長輩完成思考與反應。
答錯時不顯示正確答案，保留遊戲思考性。
提供音效與中文語音提示，也可以關閉音效。
攝影機設定不相容時，自動使用較寬鬆設定重試。
10. 隱私與安全
不需要登入。
不使用資料庫。
不保存影像。
不保存聲音。
攝影機只在遊戲頁面開啟時使用。
使用 HTTPS 公開網址，瀏覽器才能正常請求攝影機權限。
使用者第一次開啟時必須自行允許攝影機權限。
11. 部署架構

C:\\hermes\\bone\\index.html

C:\\hermes\\bone-public

Vercel Production Deployment

https://elderly-hand-reaction-game.vercel.app

其他電腦／平板／手機瀏覽器

使用者自己的攝影機

目前公開網址
https://elderly-hand-reaction-game.vercel.app

本機來源
C:\hermes\bone\index.html
更新流程
修改 C:\hermes\bone\index.html。
將最新檔案複製到 C:\hermes\bone-public\。
使用 Vercel production deploy 更新相同專案。
重新整理公開網址，必要時按 Ctrl + F5。
12. 後續可擴充功能
簡單、一般、挑戰三種難度。
左手、右手與雙手交替訓練。
調整回合數、反應時間與保持時間。
將匿名分數與反應時間保存到瀏覽器 localStorage。
增加握拳、張掌、手指伸展等暖身模式。
增加照護者設定頁面。
若未來需要跨裝置同步，再評估後端與資料庫。
13. 注意事項
本遊戲定位為娛樂與輕度手部活動，不是醫療或復健診斷工具。若使用者出現手部、手腕或關節疼痛，應停止遊戲，並依照醫療或物理治療專業人員建議調整動作。
