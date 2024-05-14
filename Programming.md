## 對於程式設計的看法
&emsp;&emsp;在程式設計中，管理與技術都是相當重要的，不過在個人的觀點中，尤其管理更為重要，雖然軟體工程師是技術含量高的職業，
但是技術日新月異，今日是最新的技術可能明年就被更新的技術取代掉，這點在前端尤其明顯，程式應在能被管理的情境下持續迭代更新，
maven的依賴管理就是屬於這種類型，舉例:在ShardingSphere-JDBC 5.1.2版本中特別依賴snakeyaml 1.33版本，
造成與spring boot 2預設依賴snakeyaml 1.30版本不相容。  

&emsp;&emsp;在設計層面上，設計應偏向抽象，而實作上則偏向細節，程式的世界就是想像的世界，沒法充分想像的，就沒辦法實現其內容，因此撰寫程式初期上，
先用抽象的概念把大致上的流程定義出來，當然一開始定義的流程可能有缺陷，不過可以在實作上再回來調整，基本上流程的變化不如細節的變化那麼大，
因此日後在調整上僅需異動實作細節，而抽象的流程仍維持一致，所以如果一開始使用細節去撰寫設計程式日後在調整上會失去一些彈性，
舉例:在設計快取時，如果直接操作jedis作為快取的實作，而不是額外建立快取的抽象物件，日後要跟換jedis元件時，改動範圍很廣。

## 對命名的省思
目前常見的程式主要以mvc、分層式架構為主流，其中業務邏輯層常以xxxService命名，就個人的經驗來看可能會遇到以下缺點:
1. 每個人對於服務的認知不一致  
   &emsp;&emsp;今天需要製作一個玩家登入的功能，就有可能出現PlayerService的login與GameService的login兩種服務的可能，
在不同工程師之間出現意見分歧的情況，在多次人員迭代後，服務與剛建立的概念相差盛遠。
2. 服務之間的呼叫可能存在重複的步驟  
   &emsp;&emsp;服務都有可能成為api呼叫的對象，因此會有檢查api的請求，然而服務的複用是相當常見的，也造成重複檢查的情況出現。
3. 與日常表達方式相距甚遠  
   &emsp;&emsp;在平常討論功能時，並不會用服務的方式來說明，像是玩家登入遊戲，而不是遊戲/玩家服務登入遊戲。

## 可能是更好的方法
&emsp;&emsp;以往在了解程式內容前，可能會先查看流程圖來快速了解業務流程，間接代表程式依賴文件來了解業務，因此如何讓程式碼文件化成為相當耐人尋味的議題，
個人推薦直接用流程圖會使用的角色名稱作為類別命名，如玩家就直接命名成Player，這邊可能會與不少人對資料表映射的POJO的命名衝突，
個人建議類別的命名應區分資料與物件，其中物件代表具有行為的事物，而資料代表物件具體的屬性，資料庫的欄位則屬於這一類，即人類就算沒有名字，
也會有進食的行為，人類屬於物件；名字屬於資料，然後因為資料的總類繁多，對於資料的命名是適合加上前綴或後綴來表達意圖，
如Request、Response、Result、Entity...等等，為了達到程式碼文件化這個目的，如何讓程式能夠清晰地顯示程式的流程就顯得相當重要，
了解在一個請求發送前，所使用物件的類型基本情況上是固定的，這時可搭配設計模式使用讓程式邏輯做分流的動作，達到清晰流程及降低邏輯複雜度的目的。

## 物件依賴關係
&emsp;&emsp;分為主動的物件與被動物件  
主動物件: 用戶、寵物  
被動物件: 錢包  
主動-被動關係: 用戶 -使用-> 錢包  
主動-主動關係: 用戶 -呼叫-> 寵物、寵物 -呼叫-> 用戶，產生循環依賴  
解除依賴方式:排程、發布事件、中介者