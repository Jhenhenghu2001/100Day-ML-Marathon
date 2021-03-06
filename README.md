# ML100課程筆記

- 以下資料整理自ML100的線上課程，絕大部分的都是資料都是課程教材，但在特徵工程的環節由於原始教材的邏輯較亂，因此我有調整內容與順序。

- 我不建議直接把這份資料拿來讀，但可以作為建模時的工具書，可以快速瀏覽流程、技巧與注意事項。

[TOC]
# 資料清理與數據前處理

> 以滾動方式進行資料清理與探索性分析

## 資料介紹與評估指標

> 準備進入資料科學領域的概念與流程與關鍵

### 學習路徑

1. **找到問題**：挑⼀個有趣的問題, 並從解決⼀個簡單的問題開始
2. **初探**：在這個題⽬上做⼀個原型解決⽅案 (prototype solution)
3. **改進**：試圖改進你的原始解決⽅案並從中學習 (如代碼優化、速度優化、演算法優化)
4. **分享**：紀錄是⼀個好習慣，試著紀錄並分享你的解決⽅案歷程
5. **練習**：不斷在⼀系列不同的問題上反覆練習
6. **實戰**：認真地參與⼀場比賽

### ⾸次⾯對資料，我們應該思考哪些問題？

當我們首次面對專案/資料，再開始動手分析之前，一定要拿以下幾個問題反覆問自己：

- **為什麼這個問題重要？**
  - 好玩：預測⽣存 (吃雞) 遊戲誰可以活得久, [PUBG](https://www.kaggle.com/c/pubg-finish-placement-prediction)
- 企業核⼼問題：⽤⼾廣告投放, [ADPC](https://www.kaggle.com/c/avito-demand-prediction)
  - 公眾利益 / 影響政策⽅向：[停⾞⽅針](https://www.kaggle.com/new-york-city/nyc-parking-tickets/home), [計程⾞載客優化](https://www.kaggle.com/c/nyc-taxi-trip-duration)
  - 對世界很有貢獻：[肺炎偵測](https://www.kaggle.com/c/rsna-pneumonia-detection-challenge)
  
- **資料從何⽽來？**
  - 來源與品質息息相關， 根據不同資料源，我們可以合理的推測/懷疑異常資料異常的理由與頻率
  - 資料來源如：網站流量、購物⾞紀錄、網路爬蟲、格式化表單、[Crowdsourcing](https://en.wikipedia.org/wiki/Crowdsourcing)、紙本轉電⼦檔

- **資料的型態是什麼?**
  - 結構化資料：檢視欄位意義以及名稱，如數值, 表格,...etc 
  - 非結構化資料：需要思考資料轉換與標準化⽅式（如圖像、影片、⽂字、⾳訊…etc）

- **要回答什麼問題?**
  - 每個問題都應該要可以被評估、被驗證，常⾒的[衡量指標](https://blog.csdn.net/aws3217150/article/details/50479457)如：
     - 分類：Accuracy, AUC, MAP, F1-score, ...etc

     - 迴歸：MAE, RMSE, R-Square, ...etc

### 範例：我們應該要/可以回答什麼問題？

- ⽣存 (吃雞) 遊戲
  - 玩家排名：平均絕對誤差 (Mean Absolute Error, MAE)
  - 怎麼樣的⼈通常活得久/不久 (如加入遊戲的時間、開始地點、單位時間內取得的資源量, ...) → 玩家在⼀場遊戲中的存活時間：迴歸 (Mean Squared Error, MSE)
- 廣告投放
  - 不同時間點的客群樣貌如何 → 廣告點擊預測 → 預測哪些受眾會點擊或⾏動：Accuracy / Receiver Operating Curve, ROC
  - 哪些素材很好/不好 → 廣告點擊預測 → 預測在版⾯上的哪個廣告會被點擊：ROC / MAP@N (eg. MAP@5, MAP@12)

### 重要知識點複習

- 初入資料科學的探索流程

  找到問題 → 初探 → 改進 → 分享 → 練習 → 實戰

- ⾯對問題需要思考的關鍵點

  - 為什麼這個問題重要
  - 資料從何⽽來
  - 資料的型態是什麼
  - 回答問題的關鍵指標是什麼

### 參考資料

- [Data Scientist vs Data Engineer](https://www.datacamp.com/community/blog/data-scientist-vs-data-engineer)
- [Data Scientist、Data Analyst、Data Engineer 的区别是什么?](https://www.zhihu.com/question/23946233)
- [Why Data Scientists Must Focus on Developing Product Sense](https://www.kdnuggets.com/2018/04/data-scientists-product-sense.html)
- [Why so many data scientists are leaving their jobs](https://www.kdnuggets.com/2018/04/why-data-scientists-leaving-jobs.html)



## 讀取資料

### 資料簡介：房貸風險預測

[Home Credit Default Risk](https://www.kaggle.com/c/home-credit-default-risk)

- 為什麼這個問題重要？

  - 許多⼈因為沒有信⽤歷史， 所以沒辦法申請貸款

     → 這群⼈常會轉向風險較⾼的放款者

     → 可能導致這群⼈的⽣活狀況更糟

     → 如果這群⼈可以接受正向的幫助，他們將能步入良好正常⽣活

  - Home Credit 想透過放寬貸款條件，提供給這群⼈可以有好的借貸經驗

     → 但即使放寬貸款條件，公司仍不能接受嚴重呆帳 (未還款) 發⽣

     → 預測還款能⼒，讓公司可以在放寬貸款條件下，仍不致有貸給無法還債者。

- 資料從何⽽來？

  - 信⽤局 (Credit Bureau) 調閱紀錄、 Home Credit 內部紀錄 (如過去借貸狀況、信⽤卡狀況)

- 資料的型態是什麼？

  - 皆為結構化資料：數值、類別資料

- 我們可以回答什麼問題 ？問題 : 指標

  - [Evaluation](https://www.kaggle.com/c/home-credit-default-risk) 分類問題, 預測各個客⼾ ID 是否會還款，以還款機率 (0 ~ 1) 作為最終輸出。以 [Area Under the ROC curve](https://zh.wikipedia.org/wiki/ROC%E6%9B%B2%E7%BA%BF) 評估

### 資料的樣子是什麼？

- 我們有多少資料
  - 多少個資料來源？資料的格式是什麼？資料之間關係是什麼？
  - 資料欄位的意義？每⼀ row 的意義？
  - 仔細閱讀 Kaggle 上提供的[資料說明](https://www.kaggle.com/c/home-credit-default-risk/data)
- 你會遇到很多具體的問題
  - 怎麼讀資料？在 Python 做資料前處理，我們第⼀步就是引入常⽤的套件
    - [pandas](https://pandas.pydata.org/) : ⽤於讀取以及管理資料
    - [numpy](http://www.numpy.org/) : ⽤於數學函數的運算
  - 有多少筆資料？有多少個欄位？
  - 有沒有遺失值等等

> 這些問題的本質其實是在了解資料，我們稱為「探索式資料分析」(Exploratory Data Analysis)

### 什麼是EDA？

- 初步透過視覺化/統計⼯具進⾏分析，達到三個主要⽬的
  - 了解資料：獲取資料所包含的資訊、結構和特點
  - 發現 outliers 或異常數值：檢查資料是否有誤
  - 分析各變數間的關聯性：找出重要的變數
- 從 EDA 的過程中觀察現象，檢查資料是否符合分析前的假設
  - 可以在模型建立之前，先發現潛在的錯誤
  - 也可以根據 EDA 的結果來調整分析的⽅向

### 參考資料

- [探索式資料分析簡介](http://www.hmwu.idv.tw/web/R_AI/AI-D3-1-hmwu_R_EDA.pdf)
- [What is Exploratory Data Analysis?](https://towardsdatascience.com/exploratory-data-analysis-8fc1cb20fd15)



## 如何新建一個 DataFrame

> 了解如何快速驗證 dataframe 操作的程式碼

### 為什麼新建⼀個 DataFrame 重要？

- 需要把分析過程中所產⽣的數據或者結果儲存為結構化的資料
  - Ex 1: 將每筆交易資料匯總計算平均值、標準差等統計數值
  - Ex 2: Kaggle 比賽要上傳的結果
- 測試程式碼
  - 有時候原始資料太⼤了，有些資料的操作很費時，先在具有同樣結構的資料上測試程式碼是否能夠得到理想中的結果。
  - 不確定視覺化程式碼中所需要的資料結構，⽤新建立的dataframe 結構來去了解，⽽不是急著在原始資料上操作。

### 重要知識點複習

- 在資料量很⼤時，可以先在和資料具有同樣結構的⼩樣本驗證程式碼執⾏的結果是否符合預期
- ⽤ pd.DataFrame 來創建⼀個 dataframe
- ⽤ np.random.randint 來產⽣隨機數值



## 如何讀取資料？

> 學會如何讀取其他資料格式：txt / jpg / png / json / mat / npy / pkl ...

### 讀取資料的格式與範例

- ⽂本 (txt) 

  ```python
  with open('example.txt', 'r') as f:
      data = f.readlines() 
  ```

- Json

  ```python
  import jsonwith open('example.json', 'r') as f:
      data = json.load(f) 
  ```

- 矩陣檔 (mat) 

  ```python
  import scipy.io as sio
  data = sio.loadmat('example.mat')
  ```

- 圖像檔 (PNG / JPG …) 

  ```python
  image = cv2.imread(...) # 注意 cv2 會以 BGR 讀入
  image = cv2.cvtcolor(image, cv2.COLOR_BGR2RGB)
  from PIL import Image
  image = Image.read(...)
  import skimage.io as skio
  image = skio.imread(...)
  ```

- Python npy 

  ```python
  import numpy as np
  arr = np.load(example.npy)
  ```

- Pickle (pkl)

  ```python
  import pickle
  with open('example.pkl', 'rb') as f:
      arr = pickle.load(f)
  ```

### 重要知識點複習

- 不同的資料有不同讀取⽅式
  - ⽂字格式通常可以⽤ with open()
  - 圖像格式可以使⽤ PIL, Skimage 或是 CV2
    - CV2 的速度較快，但須注意讀入的格式為 BGR
- 其他形式如 npy / pickle 可以儲存經過處理後的資料

### 參考資料

- [pandas Foundations](https://www.datacamp.com/courses/pandas-foundations)
- [pandas_exercises](https://github.com/guipsamora/pandas_exercises)

## 欄位的資料類型介紹及處理

> 了解 pandas dataframe 欄位的基本資料類型

### 資料類型

1. 資料的欄位變數⼀般可分為
   - 離散變數: 只能⽤整數單位計算的變數
     - 房⼦的房間數量、性別、國家
   - 連續變數: 在⼀定區間內可以任意取值的變數
     - 測量的⾝⾼、⾶機起⾶到降落所花費的時間、⾞速
2. Pandas DataFrame中最常⾒的欄位資料類型有三種
   - float64 ： 浮點數，可表⽰離散或連續變數
   - int64 ： 整數，可表⽰離散或連續變數
   - object ： 包含字串，⽤於表⽰類別型變數
3. 當然還有⽇期、boolean 等等不同的格式，實務中遇到再 google 就好

### 重要知識點複習

- 拿到資料的第⼀步，通常就是看我們有什麼，觀察有什麼欄位，這些欄位代表什麼意義、以什麼樣的資料類型來儲存
- 資料原來是字串/類別的話，如果要做進⼀步的分析時（如訓練模型），⼀般需要轉為數值的資料類型，轉換的⽅式通常有兩種
  - Label encoding：使⽤時機通常是該資料的不同類別是有序的，例如該資料是年齡分組，類別有⼩孩、年輕⼈、老⼈，表⽰為 0, 1, 2是合理的，因為年齡上老⼈ > 年輕⼈、年輕⼈ > ⼩孩
  - One Hot encoding：使⽤時機通常是該資料的不同類別是無序的，例如國家

### 參考資料

- [Label Encoder vs. One Hot Encoder in Machine Learning](https://medium.com/@contactsunny/label-encoder-vs-one-hot-encoder-in-machine-learning-3fc273365621)
- [Pandas dtypes](https://blog.csdn.net/claroja/article/details/72622375)

## 資料分佈

> 了解如何通過基本的統計數值以及畫圖來了解資料

### 統計的量化⽅式

- 以單變量分析來說，量化的分析⽅式可包含
  - 計算集中趨勢
    - 平均值 Mean
    - 中位數 Median
    - 眾數 Mode 
  - 計算資料分散程度
    - 最⼩值 Min
    - 最⼤值 Max
    - 範圍 Range
    - 四分位差 Quartiles
    - 變異數 Variance
    - 標準差 Standard deviation
- 基本上使⽤上述統計特徵就可以讓我們初步了解資料的樣⼦，並且觀察是否有異樣

### EDA視覺化的⽅式

> 有句話「⼀畫勝千⾔」，除了數字，視覺化的⽅式也是⼀種很好觀察資料分佈的⽅式，可參考 python 中常⽤的視覺化套件

- [matplotlib](https://matplotlib.org/gallery/index.html)
- [seaborn](https://seaborn.pydata.org/examples/index.html)

### 重要知識點複習

- 資料⼤部分時候都是非常多的，我們沒辦法⽤眼睛⼀筆⼀筆都看完，平均值、標準差、最⼤最⼩值等統計數值能幫助我們迅速對資料有初步的了解。
- 了解統計數值後，把資料的圖畫出來除了能夠更全⾯地了解資料，也能幫我們快速觀察到異常的地⽅
- pandas 有許多已經寫好⽤來做以上這些觀察的函數，熟悉這
  些函數的使⽤能加速觀察資料的過程

### 參考資料

- [敘述統計與機率分布](http://www.hmwu.idv.tw/web/R_AI_M/AI-M1-hmwu_R_Stat&Prob.pdf)
- [Standard Statistical Distributions (e.g. Normal, Poisson, Binomial) and their uses](https://www.healthknowledge.org.uk/public-health-textbook/research-methods/1b-statistical-methods/statistical-distributions)
- [List of probability distributions](https://en.wikipedia.org/wiki/List_of_probability_distributions)

## Outlier 及處理

> - 了解什麼是例外值 (outlier)
> - 學會如何透過資料探勘⽅法找到例外值

1. 異常值 (Outliers) 出現的可能原因
   - 所以未知值，隨意填補 (約定俗成的代入)
     如年齡 = -1 或 999, 電話是 0900-123-456
   - 可能的錯誤紀錄/⼿誤/系統性錯誤
     如某本書在某筆訂單的銷售量 = 1000 本
2. 檢查 Outliers 的流程與⽅法
   - 盡可能確認每⼀個欄位的意義 (但有些競賽資料不會提供欄位意義)
   - 透過檢查數值範圍 (五值、平均數及標準差) 或繪製散點圖 (scatter)、分布圖 (histogram) 或其他圖檢查是否有異常。
3. 對 Outliers 的處理⽅法
   - 新增欄位⽤以紀錄異常與否
   - 填補 (取代)
   - 視情況以中位數, Min, Max 或平均數填補(有時會⽤ NA)

### 重要知識點複習

- 檢查異常值的⽅法
  - 統計值：如平均數、標準差、中位數、分位數
  - 畫圖：如直⽅圖、盒圖、次數累積分布等
- 處理異常值
  - 取代補值：中位數、平均數等
  - 另建欄位
  - 整欄不⽤

### 參考資料

- [Ways to Detect and Remove the Outliers](https://towardsdatascience.com/ways-to-detect-and-remove-the-outliers-404d16608dba)
- [How to Use Statistics to Identify Outliers in Data](https://machinelearningmastery.com/how-to-use-statistics-to-identify-outliers-in-data/)

## 常用的數值取代：中位數與分位數 連續數值標準化

> - 如何處理例外值
> - 如何進⾏數據標準化

### 常⽤以填補的統計值

- 中位數 (median)

  ```python
  np.median(value_array)
  ```

- 分位數 (quantiles)

  ```python
  np.quantile(value_arrar, q = …)
  ```

- 眾數 (mode)

  ```python
  scipy.stats.mode(value_array): 較慢的⽅法
  dictionary method: 較快的⽅法
  ```

- 平均數 (mean) 

  ```python
  np.mean(value_array)
  ```

### 連續型數值標準化

- 為何要標準化

  改變⼀單位的 x2 對 y 的影響完全不同

  ![](https://lh3.googleusercontent.com/Ewdqr5FOohRyWnOG9dP8ThYx10sg638dqgqYrP90jCv9cjG6q7Rl7FYKUnxz9RTpwuqst6ftuhPa2RneF8lmzeOe5i-LD5QKkunTCr4FHtWqcxpoGQz0Swe50uqPfq8cjaiqOmMnQkhkmJ2VYdAm1PT6VLQTASf-csXfEDIzn3HeEXpLhMG05cRsX7ki7aIjYWwDOex0WF5QiSdhSwU4IES_BsFIKFVaXqTTLrvYvIf-ZrfLwNeskEWwxjpte7Pu4ntQk01MKoq-SRMxK1C09EdWq4rLrRghQ4mrW_aRqd9S_8dbJ5cqbk6wnauZ_47tAdwdeu0Ur2cW3z0vqI0rNypz8T-IjhAsEILJ83fuWIHQzAUYJWy9tJvjY3uhtK91iJUS6pOix3rMwoqmxI2_NFZ3qXTYx6gnTTismV2zm0_kBvFp3PF-yEIAk4tnFq4SA_ByB1AmhpHUERrCxl99qD3ir-u0-nul7qWHUJLrXa3cWmi-nEvCj2SJpNyp3QGUOB95us09UR0mU6rg_uKomvCEFuRJlWhdm3CLeZnoHHT-U6xGbbmPARPc71h3jQzvFx_LISYVIWRbwFyhNE-QR4etLLdZiY7F34fjqINPCD427aQ5oc83GO97P3Y2Jp5-JrULdCl62GyUr2oFX8RMg0GqnVBgkUf4O5jsos0P9z6f6_XzYuK5wkDizIk5c36IU4sfrQ0TbLdQ7xLPis563n68=w914-h163-no)

- 是否⼀定要做標準化 (有沒有做有差嗎)

  看使⽤的模型⽽定

  - Regression model：有差
  - Tree-based model：沒有太⼤關係

- Z 轉換
  $$
  \frac {(x-mean(x))}{std(x)}
  $$

- 空間壓縮

  - Y = 0 ~ 1
    $$
    \frac{x-min(x)}{max(x)-min(x)}
    $$

  - Y = -1 ~ 1
    $$
    (\frac{x-min(x)}{max(x)-min(x)}-0.5)*2
    $$

  - Y = 0 ~ 1(影像資料)
    $$
    \frac{x}{255}
    $$

  - Y = 0 ~ 100

    用分位數來進行normalization

### 參考資料

[Is it a good practice to always scale/normalize data for machine learning?](https://stats.stackexchange.com/questions/189652/is-it-a-good-practice-to-always-scale-normalize-data-for-machine-learning)

## 常用的 DataFrame 操作

> 熟悉 python 常⽤套件 pandas 的操作⽅式，如排序、合
> 併、分組操作、Indexing 等

### 轉換與合併 dataframe

- pd.melt(df)

  將 column 轉為 row 

- pd.pivot(columns='var', values='val')

  將 row 轉為 column

- pd.concat([df1,df2])

  沿 row 合併兩個 dataframe

- pd.concat([df1,df2],axis=1)

  沿columns合併兩個 dataframe

- pd.merge(df1, df2, on='id', how='outer')

  將df1, df2 以'id'欄位做全合併

- pd.merge(df1, df2, on='id', how='inner')

  將df1，df2 以'id'欄位做部分合併

![](https://lh3.googleusercontent.com/2Dk-QkxFMvRnnqDda85yaTly_MCzcGyBzgcRHJ6gBthtHXxGIGNT_UzSfrEVM8KZ_brrf1YJAai7DELuKTq70UGLgHa0gjRLvKrAiYXAwl8jVdjNjIqleOywTLAMDhjq5wH_adxFQELXfAIkNpQHzWy5GC8H9PRP9X18JAQB5_ObHrtJIsvhDFtA6B67ar1p-V1OVEm9esfLB0uOOgof70nijGEzGY4d-JMTuCxcV_4Hbb8WkNDzxK9IT6g8DQhaaXfn0rx8gmZtW3sd1kMYPpOQbLxY8RFBruq0OPrlIhJ0QqTJGv492W3nrNdRIpaZB102hWd_dzKnfmPXO0NiyCSPixhhawbOwSrfKJK7sK2zBhQDSc5X7gunYwbrBA0uXJm35mCeGcAD2TUf_N8dG3AcwJemwiEHoX1xx3JLwyOVeuzqqO9i68KroURkRBWOdldeKg2aamEeIq7kDLfPPa2ecs6-B-sK_YeLdVpYLiqJsHtDDBp0a5VNa5Es0B8e-PJbSou9-wgdi_rpUzOyMOMbY8qYPnnSZteKkijRrWKGlCIPno6A2qV_90bBwvEUvKrDwXGoaUa0T66xPIVJ5iKgN-YISROCG2MoHxxaX4ZlQqPX0F7q81kXpmjfWWuBnpHo4RqCqvJmbECDer2YhhtCdCqHWXGKZWPHu1LbZ6st7uzWhndpxQO7dKAR15TTYkSuOcFJ-YqzBopWrxpx-gIk=w698-h575-no)

### Subset

- Row 篩選

  - 邏輯操作

    ```python
    df[df['age'] > 20]
    ```

    - 大於： >

    - 小於： <

    - 等於：==

    - 不等於： !=

    - 小於等於： <=

    - 大於等於： >=

    - 且(and)：&

    - 或(or)：|

    - not： ~

    - xor：^

    - 欄位中包含特定Value

      ```python
      df.columns.isin(value)
      ```

    - 為 Nan

      ```python
      df.columns.isnull()
      ```

    - 非 Nan

      ```python
      df.columns.notnull()
      ```

  - 移除重複

    ```python
    df.drop_duplicates()
    ```

  - 前 n 筆

    ```python
    df.head(n=10)
    ```

  - 後 n 筆

    ```python
    df.tail(n=10)
    ```

  - 隨機抽樣

    ```python
    df.sample(frac=0.5) # 抽50%
    df.sample(n=10) # 抽10筆
    ```

  - 第 n 到 m 筆

    ```python
    df.iloc[n:m]
    ```

- column 篩選

  - 選擇單一欄位

    ```python
    df['col1']
    df.col1
    ```

  - 選擇多個欄位

    ```python
    df[['col1', 'col2', 'col3']]
    ```

  - Regex 篩選

    ```python
    df.filter(regex=……)
    ```

### Group operations

- 計算各組的數值

  ```python
  df.groupby(['col1']).size()
  ```

- 得到各組的基本統計值

  ```python
  df.groupby(['col1']).describe()
  ```

- 根據col1分組後，計算col2的統計值

  ```python
  df.groupby(['col1'])['col2'].mean()
  ```

- 對依col1分組後的col2引用操作

  ```python
  df.groupby(['col1'])['col2'].apply()
  ```

- 對依col1分組後的col2繪圖

  ```python
  df.groupby(['col1'])['col2'].hist()
  ```

### 重要知識點複習

- 合併 (concat) 常⽤於將多個表依照某欄 (key) 結合使⽤
- 分組 (groupby) 是常⽤在計算"組"統計值時會⽤到的功能
- 許多基本操作 (如 >, ==, <, ~) 都是可以在pandas 作為篩選條件使⽤

### 參考資料

- [Pandas Cheat Sheet](https://pandas.pydata.org/Pandas_Cheat_Sheet.pdf)

## 相關係數簡介

> 了解相關係數

### Correlation Coefficient

- 相關係數是其中⼀個常⽤來了解各個欄位與我們想要預測的⽬標之間的關係的指標。相關係數衡量兩個隨機變量之間線性關係的強度和⽅向。雖然不是表⽰變數之間關係的最好⽅法，但可以提供我們很直觀的了解。
  $$
  r = \frac {1}{n-1} \sum^n_{i=1} (\frac{x_i-\bar x}{S_x})(\frac{y_i-\bar y}{S_y})
  $$

- 相關係數是⼀個介於 -1～1 之間的值，負值代表負相關，正值代表正相關，數值的⼤⼩代表相關性的強度

  - 相關性(含正負號)

    - 非常弱相關：0.0~0.19
    - 弱相關：0.2~0.39
    - 中度相關：0.4~0.59
    - 強相關：0.6~0.79
    - 非常強相關：0.8~1.0

    ![](https://lh3.googleusercontent.com/N-1nRtfzQQeEcCPhB2BsDKPxbe_aeJlbTJoC505odQ17jlKGSJsJa6r5YX6dpXHGnq2fp2FWeaX9TSMvg1TnFo_9e-zkOBOuEQIlQN1BGeyiDEvmTZSs0pNHjkNvsqK5luViqDP1ynIsulYMipiU-okEvH7scYkov9JVPXRmDu0pKVT2lGckVD7QWEASrVSmh_kN7DOQii2_TQZ2h7nPDVbW0lyU_wlLWU8WvzglHFLh85whTee_lQ7WpiT1SJHRc0689kW9TjDch2m_TsWkpENZMTXGB3bXkdsWwZ-mIEuc-KVlW0SBrZqqjPnAsDQyAUIXzx8sbHsWZu8cPCRekKXmxX0VjCMNkACgwTkIIxIUDv0PQ1iB2w8UIqC-dIUGuKUnfvFP2l5HXMysm5_fZjr8qxcm8KSY9t9cvsk6mHkbFZTP7AOEelgtcfrrFdKIJkKsqC2nOMPuv78Pmec7KxwryQrF97bVAx7ns0nwBDBcwNOP_nmgg24eqasI_hi5gwfKwYryswSZ0nVTWjHNeb8el05No6L66O8lQ7Aux7i6cdMfvd1kT56mn8wSy5O8PGitRkHjupyiqEWkX9NCgEeMkPnaM__Ztg2_r2Dq3HL6QfE1zK2tAHpaTCkwyA836NlEjpb617IA3dL3C-Jty_9iCGY-YYvs6RvQbXrtoYMs_vpzORSn1CAqel4tF6x_OVSLpn0PfN_OT0fnR9rh9hj7=w742-h326-no)

### 重要知識點複習

基本的統計數值只能了解⼀個變數，如果我們想要了解兩個變數之間的線性關係時，相關係數是⼀個還不錯的簡單⽅法，能給出⼀個 -1~1 之間的值來量化兩個變數之間的關係。

### 參考資料

- [Guess The Correlation](http://guessthecorrelation.com/)

## 相關係數實作

> 可以⽤相關係數來迅速找到和預測⽬標最有線性關係的變數

- Tips: 遇到 y 的本質不是連續數值時

  ![](https://lh3.googleusercontent.com/1YlHVASueuj2MzWiULompmwZUu-c1Qt3ilOhr0pcgrsxFPv7VqB8BiYTriwgHeeRm-LZpQGiEJOeGgjgk50m66XtL99slTdy08t1T05UUplwRs8fbWSaR_b-ukEu-SuhReLjFi65J3nhvo7NtgSIC9J81rJ_ujkOjJty8I1khdVngy9BP6dhVMI7BVFCjZqr0pOEIKOEKt47bL-UlPMKvEB4szGXcZp9oYRNmdFizrtfhhxe1L_4uN5445Mh27pTGcBjxvcll_drXKnISmLbjmHjBoXh2t1YwKKwzvzow8Tf_2MD-FX7vTaimI2Ph_I3qL3JBFWkvdG4WArYPMwUNfAayEJy9-XgqeAgnKO67BpwcTmJOwFrTZifPw-28hUpo9bDgS1GLhx4oeQcmMpLiWwMED3C7nvFcK01jobAjrzSc6PxCf8y24Cayfld44joM07lfIMb9HheoP0HibAymD_LQI0Fv0B4yO_bwmXr2ATxujtRxF2jecebV4gEqtLFxZhPwA_5nJZiYyxDoy8r09EtrvGuCpfKus7S-P5inOnhOtU4HHD-dRoeOxIam5X_55HQCd6dYYaYmxKeW_xzcAAFaVTL9nIcVH14W7IyfISbwhJ9UfgJA2Opxz2JdVw4uGzm244yvdVWofjBN_bNfXWPajzmPOPzxZBnKP7ucVTMVvtZ-wzw3Or6vfYRXb8iBrdmcAFuxUkio04H3qFCCFsk=w1010-h420-no)

- Tips: 檢視不同數值範圍的變數

  ![](https://lh3.googleusercontent.com/xs6MIAIdwvGFfvfNKDKmNQHLz2AyF9I_u0T5WMeOl12M0kFQNjl_f_tR2VIGlegQxnPHhMTjvuqClKu_W-FxBslXvMPi8tLC3S2qoQZrXMgP5L7a2BgdfrrXEQOATonQMvisGsjjLCo7hIALq16Tt41CAnVmugAPKaArEIhgwbSkP2jKk1RO8Y_p9gT5fBad7JGGAqv-qG535aJ8MnsAmkwe7TzUBfyBKF_yse_WE5sjB3NdFbMt75GDweNmFKSIHnC2_cUe-Weezv9b4L0GtxXstB3w2Vl4YAVhH-VhBfPQBHZ6qJFdC58tsqlE5SeONqhnW_RnCMuumKH7Fc86eARDC9xDu0Hc5o7RNJd8B8YfNawTB-RZELfCVZwCTVLfuEjygpFUrwqYE4NqklZq1Yl4r-TKXX4ugLAqFmvoLK34GKz5eoacSSXmYy7P8AvbE6O2yAEKmf8HNnhq6N0Q0enYEOrU4k90vYN5SbPiOQzTu_dVs3ODhn28USw-A15WUNZYUK8W8h7ovzh3KZ5yamBiTAFTo5qNKNtcs5P4Kvi-iq9beP06TanYRM--lKvO8JQ5hjtMRmJXgj61QtKIDkPnCtyY4cTV5aP5qtIDpAKWpwmstxKLZyxw73fHEsOZv5tBHViKT_Bwlv0KjsRwnPEbUVyyoIxc4KNAMbZBgY4bUrCSu_Bx7YONGhmdS4iF8zFvMJzOKq5FcZXh4ZP9Nbkw=w982-h411-no)

## 繪圖與樣式 ＆ Kernel Density Estimation (KDE)

> - 知道 matplotlib 的其他 theme
> - 學會什麼是 Kernel Density Estimation (KDE) 與如何繪製

### 繪圖風格

⽤已經被設計過的風格,讓觀看者更清楚明瞭，包含⾊彩選擇、線條、樣式等。

```python
plt.style.use('default') # 不需設定就會使⽤預設
plt.style.use('ggplot')
plt.style.use('seaborn') # 或採⽤ seaborn 套件繪圖
```

![](https://lh3.googleusercontent.com/ng0N7k4WMKWqGg3vrJxImznJvitqADU2mwQlmZjzXBpHFUIucoZBUaghZMt9qSK0GIS7nk16e4LA4zZlm7RUkZW_7iG61ITxfkCavcqL-kH-0qIt58TivnMyRVRbRZLILBw9FDFgAQkGdzhFY4hWl1AdOmpNT0lnOm5aesbdimFPsIwLCD2B06ykv-AQUuYQyWZcMIEBBos8hOKdRp9_MbAlg_qwJv2K4hAEUFYyIgBwJ6-gOrq-Gm1WpJ2OqKfikCQVfRK-1uI5gBs6Xj3ZUb0WJvZiKVxHgEPxn3P7mv-uaciPmcn4B_ea-VUIsJt1Lf_-9cLaz5743-Koe_87PPQdouP1UpAcLR1UdDu5VorOKtTIICKt4Df4a7XmLSqMcp3I0RVWNqGebTYy02HCslpEh06msL8Ej5Du62bhJY55xIS02TOIl4Xi9znCiPcFcQ7MiEUzzL-R4BY2Ff9UxFxd0VqOEmu_kz2yeymQda_jC2kUAWn_XSA7BksqV1t87Nc6iQu9413YBYgciMzqmMERvyHJFDy_zxsDt5fS7bO8VGR3UbdRdKC15KEuTEUFd65H7NOKqVHlPeH6TL6wt6M-Y-beaEwRVe9rMdlMUEA0-6cjMmbDrBRDIrfVmZzGDkgbKM0FsGkdIdOQb_0oGD458JiOYFtm0rqLDekAPCJ6VUayC2choNt9C2k0fa2csVsMSbwqytrO39TLjYUc6lJ4=w891-h435-no)

### Kernel Density Estimation (KDE)

- 採⽤無⺟數⽅法畫出⼀個觀察變數的機率密度函數

  某個 X 出現的機率為何

- Density plot 的特性

  - 歸⼀：線下⾯積和為 1
  - 對稱：K(-u) = K(u)

- 常⽤的 Kernel function

  - Gaussian (Normal dist)
  - Cosine

![](https://lh3.googleusercontent.com/n2zOyNKlVfUkdhQPjOhQy8zfsAAVJQHuHp4vz7BxWlts7nfeG4Zoq-tJlSx6RM2om16prSokqtql85PYq2NLCtLbHtX4AN6ijwyC43vNEyC5C84RnMpSDMHjGLBF_EK0ZbaTEnOH-ci2ZdBeJ8lWxcQPLXZh7ROJWdE4rJIy6LiW-hrrrhCXo7f1kiqSWtZ7QdjRf70bZ2KHL6LMe6o9VwZ96ur7yGS4M-WAOF81FIADIvVS1vrl0U-_LwlksM5QfQMfYIgqJ9rkpGKIUw2UoLy0GZnezP7MGv3L_7wRlmbqNLl46qVi2RZIdEBoZwLZljAboj4UnjDGIWWnIZ9lS0D8a0n__xUJGx4zQsWKIqb8GPX2cEF796eUqhxQqFaR_vkzJOTe9pgOYDwQLY8beZX6cfyJGx0-o3fl3-v_GwnxKnGZceXEdZMLIT_Ovo2ADi7jXiU4GXVhy-NCITPH6gxy87H0j7wLK7cwv_ez7C0XhWlmem9iQ9UymitpbrqBClSfoqXNJmJCZ7H4wg9-wPU19AkqsXp9ZdEr1hBktawqVt7tu6EBl4uIjmWqKzmnLbCBO9UVGFax66hTFtnRTfGWI48rg0My1aRoYuNyrHWK-hH5BF5hRqNXL7vvQZf_S4R01NHoukwMbIIjZ7gKvE51kAzEY7QRo_wr5mbU1jkeSzB9aBBhxrOjgPjQCnxWGz6JP6VnaZ-_HyjpfUUDzLHE=w703-h521-no)

### 重要知識點複習

- KDE 的優點與缺點
  - 優：無⺟數⽅法，對分布沒有假設 (使⽤上不需擔
    ⼼是否有⼀些常⾒的特定假設，如分布為常態)
  - 缺：計算量⼤，電腦不好可能跑不動
- 透過 KDE plot，我們可以較為清楚的看到不同組間的分布差異

### 參考資料

- [matplotlib](https://matplotlib.org/gallery/index.html)
- [seaborn](https://seaborn.pydata.org/examples/index.html)
- [Python Gallery](https://python-graph-gallery.com/)
- [R-Gallery](https://www.r-graph-gallery.com/)
- [R-Gallery (Interactive plot)](https://bl.ocks.org/mbostock)
- [D3.js](https://d3js.org/)
- [核密度估计基础-Part I](https://blog.csdn.net/david830_wu/article/details/66974189)
- [核密度估计 Kernel Density Estimation(KDE)](https://blog.csdn.net/unixtch/article/details/78556499)

## 把連續型變數離散化

> 了解離散化連續數值的意義以及⽅法

### 連續型變數離散化

- 目標
  - 變得更簡單 (可能性變少了)
    - 假設年齡 0-99 (100 種可能性) >> 每 10 歲⼀組 (10 種可能性)
  - 離散化的變數較穩定
    - 假設年齡 > 30是 1，否則 0。如果沒有離散化，outlier 「年齡 300歲」 會給模型帶來很⼤的⼲擾。
- 關鍵點
  - 組的數量
    - ⼀樣以年齡為例⼦，每 10 歲⼀組就會有 10 組
  - 組的寬度
    - ⼀組的寬度是 10 歲

- 主要的⽅法

  - 等寬劃分：

    按照相同寬度將資料分成幾等份。缺點是受到異常值的影響比較⼤。

  - 等頻劃分：

    將資料分成幾等份，每等份資料裡⾯的個數是⼀樣的。

  - 聚類劃分：

    使⽤聚類演算法將資料聚成幾類，每⼀個類為⼀個劃分。

  - 除了以上的主要⽅法，也會因需求⽽需要⾃⼰定義離散化的⽅式，如何離散化是⼀⾨學問！

### 重要知識點複習

- 離散化的⽬的是讓事情變簡單、減少 outlier 對分析以及訓練模型的影響
- 主要的⽅法是等寬劃分 (對應 pandas 中的cut) 以及等頻劃分 (對應 pandas 中的 qcut)
- 可以依實際需求來⾃⼰定義離散化的⽅式

### 參考資料

- [连续特征的离散化：在什么情况下将连续的特征离散化之后可以获得更好的效果？](https://www.zhihu.com/question/31989952)

## Subplots

> 學會如何使⽤ subplots

### 什麼時候該使⽤ subplot?

- 有很多相似的資訊要呈現時 (如不同組別的比較)
- 同⼀組資料，但想同時⽤不同的圖型呈現

![](https://lh3.googleusercontent.com/J0YKB2xbVyLuXHunZ9NhK2cn5aCbel3qJFMeC9Ne1bjBrFb2PW9sKw5fHWwnyU5R8H2uhQm5BscycTStOzzCH3EgVddL5_dHDLf3dJFJxDL0PMov0hlDOwyPJDiLDeIGLSP6smm_3P8EfpjtZUSQY99A4wpnXvzJXgr2MgALkI2J3AduSlxaiwrEgBeuZK64y-S06vj991-fpbedrQK5enjOlaeZRNm8T-z3Fd6dZka0DzM6XpLar-shGfmKlnS2VePyiVDBpI7qP5BUE4B-IsLS32o1EfZKX6DY6ymR_D-O0esOJ-Za72lI1aPdXE3YtyDvlwwSX1YFMDNFdgeoKGxGFaFwcQmT4TWIPAS5aJ4OIb8qGR0M6fYyyIdlkgPHQjifXeDnGkGqINu6l4NL5wXs4FAhPdwe7cIKi6AVx9yWEcjATtxHqvrOSYtJXkB4jHMs_QFGp03pl2rJEPo_trijGFJpmi95vd38PXG5Z2Bh3V7r3V7wt3XE5FzR2oOtvWTXE4vKQASx0CIpurQl3Gib1ejZQ5GeHHjoQllrAbsb75JVEEM_Jsb4drW-YMf_mkxq17mvwvYjQ4HbZmgA1u9X4Wc9oBvKDlqVKov_FpYPcVVwKiaempWTyJQnkW70Jeqke3SW5UOiPiQeVsvt-RIBmgGBCfazvGEDB_o4v6NluclRlMKyed52NtI-VlYhZXBiTWBvgULYa45E_Vglw3Y0=w545-h472-no)

### 重要知識點複習

- 適度地將圖片分格呈現，有助於資訊傳達
- 但過度使⽤ subplots 也將會使得資訊太多反⽽讓重點混淆
- Subplot 的坐標系 (列-欄-位置), 如
  - (321) 代表在⼀個 3列2欄的最左上⾓ (列1欄1)
  - (232) 為在⼀個 2列3欄的 (列1欄2) 位

### 參考資料

- [Subplots](https://matplotlib.org/examples/pylab_examples/subplots_demo.html)
- [Multiple Subplots](https://jakevdp.github.io/PythonDataScienceHandbook/04.08-multiple-subplots.html)
- [seaborn.jointplot](https://seaborn.pydata.org/generated/seaborn.jointplot.html)

## Heatmap & Grid-plot

> - 了解 heatmap 可以傳達的訊息
> - 學會繪製 heatmap

### Heatmap

- 常⽤於呈現變數間的相關性
- 也可以⽤於呈現不同條件下，數量的⾼低關係

![](https://lh3.googleusercontent.com/6csWSVGo3LW4nMwWFk48K9Om6F1AOnzAmEFXxReZrUPuYrykaGDRl4jAPzmPjccatbPGwinZtt-qnfUyl9lwj8AMXUiT2hrj3oHVCx9snsmRc45H6xjbAzi_uG5eWfmHqhUuI6q15qVuTvSYWX4BBYGE98yb71jRrkUqVOq1llhBNdeXvjN_dapwsZNLk1O2Q0Pd7He6Qp6-7EpSj1GWa0ZnsWIOcHn_ObAhzxN97_HiNORN7Q7ufLiEZv0ICahbT21k8O3ABmxHu9hr9w3hW-X5xRuKRn2zVzRjwP91vlgHZO06Y4fy8e_aRlPcblodE7D7w36_owwLFo2ZJeRy_pPqx8Xkq0rEJbN3LDkTt9ZcsuiWhenCe53JnFJOl6YuZTze5Jqx_ciBrh2T_CGXd83mjtEBelF0hTpJrJA9JjE6LqfVOycw6s1eZ8pKdErFIaqV_yU7_lvnXQtWzZyIkB4hu7mCg66KLSKAdWj8OUqBkiQsbiqggp8dK4GyFVSwHa4oT8jih4pkybv2RXjIduvk9JlwV-8KuDROzgWCb6--4ziw413XNcupPzJmYJf2Ijo5-kdBglORppZz2kUv9vB3nWZmtgdauyzRo_BG0WdutVZHIAWKj1DEQkxSgKv8zf9AKV5p2yZWgpbovwb8CFsQAIPlf4eN6P-Lg478zFURCMFvaPdfPBcQ-YeeRowbth0FONAOa1NKAfJklsw-pMgu=w452-h336-no)

### Gridplot

- Subplot 的延伸, 但是 seaborn 幫你做的⼜美⼜好!

  ![](https://lh3.googleusercontent.com/Et63EdYQFOoXb35V7d-B6e6p9e3amn3tGEn0OJNL01NHXr20PewMTdKMJAi5YzPFb13KCOEx6WwKZtYB_PqMJmKr1oM6giGjUNGouBGofCLzYrUBSkLLi51sjI4SK-YacnC5ycXoUeAZZoVEAq__D4lLefG6MIJM6-iRiav03kB1oTMgBlLHv16iscUuODmM5_Qd5bB53UiUW7MTzYrB0nriojo_qexGMt-aAwcBEjviI-XninZV7fShUm5ACIkvPlxza0TsKPBHP5Ovga3vd4v3gOg7_MD6k4mWp8--GP4pTY8-jURPPwVGmtz-hTm3XaJokPbBmX3xvz8O9SoCayEA-pgdMym41e2E4gKsjfj-NExZrJjUYyLyrqyTN_XbS7wOf1gMaGVlFZ3PEkPtyZu7qdTgG2yPGISzLAT6icxVj_iozChU5G8ZP74lJLCP64O1FoakaNDrd4uuEqnvKh5OERuBABTIWBonigZv1q1vDSgZzQgGdh6hJRVdpPyON47T8wJUqm5wWx3bKsLG2jXiQAl61ZQOkau0QSLkAYFKnsckEi_Ymy2sxnVwoslzEXh9G0W79kPHDn84F9DGWcEt-TVYBhQF3IdwuFJzoNPhX2NZv0InRouiFrR_o1kKvUVgOBz3j8HXv-kUMjvTevbdtEyYcv5VQIUe5v8SlSB2OhD3mBaMNzqB5a1ZQvb23wS1F1nFSAVwHq_xAdKkgCFg=s900-no)

  - 對⾓線：該變數的分布 (distribution)
  - 非對⾓線：兩兩變數間的散布圖

### 重要知識點複習

- Heatmap 常⽤於呈現訊息的強弱 (以顏⾊深淺呈現)，也常⽤於呈現混淆矩陣 (Confusion matrix,後⾯的機器學習課程會再深入介紹)
- pair/gridplot 結合了 scatter plot 與 historgram 的好處來呈現變數間的相關程度

### 參考資料

- [heatmaps](https://matplotlib.org/gallery/images_contours_and_fields/image_annotated_heatmap.html)
- [Python可视化：Seaborn库热力图使用进阶](https://www.jianshu.com/p/363bbf6ec335)
- [Visualizing Data with Pairs Plots in Python](https://towardsdatascience.com/visualizing-data-with-pair-plots-in-python-f228cf529166)
- [Pariplot](https://towardsdatascience.com/visualizing-data-with-pair-plots-in-python-f228cf529166)
- [Gridplot](https://seaborn.pydata.org/generated/seaborn.pairplot.html)

## 模型初體驗 Logistic Regression

> 以最簡單的模型體驗⼀次完整上傳 kaggle 結果的過程

### A baseline: Logistic Regression

- 我們最終的⽬的是要預測客⼾是否會違約遲繳貸款的機率，在我們開始使⽤任何複雜的模型之前，有⼀個最簡單的模型當作 baseline 是⼀個好習慣。
- 我們使⽤ Logistic Regression 作為 baseline，理論的部分可以參考[Andrew Ng‘s videos about logistic regression](https://www.youtube.com/playlist?list=PLNeKWBMsAzboR8vvhnlanxCNr2V7ITuxy)

### 重要知識點複習

- ⼤部分的情況，如何通過資料得到模型的具體計算過程的程式碼是不需要我們⾃⼰的，python 有很豐富的資源，我們只要把相關的套件 import 進來，看官⽅⽂檔或者 google 怎麼使⽤就好。（通常只是⼀個 .fit 的過程）
- 相反地，時間會花更多在讀取資料、處理資料上，甚⾄，第⼀次把模型結果儲存為對的格式可能比訓練模型還花時間。

### 參考資料

- [Home Credit Default Risk](https://www.kaggle.com/c/home-credit-default-risk)

# 資料科學特徵工程技術

> 使用統計或領域知識，以各種組合調整方式，生成新特徵以提升模型預測力

## 特徵工程簡介

> - 初步理解特徵⼯程的概念
> - 能從程式中辨識特徵⼯程的區塊與意義
> - 知道特徵⼯程⾄少需要那些部分
> - 資料中常⾒的有哪些特徵類？
> - 上⾯這些類型：要轉換成對⽬標的猜測，有哪些要特別注意的地⽅?
> - 資料當中，缺失值應該怎麼補？
> - 補缺失值時該注意什麼？
> - 將資料標準化的意義在哪裡？
> - 什麼時候該⽤標準化？ 什麼時候⼜該⽤最⼤最⼩化呢？
> - 初步理解什麼是離群值? 出現時會有什麼問題?
> - 離群值處理，會有哪些優缺點?
> - 在哪些情況下，需要對資料去偏態
> - 去除偏態有哪幾種⽅式?
> - 使⽤ box-cox 去除偏態時，該注意什麼細節?

- 大多數的參數統計數值，如均值、標準差、相關係數 等，以及基於這些參數的統計分析，均對離群值高度敏感。因此，離群值的存在會對資料分析造成極大影響

- 在對各欄位進行歸一化之前，需要先將各欄位中的離群值進行處理，否則在歸一化後「非離群值」之間的差距反而無法呈現，影響模型的精準度與穩定性。

### 什麼是特徵工程? 

- 顧名思義，其本質是一項工程活動，目的是最大限度地從原始資料中提取特徵以供演算法和模型使用。

- 通常我們會這樣考慮事情...

  ![](https://lh3.googleusercontent.com/BWx3jLHzQ3UR_YNGZF45ZmEo_D_84CIPKX288qCoMCjnpXZ9vonJbQZk8rkGN6Yijyo4Mcfkfg3wIdzK9e6JzYjMNJIc6Ww14UmQ6FBqKgVy6_ITGQoWm-t7OCmFFdWEcEzKOsoLZQFuklMcsEgMPG1HBNYFYPH3QO1XtmHOmgNeQMIjP9mDgux6nJJ6CC__tzwj--pjheN74SqQJl4NJKGjEoSnncEwTLw1j7m5sacHwLHpDRjBr_02AGPe0BIbwMu17pHyszQJl787ABJ__Z-RF3Q-8o9hbGBQO0tKQenD5BvtpP_MDhjOtJYFLgw2tO9Gm6DxScmk4p3bqdlzu6M3S-LnNoiM66EQHHCrTEPoIFnEZMTWSLWBGJS3p4Ik9hgXTnsA2ikGISvZBlciVzg3fChZxpwLXcM42ijH9rX-85ifLHTvvQ3ijoH79b3SZQH3DedJbd44OGuHwrcoQ5UVweK8mMUXmnR3NoesboW_UVWGhQidDHwZxE9iynxQfU6bD2TOoMyG5eUvqCe0fZCrF9dIHxvpKsnZ0JiNc157kwDEDyOb7ah0l6-MeorVoaXZlL8ulIYm6K1yNx_YBz6ieMs7_LPqfAaFUB1765UL84aqpROZHnZuMLKkhFKUweYLU_hmClase0qavelJP_ZPkNolwUyrmnKDxMP-7yjwInFzpng2_R1IsbDHt9UKUtV9eL0RQeQw8tNs-hIoc697=w937-h332-no)

- 但其實考慮的關鍵是...

  ![](https://lh3.googleusercontent.com/7zdzpj1w2aWYPR8URd68QacrGlBcgiLTQRDcoTzwS1UBegSxXdm4L181doONzwtXRLMn64msdh-8eiqqBtvyESeF9XRgSs4MQPSemCgyLXgU9Ldcu1wfVbuV20vlR5uUuGbLJ7LPc3VfyJJQJI8QCD98OSVKvx-SblWweZ0lpXBHS6CAwUmyxQq8nOwiCg-8zGuHy65BwkBfk0J3dKBytsFjT-6kx9xLEqMbT6KPDE5UnSAC4YcMjsBTgUN8XumEPs_HaUfQFE0gZicw_ztSG1X_O7IUPXnBbxlFgt0rczkzOqcPgNhePQ0sIajDvBbremhAq5r1cDk_lUFNZQN7AJn8me2ugr8O77HR9x7PzaNPruc7HG4zJCQh08ryAbtkLJPsx_5gmLh5GfpwwC7MAdAtjsFQoLxn4HvzweQfM4Qkfop5dJZXTLXt_ejoeXZ5CY3IDLQ6EAnlkE0eShxHaGH-2CqqRlJS9viQx4wr5ntm-fbM_JR-IOh6KvA4H2YQcCsq5brxj86O6_IrJigKii1SQMi53GgPdiU_amYFCr-JBwongCqKSiMaaXgcS0ZhDlZFYgHCjmUyYpt5v4S_QJDXSyfYHuyideiE2N17MHllnyIxzNH8uE87yJut7idV0s-5xVtCR-c3jcxZ_KGW3hJvlGN-at5k44qWty44pYDX_P9wQd2VtiXNJat5d85JndRJBhgR98H483dDXr9L4p0U=w907-h353-no)

>  從事實到對應分數的轉換，我們稱為特徵⼯程

### 特徵工程舉例

- 點數未必直接對應到總價或機率，但會是後續評估的依據

  ![](https://lh3.googleusercontent.com/rmaR5zLfq2IvEVySCogV1JnPs0yKalnnpYjPUinEy9QhBM633EY7tdVEtCetsC1dwsV32MQDylXu9t7ZZ5GJUcbvQ0dzNj5IAlA799OoWJj6sm794E4IWsWBU8yz8kFzLZRLLqcFODpwCE8IPffoS1EMcsfX40zrDU0xOOjrPWQ77zZY1dF-UTNQ4YXDRKqRRp-OWYF6wOYBbad_nQmevvK_ywtK3vUq1fsDHAoRycRfgtX0uINgFismpc8yhrpm4NWGVRSoEB5GCuYgD9rYntS27u2cvmhu8optQy1YYeuw9ka9MvwGl8lUZMyE2JI7oNUJ1O9kcxtUU0-6CTLuTnabcsCJ6pz3sU8WfTXyM1j6zs-qhln07MNtnP3ycQUFtINP4nh_AZ2aE84wQ4WPbqEcx7-pCly2Yot-wDNmqQZ0eSerx-pV227kNqSFDu3yWWDrVysq6wm9v1AknraCT-36BB_xPAS9rNIgZ0IAfGl1IFBGbzyLjeCS_fnzY3ctah42kg-B5aT2Ug1tQ80hFg7fv_AJnQDUtUiohpt_rPqW3brDZQg0-uGA9xbRGN0rX_VS51PtXJLObQiTeaDEUN1nUInBUy9XXmQcJ0N-pwsUszLXD1StlKhxJjdvRVjKgF_nKsP5m4ekdrkQJ53aOmLk6NYBhTxqBaTM8g6l0I8gLov3kY5AMsPjo6ErVnek6nOcZKWRaDQqoZDkGT7vFysV=w992-h382-no)

### 特徵類型

- 類別型特徵
- 順序型特徵
- 等距型特徵
- 等比型特徵

### 特徵工程流程

![](https://lh3.googleusercontent.com/bYbK9cqZ4Ona4EnRLk2XgNQ2P4Mv-TD-Vr-MNLBkU6LolteuA63wG0uaw0XId7Nryn5or5PKCkSihM-Vc_aZHCUTCdM_v8tIb_A6d2wuTmC5NqA7v3ckoenkoP1ngBWDjIMl65HMbZoZpY13IPODPxeCbyNpihPuzw4oUReEh2saPMMO1BUzeEOEDaVLe3c89wkIQ3xVe7bXg9hZBctEl-Cp12F6K1cpO5SuY530a-bzm57It3L23a3JZTY9wH8Cp31czO5dXPGnoKiGI74gmCbCsBUKp1QGjHNlGKIcYRhb1RjuPzrlXLX7nUk69kBWO59NfAdSpJTZ_Url1-H94hMp1DWG7Ay2iTOgL_x8Ivj_LYdkZY3K4xOLkQJTaLuqVEVUe0iYDUCkt-hHWVm7AwAPtiIXpREeF9RD7E0-NxOfO_ottj1n4q_RoCe7TV01g-1ZG58wWt7nn2OoVlZ7oMAAh5MeZrIMWLTY0AjONNyicvMy1-a3wlHsB1MUlP5TixNeae6w8uGfGPxgv1agAovWNh22-17e8hQ40nFOEMAKQv4kS23I1t3st1T9N9GjwI8jCHpo5Pnjr33mfqM02TEX_gHIhDxdNcoq5YVWjQ7Q0AEfrbhMyOcaoH4KarM_7vtVeGrlQQmRy82xX-xCBn5mBNbZbCqsSoa7KerLGX4KBG2X8HN5W-zdg9CtfMEKNnA16QEwZy_NpuQDNcp1cQhG=w796-h476-no)

### 重要知識點複習

- 特徵⼯程是事實對應到後續評估分數的轉換
- 在程式語法中，特徵⼯程位於資料彙整之後，以及擬合模型之前
- 由於資料包含類別型(⽂字型)特徵以及數值型特徵，所以最⼩的特徵⼯程⾄少要包含⼀種類別編碼(範例使⽤標籤編碼)，以及⼀種特徵縮放⽅法(範例使⽤最⼩最⼤化)

### 參考資料

- [特征工程到底是什么？](https://www.zhihu.com/question/29316149)
- [為什麼特徵工程很重要](https://ithelp.ithome.com.tw/articles/10200041?sc=iThelpR)
- [Kaggle競賽-鐵達尼號生存預測(前16%排名)]([https://medium.com/jameslearningnote/%E8%B3%87%E6%96%99%E5%88%86%E6%9E%90-%E6%A9%9F%E5%99%A8%E5%AD%B8%E7%BF%92-%E7%AC%AC4-1%E8%AC%9B-kaggle%E7%AB%B6%E8%B3%BD-%E9%90%B5%E9%81%94%E5%B0%BC%E8%99%9F%E7%94%9F%E5%AD%98%E9%A0%90%E6%B8%AC-%E5%89%8D16-%E6%8E%92%E5%90%8D-a8842fea7077](https://medium.com/jameslearningnote/資料分析-機器學習-第4-1講-kaggle競賽-鐵達尼號生存預測-前16-排名-a8842fea7077))



## 類別型特徵

- 可以依據特征下的值(value)的數量分為二分類與多分類的類別型特徵，這類特徵的值之間不具有大於小於的關係，也不能用來進行加減乘除等運算

### 遺漏值填補

- 填補眾數
- 填補指定值
- 填補「其他」

### 稀有值處理

指某些類別型變數會出現佔比極低的內容值。在這樣的情況下，可能會因筆數太少使得資料的代表性不足。

- 判斷方式
  - 發生次數：該類別事件小於設定的最小次數門檻
  - 發生比例：該類別事件的事件比率小於設定的最小次數百分比，常用5%作為門檻。
- 處理方法
  - 刪除樣本
  - 刪除欄位
  - 整併為「其他」類別

### 轉為指定值

- 依照Domain Knowknowledge將離散資料轉為指定值，藉以賦予連續型資料的特征。
  - 如將教育程度轉為教育年數：小學為6年，國中9年，高中12年等等。

### 均值編碼（mean encoding）

> - 知道當類別特徵與⽬標明顯相關時，該⽤什麼編碼⽅式
> - 知道均值編碼可能有什麼問題
> - 知道應該使⽤何種⽅式修正均值編碼的問題

- 如果某一個特徵是定性的（categorical），而這個特徵的可能值非常多（高基数），那麼平均數編碼是一種高效的編碼方式。我们可以嘗試使用平均数编碼的編碼方法，在貝葉斯的架構下，利用所要預測的應變量（target variable），有監督地確定最適合這個定性特徵的編碼方式。
- 均值編碼的缺點是容易過擬合（因為提供了大量數據），所以使用時要配合適當的正則化技術。
- 平滑化的⽅式能修正均值編碼容易 Overfitting 的問題，但效果有限，因此仍須經過檢驗後再決定是否該使⽤均值編碼

![](https://lh3.googleusercontent.com/nx3knWrJTN3iibWvU0j_ZDBuQL0NFtzXLw9Q-CFg1-2XKAwW7Ol1pNSE7RInslFpt2m88Q7nwYYRaFeTJ1ZwinzIyFda4lTOBOWtI5NzBXxOq8nhCqhXYCehLVp-Z3jQCwvsddcuJ7u6EZ311pOj7Z87R3gJdWarJMyhR_xOAC2J-6TYephvJA3roAHxrjdCgadRINzqFIobIVvZq4rdFw1dQzIxGmggUkEdjmUIivwPUm5RKlG2vkMRWGKGsHAmJhmxgAnaC-H0cIMtW_U_ajW3GaDap7j2-FYQ3v42WR9FajOkpoWLUu85I3sqJLjBKm1SUWyi9eiDt37hDByjmkyl31VToLfaz1lMLli_epggWQ7mQloOhFoJLvVx4wEi1zravbGmP3F1cKAqHf6fIyJaT0_qfnRrUOT2JIzJmmIr0fsrCZ8Z_fgTmQH2eShqUuetzZrCbALr8EiZXa4i4osjxYMxmNFTKctm9am4S76cWeYeuGe8SOS-5DiCWiLmQVbw-uIcb8_ywsYe3jnbvXV_jQOWTfYgOuJcDlNvb-uNZp7CjVkO4zhSpxZEBzIQ9zCcJWyf3P9kl4eFgkNVH3Dbw_QFvX2jGkfh0f4xg2XZz98mMmMcntaVTLXjeuFIt-e1eOuHKVgxE3ig5PWlxyPDVOzqDkqexGl9duk4JbiLEAwjq_Xlq3t5GSh5YSh9hl5aNKIpEuv9BjvHP6iIhKEJ=w396-h345-no)

### 計數編碼

> - 什麼是計數編碼，在什麼條件下可以考慮使⽤
> - 雜湊編碼在什麼情況下可以考慮使⽤

- 如果類別的⽬標均價與類別筆數呈正相關 ( 或負相關 )，也可以將筆數本⾝當成特徵。
  - 例如 : 購物網站的消費⾦額預測
- 計數編碼是計算類別在資料中的出現次數，當⽬標平均值與類別筆數呈正/負相關時，可以考慮使⽤

### 特徵雜湊 ( Feature Hash )

- 類別型特徵最⿇煩的問題 : 相異類別的數量非常龐⼤, 該如何編碼?

  - 舉例 : 鐵達尼⽣存預測的旅客姓名

- 這個問題沒有很好的通⽤解法...只能採折衷⽅案或個別情況解決

  - 特徵雜湊是⼀種折衷⽅案
  - 將類別由雜湊函數定應到⼀組數字
  - 調整雜湊函數對應值的數量
  - 在計算空間/時間與鑑別度間取折衷
  - 也提⾼了訊息密度, 減少無⽤的標籤

  ![](https://lh3.googleusercontent.com/8tqW1m9-n06lSMgT3efMWSdvvJA3Dr9mrJHmEUH0L3BtrIywHGxpUR13pOS66lHFkxq4t0gWIQUrOn5qjEXMZJx3D4dOQVp41gvzodhR3ILBfih4BIcx6dNTmeEGLGOjYlQB5qoCwyU5Gme8VHpabLB7yNcezMJbn-4oIsKzS8AlB6-MYREPkHEwqNBATmjOvKekW1Kt9xa-mfpk_pvId1muM6T8T_3haJud8gTqt6GDjrWy4SVVEvOx7wIkHdTWImoyZgfaXNDhZN06ht4AdcG0Gi3mXvmc1R1VXcq36tGuZKXlV9qlTXf3aWQ0INbsjNkrSEv6ZnA5_r6akfmoAGlDmIQtNSZ-a-wscxC1gKorPvEAA0T7BxsbPVmJbtNBjXIO53Bns_xaxI2mnnvAw_iNV1EDO3KhhYrg9sQ6Pm3MSgQBLJ4CuWzZ7rRjqp7acaj7ziIrZ7IDUWQD_FvmuQ-GXscJtxHwJLUfzKIbC9ivDBgehV1HZcVdS9wgJTXYpbY-ZYIJkrT-HmQ1Ob55aOKfGKWrt2XqfH1FKd5zlUxPm8cLNXJv47oqdJbH8hbvc2ydysewrybi8hbPjtHNdhuQZ_5yLNwzmL_0ffILF25GCdsGvHp18gudB76bY_cYQfMmV0JvBPqk-Vb-7BlExBHecDI6VimtRZbLmGKKQK-TWl4VkLJzhCEIFtmUOydHSPalN9tpsfFwYy0QtcLZpQ99=w492-h410-no)

- 當相異類別數量相當⼤時，其他編碼⽅式效果更差，可以考慮雜湊編碼以節省時間

- 雜湊編碼效果也不佳，這類問題更好的解法是嵌入式編碼(Embedding)，但是需要深度學習並有其前提

### Label Encoding

- 類似於流⽔號，依序將新出現的類別依序編上新代碼，已出現的類別編上已使⽤的代碼
- 優點是能夠節省記憶體的使用量
- 確實能轉成分數，但缺點是分數的⼤⼩順序沒有意義

### One Hot Encoder

- 為了改良數字⼤⼩沒有意義的問題，將不同的類別分別獨立為⼀欄

- 缺點是需要較⼤的記憶空間與計算時間，且類別數量越多時越嚴重

- 當特徵重要性⾼，且可能值較少時，才應該考慮獨熱編碼

  - 二分類變數

    透過Label Encoder將資料轉換為1, 0的形式後進行分析

  - 多分類變數

    先將資料透過Label Encoder進行轉換，接著再運用One Hot Encoder轉換成虛擬變量。

    > 若未轉換成虛擬變量，會被視為連續型的特徵進行分析。（但資料的本質上卻沒有這個特質）

### 重要知識點複習

### 參考資料
- [平均数编码：针对高基数定性特征（类别特征）的数据预处理/特征工程](https://zhuanlan.zhihu.com/p/26308272)
## 順序型特徵

- 通常會用整數來描述這類資料間的強烈程度，但各個整數間的距離並不代表各個類別間的實際差距。因此類別間雖具有大小關係，但不能用來進行加減乘除等運算，如
  - Likert量表的滿意度調查
  - 班級排名

- 雖然對此資料進行加減運算並不符合定義，但通常會當作數值特徵處理，因為當作類別型會失去排序的資訊

### 重要知識點複習

### 參考資料

## 數值型特徵

- 資料之間的距離反映了實際的差距，因此可以進行加減運算，但可以再根據資料是否具有絕對的0，區分為等距資料與等比資料，前者的零點是任意選取的，因此對此類資料進行乘法和除法運算的結果並不唯一，意即對此類資料乘除並沒有意義；而等比資料的0具有絕對的0，因此允許乘除運算。
  - 等距尺度
    - 年份
    - 攝氏、華氏溫度
  - 等比尺度
    - 質量
    - 長度

### 遺漏值填補

- 填補統計值
  - 填補平均值(Mean) : 數值型欄位，偏態不明顯
  - 填補中位數(Median) : 數值型欄位，偏態很明顯
- 填補指定值 - 需對欄位領域知識已有了解
  - 補 0 : 空缺原本就有 0 的含意，如前⾴的房間數
- 填補預測值 - 速度較慢但精確，從其他資料欄位學得填補知識
  - 若填補範圍廣，且是重要特徵欄位時可⽤本⽅式
  - 本⽅式須提防overfitting : 可能退化成為其他特徵的組合

### 離群值處理

- 有時會有與其他數值差距很⼤的離群值存在，只要離群值數量夠少，除去離群值，將可使得模型預測較為準確。

- **判斷方式**

  - **標準分數**：將資料轉為Z分數，並以±3個標準差作為作為臨界值，超過設定的臨界值即判定為離群值。

  - **百分位數**：將最低與最高5%的資料視為離群值。

  - **四分位距(Interquartile range, IQR)**：

    從分位數的角度來偵測，IQR=Q3 - Q1

    將(Q1 - 1.5 * IQR)與 (Q3 + 1.5 * IQR)視為離群值

  - **平均絕對離差(Mean Absolute Deviation, MAD)**:$MAD = \frac{1}{n}\Sigma^n_1|x_i- \bar x| $

    將mean ± 9 * MAD外的值視為離群值。

- **處理方法**

  - **刪除樣本**：當離群值的數量相當少時，可以使用此方法
  - **刪除欄位**：若是題目設計的問題導致某欄位中存在許多離群值，可以考慮刪除該欄位。
  - **修正資料**：
    - 縮尾：將超出變數特定百分位元範圍的數值替換為其特定百分位數值的方法。    
    - 截尾：將超出變數特定百分位元範圍的數值予以**刪除**的方法。
    - 插值：應用原有資料資訊對離群值賦予一個相對合理的新值的方法

### 二值化

設定一個閾值，大於閾值的赋值為1，小於等於閾值的赋值為0，公式表達如下：
$$
x =
\begin{cases}
1,\quad x > threshold \\
0,\quad x \leq threshold
\end{cases}
$$

### Binning

以 age 這樣的特徵為例，你可以把所有年齡拆分成 n 段，0-20 歲、20-40 歲、40-60 歲等或是 0-18 歲、18-40 歲、40-70 歲等（等距或等量），然後把個別的年齡對應到某一段。

### 去除偏態 

- 對於左偏、右偏的資料型態都會減少平均數的代表性，因為會拉低/拉高整體的平均值(如台灣的平均薪資會被少數富翁拉高)。
- 對存在離群值的變數作對數轉換可以克服其離群值問題，且對數轉換並不影響各觀察值之間在此變數上的相對大小。轉換後資料的分佈會變得更加集中。
- 換句話說，雖然對數轉換並不改變原有數值的相對大小關係，但卻使得轉換後數值之間的相對距離縮小了。使資料更接近常態分佈，讓平均值更有代表性。
- 這時候可以考慮以下幾種處理方式：
  - 對數去偏(log1p) 
    - 對數去偏就是使⽤⾃然對數去除偏態
    - 常⾒於計數 / 價格這類非負且可能為 0 的欄位
    - 因為需要將 0 對應到 0，所以先加⼀ (plus one) 再取對數 (log)，還原時使⽤ expm1，也就是先取指數 (exp) 後再減⼀ (minus one)
  - ⽅根去偏(sqrt)
    - 就是將數值減去最⼩值後開根號，最⼤值有限時適⽤ (例 : 成績轉換) 
  - 分布去偏(boxcox) 
    - 是採⽤boxcox轉換函數，函數的 lambda(λ) 參數為 0 時等於 log 函數，lambda(λ) 為 0.5 時等於開根號 (即sqrt)，因此可藉由參數的調整更靈活地轉換數值，但要特別注意 Y 的輸入數值必須要為正 (不可為0)

### 特徵縮放(Feature Scaling)

- 以合理的⽅式，平衡特徵間的影響⼒
  - 標準化 (Standard Scaler) : 假定數值為常態分佈，適合本⽅式平衡特徵。若資料不符合常態分佈，使用此方法進行Normalization的效果會變得很糟糕。
    $$
    \frac {(x-mean(x))}{sd(x)}
    $$

  - 最⼩最⼤化 (MinMax Scaler) : 將資料按比例縮放，使之落入一個小的特定區間，例如[0, 1]等。在某些比較和評價的指標處理中經常會用到，去除數據的單位限制，將其轉化為無量綱的純數值，便於不同單位或量級的指標能夠進行比較和加權，歸一化公式如下：
    $$
    \frac {(x-min(x))}{max(x)-min(x)}
    $$

- 適用場合
  - 樹狀模型或非樹狀模型
    - 非樹狀模型 : 如線性迴歸, 羅吉斯迴歸, 類神經...等，標準化 / 最⼩最⼤化後對預測會有影響
    - 樹狀模型 : 如決策樹, 隨機森林, 梯度提升樹...等，標準化 / 最⼩最⼤化後對預測不會有影響
  - 標準化 / 最⼩最⼤化 使⽤上的差異
    - 標準化 : 轉換不易受到極端值影響
    - 最⼩最⼤化 : 轉換容易受到極端值影響

  > 去過離群值的特徵，比較適⽤最⼤最⼩化

### 重要知識點複習

- 補缺失值的⽅法因特徵類型與缺的意義不同，會有許多不同補法，需要因資料調整，無法⼀概⽽論
- 除了上⾯兩點，補缺失值還要注意盡量不要破壞資料分布
- 標準化的意義：平衡數值特徵間的影響⼒
- 因為最⼤最⼩化對極端數值較敏感，所以如果資料不會有極端值，或已經去極端值，就適合⽤最⼤最⼩化，否則請⽤標準化
- 離群值是與正常數值偏離較遠的數值群，如果不處理則特徵縮放(標準化 / 最⼩最⼤化)就會出現很⼤的問題
- 處理離群值之後，好處是剩餘資料中模型較為單純且準確，壞處是有可能刪除掉重要資訊，因此刪除前最好能先了解該數值會離群的可能原因
- 當離群資料比例太⾼，或者平均值沒有代表性時，可以考慮去除偏態
- 去除偏態包含 : 對數去偏、⽅根去偏以及分布去偏
- 使⽤ box-cox 分布去偏時，除了注意 λ 參數要介於 0 到 0.5 之間，並且要注意轉換前的數值不可⼩於等於 0

### 參考資料
- [离群值！离群值？离群值！](https://zhuanlan.zhihu.com/p/33468998)
- [【Python数据分析基础】: 数据缺失值处理](https://juejin.im/post/5b5c4e6c6fb9a04f90791e0c)
- [数据标准化/归一化normalization](https://blog.csdn.net/pipisorry/article/details/52247379)

- [偏度与峰度及其python实现](https://blog.csdn.net/u013555719/article/details/78530879)

- [Data Types: A Better Way to Think about Data Types for Machine Learning](https://towardsdatascience.com/7-data-types-a-better-way-to-think-about-data-types-for-machine-learning-939fae99a689)
- [Feature Engineering 特徵工程中常見的方法](https://vinta.ws/code/feature-engineering.html)
## 時間型特徵

> - 時間型特徵最常⽤的編碼⽅式
> - 如何將時間最重要的特性 「循環」 改成特徵
> - 時間常⾒的週期循環特徵有哪些，有什麼要注意的地⽅?

- 時間型特徵最常⽤的是特徵分解 - 拆解成年/⽉/⽇/時/分/秒的分類值
- 週期循環特徵是將時間"循環"特性改成特徵⽅式, 設計關鍵在於⾸尾相接, 因此我們需要使⽤ sin /cos 等週期函數轉換
- 常⾒的週期循環特徵有 - 年週期(季節) / 周周期(例假⽇) / ⽇週期(⽇夜與⽣活作息), 要注意的是最⾼與最低點的設置
- 雖然時間型特徵可當作數值型特徵或類別型特徵，但都不適合
  - 取總秒數雖可變為數值，但會失去週期性 (ex ⽉ / 星期)
  - 使⽤本⾝可以當作類別，但會失去排序資訊，類別數量也過⼤

- 週期循環特徵

  - **年週期：**與春夏秋冬季節溫度相關（正：冷 / 負：熱）

    $cos((月/6 + 日/180) \pi)$

  - **月週期：**與薪⽔、繳費相關

  - **週週期：**與周休、消費習慣相關（正：精神飽滿 / 負：疲倦）

    $sin((星期幾/3.5 + 小時/84) \pi)$

  - **日週期：**與⽣理時鐘相關（正：精神飽滿 / 負：疲倦）

    $sin((小時/12 + 分/720 + 秒/43200) \pi)$

  - 前述的週期所需數值都可由時間欄位組成, 但還⾸尾相接
    因此週期特徵還需以正弦函數( sin )或餘弦函數( cos )加以組合

- 時段特徵
  - 短暫時段內的事件計數，也可能影響事件發⽣的機率
    - 如 : 網站銷售預測，點擊網站前 10分鐘 / 1⼩時 / 1天 的累計點擊量
  - 以⼀筆 17:05 發⽣的網站瀏覽事件為例
    - 同樣是1⼩時的統計，基礎分解會統計當⽇ 17 時整個⼩時的點擊量
      時段特徵則是會統計 16:05-17:04 的點擊量
      兩者相比，後者較前者更為合理

### 重要知識點複習

### 參考資料
- [Python-基础-时间日期处理小结](http://www.wklken.me/posts/2015/03/03/python-base-datetime.html)
- [Basic date and time types](https://docs.python.org/3/library/datetime.html)
## 文本特徵

### 詞頻統計

- 如果是文本類型的數據，比如詞袋，則可以在文本數據預處理後，去掉停用詞，剩下的詞使用Hash技巧做一些詞頻統計。

![](C:\Users\TL_Yu\Google 雲端硬碟\Notes\DataScience\DataScience\v2-754386a5ad0c826688a41505b1227f6e_r.jpg)

### TD-IDF

- 到TF-IDF这种统计方法。字词的重要性随着它在文件中 出现的次数成正比增加，但同时会随着它在语料库中出现的频率成 反比下降。

### 特徵雜湊(Feature Hash)

- 類別型特徵最⿇煩的問題 : 相異類別的數量非常龐⼤, 該如何編碼?
  *舉例 : 鐵達尼⽣存預測的旅客姓名

- 特徵雜湊是⼀種折衷⽅案，將類別由雜湊函數定應到⼀組數字。調整雜湊函數對應值的數量，在計算空間/時間與鑑別度間取折衷，也提⾼了訊息密度, 減少無⽤的標籤

### 重要知識點複習

### 參考資料
- [数据特征处理之特征哈希（Feature Hashing）](https://www.jianshu.com/p/9c40b8dc60bf)
- [哈希算法-MD5](https://www.jianshu.com/p/2c01e37dcbd8?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation)
- [基于sklearn的文本特征抽取](https://www.jianshu.com/p/063840752151)
## 其他特徵

- 影像特徵

- 影片特徵

- 聲音特徵

## 特徵組合

### 數值與數值組合

> - 數值與數值的特徵組合，除了基礎的加減乘除等四則運算，最關鍵的部分是什麼?
> - 機器學習的關鍵⼜是什麼?

- 有些特徵需要一起考慮才有意義，如在分析計程車的運輸資料時，會有起點的經緯度與終點的經緯度等4個變項。
- 單獨各自使用「起點經度」、「起點緯度」、「終點經度」或「終點緯度」都是沒有意義的。必須要將這四個變數進行組合，並計算實際距離。或更細緻的處理每個緯度長度不一致的問題後計算實際距離，能夠再進一步提高預測的精準度。

### 類別與數值組合

> - 類別型特徵也能和數值特徵組成新特徵嗎?
> - 群聚編碼有哪些操作運算可以使⽤?
> - 群聚編碼與之前的均值編碼最主要有什麼不同?

- 群聚編碼(Group by Encoding)

  均值編碼是計算各個類別在目標變數的平均值，而群聚編碼則是針對其他數值變數計算類別平均值 (Mean)、中位數 (Median)，眾數(Mode)，最⼤值(Max)，最⼩值(Min)，次數(Count)...等。

- 群聚編碼的使用時機是，先以 領域知識 或 特徵重要性 挑選強⼒特徵後, 再將特徵組成更強的特徵

- 可以依照領域知識挑選,或亂槍打⿃後再以特徵重要性挑選

- 以前是以非樹狀模型為主, 為了避免共線性, 會很注意類似的特徵不要增加太多，但現在強⼒的模型都是樹狀模型, 所以只要有可能就通通可以做成特徵嘗試!

### 葉編碼

> - 多個分類預測結果，該如何合併成更準確的預測
> - 葉編碼的⽬的是什麼？如何達成該項⽬的？
> - 葉編碼編完後，通常該搭配什麼使⽤？

- 葉編碼 (leaf encoding) 顧名思義，是採⽤決策樹的葉點作為編碼依據重新編碼

- 概念是將每棵樹都視為⼀個新特徵，樹下的 n 個節點則作為新特徵的 n 個類別值，由於每個葉節點的性質接近，因此可視為資料的⼀種分組⽅式。

- 雖然不適合直接沿⽤樹狀模型機率，但分組⽅式有代表性，因此按照葉點將資料 離散化 ，會比之前提過的離散化⽅式跟有助於提升精確度

- 葉編碼的結果，是⼀組模型產⽣的新特徵，我們可以使⽤邏輯斯回歸，重新賦予機率 (如下葉圖)，也可以與其他算法結合 (例如 : 分解機 Factorization Machine )使資料獲得新⽣，最後再以邏輯斯迴歸合併預測

  ![](https://lh3.googleusercontent.com/Fu1ppabaRpOcfZ1EsWvGRBxVtLz113i_INBrujwkufjo9-xUvXbVrTruCUgSx04xMaJxOxlNb5jaXOganmyGA32mlcSAUIlNb4Po5qD-GRCWl9-khVuWx-5xAkF_jtmbUkc53PNsRZZCBr2PvzxCYlICqEzY_iaVVSjifprLrsosFhZjmkPhYlkO8u_wT1P80E4T65-XsKx9x-Wvk4M7ht9lD6NyV7iTGRjYtD1fsGBd8ILmIbVmMTswrjL6xiTt-EEGr6ZrW3hVqELLzoVFZ9jHk7uRA6BofNiEkZ2MCRiqpcDu8zlY_55pEmVQmB2GhRVl_fA7SH4TdL9U2UqHZSpbkPxXMAj4VIf75FdXadqudS6sJLTHPixaeQGOIkYBko_tuz-lWRj4uUNJNjYTrUTgbPrcPQRn_RLVN6UXWrrnnNMycPaifC2-9WRrR1Yip0pxlGW6GdhhekdMvEmQyrZYjG0mzWyaJjNGjSze6YFZeRRefmWakyK_mOqIBxUIub9zV_-VlNn43-MAte2RvuTGHWQ06Y8_TtixmQuHAnssN9DuQVU6B_x7nnMM5wec_6Bk2W6IBAnqHmZ_c2yt6cE7VBj5EeIGYHqMHg4AwTM3MJNXgl0cHE-mR5lHYWyzdrLsHC8knlwNiBUHvGowl5M6ZgFeXDDoNXMTdiFXKgTX3kAJmbzgAZwEUtyxFyprkn2VjxFkdL0j9N57OWDkO3SK=w459-h365-no)

### 葉編碼 (leaf encoding) + 邏輯斯迴歸

- 葉編碼需要先對樹狀模型擬合後才能⽣成，如果這步驟挑選了較佳的參數，後續處理效果也會較好，這點與特徵重要性類似

- 實際結果也證明，在分類預測中使⽤樹狀模型，再對這些擬合完的樹狀模型進⾏
  葉編碼+邏輯斯迴歸，通常會將預測效果再進⼀步提升

  ![](https://lh3.googleusercontent.com/UJ3qH8VF0i7DwMoG-5z24kopMAUon2gJzhNZ7uSKRGjEBBiJ_ATsXVrPl91IY8_uOlDq3QYrwtyu6klfXDz-3f5FhhS4kaxZl_gHGnMsfPD6kReUWYmJfOCs6Z5YkIXaxypD1YB8nxrN3DtnqW4TQ9TePasZ-59MNuZ7TeRc1N1wHl-WoE5eNr3IiyAUceVppDykQBw4rj7iSWlD1DD88R3QffNXuvnZV8DHI410XJJQ33YNuzuqY4XBpigkgM1XKeG2_Cg1nb0WohxoU9-sAnT8IA-fqKrIoUDYPq0Xbz4lZC2Kp12Tt0QxbLndap32oPIsaxQHoOMpkd91SAdHGaAypSPEzfyplfTJyPjdB4ccJdWcyaUYpw20UlfaYcM1BOMhYkNAFzZoy03VSVjDMMmwrAhTK0URhul8KvbxKXdG_df31w8hi40Syk-8Uk0YlMux2C5kOrp3vg4laCNAMOgJTf49d-T4GuOu__JQkK6DiMa5uph4NKrEbbBgnrh7bRSGQe0_oSRfTQr6t642bQzZH4TotOFmWW-BOJpKb0QhOwavihWO5P-VSeQ5b9D7nJaMau7ulBd8DVhARxzcTblALuR6aIpmIZ0EuWUCxu5GLtUlNxjSv0ICEWS5p9kISoUUhP3o779fwyKdBvLET9jwWunrc38ud8YYROabd1cefarrwQFfxGkE0p42k7a8WGbC7IjJP0zMCf2d5Qk0jZLq=w660-h242-no)

### 重要知識點複習

- 機器學習的關鍵在特徵⼯程；而特徵⼯程的關鍵在領域知識
- 類別特徵與數值特徵，可以使⽤群聚編碼組合出新的特徵群聚編碼最常使⽤的運算是 mean, 除此之外還有 median、mode、max、min、count等統計量可以使⽤
- 群聚編碼與之前的均值編碼最主要的差異，⼀個是特徵彼此之間與特徵⽬標值之間的差異，另⼀個最⼤的差異是 :群聚編碼因為與⽬標值無關，因此不容易 Overfitting，也因此比均值編碼使⽤頻率⾼得多
- 多個分類預測結果，需要先將機率倒推回對應數值，相加後再由sigmoid 函數算回機率，類似邏輯斯回歸的算法
- 葉編碼的⽬的是重新標記資料，以擬合後的樹狀模型分歧條件，將資料離散化，這樣比⼈為寫作的判斷條件更精準，更符合資料的分布情形
- 葉編碼編完後，因為特徵數量較多，通常搭配邏輯斯回歸或者分解機做預測，其他模型較不適合

### 參考資料

- [特征组合&特征交叉 (Feature Crosses)](https://segmentfault.com/a/1190000014799038)
- [利用python数据分析之数据聚合与分组](https://zhuanlan.zhihu.com/p/27590154)
- [Practical Lessons from Predicting Clicks on Ads at Facebook](http://quinonero.net/Publications/predicting-clicks-facebook.pdf)
- [Feature transformations with ensembles of trees](https://scikit-learn.org/stable/auto_examples/ensemble/plot_feature_transformation.html#example-ensemble-plot-feature-transformation-py)
- [CTR预估: Algorithm-GBDT Encoder](https://zhuanlan.zhihu.com/p/31734283)
- [三分鐘了解推薦系統中的分解機方法](https://kknews.cc/code/62k4rml.html)



## 特徵選擇

> - 特徵選擇/篩選與特徵組合的差異是?
> - 特徵選擇主要包含哪三⼤類⽅法?
> - 特徵選擇中，計算時間較長，但是能排除共線性且比較穩定的⽅式是哪⼀種?

### 特徵選擇概念

- 特徵需要適當的增加與減少，以提升精確度並減少計算時間
  - 增加特徵 : 特徵組合，群聚編碼
  - 減少特徵 : 特徵選擇
- 特徵選擇有三⼤類⽅法
  - 過濾法 (Filter) : 選定統計數值與設定⾨檻，刪除低於⾨檻的特徵
  - 包裝法 (Wrapper) : 根據⽬標函數，逐步加入特徵或刪除特徵
  - 嵌入法 (Embedded) : 使⽤機器學習模型，根據擬合後的係數，刪除係數低於⾨檻的特徵

### 過濾法(Filter)

按照發散性或者相關性對各個特徵進行評分，設定閾值或者待選擇閾值的個數選擇特徵。

- **方差選擇法**

  先要計算各個特徵的方差，然後根據閾值，選擇方差大於閾值的特徵

- **相關係數法**

  皮爾森相關係數是一種最簡單的，能説明理解特徵和回應變數之間關係的方法，該方法衡量的是變數之間的線性相關性，結果的取值區間為 $-1$ 至 $1$  ， $-1$ 表示完全的負相關(這個變數下降，那個就會上升)，$+1$ 表示完全的正相關，$0$ 表示沒有線性相關。

  Pearson相關係數的一個明顯缺陷是，作為特徵排序機制，他只對線性關係敏感。如果關係是非線性的，即便兩個變數具有一一對應的關係，Pearson相關性也可能會接近 $0$

- **卡方檢驗(K-Best)**

  傳統的卡方檢驗是檢驗類別變數對類別目標變數的相關性。假設自變數有 $N$ 種取值，目標變數有 $M$ 種取值，考慮自變數等於 $i$ 且目標變數等於 $j$ 的樣本頻數的觀察值與期望的差距，構建統計量：

$$
\chi^2 = \sum \frac{(A-E)^2}{E}
$$


### 包装法(Wrapper)

包裹型是指把特徵選擇看做一個特徵子集搜索問題，根據目標函數（通常是預測效果評分），每次選擇/刪除若干特徵，藉以評估效果。

- **向前搜索**

  前向搜索就是每次從剩餘未選中的特徵選出一個加入特徵集中，待達到閾值或者 $n$ 时，從所有的 $F$ 中選出錯誤率最小的。

- **向後搜索**

  先将 $F$ 设置为 $\{1,2,...,n\} $，然后每次删除一個特徵並評價，直到達到閾值或者為空，然後選擇最佳的 $F$ 。

- **遞歸特徵消除法**

  遞迴消除特徵法使用一個基礎模型來進行多輪訓練，每輪訓練後，消除若干權值係數的特徵，再基於新的特徵集進行下一輪訓練。



### 嵌入法(Embedded)

先使用某些機器學習的演算法和模型進行訓練，得到各個特徵的權值係數，根據係數從大到小選擇特徵。類似於Filter方法，但是是通過訓練來確定特徵的優劣。

- 基於懲罰項的特徵選擇法(Lasso)
  - 通過L1正則項來選擇特徵：L1正則方法具有稀疏解的特性，因此天然具備特徵選擇的特性，但是要注意，L1沒有選到的特徵不代表不重要，原因是兩個具有高相關性的特徵可能只保留了一個，如果要確定哪個特徵重要應再通過L2正則方法交叉檢驗。
  - L1懲罰項降維的原理在於保留多個對目標值具有同等相關性的特徵中的一個，所以沒選到的特徵不代表不重要。故可結合L2懲罰項來優化。
    - L1正則化是指權值向量w中各個元素的絕對值之和,L1正則化可以產生稀疏權值矩陣，即產生一個稀疏模型，可以用於特徵選擇
    - L2正則化是指權值向量w中各個元素的平方和然後再求平方根L2正則化可以防止模型過擬合（overfitting）。當然，一定程度上，L1也可以防止過擬合



- 基於模型的特徵選擇法(Model based ranking)
  - 直接使用機器學習演算法，針對每個單獨的特徵和目標變數建立預測模型。假如某個特徵和目標變數之間的關係是非線性的，可以用基於樹的方法（決策樹、隨機森林）、或者擴展的線性模型等。基於樹的方法比較易於使用，因為他們對非線性關係的建模比較好，並且不需要太多的調試。但要注意過擬合問題，因此樹的深度最好不要太大，再就是運用交叉驗證。通過這種訓練對特徵進行打分獲得相關性後再訓練最終模型。
  - 使⽤梯度提升樹擬合後，以特徵在節點出現的頻率當作特徵重要性，以此刪除重要性低於⾨檻的特徵

### 重要知識點複習

- 相對於特徵組合在增加特徵，特徵選擇/篩選是在減少特徵
- 特徵選擇主要包含 : 過濾法 (Filter)、包裝法 (Wrapper)與嵌入法 (Embedded)
- 特徵選擇中，計算時間較長，但是能排除共線性且比較穩定的⽅式是梯度提升樹嵌入法

### 參考資料

- [特征选择](https://zhuanlan.zhihu.com/p/32749489)
- [特徵選擇 Feature Selection](https://machine-learning-python.kspax.io/intro-1)
- [谈谈 L1 与 L2-正则项](https://liam.page/2017/03/30/L1-and-L2-regularizer/)

## 特徵評估

> - 樹狀模型的特徵重要性，可以分為哪三種?
> - sklearn 樹狀模型的特徵重要性與 Xgboost 的有何不同
> - 特徵⼯程中，特徵重要性本⾝的重要性是什麼

### 特徵重要性計算方法

- 在樹模型中特徵的分支次數：weight
- 特徵覆蓋度：cover
- 損失函數降低量：gain

### 機器學習中的優化循環

- 機器學習特徵優化，循環⽅式如圖

![](https://lh3.googleusercontent.com/dbXXWl0Muq9kKqU9j3atMgOR4JLlW1_ECk4UkC1g_xvkLXphfzx4IJJcYIGoY1sYV0cnqiRED_nlK_1KXF8Th-1CPac74FfD4y8XK-Z-whFFdpILHNdvV1rZNLIuxtqNdG2-1IP_lv_dAyBv49apA0KFBBG4ch_0xr-GKp_0DIqXzVOyr32ksT9Ra6JhescVbamZNkENhkLoB4lyA5fdy7BPSMtgB0Q94KW7NPKgtU0Q0xkteB50fQF0VceDiBmgeQB9ZVMpU89RSK0tVqgr-Gj09_i8DjEDGCcvvaRFePFiSzA-vnBEWnB9DhCiuYjQdK1E3HlJR0Y-oNH31q-p6-C5AhlKr2fQKbNxZJAnzLvVtIT5SM-10Jiwuxo0Eo9B-KB-3ysvlLVVggLy3SgB-hBfuCYLTbCj1PDMXbk0xapXe0f2EGBFziLo3aQYHISxJWx3oC6HMg3u1y9YA0fclufyGwZYS4Njkbpic0eUb3atkNyvPz-e5trOzdQHg2Bk7YMM_NL_Z9lsWgIUUKe7unhtE3Jo6tpxPLXrpIlrvjJ7T_JWNC9GYOMAA0Un3eIaNFqwFv2Lh4lcyjI8GIGZ25W9eIRbLjlS3Gd31FXP-EmpqH5NfWkYs4Lqpd4UJTJBcdQcwdiGn_8Q5oCed9FYSibOU1wcI4QV0aNKk0CpSRGI0fDxOfM9owxNSnz5jZEqiOzN_BWI-OIba9SYNOP7g2B3=w428-h387-no)

- 其中增刪特徵指的是

  - 特徵選擇(刪除)

    - 挑選⾨檻，刪除⼀部分特徵重要性較低的特徵

  - 特徵組合(增加)

    依領域知識，對前幾名特徵做特徵組合或群聚編碼，形成更強⼒特徵

- 由交叉驗證確認特徵是否有改善，若沒有改善則回到上⼀輪重選特徵增刪

### 排列重要性 (permutation Importance)

- 雖然特徵重要性相當實⽤，然⽽計算原理必須基於樹狀模型，於是有了可延伸⾄非樹狀模型的排序重要性
- 排序重要性計算，是打散單⼀特徵的資料排序順序，再⽤原本模型重新預測，觀察打散前後誤差會變化多少

### 重要知識點複習

- 樹狀模型的特徵重要性，可以分為分⽀次數、特徵覆蓋度、損失函數降低量三種
- sklearn 樹狀模型與 XGBoost 的特徵重要性，最⼤差異就是在 sklearn 只有精準度最低的「分⽀次數」
- 特徵重要性本⾝的重要性，是在於本⾝是增刪特徵的重要判定準則，在領域知識不⾜時，成為改善模型的最⼤幫⼿

### 參考資料

- [机器学习 - 特征选择算法流程、分类、优化与发展综述](https://juejin.im/post/5a1f7903f265da431c70144c)
- [Permutation Importances](https://www.kaggle.com/dansbecker/permutation-importance?utm_medium=email&utm_source=mailchimp&utm_campaign=ml4insights)

## 衍伸討論:有關樹狀模型與模型可解釋性

- 樹狀模型有幾個重要的應⽤
  - 特徵重要性(feature importance) : ⽬前是特徵選擇的最主流作法
  - 葉編碼 : 將特徵打散，完全依照樹狀模型的葉點重新編碼，再加上邏輯斯迴歸，可以再進⼀步提升分類預測能⼒
- 上述樹狀模型的獨特應⽤，都是基於⼈們對決策樹的理解與可解釋性(explainable)⽽有的設計
- 但⽬前深度學習的基礎 : 類神經網路，最缺乏的就是可以解釋性，若類神經網路能在可解釋性上更進⼀步，則可以想⾒也可以有更多的衍伸應⽤(例如 : capsule 模型)



## 參考資料

4. [干货：结合Scikit-learn介绍几种常用的特征选择方法](http://dataunion.org/14072.html)
5. [机器学习中，有哪些特征选择的工程方法？](http://www.zhihu.com/question/28641663/answer/41653367)



# 機器學習基礎模型建立

> 學習透過Scikit-learn等套件，建立機器學習模型並進行訓練

## 機器學習概論

> - 了解機器學習與⼈⼯智慧的意涵
> - 能夠說明機器學習、深度學習與⼈⼯智慧之間的差別
> - 機器學習中不同領域的意義與應⽤

- 機器學習是甚麼? 
  - 讓機器從資料中找尋規律與趨勢⽽不需要給定特殊規則
  - 給定⽬標函數與訓練資料，學習出能讓⽬標函數最佳的模型參數

### 機器學習的組成及應用

- **監督式學習 (Supervised Learning)**

  會有⼀組成對的 (x, y) 資料，且 x 與 y 之間具有某種關係，如圖像分類，每⼀張圖都有對應到的標記 (y)，讓模型學習到 x 與 y 之間的對應關係

  ⽬前主流且有⾼準確率的機器學習應⽤多以此類型為主，但缺點是必須要蒐集標註資料。

  應用在圖像分類、詐騙偵測領域。

- **非監督式學習(Unsupervised Learning)** 

  僅有 x 資料⽽沒有標註的 y，例如僅有圖像資料但沒有標記。
  非監督式學習通常透過降維 (Dimension Reduction)、分群 (Clustering) 的⽅式實現
  非監督式的準確率通常都低於監督式學習，但如果資料收集非常困難時，可應⽤此⽅法。

  應用在維度縮減、分群、壓縮等。

- **強化學習**

  增強式學習是透過定義環境(Environment)、代理機器⼈ (Agent)及獎勵 (Reward)，讓機器⼈透過與環境的互動學習如何獲取最⾼的獎勵。
  Alpha GO 就是透過增強式學習的⽅式訓練，增強式學習近幾年在棋類、遊戲類都取得巨⼤的進展，是⽬前非常熱⾨的研究領域。

  應用在下圍棋、打電玩。

### 參考資料

- [ML Lecture 0-1: Introduction of Machine Learning](https://www.youtube.com/watch?v=CXgbekl66jc)
- [機器學習的機器是怎麼從資料中「學」到東西的？](https://kopu.chat/2017/07/28/機器是怎麼從資料中「學」到東西的呢/)
- [我們如何教導電腦看懂圖像](https://www.ted.com/talks/fei_fei_li_how_we_re_teaching_computers_to_understand_pictures?language=zh-tw)

## 流程與步驟

> - 了解⼀個完整機器學習專案的細節
> - 機器學習專案的開發流程步驟
> - 每個步驟的意義及該如何進⾏

### 機器學習專案開發流程

- 資料搜集、前處理
- 定義⽬標與評估準則
- 建立模型與調整參數
- 導入

### 資料搜集、前處理

- 資料在哪? 結構為何?
  - 政府公開資料集、Kaggle 資料集
- 結構化資料
  - Excel 檔 (.xlsx)
  - CSV 檔 (.csv, 逗號分隔)
- 非結構化資料
  - 圖片
  - 影⾳
  - ⽂字
- 如何開啟、處理檔案?
  - 多數檔案都能使⽤ Python 的套件開啟
    - 開啟圖片: PIL, skimage, open-cv…
    - 開啟⽂件: pandas
  - 資料前處理
    - 缺失值填補
    - 離群值處理
    - 標準化

### 定義目標

- 回歸問題? 分類問題?
- 要預測的⽬標是甚麼? (target 或 y)
- 要⽤甚麼資料來進⾏預測? (predictor 或 x)
- 將資料分為
  - 訓練集, training set
  - 驗證集, validation set
  - 測試集, test set
- 設定評估準則
  - 不同問題有不同的評估指標
  - 回歸問題 (預測值為實數)
    - RMSE, Root Mean Square Error
    - Mean Absolute Error
    - R-Square
  - 分類問題 (預測值為類別)
    - Accuracy
    - F1-score
    - AUC, Area Under Curve

### 建立模型並調整參數

- 根據設定⽬標建立機器學習模型
  - Regression, 回歸模型
  - Tree-based model, 樹模型
  - Neural network, 神經網路
- 各模型都有其超參數需調整，根據經驗與對模型了解、訓練情形等進⾏調參

### 導入

- 建立資料搜集、前處理等流程
- 送進模型進⾏預測
- 輸出預測結果
- 視專案需求整合前後端
  - 建議統⼀資料格式，⽅便讀寫 (.json, .csv)
- 如何確立⼀個機器學習模型的可⽤性？
  - 當我們訓練好⼀個機器學習模型，為了驗證其可⾏性，多半會讓模型正式上線，觀察其在實際資料進來時的結果；有時也會讓模型跟專家進⾏PK，挑⼀些真實資料讓模型與專家分別測試，評估其準確率。

### 參考資料

- [The 7 Steps of Machine Learning (AI Adventures)](https://www.youtube.com/watch?v=nKW8Ndu7Mjw)

## 機器如何學習?

> - 了解機器學習的原理
> - 機器學習的模型是如何訓練出來的
> - 過擬合 (Overfitting) 是甚麼，該如何解決

### 機器學習有三個步驟

1. 定義好模型 (可以是線性回歸、決策樹、神經網路等等)
2. 評估模型的好壞
3. 找出讓訓練⽬標最佳的模型參數



### 定義好模型

- ⼀個機器學習模型中會有許多參數(parameters)，例如線性回歸中的 w (weights)跟 b (bias) 就是線性回歸模型的參數

- 當我們輸入⼀個 x 進到模型中，不同參數的模型就會產⽣不同的 ŷ 
  - 希望模型產⽣的 ŷ 跟真實答案的 y 越接近越好
  - 找出⼀組參數，讓模型產⽣的 ŷ 與真正的 y 很接近，這個步驟就有點像學習的概念

### 評估模型好壞

- 定義⼀個⽬標函數 (Objective function) 也可稱作損失函數 (Loss function)，來衡量模型的好壞，Loss 越⼤，代表這組參數的模型預測出的 ŷ 越不準，也代表不應該選這組參數的模型
  - **線性回歸模型**：觀察「預測值」 (Prediction) 與「實際值」 (Ground truth) 的差距

    - MAE, Mean Absolute Error
    - MSE, Mean Square Error：$MSE = \frac{1}{n} \sum_{n=1}^n{(y_i - y_i^~)}^2$

    - R-square, 範圍: [0, 1]

  - **分類模型**：觀察「預測值」 (prediction) 與「實際值」 (Ground truth) 的正確程度

      - Accuracy
    - AUC, Area Under Curve

    - Precision: 模型判定瑕疵，樣本確實為瑕疵的比例

    - Recall: 模型判定的瑕疵，佔樣本所有瑕疵的比例
    - F1 - Score (Precision, Recall), 範圍: [0, 1]


### 找出最佳參數

- 模型的參數組合可能有無限多組，我們可以⽤暴⼒法每個參數都試看看，從中找到讓損失函數最⼩的參數
- 但是這樣非常沒有效率，有許多像是梯度下降 (Gradient Descent)、增量訓練 (Additive Training) 等⽅式，這些演算法可以幫我們找到可能的最佳模型參數

### 過擬合 (Over-fitting)

- 模型的訓練⽬標是將損失函數的損失降⾄最低

- 過擬合代表模型可能學習到資料中的噪⾳，導致在實際應⽤時預測失準

  ![](https://lh3.googleusercontent.com/LX_68rjUR9qhcmgY6IKZaBFmoEG_xsOiHx8scVquqB7nrwHHSvlB8JJ74OpZxlPOS4Vyv04LRc2bTChyXOVx5eZQl2v6s2DGyhdCHy_UFD7QzZOlsPNFhZ-Ogxi0uP0RevdIe0qQs0YMu4XiOYpoR8KY1rPH9oci-z0W0-lx2JLeopj2gAZUpbvol2uwUqS0aR29-5DnfWka5Bp6ua5Urkb9ai0BWMejvG3ZiJDgAANypm0qrBbQvWFTQCS79qyxalNL3HoQvZlrimGf_IviHUADpDOMnyxNUrXOzAthzdht3CqpDZ6UgL2TDQtXs9W6xXYdhp4cZPKZhAOHKOT7KDhQfrHVrCAmFCFy7rbubY6VTAreKknnK--GAHct3UDoOWVA7aFmNFkwqYUjPLaq4IzRhDqfvP2HSeoTij0GtfvpNIbQP7RSr08Qmf1P-lkdxQnP_JBydYLvwufPi0OKle5sFXIlgn6ugR1yzg9HxAxAsOf7iVZi17ZLprA5VVEEWds__ZEBBYfp3dxuBi5rj4cYZRSc0OgYob4MYPcNkP1J9a54mAups7xNxwyQdySBBYmMgsMetfd056fIS88iPPbMQhqUT15NaxOBNNS1X8T44MixoiI4maFwxU5PWZFJwZuUq6R_YWPoAI5QC2lZ_m2Nj-VtU5ZTHkhlurasDP3JlEFj6x-vnXs1a35qlmkzaqlBaJbMPoJY3bWpPMXBKjUD=w958-h333-no)

- 如何知道模型已經過擬合了?

- 學習曲線 Learning curve

  - 保留⼀些測試資料，觀察模型對於訓練資料的誤差與測試資料的誤差，是否有改變的趨勢

    ![](https://lh3.googleusercontent.com/indn0jbFtM0f4fjkVVa2cyQBimJMOX_cwJOpECDnHkUmV7oBntTy94xBIxjgpgpEjwasMhE4UKPzmhrBVLXp6BObiGP_7aKjC9TDX0CTnEGDnqMQvhh8qcS6GabOyPp1J2oUgki4-Re1OyL3C9N3ZYZY8gAeF8ETgwSDKAhOffppJNwoRBEmEQ-iC0MTFqYLski8LqRb-cJ86emvjz_JLhL7jNb8I_IVRauCUedFj_QFRhmoDWkzabIjW_y5HTAEy7shvqgYwQXgZFIaSFplwQ9ILZZ2pFjEO_IdSrlgW3J9-28vDMAVo7kfXYFpSAb7vu6adSmsRl5DEM3BwjDHWyWzL783oeXhmzKkv0lvjx5N26OOkiObSnYMLFmlqbg_OWgZDKA4qEuAas0KLPaeI-b4TyrLcLkElHaHN4f1YRmSh_KX0t1UcA3Dua8DHTzjupH1zM_6Z4xoqQu7nNUpHYgmFTM0jTIhIspdmAYigBAo2AjLfuXAtVQvx-qZHBbnJ3D7BbHOAPeJgbTRovqVg_sjYB7a6Ii9OQ3lZE2hfcB4vwAgUX7KLuw43Hst2kWUFeO_vVmQYOP3ARZVaF61twRamQCfRLKejOR92IyUzInEswPtkvnBzQYibVZj2aMXpPaJlsKXf-oNdL9h3C7rAgmIU-KP7duCh8aGCZ0UIrN5lq6pPes6At3ztyP6gzCMB0p6SpsWPZbU93Vgzic8eSnY=w560-h420-no)

- **如何解決過擬合或⽋擬合**
  - 過擬合
    - 增加資料量
    - 降低模型複雜度
    - 使⽤正規化 (Regularization)
  - ⽋擬合
    - 增加模型複雜度
    - 減輕或不使⽤正規化



### 參考資料

- [機器學習老中醫：利用學習曲線診斷模型的偏差和方差](http://www.sohu.com/a/218382300_465975)
- [谈谈 Bias-Variance Tradeoff](https://liam.page/2017/03/25/bias-variance-tradeoff/)
- [ML Lecture 1: Regression - Case Study](https://www.youtube.com/watch?v=fegAeph9UaA)



## 訓練/測試集切分

> - 了解機器學習中資料的切分
> - 為何要進⾏訓練/測試集切分
> - 不同的切分⽅法以及意義

### 為何需要切分訓練/測試集?

- 機器學習模型需要資料才能訓練，若將⼿上所有資料都送進模型訓練，這樣就沒有額外資料來評估模型訓練情形！

- 機器學習模型可能會有過擬合 (Over-fitting) 的情形發⽣，需透過驗證/測試集評估模型是否過擬合

- 有些資料要特別注意!
  - 時間序列資料
  - 同⼀⼈有多筆資料
  
  ```python
  sklearn.model_selection.train_test_split()
  ```

### K-fold Cross-validation

- 若僅做⼀次訓練/測試集切分，有些資料會沒有被拿來訓練過，因此後續就有 cross-validation 的⽅法，可以讓結果更為穩定，Ｋ為 fold 數量

- 每筆資料都曾經當過⼀次驗證集，再取平均得到最終結果。

- 根據切分的方法不同，交叉驗證分為下面三種：　　　

  - 簡單交叉驗證，所謂的簡單，是和其他交叉驗證方法相對而言的。首先，我們隨機的將樣本資料分為兩部分（比如： 70%的訓練集，30%的測試集），然後用訓練集來訓練模型，在測試集上驗證模型及參數。接著，我們再把樣本打亂，重新選擇訓練集和測試集，繼續訓練資料和檢驗模型。最後我們選擇損失函數評估最優的模型和參數。　

  - 第二種是 S 折交叉驗證（ S-Folder Cross Validation），和第一種方法不同， S 折交叉驗證先將資料集 D 隨機劃分為 S 個大小相同的互斥子集，即

    $$D=D_1\cup D_2\cup ...\cup D_S,D_i\cap D_j=\varnothing(i\ne j)$$

    每次隨機的選擇 份作為訓練集，剩下的1份做測試集。當這一輪完成後，重新隨機選擇 份來訓練資料。若干輪（小於 ）之後，選擇損失函數評估最優的模型和參數。注意，交叉驗證法評估結果的穩定性和保真性在很大程度上取決於 取值。

  - 第三種是留一交叉驗證（Leave-one-out Cross
    Validation），它是第二種情況的特例，此時 S 等於樣本數 N ，這樣對於 N 個樣本，每次選擇 N-1 個樣本來訓練資料，留一個樣本來驗證模型預測的好壞。此方法主要用於樣本量非常少的情況，比如對於通適中問題， N 小於 50 時，我一般採用留一交叉驗證。

    ![](https://lh3.googleusercontent.com/Q8wUvU5LNtUC-KfgXi6onDlAYzhwzrMtJLqAETx9lxiICpwMQ6avrzQZeZuTbk4jLfy8yLzQE8GtQVPhvwQLLgBCwHahR80HYHnhk9HFYw2XFXojQJyN1aCx4xGwIKHXws0zaCJhfP2fvpcaRcjyX6qpeyTANWU6x8PgTaG7QZibxwBa0HhRGkZvFGJvgpEg8cQRENu7O3tVghzmIrTMDl_DT1R71SLi5cuC8nRWwfgy2mC7k5QZQemELATPskGnC9m8ocq6j526DKheHdUzg_H-RNnsXW4VSZ0SAmtrxM2wYv4Yr-giyt2aKau593Ed7IV052HnELmbfAK02ytqJ4STKzgQODjgydWn686EgWfb2XsEjg-_pppEbeNL5PGbHxGdSrrGVLSH_njIWlA6AGnT5Zl5N6EaCYvqqOmz_d3bF2I1uXyHEBdW9DLk-Biw-I7wfoe-1VYG7PVzQuNNYktqS59V3jq71PbMB0JlwnoYq0NeFEBHiAr4LlSCNLkRUnNLIx36BM7yWvCANBz7ueVNnSrdp6wXachkE5i9CGqkZHodJTs1L05ztMF3e-quBPhd87tfa_zwRO74sE44PofvkH38qvFE0--rQJnXHWZZ9n88ilp12CYyxrhRLWEoCMpDA3ZQPlTk9yARiH-Em5EfHu8xppfFGz5gdf6zvROpAxFtbrVKMmHKkchUIG9x79xLl7ZYzNesryK6qLirr41EH-Dd2S29eGEBkEMFHLiQ8fQ=w665-h303-no)

    ```python
    sklearn.model_selection.KFold()
    ```

### 常見問題

- 驗證集 (validation set) 與測試集 (testing set)有甚麼差異？
  - 驗證集常⽤來評估不同超參數或不同模型的結果。⽽測試集則是在機器學習專案開始前先保留⼀⼩部分資料，專案進⾏中都不能使⽤，最終再拿來做測試。

### 參考資料

- [ML Lecture 2: Where does the error come from?](https://www.youtube.com/watch?v=D_S6y0Jm6dQ&feature=youtu.be)

## Regression vs. Classification

> - 了解機器學習中迴歸與分類的差異
> - 迴歸問題與分類問題的定義
> - 什麼是多分類問題？多標籤問題？

### 迴歸 vs. 分類

- 機器學習的監督式學習中主要分為回歸問題與分類問題。
- 回歸代表預測的⽬標值為實數 (-∞ ⾄ ∞)
- 分類代表預測的⽬標值為類別 (0 或 1)

### 回歸轉分類

- 回歸問題是可以轉化為分類問題
  - 模型原本是直接預測⾝⾼ (cm)
  - 可更改為預測是否⾼於中位數 (yes or no)
  - 或是預測多個類別，如矮、中等、⾼
- 可根據專案的需求⾃⾏變化⽬標定義

### binary-class vs. Multi-class

- ⼆元分類，顧名思義就是⽬標的類別僅有兩個。像是詐騙分析 (詐騙⽤⼾ vs. 正常⽤⼾)、瑕疵偵測 (瑕疵 vs. 正常)
- 多元分類則是⽬標類別有兩種以上。如⼿寫數字辨識有 10 個類別(0~9)，影像競賽 ImageNet 更是有⾼達 1,000 個類別需要分類

### Multi-class vs. Multi-label

- 當每個樣本都只能歸在⼀個類別，我們稱之為多分類 (Multi-class) 問題；⽽⼀個樣本如果可以同時有多個類別，則稱為多標籤 (Multi-label)。
- 了解專案的⽬標是甚麼樣的分類問題並選⽤適當的模型訓練。

## 評估指標選定

> - 了解機器學習中評估指標的意義及如何選取
> - 迴歸、分類問題應選⽤的評估指標
> - 不同評估指標的意義及何時該使⽤

- 設定各項指標來評估模型預測的準確性，最常⾒的為準確率

  (Accuracy) = 正確分類樣本數/總樣本數

- 不同評估指標有不同的評估準則與⾯向，衡量的重點有所不同

### 回歸模型

- 觀察「預測值」 (Prediction) 與「實際值」 (Ground truth) 的差距
  - MAE, Mean Absolute Error, 範圍: [-∞, ∞]
  - MSE, Mean Square Error, 範圍: [-∞, ∞]
  - R-square, 範圍: [0, 1]

### 分類模型

- 觀察「預測值」 (prediction) 與「實際值」 (Ground truth) 的正確程度
- AUC, Area Under Curve, 範圍: [0, 1]
  - AUC 指摽是分類問題常⽤的指標，通常分類問題都需要定⼀個閾值(threshold) 來決定分類的類別 (通常為機率 > 0.5 判定為 1, 機率 < 0.5 判定為 0)
  - AUC 是衡量曲線下的⾯積，因此可考量所有閾值下的準確性，因此 AUC 也廣泛地在分類問題的比賽中使⽤

- F1 - Score (Precision, Recall), 範圍: [0, 1]

  - 分類問題中，我們有時會對某⼀類別的準確率特別有興趣。例如瑕疵/正常樣本分類，我們希望任何瑕疵樣本都不能被漏掉。

  - Precision，Recall 則是針對某類別進⾏評估

    - Precision: 模型判定瑕疵，樣本確實為瑕疵的比例
    - Recall: 模型判定的瑕疵，佔樣本所有瑕疵的比例
      (以瑕疵檢測為例，若為 recall=1 則代表所有瑕疵都被找到)

  - F1-Score 則是 Precision, Recall 的調和平均數

    ![](https://lh3.googleusercontent.com/WuhO8uADwyUc_zcO6NQUfClOSxE3kL-MpaM_tizuZS8kbthqkD2RghJBybUDwGD4YDDdexEKsnvSWxcRX_dChJDBfm_mopblXL9_AzGwtYFg1Xx18q3f5McR-pXGI2QaWD56Yu4QE8Ao5NdxjJpsu4clvxm4Q2ImAQPwP-W1siThlAnC5mCYR7VQdkB78uWeJnp6EZXpRse_4ufg96fqLP5Q0F1SWpqV_FFJoErhqekOWzIFWzc6P_sP9IHwARGkQRb4dq703zrWYp1-Hjl7LQaCjMhIzXXPZYlD0WWyQI_USaOhFLz5vXClBW3S0EtfP9RSZxNjpaYb4Bqgb5FQ2hegnfFrkwqbV8IiDkYkk4wslWcEvtBd74qlcEuPk_OkSD8151WPrAt1zkyw9fisWi1rLPWB4u-nkTWOxP22q2VxzGHmNMx-nVRu-LyXc-ANdNOG_U0T-bxITrIrwlx0aX-O5WXgc79p8p8kBJVDyH989mpG7KwV2cSJAcLUk_d03x6slHGQocVPVyGraiMjFfRLNmyfucPzmD4zt6mhfAyrSHKr7yTAY7UTcGosBG4GL_RwL0mn2ae7LxWHTubmQACVyAUvmCtv6LBxQwtAgz0Z2BIXualv84qQUP2hZ-vEu8dyUcHLhGCxWzTsPh0QWjq0oFRv5aXJiS-d9xiYucAUu-phWro30REt5VYc__2zC87hDLWB_0dxjF3ilSROyKNq=w504-h915-no)

  - 分類 - 混淆矩陣 (Confusion Matrix)
  
    - 縱軸為模型預測
    - 橫軸為正確答案
    - 可以清楚看出每個 Class 間預測的準確率，完美的模型就會在對⾓線上呈現 100 % 的準確率
    
    ![](https://lh3.googleusercontent.com/DiuwfL6oiiIiKkOKoE8jnu1vvZvaz9srnBi7FzgdR6EPjaX9hribT5ALXt8QPd87stJo-3S39zVBaFMx334SsLCuafq1Lvk7-XUiEWpyM-5Esoz0zWHQ5WWrK4MMecWD2VCJ_g6fIhdrYmJRnMWMHdMMIiGRZEIAYjmAHgZVyhFLwA5urbsHKY2uw_so2VZrl8WHd_bNc0-8Gx-tI5-DisPuh5XHMc7T4nAUdzyLC7HKy8BqqCVj6NOyRJpyaGaWLKyygah6oLv-liuk7-FT0B1hGif_E9dz-se6s-BvbJBRe63nW7AYx6CB0RoOsUkkK-2DYZ7ur3SXyInw6dnaMj7GAOCK4pgIqhoz7Sbg_09zMH7qMTRM8TFOYgHUWc3YL424hXjNfXhncAUIgRQ4XKMtuBcTakdlgK72HmHPhYuUPt3Jdno18vf3w9Pwa1JQKbDDRzx9I5fObJrpKgoxYdAcib_71sgsHf1T0fltk-D_y88kveds_Wi7BNbGgUX62WYwLkQ4py-_L2pgWtx5mL-VpOecUa_1_MEz0xmKUNtEWnCrEY3VyAPrD6GxwYNm4KD8QAw1ACuoPGsiDHzxllFJktHntUMITELx93jFoP94VFjeF8nDq0L0_1LIM8_GZwAJ5HGHBZTIOppIcHA4eAODS3sBUh4KHiDtzvge6Z2omebnxqKpUx7DmNEM3iynjEr7BtbNscxi24Ne1hEmsbkI=s600-no)

### 常見問題

- 這麼多評估指標，該怎麼選擇?
  - 回歸問題可以透過 R-square 很快了解預測的準確程度；分類問題若為⼆分類 (binary classification)，通常使⽤ AUC 評估。但如果有特別希望哪⼀類別不要分錯，則可使⽤ F1-Score，觀察 Recall 值或是Precision 值。若是多分類問題，則可使⽤ top-k accuracy，k 代表模型預測前 k 個類別有包含正確類別即為正確 (ImageNet 競賽通常都是比 Top-5 Accuracy)
- Sklearn 的 AUC 計算結果怪怪的？F1-Score 計算時出現錯誤？
  - AUC 計算時 y_pred 的值必須填入每個樣本的預測機率 (probability) ⽽非分類結果！
  - F1-Score 計算時則需填入每個樣本已分類的結果，如機率 >=0.5 則視為 1，⽽非填入機率值

### 參考資料

- [ROC curves and Area Under the Curve explained (video)](https://www.dataschool.io/roc-curves-and-auc-explained/)
- [机器学习模型评估](https://zhuanlan.zhihu.com/p/30721429)

## Regression 模型

> - 了解線性回歸與羅吉斯回歸的基本定義
> - 線性回歸與羅吉斯回歸的差異
> - 回歸模型使⽤上的限制

### Linear Regression

- 簡單常⾒的線性模型，可使⽤於回歸問題。
- 線性回歸通過使用最佳的擬合直線（又被稱為回歸線），建立因變數 Y 和一個或多個引數 X 之間的關係。
- 它的運算式為：$Y = a + bX + e$  ，其中 $a$ 為直線截距，$b$ 為直線斜率，$e$ 為誤差項。如果給出了自變量 $X$ ，就能通過這個線性回歸表達式計算出預測值，即因變數 $Y$。

- 透過最小平方法(Ordinal Least Square, OLS)期望將$\sum(Y-\hat{Y})^2$最小化
  $$
  b = \frac{Cov_{XY}}{S_x^2} = \frac{\sum^n_{i=1}(x_i - \bar{x})(y_i - \bar{y})}{\sum^n_{i=1}(x_i - \bar{x})^2}
  $$

  $$
  a = \bar{Y} - b\bar{X}
  $$

- 訓練速度非常快，但須注意資料共線性、資料標準化等限制。通常可作為 baseline 模型作為參考點

- Scikit-learn 中的 linear regression
  ```python
  from sklearn.linear_model import LinearRegression
  reg = LinearRegression().fit(X, y)
  ```

  

### Logistics Regression

- 雖然有回歸兩個字，但 Logsitics 是分類模型

- 將線性回歸的結果，加上 Sigmoid 函數，將預測值限制在 0 ~ 1之間，即為預測機率值。

- Scikit-learn 中的 Logistic Regression

  ```python
  from sklearn.linear_model import LogisticRegression
  reg = LogisticRegression().fit(X, y)
  ```

### LASSO, Ridge Regression

> - 了解 Lasso, Ridge 回歸的基本定義
> - Lasso, Ridge 回歸的差異
> - L1 / L2 的意義與使⽤

- 機器學習模型中的⽬標函數
  - 機器學習模型的⽬標函數中有兩個非常重要的元素

    - 損失函數 (Loss function)

      損失函數衡量預測值與實際值的差異，讓模型能往正確的⽅向學習

    - 正則化 (Regularization)

      正則化是為了解決過擬合問題，分為 L1 和 L2 正則化。主要通過修正損失函數，加入模型複雜性評估

      正則化是符合**奧卡姆剃刀原理**：在所有可能的模型中，能夠很好的解釋已知數據並且十分簡單的才是最好的模型。

- 回歸模型與正規化

  - 先前學習到的回歸模型，我們只有提到損失函數會⽤ MSE 或 MAE
  - 為了避免 Over-fitting，我們可以把正則化加入⽬標函數中，此時⽬標函數 = 損失函數 + 正則化
  - 正則化可以懲罰模型的複雜度，當模型越複雜時其值就會越⼤

- 正則化函數

  - ⽤來衡量模型的複雜度

  - 該怎麼衡量？有 L1 與 L2 兩種函數

    - L1： $\alpha \sum|weights|$

      向量中各元素絕對值之和。又叫做稀疏規則運算元（Lasso regularization）。關鍵在於能夠實現特徵的自動選擇，參數稀疏可以避免非必要的特徵引入的雜訊

    - L2： $\alpha \sum(weights)^2$

      L2 正則化。使得每個元素都盡可能的小，但是都不為零。在回歸裡面，有人把他的回歸叫做嶺回歸（Ridge Regression），也有人叫他 “權值衰減”（weight decay） 

  - 這兩種都是希望模型的參數數值不要太⼤，原因是參數的數值變⼩，噪⾳對最終輸出的結果影響越⼩，提升模型的泛化能⼒，但也讓模型的擬合能⼒下降

- LASSO, Ridge Regression

  - LASSO 為 Linear Regression 加上 L1
  - Ridge 為 Linear Regression 加上 L2
  - 其中有個超參數 α 可以調整正則化的強度
  - 簡單來說，LASSO 與 Ridge 就是回歸模型加上不同的正則化函數
    - L1 會趨向於產生少量的特徵，而其他的特徵都是 0
    -  L2 會選擇更多的特徵，這些特徵都會接近於 0

- Sklearn 使⽤ Lasso Regression

  ```python
  from sklearn.linear_model import Lasso
  reg = Lasso(alpha=0.1)
  reg.fit(X, y)
  print(reg.coef_) # 印出訓練後的模型參數
  ```

- Sklearn 使⽤ Ridge Regression

  ```python
  from sklearn.linear_model import Ridge
  reg = Ridge (alpha=0.1)
  reg.fit(X, y)
  print(reg.coef_) # 印出訓練後的模型參數
  ```

  



### 常見問題

- 這些模型的數學式⼦都很多，⼀定要完全看懂才繼續往下嗎? 不會推導可以嗎?
  - 回歸模型是機器學習模型中的基礎，雖然實務上應⽤的機會不多 (因為模型過於簡單)，但是之後更複雜的模型都是基於回歸模型做加強，所以對基本原理有⼀定的了解會比較好。畢竟 Python 使⽤線性回歸只要⼀⾏程式碼，但是不了解原理，就會陷入當遇到錯誤不知如何修正的情況。
- Lasso 跟 Ridge 都是回歸問題的模型，那麼在使⽤時應該先⽤哪個模型跑呢？
  - 從模型的特性來看，Lasso 使⽤的是 L1 regularization，這個正則化的特性會讓模型變得較為稀疏，除了能做特徵選取外，也會讓模型變得更輕量，速度較快。實務上因為訓練回歸模型非常容易，可以兩者都跑跑看，在比較準確率，應該不會有太⼤的差異！

### 參考資料

- [Logistic Regression — Detailed Overview](https://towardsdatascience.com/logistic-regression-detailed-overview-46c4da4303bc)
- [線性迴歸的運作原理](https://brohrer.mcknote.com/zh-Hant/how_machine_learning_works/how_linear_regression_works.html)
- [邏輯斯回歸(Logistic Regression) 介紹]([https://medium.com/jameslearningnote/%E8%B3%87%E6%96%99%E5%88%86%E6%9E%90-%E6%A9%9F%E5%99%A8%E5%AD%B8%E7%BF%92-%E7%AC%AC3-3%E8%AC%9B-%E7%B7%9A%E6%80%A7%E5%88%86%E9%A1%9E-%E9%82%8F%E8%BC%AF%E6%96%AF%E5%9B%9E%E6%AD%B8-logistic-regression-%E4%BB%8B%E7%B4%B9-a1a5f47017e5](https://medium.com/jameslearningnote/資料分析-機器學習-第3-3講-線性分類-邏輯斯回歸-logistic-regression-介紹-a1a5f47017e5))
- [你可能不知道的邏輯迴歸 (Logistic Regression)](https://taweihuang.hpd.io/2017/12/22/logreg101/)
- [Linear regression with one variable](https://www.coursera.org/lecture/machine-learning/model-representation-db3jS)
- [逻辑回归常见面试题总结](https://www.cnblogs.com/ModifyRong/p/7739955.html)
- [Homemade Machine Learning](https://github.com/trekhleb/homemade-machine-learning)
- [2 WAYS TO IMPLEMENT MULTINOMIAL LOGISTIC REGRESSION IN PYTHON](http://dataaspirant.com/2017/05/15/implement-multinomial-logistic-regression-python/)
- [脊回归（Ridge Regression）](https://blog.csdn.net/daunxx/article/details/51578787)
- [Linear least squares, Lasso,ridge regression有何本质区别？](https://www.zhihu.com/question/38121173)

## Tree based model

> - 了解決策樹的原理、定義與其使⽤限制
> - 如何⽤ gini-index / entropy 來衡量資料相似程度
> - 決策樹是如何對⼀筆資料做決策

### 决策树

> - 透過⼀系列的是非問題，幫助我們將資料進⾏切分
> - 可視覺化每個決策的過程，是個具有非常⾼解釋性的模型

- 從訓練資料中找出規則，讓每⼀次決策能使訊息增益(Information Gain) 最⼤化
- 訊息增益越⼤代表切分後的兩群資料，群內相似程度越⾼

- 訊息增益 (Information Gain)

  決策樹模型會⽤ features 切分資料，該選⽤哪個 feature 來切分則是由訊息增益的⼤⼩決定的。希望切分後的資料相似程度很⾼，通常使⽤吉尼係數來衡量相似程度。

- 衡量資料相似: Gini vs. Entropy

  - 兩者都可以表示數據的不確定性，不純度

    - Gini 指數的計算不需要對數運算，更加高效；
    - Gini 指数更偏向於連續属性，熵更偏向於離散屬性。

    $Gini = 1 - \sum_j p_j^2$

    $Entropy = - \sum_jp_j log_2 p_j$

- 決策樹的特徵重要性 (Feature importance)
  - 我們可以從構建樹的過程中，透過 feature 被⽤來切分的次數，來得知哪些features 是相對有⽤的
  - 所有 feature importance 的總和為 1
  - 實務上可以使⽤ feature importance 來了解模型如何進⾏分類

- 使⽤ Sklearn 建立決策樹模型

  ```python
  from sklearn.tree_model import DecisionTreeRegressor
  from sklearn.tree_model import DecisionTreeClassifier
  clf = DecisionTreeClassifier()
  ```

  - Criterion: 衡量資料相似程度的 metric
  - Max_depth: 樹能⽣長的最深限制
  - Min_samples_split: ⾄少要多少樣本以上才進⾏切分
  - Min_samples_lear: 最終的葉⼦ (節點) 上⾄少要有多少樣本

### 隨機森林

> - 了解隨機森林的基本原理與架構
> - 決策樹與隨機森林的差異
> - 隨機森林如何彌補了決策樹的缺點

- 決策樹的缺點

  - 若不對決策樹進⾏限制 (樹深度、葉⼦上⾄少要有多少樣本等)，決策樹非常容易 Overfitting
  - 為了解決決策樹的缺點，後續發展出了隨機森林的概念，以決策樹為基底延伸出的模型

- 集成模型

  - 集成 (Ensemble) 是將多個模型的結果組合在⼀起，透過投票或是加權的⽅式得到最終結果
  - 透過多棵複雜的決策樹來投票得到結果，緩解原本決策樹容易過擬和的問題，實務上的結果通常都會比決策樹來得好

- 隨機森林 (Random Forest), 隨機在哪？

  - 訓練樣本選擇方面的 Bootstrap方法隨機選擇子樣本
  - 特徵選擇方面隨機選擇 k 個屬性，每個樹節點分裂時，從這隨機的 k 個屬性，選擇最優的。
  - 隨機森林是個集成模型，透過多棵複雜的決策樹來投票得到結果，緩解原本決策樹容易過擬和的問題。

- 訓練流程

  1. 從原始訓練集中使用bootstrap方法隨機有放回採樣選出 m 個樣本，共進行 n_tree 次採樣，生成 n_tree 個訓練集

  2. 對於 n_tree 個訓練集，我們分別訓練 n_tree 個決策樹模型

  3. 對於單個決策樹模型，假設訓練樣本特徵的個數為 n_tree，那麼每次分裂時根據資訊增益/資訊增益比/基尼指數選擇最好的特徵進行分裂

  4. 每棵樹都一直這樣分裂下去，直到該節點的所有訓練樣例都屬於同一類。在決策樹的分裂過程中不需要剪枝

  5. 將生成的多棵決策樹組成隨機森林。

     - 對於分類問題，按多棵樹分類器投票決定最終分類結果
     - 對於回歸問題，由多棵樹預測值的均值決定最終預測結果

      

- 使⽤ Sklearn 中的隨機森林

  ```python
  from sklearn.ensemble import RandomForestClassifier
  from sklearn.ensemble import RandomForestRegressor
  clf = RandomForestRegressor()
  ```

  - n_estimators:決策樹的數量
  - max_features:如何選取 features



### 梯度提升機

> - 了解梯度提升機的基本原理與架構
> - 梯度提升機與決策樹/隨機森林的差異
> - 梯度提升機的梯度從何⽽來，⼜是怎麼計算的

- 隨機森林使⽤的集成⽅法稱為 Bagging (Bootstrap aggregating)，⽤抽樣的資料與 features ⽣成每⼀棵樹，最後再取平均
- 訓練流程
  1. 將訓練資料集中的每個樣本賦予一個權值，開始的時候，權重都初始化為相等值
  2. 在整個資料集上訓練一個弱分類器，並計算錯誤率
  3. 在同一個資料集上再次訓練一個弱分類器，在訓練的過程中，權值重新調整，其中在上一次分類中分對的樣本權值將會降低，分錯的樣本權值將會提高
  4. 重複上述過程，串列的生成多個分類器，為了從所有弱分類器中得到多個分類結果
  5. 反覆運算完成後，最後的分類器是由反覆運算過程中選擇的弱分類器線性加權得到的
- Boosting 則是另⼀種集成⽅法，希望能夠由後⾯⽣成的樹，來修正前⾯樹學不好的地⽅
- 要怎麼修正前⾯學錯的地⽅呢？計算 Gradient!
- 每次⽣成樹都是要修正前⾯樹預測的錯誤，並乘上 learning rate 讓後⾯的樹能有更多學習的空間，緩解原本決策樹容易過擬和的問題，實務上的結果通常也會比
  決策樹來得好

- Bagging 與 Boosting 的差別

  - 樣本選擇上
    - Bagging：訓練集是在原始集中有放回選取的，從原始集中選出的各輪訓練集之間是獨立的。
    - Boosting：每一輪的訓練集不變，只是訓練集中每個樣例在分類器中的權重發生變化。而權值是根據上一輪的分類結果進行調整。
  - 樣例權重
    - Bagging：使用均勻取樣，每個樣例的權重相等。 
    - Boosting：根據錯誤率不斷調整樣例的權值，錯誤率越大則權重越大。
  - 預測函數
    - Bagging：所有預測函數的權重相等。
    - Boosting：每個弱分類器都有相應的權重，對於分類誤差小的分類器會有更大的權重。
  - 使用時機
    - Bagging：模型本身已經很複雜，一下就Overfit了，需要降低複雜度時
    - Boosting:模型無法fit資料時，透過Boosting來增加模型的複雜度
  - 主要目標：
    - Bagging：降低Variance
    - Boosting：降低bias
  - 平行計算： Bagging：各個預測函數可以並行生成。 Boosting：各個預測函數只能順序生成，因為後一個模型參數需要前一輪模型的結果。

- 使⽤ Sklearn 中的梯度提升機

  ```python
  from sklearn.ensemble import GradientBoostingClassifier
  from sklearn.ensemble import GradientBoostingRegressor
  clf = GradientBoostingClassifier()
  ```

  - 可決定要⽣成數的數量，越多越不容易過擬和，但是運算時間會變長
  - Loss 的選擇，若改為 exponential 則會變成Adaboosting 演算法，概念相同但實作稍微不同
  - learning_rate是每棵樹對最終結果的影響，應與，n_estimators 成反比
  - n_estimators: 決策樹的數量

### XGBoost

- 簡介

  - XGB的建立在GBDT的基礎上,經過目標函數、模型演算法、運算架構等等的優化,使XGB成為速度快、效果好的Boosting模型

    - 目標函數的優化:

      模型的通則是追求目標函數的「極小化」,其中損失函數會隨模型複雜度增加而減少,而XGB將模型的目標函數加入正則化項,其將隨模型複雜度增加而增加,故XGB會在模型準確度和模型複雜度間取捨(trade-off),避免為了追求準確度導致模型過於複雜,造成overfitting

- 訓練流程

- 調參順序

  1. 設置一些初始值。

     ```python
     - learning_rate: 0.1
     - n_estimators: 500
     - max_depth: 5
     - min_child_weight: 1
     - subsample: 0.8
     - colsample_bytree:0.8
     - gamma: 0
     - reg_alpha: 0
     - reg_lambda: 1
     ```

  2. estimdators

  3. min_child_weight 及 max_depth

  4. gamma

  5. subsample 及 colsample_bytree

  6. reg_alpha 及 reg_lambda

  7. learning_rate， 這時候要調小測試

     

      

### 常見問題

- 隨機森林的模型準確率會比決策樹來的差嗎?
  - 若隨機森林中樹的數量太少，造成嚴重的 Overfit，是有可能會比較差。但如果都是⽤預設的參數，實務上不太會有隨機森林比決策樹差的情形，要特別注意程式碼是否有誤

### 參考資料

- [決策樹(Decision Tree)以及隨機森林(Random Forest)介紹](https://medium.com/jameslearningnote/%E8%B3%87%E6%96%99%E5%88%86%E6%9E%90-%E6%A9%9F%E5%99%A8%E5%AD%B8%E7%BF%92-%E7%AC%AC3-5%E8%AC%9B-%E6%B1%BA%E7%AD%96%E6%A8%B9-decision-tree-%E4%BB%A5%E5%8F%8A%E9%9A%A8%E6%A9%9F%E6%A3%AE%E6%9E%97-random-forest-%E4%BB%8B%E7%B4%B9-7079b0ddfbda)
- [Let’s Write a Decision Tree Classifier from Scratch - Machine Learning Recipes](https://www.youtube.com/watch?v=LDRbO9a6XPU)
- [HOW DECISION TREE ALGORITHM WORKS](http://dataaspirant.com/2017/01/30/how-decision-tree-algorithm-works/)

- [Creating and Visualizing Decision Trees with Python](https://medium.com/@rnbrown/creating-and-visualizing-decision-trees-with-python-f8e8fa394176)
- [[ML] Random Forest](http://hhtucode.blogspot.com/2013/06/ml-random-forest.html)
- [How Random Forest Algorithm Works in Machine Learning](https://medium.com/@Synced/how-random-forest-algorithm-works-in-machine-learning-3c0fe15b6674)
- [Random Forests - The Math of Intelligence (Week 6)](https://www.youtube.com/watch?v=QHOazyP-YlM)
- [A Kaggle Master Explains Gradient Boosting](http://blog.kaggle.com/2017/01/23/a-kaggle-master-explains-gradient-boosting/)
- [ML Lecture 22: Ensemble](https://www.youtube.com/watch?v=tH9FH1DH5n0)
- [How to explain gradient boosting](https://explained.ai/gradient-boosting/index.html)
- [GBDT︰梯度提升決策樹](https://ifun01.com/84A3FW7.html)
- [Kaggle Winning Solution Xgboost Algorithm - Learn from Its Author, Tong He](https://www.youtube.com/watch?v=ufHo8vbk6g4)
- [Introduction to Boosted Trees](https://homes.cs.washington.edu/~tqchen/pdf/BoostedTree.pdf)
- [Complete Machine Learning Guide to Parameter Tuning in Gradient Boosting (GBM) in Python](https://www.analyticsvidhya.com/blog/2016/02/complete-guide-parameter-tuning-gradient-boosting-gbm-python/)
- [bootstrap自采样再理解](https://blog.csdn.net/iterate7/article/details/79740136)
- [Boosting 算法介绍](https://zhuanlan.zhihu.com/p/75330932)

- [xgboost参数调节](https://zhuanlan.zhihu.com/p/28672955)

- [一文读懂机器学习大杀器XGBoost原理](https://zhuanlan.zhihu.com/p/40129825)

- [Complete Guide to Parameter Tuning in XGBoost with codes in Python](https://www.analyticsvidhya.com/blog/2016/03/complete-guide-parameter-tuning-xgboost-with-codes-python/)
- [XGboost数据比赛实战之调参篇(完整流程)](https://segmentfault.com/a/1190000014040317)







## 推薦書單與公開課

**网络公开课：**

- [麻省理工公开课 线性代数](http://link.zhihu.com/?target=http%3A//open.163.com/special/opencourse/daishu.html)——学习矩阵理论及线性代数的基本知识，推荐笔记[MIT线性代数课程精细笔记by忆瑧](https://zhuanlan.zhihu.com/p/28277072)。
- [台大机器学习公开课](http://link.zhihu.com/?target=https%3A//www.csie.ntu.edu.tw/%7Ehtlin/mooc/)——授课人林轩田，课程分为机器学习基石和机器学习技法两部分。
- [华盛顿大学机器学习公开课](http://link.zhihu.com/?target=https%3A//www.coursera.org/specializations/machine-learning)——华盛顿大学在Coursera开的机器学习专项课，共有四个部分，这个课直接从应用案例开始讲起，对于回归，分类，协同过滤和情感分析等都会具体去讲怎么实现应用，并且会告诉你如何在Python中利用网上一些现有的库来实现特定的功能，也就是说基本上在课程的第一部分你就可以全面的知道机器学习能够在现实生活中的应用，以及简单方式去实现一些功能。
- [斯坦福大学公开课 机器学习](http://link.zhihu.com/?target=http%3A//open.163.com/special/opencourse/machinelearning.html)——Andrew Ng（吴恩达）在斯坦福开设的CS229，难度远高于Coursera上面的课程。
- [Google線上課程](https://developers.google.cn/machine-learning/crash-course/)

**书单：**

- [《机器学习》](http://link.zhihu.com/?target=https%3A//book.douban.com/subject/26708119/)by 周志华，这是一本中国无数Machine Learning热爱者的启蒙教材，它非常合适没有任何背景的初学者看，每一个概念的来龙去脉讲的都很细致，是一本大而全的教材。
- [《统计学习方法》](http://link.zhihu.com/?target=https%3A//book.douban.com/subject/10590856/)by 李航，这本书主要偏优化和推倒，推倒相应算法的时候可以参考这本书。虽然只是薄薄的一本，但全是精华内容。
- [《机器学习实战](http://link.zhihu.com/?target=https%3A//book.douban.com/subject/24703171/)》by Peter Harrington，可以对应《统计学习方法》进行实现代码。
- [《Pattern Recognition And Machine Learning》](http://link.zhihu.com/?target=http%3A//www.rmki.kfki.hu/%7Ebanmi/elte/Bishop%2520-%2520Pattern%2520Recognition%2520and%2520Machine%2520Learning.pdf) by Christopher Bishop，属于机器学习进阶书籍，内容全，建议首先完成以上三本书籍，再看这本。
- [《利用Python进行数据分析》](http://link.zhihu.com/?target=https%3A//book.douban.com/subject/25779298/)——Python常用的库学习（numpy，pandas）
- [《剑指offer》](http://link.zhihu.com/?target=https%3A//book.douban.com/subject/25910559/)——常见面试题，面试必备。

最后推荐一个网站，收集了进阶的机器学习各种资源[Github机器学习Machine-Learning](http://link.zhihu.com/?target=https%3A//github.com/JustFollowUs/Machine-Learning%23learning_route)



> 学习建议：多考虑各模型之间的优缺点，区别和适用范围。不要只知道用了哪些模型，而且知道为什么用这个模型。

# 機器學習調整參數

## 超參數調整與優化

### 機器學習模型中的超參數

- 之前接觸到的所有模型都有超參數需要設置
  - LASSO，Ridge: α 的⼤⼩
  - 決策樹：樹的深度、節點最⼩樣本數
  - 隨機森林：樹的數量

- 這些超參數都會影響模型訓練的結果，建議先使⽤預設值，再慢慢進⾏調整

- 超參數會影響結果，但提升的效果有限，資料清理與特徵⼯程才能最有效的提升準確率，調整參數只是⼀個加分的⼯具。

### 超參數調整方法

- 窮舉法 (Grid Search)：直接指定超參數的組合範圍，每⼀組參數都訓練完成，再根據驗證集 (validation) 的結果選擇最佳參數

- 隨機搜尋 (Random Search)：指定超參數的範圍，⽤均勻分布進⾏參數抽樣，⽤抽到的參數進⾏訓練，再根據驗證集的結果選擇最佳參數

- 隨機搜尋通常都能獲得更佳的結果，詳⾒[Smarter Parameter Sweeps (or Why Grid Search Is Plain Stupid)](https://medium.com/rants-on-machine-learning/smarter-parameter-sweeps-or-why-grid-search-is-plain-stupid-c17d97a0e881)

### 正確的超參數調整步驟

- 若持續使⽤同⼀份驗證集 (validation) 來調參，可能讓模型的參數過於擬合該驗證集，正確的步驟是使⽤ Cross-validation 確保模型泛化性
  - 先將資料切分為訓練/測試集，測試集保留不使⽤

  - 將剛切分好的訓練集，再使⽤Cross-validation 切分 K 份訓練/驗證集

  - ⽤ grid/random search 的超參數進⾏訓練與評估

  - 選出最佳的參數，⽤該參數與全部訓練集建模

  - 最後使⽤測試集評估結果

    ![Cross Validation](https://lh3.googleusercontent.com/AyYCe_Yfd-SzpV38jADYV0DMNCHgCPOCkrglQXOpO8D8JeyuUfrEmhdIIiB6uCLIeg48H9Ypu59tguI2MsunnUUJv5L3yU0v1pc9tKMDwt-nW4hVM7I5UdHC0VyfXPUOljwpX0RD6wRGeoOzHhsAzu49mMTq2a_Tj1lnnLW_834qo38hJzZMhbHnc_9N9XYP2BzcwPAgyxjWxCmcawSqWxSRHvOzqX__9oFSgEQRUYBX4OD0NazCnYYmIxVHYQ-7FNMe5-MuYIyw7NtKdFnoJJNdis_YyjXTz7R9XXYfwgpJMbPIzbH_IykYlfriDdATNGm5uTkXnKs3H-bgymX4I4_Y8fs9B5IOYkkUPHS-JlHdKElZijbQjjWN5riQsWRg2hu0rpQnQv6oowJlyWSmz2uwceUc-EuOIgVIaGqIocSArSD1HTgomxqlPVzNMFQtvaauaQ26mexDy-layK-yUK_ACs-XGI3H7QNgKkJSe1vSqA6qlR2qphXACOOxMaa6EJ4CjxJ8ifta7PK4DhrsujI4r4eVlQ1XRim3JULteCOriC17byiUxOB-8s97x1ZuCbNreF-fIRjg92ZzNyEgBZymbFIGz0Zi9_uNgYZp2QLexZO-Bi2tWFpGvy6HFU53r933bHtGCwHBj7mwSRvmiaVb-5graFdw-eyRKVOsF6V-RDv22xv-AZbYlwptin2ZXurtnbgHvRnyguAePWJ6wW42=w958-h583-no)

### 參考資料

- [Scanning hyperspace: how to tune machine learning models](https://cambridgecoding.wordpress.com/2016/04/03/scanning-hyperspace-how-to-tune-machine-learning-models/)
- [Hyperparameter Tuning the Random Forest in Python](https://towardsdatascience.com/hyperparameter-tuning-the-random-forest-in-python-using-scikit-learn-28d2aa77dd74)



# 非監督式機器學習

利用分群與降維方法探索資料模式

## 非監督式機器學習簡介

> - 瞭解非監督式學習 (unsupervised learning)相關技術概要。
> - 瞭解非監督式學習的應⽤場景。

### 什麼是非監督式學習？

- 非監督學習允許我們在對結果無法預知時接近問題。非監督學習演算法只基於輸入資料找出模式。當我們無法確定尋找內容，或無標記 (y) 資料時，通常會⽤這類演算法，幫助我們了解資料模式。

### 應⽤案例

- 客⼾分群

  在資料沒有任何標記，或是問題還沒定義清楚前，可⽤分群的⽅式幫助理清資料特性。
  
- 特徵抽象化

  特徵數太多難於理解及呈現的情況下，藉由抽象化的技術幫助降低資料維度，同時不失去原有的資訊，組合成新的特徵。
  
- 購物籃分析

  資料探勘的經典案例，適⽤於線下或線上零售的商品組合推薦。

- 非結構化資料分析

  非結構化資料如⽂字、影像等，可以藉由⼀些非監督式學習的技術，幫助呈現及描述資料。

### 非監督學習算法概要

- 聚類分析 : 尋找資料的隱藏模式
- 降低維度 : 特徵數太⼤且特徵間相關性⾼，以此⽅式縮減特徵維度
- 其他 : 關聯法則 (購物籃分析)、異常值偵測、探索性資料分析等

### 重要知識點複習

- 在不清楚資料特性、問題定義、沒有標記的情況下，非監督式學習技術可以幫助我們理清資料脈絡
- 特徵數太龐⼤的情況下，非監督式學習可以幫助概念抽象化，⽤更簡潔的特徵描述資料
- 非監督式學習以聚類算法及降低維度算法爲主，本課程也以這兩⾨技術進⾏探究

### 參考資料

- [UnSupervised Learning by Andrew Ng](https://www.youtube.com/watch?v=hhvL-U9_bLQ)

- [Unsupervised learning：PCA ](http://speech.ee.ntu.edu.tw/~tlkagk/courses/ML_2017/Lecture/PCA.mp4)

- [Scikit-learn unsupervised learning](http://scikit-learn.org/stable/unsupervised_learning.html)



## Cluster

> - 聚類算法與監督式學習的差異
>
> - K-means 聚類算法簡介
> - K-means 聚類算法的參數設計
> - ⼤致了解輪廓分析的設計想法與⽤途
> - 如何使⽤輪廓分析觀察 K-mean 效果
> - 瞭解階層分群算法流程，及群數的定義
> - 瞭解階層分群與 k-means 差異，及其優劣比較
> - 階層分群的距離計算⽅式

- 聚類算法⽤於把族群或資料點分隔成⼀系列的組合，使得相同 cluster 中的資料點比其他的組更相似
- 監督式學習⽬標在於找出決策邊界(decision boundary)
- Clustering ⽬標在於找出資料結構

### Why clustering?

- 在資料還沒有標記、問題還沒定義清楚時，聚類算法可以幫助我們理解資料特性，評估機器學習問題⽅向等，也是⼀種呈現資料的⽅式。

### K-means

- 把所有資料點分成 k 個 cluster，使得相同 cluster 中的所有資料點彼此儘量相似，⽽不同 cluster 的資料點儘量不同。
- 距離測量（e.g. 歐⽒距離）⽤於計算資料點的相似度和相異度。每個 cluster有⼀個中⼼點。中⼼點可理解為最能代表 cluster 的點。

- 算法流程
  1. 決定要將資料分成 k 組
  2. 隨機選取 k 個點，稱爲 cluster centroid.
  3. 對每⼀個 training example 根據它距離哪⼀個 cluster centroid 較近 ， 標記爲其中之⼀ (cluster assignment)
  4. 然後把 centroid 移到同⼀群 training examples 的中⼼點 (update centroid)
  5. 反覆進⾏ cluster assignment 及 update centroid, 直到 cluster assignment 不再導致 training example 被 assign 爲不同的標記 (算法收斂)

- 整體目標：K-means ⽬標是使總體群內平⽅誤差最⼩

$$
\sum^n_{i=0} \min_{\mu \epsilon C}(||X_i -  \mu_j||^2)
$$

- 注意事項
  1. Random initialization: initial 設定的不同，會導致得到不同 clustering 的結果，可能導致 local optima，⽽非 global optima。
  2. 因爲沒有預先的標記，對於 cluster 數量多少才是最佳解，沒有標準答案，得靠⼿動測試觀察。
  3. kmeans是透過距離來評估相識度，因此對於離群值會非常敏感。

### 輪廓分析(Silhouette analysis)

- 分群模型的評估
  
  - 與監督模型不同，非監督因為沒有⽬標值，因此無法使⽤⽬標值的預估與實際差距，來評估模型的優劣
- 評估⽅式類型
  - 有⽬標值的分群
    - 如果資料有⽬標值，只是先忽略⽬標值做非監督學習，則只要微調後，就可以使⽤原本監督的測量函數評估準確性
  - 無⽬標值的分群
    - 但通常沒有⽬標值/⽬標值非常少才會⽤非監督模型，這種情況下，只能使⽤資料本⾝的分布資訊，來做模型的評估

- 輪廓分析

  - 歷史

    - 最早由 Peter J. Rousseeuw 於 1986 提出。它同時考慮了群內以及相鄰群的距離，除了可以評估資料點分群是否得當，也可以⽤來評估不同分群⽅式對於資料的分群效果

  - 設計精神

    - 同⼀群的資料點應該很近，不同群的資料點應該很遠，所以設計⼀種當 同群資料點越近 / 不同群資料點越遠 時越⼤的分數
    - 當資料點在兩群交界附近，希望分數接近 0

  - 單點輪廓值

    - 對任意單⼀資料點 i，「與 i 同⼀群」 的資料點，距離 i 的平均稱為 ai
    - 「與 i 不同群」 的資料點中，不同群距離 i 平均中，最⼤的稱為bi ( 其實就是要取第⼆靠近 i 的那⼀群平均，滿⾜交界上分數為0 的設計)
    - i 點的輪廓分數 si : (bi-ai) / max{bi, ai}
    - 其實只要不是刻意分錯，bi 通常會⼤於等於 ai，所以上述公式在此條件下可以化簡為 1 - ai / bi 

  - 整體的輪廓分析

    - 分組觀察 如下圖，左圖依照不同的類別，將同類別的輪廓分數排序後顯⽰，可以發現黃綠兩組的輪廓值⼤多在平均以下，且比例上接近 0的點也比較多，這些情況都表⽰這兩組似乎沒分得那麼開 (可對照下圖)

      ![](https://lh3.googleusercontent.com/gjSeS-QxeX7aQgk6qeimUFxEGdbgRik64dDZttLGBmZf06fjfAfwxG1rS0nZIYO-pUVcVFj_0jGSEOWERzUqc-iL_qcCjLwyggqHeVroC2V4HmknH1N9l_8BEadADJ9s27t1txj81mLitKe59iGX89qTOQepLAazDMGSR64LTNKBqVLFDFsXpI1zegCA8SOT6y7mrKSM8xd6UfnnI0TIDT8Wt2Y-41vxCe1vG3BTYVFcg6XGqOXqqhjTXuhHytSuSASZisaJG9NlqX1wsfCWYEc8fTDCdeve0zxESyEpPBqsHLPsFXKtiT0M0BDxpwNuIaJJZZa5lBIv-vTx3H7YoYGoaSE_pxVNgFvT57H3yrditWvqbnQhs7ta2oJvAn7NFi4K2d1MC5awNweBXDldfhSBQA3uEhYY694ayyXPYzo00f2Nad0Jz6NGCfi9QRpJjs31cdAaSu4_4FplN8O32q2FalgQWF4gRRVKBSsAep860lL3gCiijqU3ZrpZSzBnqF6OHVOVpdeWKXggHFn-JcVSxl0f7MAO5TAury_bnwa7K2hL93-nnvsc6869Ev5JPKJFrtQsYITFSXI0D0Byj7Hpc4s6CpVdDngEcXGij0Vyqd9u3RHgw5Ev8PDze93qrDaTO6ch21j-QQb5nmD04ytzftOgGd-VnfsxSL30zoOp9DC8eHSS80EAvWGYekRcx0HP_yEPj0LvmLo1tg76-B81AWLhe78ykgPz62lsA-eW7nY=w996-h414-no)

    - 平均值觀察 計算分群的輪廓分數總平均，分的群數越多應該分數越⼩，如果總平均值沒有隨著分群數增加⽽變⼩，就說明了那些分組數較不洽當

### 階層分群算法(Hierarchical Clustering)

- ⼀種構建 cluster 的層次結構的算法。該算法從分配給⾃⼰ cluster 的所有資料點開始。然後，兩個距離最近的 cluster 合併為同⼀個 cluster。最後，當只剩下⼀個 cluster 時，該算法結束。

- K-means vs. 階層分群
  - K-mean 要預先定義群數(n of clusters)
  - 階層分群可根據定義距離來分群(bottom-up)，也可以決定羣數做分群 (top-down)

- 算法流程

  1. 每筆資料為⼀個 cluster

  2. 計算每兩群之間的距離

  3. 將最近的兩群合併成⼀群

  4. 重覆步驟 2、3，直到所有資料合併成同⼀ cluster

- 距離計算方式

  - Single-link：不同群聚中最接近兩點間的距離。

  - Complete-link：不同群聚中最遠兩點間的距離，這樣可以保證這兩個集合
    合併後, 任何⼀對的距離不會⼤於 d。

  - Average-link：不同群聚間各點與各點間距離總和的平均。

  - Centroid：

- 階層分群優劣分析

  - 優點：
    1. 概念簡單，易於呈現
    2. 不需指定群數

  - 缺點：
    1. 只適⽤於少量資料，⼤量資料會很難處理

### 重要知識點複習

- Kmeans
  - 當問題不清楚或是資料未有標註的情況下，可以嘗試⽤分群算法幫助瞭解資料結構，⽽其中⼀個⽅法是運⽤ K-means 聚類算法幫助分群資料
  - 分群算法需要事先定義群數，因此效果評估只能藉由⼈爲觀察。
- 輪廓分析
  - 輪廓分數是⼀種 同群資料點越近 / 不同群資料點越遠 時會越⼤的分數，除了可以評估資料點分群是否得當，也可以⽤來評估分群效果
  - 要以輪廓分析觀察 K -mean，除了可以將每個資料點分組觀察以評估資料點分群是否得當，也可⽤平均值觀察評估不同 K 值的分群效果
- 階層式分群
  - 階層式分群在無需定義群數的情況下做資料的分群，⽽後可以⽤不同的距離定義⽅式決定資料群組。
  - 分群距離計算⽅式有 single-link, complete-link, average-link。
  - 概念簡單且容易呈現，但不適合⽤在⼤資料

### 參考資料

- Kmeans
  - [StatsLearning Lect12c 111113](https://www.youtube.com/watch?v=aIybuNt9ps4)
  - [KMeans Algorithm](https://www.youtube.com/watch?v=hDmNF9JG3lo)
  - [Unsupervised Machine Learning: Flat Clustering](https://pythonprogramming.net/flat-clustering-machine-learning-python-scikit-learn/)
- Hierarchical Clustering
  - [StatsLearning Lect12d](https://www.youtube.com/watch?v=Tuuc9Y06tAc)
  - [StatsLearning Lect12e](https://www.youtube.com/watch?v=yUJcTpWNY_o)

## Dimension reduction

> - 降低維度的好處，及其應⽤領域
> - 主成分分析 (PCA) 概念簡介

### PCA

- 說明
  - 實務上我們經常遇到資料有非常多的 features, 有些 features 可能⾼度相關，有什麼⽅法能夠把⾼度相關的 features 去除？
  - PCA 透過計算 eigen value, eigen vector, 可以將原本的 features 降維⾄特定的維度
    - 原本資料有 100 個 features，透過 PCA，可以將這 100 個 features 降成 2 個features
    - 新 features 為舊 features 的線性組合
    - 新 features 之間彼此不相關

- 爲什麼需要降低維度 ? 

  降低維度可以幫助我們壓縮及丟棄無⽤資訊、抽象化及組合新特徵、呈現⾼維數據。常⽤的算法爲主成分分析。

  - 壓縮資料

    - 有助於使⽤較少的 RAM 或 disk space，也有助於加速 learning algorithms

    - 影像壓縮

      - 原始影像維度爲 512, 在降低維度到 16 的情況下 , 圖片雖然有些許模糊 ,但依然保有明顯的輪廓和特徵

        ![](https://lh3.googleusercontent.com/p7w41bPYhLPENoIaH7t_xqsPB1IMINSe918mSI3GELa3uNbzxHBSS66Th8ahXaYIuU9fpEAzsZRyKNuD4hZd9On0axuGqgU3cXimCeJtA_STghhJKa-oZZYYTPah9NqQ5oLj5AuhGPpzmMxA1VmNDSZ5PYAEy5u-GBhFupbLJD5XtcrSTnHm7hTuDj3Fatv8BmCJXUJ3QWeB2L2P4wJduMs7rNt9dI9GE-_v2e5fcay0sBWNa9eCGadZbyemHZZd5FPpaCFpbN-s-NsdUuBCVQ7tN6rgpTgIIiCf0DXyf22oi1gPj3or-dAHXlX4aFHZQC97NvbW3rVYCAIEFZW3tXN5zLdqs5wV_EESqp6AXrGsObv0xbbYp4MlbbabsqcPlbQoRmq9niu9leNi3p2l5bKLE9encAsGTDXE4cm3I57bDlNIjZeTsCtBfL_e0g6WDrdJ_A4NnNy_8LrJpZ0ckX5bAbfTpPxGTvGMK91CcNrMkerRHeBz2tbBD8mpmHrqBYkwUUPFjW2gPlK317vpOb9GHep-TEh6BsZ29ldVvanmbd6zcQtrRiit08cScFQcXcRnQirzfzs5Rn5VRFos7FcIqezZfMPWxpKGXrQCuyWnhX5gQuR00xyUjPNsF-wWVS1pJloFyPJIc7D38vsY-bjbXYWS2xeq_bSMhaMGnDavuimr3dN6qWG5XUXI-zmneS8uTq0Vt7BEvdZGnHFWTygi3oAjaQ6cjYE94jMlOqS3LFA=w646-h514-no)

  - 特徵組合及抽象化

    - 壓縮資料可進⽽組合出新的、抽象化的特徵，減少冗餘的資訊。

    - 左下圖的 x1 和 x2 ⾼度相關 , 因此可以合併成 1 個特徵 (右下圖)。

      - 把 x(i) 投影到藍⾊線 , 從 2 維降低爲 1 維。

      ![](https://lh3.googleusercontent.com/mgqelyYL1QQbGhn9eJhmlb2b0zl72fOr3QzCuK6Kqz0tcva4jR_sBYCgYPtq8VJ0VFTQbgWExqcaVCxHpn9h_dNwCaxx1hIyxFVRk2WP2crTOkqh0l3YT36e_Ckao-_zQSfBBmBPA3spWswzmE_AN5a52iAtH0GTZqx7LrleVS5KFyt2Ih0grm7PNWiBi_9-rHpG5gdyAH0fYYf2sJ04kQyQEEvDLLwaLIHvBbUUTkhV-gNlpdASvhAefrj1LSaGULQSPtn2F1SpQ5D4r9n741OrX9pjuaQvd1I99ZZxGjpCMAlY4IX1K4wQTC9VggxhcqbRmOTzsob7dIexz1u5o8SykSr1AJ7o6VcJFzxogH5h3bKDyZlY6Z2fUs9VwTDgOpGKnw_fjYs5PuBApXCgPiDYbbSD5og9GWu_onWDEB2xWUxbKJJIVukO-w0px7NJZ_uGGQUAw26A2jJWgYbJBKAcsT7vyPitfi287zGMXTyP5ECxoXAJk2ejXmjhxQ-XyoIstOMf4BVGtFJVos3DrhaKN97wv-TI8J63LlbmCtVFu70uOAtxc7QX_miA6JSvCYgwM61eAht292akoFg_xzb7go6IqB4Ev5uRLt5x2TGwQErRxcr7nY-ytEGcAQe7WdrB3aydLaJkG7n7jKjUbeh5OsuKF8eMOfBoi4Yr4oBpgOeI2yLynBGDHVJmZ1RD1PUzLepAi37FZC31CvIFZeZYVuDdvNTf1mdiPH-d4Rt4xKM=w793-h372-no)

  - 資料視覺化
    - 特徵太多時，很難 visualize data, 不容易觀察資料。
    - 把資料維度 (特徵) 降到 2 到 3 個 , 則能夠⽤⼀般的 2D 或 3D 圖表呈現資料

- 應⽤
  - 組合出來的這些新的 features 可以進⽽⽤來做 supervised learning 預測模型
  - 以判斷⼈臉爲例 , 最重要的特徵是眼睛、⿐⼦、嘴巴，膚⾊和頭髮等都可捨棄，將這些不必要的資訊捨棄除了可以加速 learning , 也可以避免⼀點overfitting。
- 如何決定要選多少個主成分?
  - Elbow
  - 累積的解釋變異量達85%
- 注意事項
  - 不建議在早期時做 , 否則可能會丟失重要的 features ⽽ underfitting。
  - 可以在 optimization 階段時 , 考慮 PCA, 並觀察運⽤了 PCA 後對準確度的影響
  - PCA是透過距離來進行運算，因此在跑PCA之前需要對資料做標準化。避免PCA的結果因為測量範圍的不一致，導致只反映其中範圍較大的變量。

### t-SNE

t-Distributed Stochastic Neighbor Embedding

> - 瞭解 PCA 的限制
> - t-SNE 概念簡介，及其優劣

- PCA 的問題

  - 求共變異數矩陣進⾏奇異值分解，因此會被資料的差異性影響，無法很好的表現相似性及分佈。
  - PCA 是⼀種線性降維⽅式，因此若特徵間是非線性關係，會有
    underfitting 的問題。

- t-SNE

  - t-SNE 也是⼀種降維⽅式，但它⽤了更複雜的公式來表達⾼維和低維之間的關係。
  - 主要是將⾼維的資料⽤ gaussian distribution 的機率密度函數近似，⽽低維資料的部分⽤ t 分佈來近似，在⽤ KL divergence 計算相似度，再以梯度下降 (gradient descent) 求最佳解。

- t-SNE 優劣

  - 優點
    - 當特徵數量過多時，使⽤ PCA 可能會造成降維後的 underfitting，這時可以考慮使⽤t-SNE 來降維
  - 缺點
    - t-SNE 的需要比較多的時間執⾏

- 計算量太大了，通常不會直接對原始資料做TSNE,例如有100維的資料，通常會先用PCA降成50維，再用TSNE降成2維

- 如果有新的點加入，如果直接套用既有模型。因此TSNE不是用來做traing testing，而是用來做視覺化

- 流形還原

  - 流形還原就是將⾼維度上相近的點，對應到低維度上相近的點，沒有資料點的地⽅不列入考量範圍
  - 簡單的說，如果資料結構像瑞⼠捲⼀樣，那麼流形還原就是把它攤開鋪平 (流形還原資料集的其中⼀種，就是叫做瑞⼠捲-Swiss Roll)
  - 流形還原就是在⾼維度到低維度的對應中，盡量保持資料點之間的遠近關係，沒有資料點的地⽅，就不列入考量範圍
  - 除了 t-sne 外，較常⾒的流形還原還有 Isomap 與 LLE (Locally Linear Embedding) 等⼯具

  

### 重要知識點複習

- PCA
  - 降低維度可以幫助我們壓縮及丟棄無⽤資訊、抽象化及組合新特徵、呈現⾼維數據。常⽤的算法爲主成分分析。
  - 在維度太⼤發⽣ overfitting 的情況下，可以嘗試⽤ PCA 組成的特徵來做監督式學習，但不建議⼀開始就做。
- t-SNE
  - 特徵間爲非線性關係時 (e.g. ⽂字、影像資料)，PCA很容易 underfitting
  - t-SNE 對於特徵非線性資料有更好的降維呈現能⼒。

### 參考資料

- PCA
  - [StatsLearning Lect12a](https://www.youtube.com/watch?v=ipyxSYXgzjQ)
  - [StatsLearning Lect12b](https://www.youtube.com/watch?v=dbuSGWCgdzw)
  - [StatsLearning Lect8k](https://www.youtube.com/watch?v=eYxwWGJcOfw)
  - [Principal Component Analysis Algorithm](https://www.youtube.com/watch?v=rng04VJxUt4)

- [Visualizing Data Using t-SNE](https://www.youtube.com/watch?v=RJVL80Gg3lA)
- [ML Lecture 15: Unsupervised Learning - Neighbor Embedding](https://www.youtube.com/watch?v=GBUEjkpoxXc)



# 深度學習理論與實作

神經網絡的運用

## 神經網路介紹

> - 類神經網路與深度學習的比較以及差異性
> - 深度學習能解決哪些問題？
> - 深度類神經網路常⾒名詞與架構

### 類神經網絡 (Neural Network)

- 在1956年的達特茅斯會議中誕⽣，以數學模擬神經傳導輸出預測，在初期⼈⼯智慧領域中就是重要分⽀
- 因層數⼀多計算量就⼤幅增加等問題，過去無法解決，雖不斷有學者試圖改善，在歷史中仍不免⼤起⼤落
- 直到近幾年在算法、硬體能⼒與巨量資料的改善下，多層的類神經網路才重新成為當前⼈⼯智慧的應⽤主流

### 類神經網路與深度學習的比較

- 就基礎要素⽽⾔，深度學習是比較多層的類神經網路
- 但就實務應⽤的層次上，因著設計思路與連結架構的不同，兩者有了很⼤的差異性

| 項目       | 類神經網絡                                          | 深度學習                                |
| ---------- | --------------------------------------------------- | --------------------------------------- |
| 隱藏層數量 | 1~2層                                               | ⼗數層到百層以上不等                    |
| 活躍年代   | 1956~1974                                           | 2011⾄今                                |
| 代表結構   | 感知器(Perceptron)<br>啟動函數(Activation Function) | 卷積神經網路(CNN)<br/>遞歸神經網路(RNN) |
| 解決問題   | 基礎回歸問題                                        | 影像、⾃然語⾔處理等多樣問題            |



### 深度學習應用爆發的三大關鍵

- 類神經的應⽤曾沉寂⼆三⼗年，直到 2012 年 AlexNet 在 ImageNet 圖像分類競賽獲得驚艷表現後，才重回主流舞台
- 深度學習相比於過去，到底有哪些關鍵優勢呢?
  - 算法改良
    - 網路結構：CNN 與 RNN 等結構在神經連結上做有意義的精省，使得計算⼒得以⽤在⼑⼝上
    - 細節改良：DropOut (隨機移除) 同時有節省連結與集成的效果，BatchNormalization (批次正規化) 讓神經層間有更好的傳導⼒
  - 計算機硬體能⼒提升
    - 圖形處理器 (GPU) 的誕⽣，持續了晶片摩爾定律，讓計算成為可⾏
  - 巨量資料
    - 個⼈⾏動裝置的普及及網路速度的持續提升，帶來巨量的資料量，使得深度學習有了可以學習的素材

### 類神經網路與深度學習的比較

- **CNN**
  - 設計⽬標：影像處理
  - 結構改進：CNN 參考像素遠近省略神經元，並且⽤影像特徵的平移不變性來共⽤權重，⼤幅減少了影像計算的負擔
  - 衍伸應⽤：只要符合上述兩種特性的應⽤，都可以使⽤ CNN 來計算，例如AlphaGo 的 v18 版的兩個主網路都是 CNN

![](https://lh3.googleusercontent.com/-nIspNBfu9CfLh5HhbAUJzVdjFZxRiTqjNvj8P-8xnxL06njn__gz9CSXyC4qDgLXvunl1MjNVFwKtDXe73OmrJOhriU7wCf58l-pZcWHTXm5HjSg5m_iVnkKrvgOaZGf0KYbEn8fACjxcBWPP-mXTXlVpQfJXd60yRVEe9kVE7pOEQSGJmZqIJKBHpZV2jMOP-IU81FhPiWDennCBDH_7UXNPp0yeSBhZBs9NsP59-l0k5PMYhfEf7-6Phgu0kmrnEWeGrvYWxDyhjW8t5CW-kQK2CABYfmzTMGXCqC3aeLbj14WiDdzgJJSGegQ49k0HDsb4dIMFHvDovrCJlmtRAhDvShLrOc2PRg3qzPwZyoZsM9i9-twcQI5Ze2IaoHSNYZXpN9Nzz1sz186329yqyv3AOrggYkEwoUwifBZXWxndAB8tgsuerxaml1jVAsj1WYs7bdsHI842RsDgHPkbDMF9tqmnYks0dR7PnB-aBZtVoJIGJWOElPP3HkvCpJf_8xpb8fXc6ba600lRg0qnC_JgQh5j-Xoq-WXMT0-9_guMRHRZocoll32wWd6STZy6tCZjrubly3cilQb7IsuLjOrS0cVre53mtihFqDXkFxmC1PAe1TmEqrWUWGFX_NrcXWBp02WgF3TSqC2uk234HikN9dJW8uQJCOecmvJXksbdzIKTsPyA0GDIAVgiwn1cNZ6lxHN63ivVSdWtygKP9d=w904-h265-no)



- RNN
  - 設計⽬標：時序資料處理
  - 結構改進：RNN 雖然看似在 NN 外增加了時序間的橫向傳遞，但實際上還是依照時間遠近省略了部分連結
  - 衍伸應⽤：只要資料是有順序性的應⽤，都可以使⽤ RNN 來計算，近年在⾃然語⾔處理 (NLP) 上的應⽤反⽽成為⼤宗

![](https://lh3.googleusercontent.com/2KTKuCkCCpaAoD7D2BPzMqEzChQUNF9VanLKqzSs-39AeawzYH8sX0WIEGBwD1hX8Y_48wH2fwznTexTW69LDUFNd0QMBsCTrLdqs4B43ouQnhQZHECC06lQ3YYGZA29bsN18H3IBexYnS1uBxbPMXb6hvISUIVtFdxsnOtQZsyaeFpodF8IfbcCUBzTxehV9xiZL5vVmBtCWzkPVM_gNx8XtlxsXUc7VBBfgadP6pdo7lITk73mZIeakW01cA8aZ-Gp6GnnbCdjoR2YPCuTUS4tPRkEix46lLbI7QSixKZDdK0YAf2djYM6eYJVkOClu-tB0Ke-5W6uGJtFy3lxDyxZvdTNdsC8vY0QBu9L-LcCvwtuso_fc9zXXXLqdP8sLMnu3JXI1DnPPyKlbDKP_ZEYT-vNCCAzokLBpb0_BkeOVxX0qKlRpJJjYcPw7gvS9TUKkK70YcR84VxI5Vjfw6Zi2pqVnDNXXeQ12XbsONS1zFEa4a3oSoBomh85E80CeccO6HcmQeyMxtIm7edzAC_9EI8dbqEw2kXE1jBp3iC1YKqehhfOd7e9Mq416QCHhiPceVgz7rRic5F529ur3Nnmw-JMmzW_8omCps8N7a_FYF-oWCa816e0Sx8cnPZ1lPDU3saDbJJoIjnuGxHE2Z1NnxBEw9qxK_QcJAjY8uYBFT9kLgp-pbcO4LPuQyB4NCZsaXaSsShahmqkVFvf0LIM=w871-h338-no)



### 深度學習-巨觀結構

- 輸入層：輸入資料進入的位置
- 輸出層：輸出預測值的最後⼀層
- 隱藏層：除了上述兩層外，其他層都稱為隱藏層

![](https://lh3.googleusercontent.com/_NhfhS_ZVTpiHAh1-MkwiFMOsRrkBpe8faP8k9PD9ZA1nYpF2uFuMMF4HeeN3KdcVrfA3-ocXsFLdRue9Bi9oL8E8XrnTDkJG1fM4eVb-Ucqn1NGTNhHJ3YJ-iIISZHpjPMXv-O5ntebMKpXJ6qhbgBjVQEfKzeLSF-sRIj-n0ixtfg5im_fJjoTFwsY_qwI1AgL1FpT3zNLUHc-VPDA3nD5UrP_3DqkvI13SgCwrIbkutn_4dEEjbxyJ2GmnQuV_bSR6F0MdFSPufaxOagebEUffg30Kb_UjXHwmGdSGWE1rc2QYV0L_b87lmbgB4MoAJybEthq3QSIt6Cht6VARGxtyq5mNym3cGEsfgAsS5FHtZC5hoRHGyFwIlrKLRWkQq2mPh9yTPW1mT8PwFUDjd1V_faMD3LGLM97t7d-Zi5QLA_tiESE_dPoob8QmFNsL5JxpufMZR5tO-PZxZRof6idCDOboVY6JIR2jrXyvzbNliESNEEhtyD8sIT3NEdY3AqpoNNF0WKdM34P3nE5Dssg_pbpW6MAoxFXyqLR0VJBZfc2Fxqi8bpRWl13xri_ahlnBlOK_J_pD8azOI_A1nRbK4d0lSSF7jwFyWFCPLVkcUm_NdZ_xC5w4HLi8qJ2zqHjX-LTIjtPLVTbQ4NOoRtq2GqSRn6emsKoYwTgI4R4c30iBrU4XarNRqAfMUvu_9JIoMPJqyiQqLDa199eXStV=w648-h346-no)



### 深度學習-微觀結構

- **啟動函數(Activation Function)**

  位於神經元內部，將上⼀層神經元的輸入總和，轉換成這⼀個神經元輸出值的函數

- **損失函數(Loss Function)**

  定義預測值與實際值的誤差⼤⼩

- **倒傳遞Back-Propagation)**

  將損失值，轉換成類神經權重更新的⽅法

![](https://lh3.googleusercontent.com/KKTJNQP8DqEs5gfYISfMqq5HSbXhzcYBYF1C7yMUhg0kkxspTjindFQE_7GOKJjh64rhAJCx6HAuhTNNS6HAFaOR3QafQYSay1aRk9UUxgUsWNcc_u2pMUGd-C-7X2hZQTlXlwJu-XyvbI77E8d2txrMiEYWWtl6UFftBJEzWYH8vyI35hHV03GCgPJHL4c_85MHBBMPzARqgIPYXGg-GrZHjaIuRHsynYyf4lLxrWnrIaFI8L1scRbrsd69MXurfyIvDNRge-QEuQk1z_jxfenjhekne_0gtL3EFJgudN0tvZA0ZxR8w3A4JuvrU5pY1SVNpMv6Dnn_asFXlNw9r--2yklkCVg21OCbNKGYrw8SGJVBx59qigXpvmjC3Ki7YLlsSHEb2r_JUA673KBYFeXka7wq3DfSjyMm7ePGSVqh7NlLRwFSTULzzVgxAMrWTcfyfpmFO810Yt5s66d5xL1oNjXkNu4ivl7-Xx6_wQyz8XrFIuR-_i2aGuuyr2w3tPmufl5l6bmlyt4GLV_-c0jp0mBuDVg-czHqS-BK5z9p4jRzctZBGveUmma2rA1BtMqAVP3H1wvBpqk4obwTAO40b29xuqLnh_H1gTp28IwaJcnT6X1Q4vAxULOnpdZZwGSvg5yj5T6-Wfus8XV8sNu-L4iWYSh3JueIK8XrCUN9CKBDhAQgLBqOhT7h0Ubp4juPeR69BpdpCoWUZRjeCtKk=w736-h313-no)

### 重要知識點複習

- 深度學習不僅僅在深度⾼於類神經，因著算法改良、硬體能⼒提升以及巨量資料等因素，已經成為⽬前最熱⾨的技術
- 不同的深度學習架構適⽤於不同種類的應⽤，如卷積神經網路(CNN)適⽤於影像處理，遞歸神經網路(RNN)適⽤於⾃然語⾔處理，⾄今這些架構仍在持續演進與改良
- 深度神經網路巨觀結構來看，包含輸入層 / 隱藏層 / 輸出層等層次，局部則是由啟動函數轉換輸出，藉由預測與實際值差距的損失函數，⽤倒傳遞⽅式更新權重，以達成各種應⽤的學習⽬標

### 參考資料

- [神经网络的Python实现（一）了解神经网络](https://www.jianshu.com/p/a2bc960ee325)
- [神经网络的Python实现（二）全连接网络](https://www.jianshu.com/p/51c92cd4eb67)
- [神经网络的Python实现（三）卷积神经网络](https://www.jianshu.com/p/b6b9a1b0aabf)
- [Understanding Neural Networks](https://towardsdatascience.com/understanding-neural-networks-19020b758230)

## 深度學習體驗：模型調調整與學習曲線

> - 經由平台的操作，了解深度學習效果的觀察指標
> - 體驗類神經模型形狀：加深與加寬的差異
> - 理解輸入特徵對類神經網路的影響
> - 理解批次⼤⼩ (Batch size) 與學習速率 (Learnig Rate)對學習結果的影響
> - 經由實驗，體驗不同啟動函數的差異性
> - 體驗正規化 (Regularization) 對學習結果的影響

### 深度學習體驗平台說明

- 體驗平台網址 : https://playground.tensorflow.org
- TensorFlow PlayGround 是 Google 精⼼開發的體驗網⾴，提供學習者在接觸語⾔之前，就可以對深度學習能概略了解
- 接下來逐步帶著同學逐步操作，藉由此平台先⾏體驗 Part 7 之後課程會提到的重要概念
- 平台上⽬前有 4 個分類問題與 2 個迴歸問題，要先切換右上問題種類後，再選擇左上的資料集

![](https://lh3.googleusercontent.com/J-JoDiRHF79zpo2aZzaTk-vy7pLoFocuEnE22qYpflyOmm7DaobZ9HpiE63ZmiBwid-k7AP2qDHOUv6FwjsWN7ZFBmo4UgowS_TwL7jyV-9VqtnWPMCqVeaQIfQ-orWa5SJoQS06bOWIkg-M20B3JWoKY4jN10NCUC9MZVykVlizcExcZeQRVYMiHdzo08Dr1nFW3AOAyVrM1fQqCnKDy-_NrV1q5dNYtmoBOEXXE-qiZnSqslm9ItmpiWcBm1WnUFwqD9T0qywD_0I40fRSNf6tVA1aX0pqwVS_Ia3VYTlOI20zz5adA6iH7fVXGhQxK3mP1Nn0Q0MHvuc2yByUr3KphFQP3GcFmqlg-GfHkSPM1fCEUAzxzmuGsdZrWxU3x2ES1MIlHYQG4fCYaUVJqvCZqEFvQUSEFo2q-4nSLYCpGjbU0vFi8Izj6eN7LqFp2-acwD-tiGqdKefDnPIYr-Qe0IEBa6esprPLXwoRYunHHsM4YqG1y-zaFaTI5pzXUYIs2NYZ0CAysN-lcwq1Hbdhxme0kuVV3f5hBv0r9e2vpUPCMx03Fk_zvJ_NmNA8tr8rl0yMzFAZbEV5XTWqiFhGE0_egfumvuRbNqxK-rmb2Za_tdTSemuI-SLPVBQIniNQQCsmnu78IqBEpcc2XJ2CMJhpg_ioz0I04ff4CUcIHFJkr8P9K5N7YrggKHVADN7uzdnsahvEPYCKNVhrUPHY=w959-h541-no)

### 深度學習體驗

- 練習1：按下啟動，觀察指標變化
  - 全部使⽤預設值，按下啟動按鈕，看看發⽣了什麼變化?
    - 遞迴次數（Epoch，左上）：逐漸增加
    - 神經元（中央）：⽅框圖案逐漸明顯，權重逐漸加粗，滑鼠移⾄上⽅會顯⽰權重
    - 訓練/測試誤差：開始時明顯下降，幅度漸漸趨緩
    - 學習曲線：訓練/測試誤差
    - 結果圖像化：圖像逐漸穩定

   - 後續討論觀察，如果沒有特別註明，均以訓練/測試誤差是否趨近 0 為主，這種情況我們常稱為收斂

- 練習2：增減隱藏層數
  - 練習操作
    - 資料集切換：分類資料集(左下)-2 群，調整層數後啟動學習
    - 資料集切換：分類資料集(左上)-同⼼圓，調整層數後啟動學習
    - 資料集切換：迴歸資料集(左)-對⾓線，調整層數後啟動學習

  ![](https://lh3.googleusercontent.com/g0p-a-z2jhfk2uW6IZe6hAVeDstOJ08iRXhU4RNUu96veogjL6jGRgtshNJp76Xr_WxinGDuOiTbVyUx0EB16KNYt9jgCDU1jpscOrzlBWPBrEVECwCkzZlcvHIFFnzhAfGzKxd6EPffbAPc-nIFbFEGDslsT7AXrcB2VFfgMb69GXBAore7jfjNJ6ZZcbSFVvvu9smAOS4nd8rFdxzHNtzN2Uu5o9k4-ujs99o_bdXgm8cD6iKr3E6IQawCSiYsvgfigmn78ET9pUBeUuCnGge0p_491JIbHbIWw2l8EHQh8uWI4r2xNAAnZBlZoWlwiTICRTofREUOjDkC4Le6PMBoTan_GvqiubEgWgE7XBtkrSMO4WTIjCromybr0ViF2BGL9ti6hksIFftDi_RWNwtPtJf_hUlve5KaAHubiA22PrE3SWatLCZhgeN6IsfU-ruPys6KFgRPJ8UkZuVNkZ-_6H0wlcIDEFeaCCj9MztdmOTW137MjkeFrs3TZQ2L4fXuR9yHCfPFTkmexLkLGEFyBaipbX1Yy9z6XmHLpip5dTqSgX2I1GLv3_Ys0aHjZ5yhFRRR7QTp2reHroGdAUm3siwCTUl7TmCxjYjVfKGQdPs1XrGCrwG8b266v7HvGi1j-STsMDGYYiW7UBje8XRdCAQ42JUBsdx2snpza-Piosrti4MXlR_46P4H-yYgCw9zZQyVp51H_Qf4kBLny1Fo=w1018-h363-no)

  - 實驗結果
    - 2 群與對⾓線：因資料集結構簡單，即使沒有隱藏層也會收斂
    - 同⼼圓：資料及稍微複雜 (無法線性分割)，因此最少要⼀層隱藏層才會收斂

- 練習3：增減神經元數
  - 練習操作
    - 資料集切換：分類資料集(左上)-同⼼圓，隱藏層設為 1 後啟動學習
    - 切換 不同隱藏層神經元數量 後，看看學習效果有何不同？
  - 實驗結果
    - 當神經元少於等於兩個以下時，將無法收斂(如下圖)

  ![](https://lh3.googleusercontent.com/CPNG3ir92ZqfKRnt9BHtsb2i_zU3hVXzZZfKY59yR7BX8CLsQQwjg-rEPzRujgQ98bSXSFMq2QYlc_RxgiqsmK9Y91_wDChtZfpnxhoeRHKFaZlDG58bZV1Yxe7nLPZSg0ZKQqpy1PvoJjXIp_NA_-dLJGEpSpWbBfMCqHcVT3Emf1rzN3jZhTfQoDZ52QTroMqVdL9JTUivHVljzgxN6bIEBkLFmtYigCyVtG1X4O8t4PHSDON-tyiCcTPajTuamRkh5GZ-Wyav4VhxLHNFGGEMwYmreIrkS9xaBZ9RMaUcmbaXis3vpEYGV3VFTpn_7nsrgAP2E-Kdddy5VoJvphuj4WiJbDk-I9SzGFEBXUgQ2hru6zuUg2NOK4UycQR_UBxzqvEZ_WGPJUip9bQPXPpiiusEGr6PoNz13zeWT2CysSom9uAsNos18AApsHnKk_q3QdlxDm9rkPnBlX_rSwzTk-l-3Rm2F3wf7tBufm5VufAjpK41_WNSvuAskYouwLlNIw_qCYiBEiZazldPlKDDcyQYoj81hCi4OqcvHx4poSPqFtwdyG4v4kTpgo8_St78voRgvYi1p-MoodLFYYJU5xk3cUvs6uXxBJOE3HqkL0Ihf05yvkbBRBCIb4kbMlDbzdPSIXSCLbUyKuh-BS_RswH_l_ZCcF0GXamiAHl8uBPxTk7cvKwvoUgUS6MZcLZT4So5WifhCJkgUKoUcmlJ=w774-h286-no)

- 練習4：切換不同特徵
  - 練習操作
    - 資料集切換：分類資料集(左上)-同⼼圓，隱藏層 1 層，隱藏神經元 2 個
    - 切換 任選不同的 2 個特徵 後啟動，看看學習效果有何不同?
  - 實驗結果
    - 當特徵選到兩個特徵的平⽅時，即使中間只有 2 個神經元也會收斂

  ![](https://lh3.googleusercontent.com/3jpTlFx9xIf5oacDXExgBVTTFswVLWD5pLjo4sZRmc2MRMPGimX0jWYMC_8DB8v5_g3HU9eqRYCQqI12G0vwsV_zFIDGbX6X9jz93e6up5P96et6kLQ5OYQ3_VlTr6i7ecFZ_7J9Mydkz2vDqmcIMw1RORRyyjYWoYHBUO00U5dDU6aSqUrzmwKxf4d3fTeks7lhQTTbx5q-gh039AuQVDUMCU6uW5qY6cdvMm-nkpMYsmNjZc5dTpswz9L2m7VM3eQbrQArT1ihsGnD-sRVL9nUFEaOjrKrgtyCf4PxOQMCV0GU6aj5DFEmJr8pSw3EbyUFULYgRvrBFN8RWt7sVpCZH-R-CtZWJKa2WOMBTQQVhpX9L1t8eCYwHOryJ0lVDJVL7F8MzXR8SmJ9L47K9ejLs4vAqLqE0Y37K0R-aUhVJdL7eR-0VT_DpN6nk4N8h9mGjx3x60FjFMmsbkNHH6mA7AMSjJ5rh2d8mUHzEbM9Mqu_unzvENsYwS0WTAfIJWRjTlCNuc3x4ta901O619doeAmmBeu2ae2gCM7WUGabQbRiDynaq3ux78s3DxO4Ax1vF3IYJ7UGXBQpkUcS7hb7PHG-3PUkUYCtpcafwd2-qQJYj-J54X_kfbL8oeECcbGQhBqebdTOgLIMUGBAbm1dB-zNhzgDD59TSIWavGStmmgrNZ_-hiD7OWoIkNu1ap58SIxmzkAb7Tu_hGgQ5X_1=w755-h285-no)



- 練習 5：切換批次⼤⼩
  - 練習操作

    - 資料集切換:分類資料集(右下)-螺旋雙臂，特徵全選，隱藏層1層/8神經元
    - 調整 不同的批次⼤⼩後執⾏500次遞迴，看看學習效果有何不同?

  - 實驗結果

    - 批次⼤⼩很⼩時，雖然收斂過程非常不穩定，但平均⽽⾔會收斂到較好的結果

    > 註：實務上，批次⼤⼩如果極⼩，效果確實比較好，但計算時間會相當久，因此通常會依照時間需要⽽折衷

  ![](https://lh3.googleusercontent.com/RyGT7m_Fvz-35onVdJ_KNNvIab5rypkuCFk8jR5S_w12ACma0y0OqMkN4WMLhooyLOIa9rXAepJ3_0XyWUuU0dkIDC1ITAJMoVP8mbITk0NuowLLC4Y30Dx88GcZP9Xca6rURzhNJFL1C9pgeRusQ5ZTdmmzxum9IlWwUU3mWNad9VHerbZcL_qNnHZqIW5-kFifhrSc_5atzl8TCyrm3ljxjuouuvwaaS34W4c4ECm1-ivu9NxXWHz80UCiCy8HbHlSssWLb5vQ89nsxFZCMgCiCV1Uc2YEgpWXAf_M2zhjyc2x6h7LgF9JDfTzQGwJLldkbuRO-BP308Q_76vU-dhdkVB42gLUQF_F_fEo74OWC5GcCtOGhtJ0_FZ3OmUlUMr01JcggSslgmyubquK2FASbXvtqCrd4U4hNtUJ82s-rwdjbVzlqszjy3EBaQ0yT0O8fvB_do43RMKAaVtdMvXI6RxdkN4X_KqNkxOu6WwGV8N0KoYdAKcHxU4D4xuLj96QaGE55LtelAl0eK8-ypQHKCUwz4LA2msP7HjSdTEvnsCSMAEq9YEle5uGbIJSY9b3FNb8vdo-3mVpb86E4qw49HkCA51g2x4sV96YkRtw4H37xF_8Mej56BLQhw0rObVwtg3xO85zeOLf2GeUFDRzqhoam5EKN5U2xUWY6Rb9Z_KmpXuZXfV8WwTpgDpwUos_vKvU-c64mkBDSLvK3c9q=w418-h184-no)



- 練習 6：切換學習速率
  - 練習操作
    - 資料集切換：分類資料集(右下)-螺旋雙臂，特徵全選，隱藏層 1 層 /8 神經元，批次⼤⼩固定 10
    - 調整 不同的學習速率 後執⾏ 500 次遞迴，看看學習效果有何不同?

  - 實驗結果
    - ⼩於 0.3 時 學習速率較⼤時，收斂過程會越不穩定，但會收斂到較好的結果
    - ⼤於 1 時 因為過度不穩定⽽導致無法收斂

  ![](https://lh3.googleusercontent.com/K3yK4ia66stGDckpdgBi5eqAgDoAXVKOqRqYrc2tDgRtKN0gORUslM36dC74BXG2rirCGVnXC3oKQ-teSEGpBMNcD73OnzNyJDyh4GVY5Q-8rYk8pEf2EiA5IndphBSin4t_xI9tMu3uy2EszdofJ0BTHbc6eZBhNuBUy8qg78xZczzih6Hex8OM-WLw8JeBUAUf-7ND55yEgdF5Stpy1xvb2HSIyjb6le7nvNP1KEVK9KL62vEjw4ayKqE5SnqatYbw7Oykos6l8kx-wracNEbQ9Ow5f9xzmqKMFU5d6cx3lv5VQr2ozOL_l4MlmBtkqPIQKFkBUXQMpQGMOsy_4tQUwT82JAyJwZX_gpAEmGdAisXfVJn3eza0cH8nHXk6E9xS9HBGsH0szQ7cPOlZI-Jru75e1QEDzuX6R8_2oCSrQFgTm19PcQ9Z4ntkndcY0o_qJs-3gzPAYasw_ZfcivXEF8vFLGT-UBaU_U0ff89PmcE8Ypt14dmXIRM8NEIHm7E06pISOZgfWzrhWPgDCtvGPa6IyaqZcE6Ln2nRpxzItaMHVPDBH56cSZdG_7iKw5Wfv-vvVkHMOj_NdJeWaUaZJmLgDMZA-HROluUjgbz9YvQHSOrEZcbGNUwC4e_9aLjmiidlsAnnJVi4f8N8tvlbZvdpHBOV43eQj-VFlEBwfeWKnftNbfhHxvyijHg9VO_LP1oIR7ecBgJZOXSuN-mn=w417-h334-no)

- 練習 7：切換啟動函數
  - 練習操作
    - 資料集切換 : 分類資料集(右下)-螺旋雙臂，特徵全選，隱藏層 1層 /8 神經元，批次⼤⼩固定 10，學習速率固定 1
    - 調整不同的啟動函數 後執⾏500次遞迴，看看學習效果有何不同?
  - 實驗結果
      - 在這種極端的情形下，Tanh會無法收斂，Relu很快就穩定在很糟糕的分類狀態，惟有Sigmoid還可以收斂到不錯的結果
      - 但實務上，Sigmoid需要⼤量計算時間，⽽Relu則相對快得很多，這也是需要取捨的，在本例中因位只有⼀層，所以狀況不太明顯



- 練習 ８：切換正規化選項與參數
  - 練習操作
    - 資料集切換:分類資料集(右下)-螺旋雙臂，特徵全選，隱藏層1層/8神經元，批次⼤⼩固定 10，學習速率固定 0.3，啟動函數設為 Tanh
    - 調整不同的正規化選項與參數後執⾏500次遞迴，看看學習效果有何不同?

  - 實驗結果
      - 我們已經知道上述設定本來就會收斂，只是在較⼩的 L1 / L2 正規劃參數下收斂比較穩定⼀點
      - 但正規化參數只要略⼤，反⽽會讓本來能收斂的設定變得無法收斂，這點 L1 比 L2情況略嚴重，因此本例中最適合的正規化參數是 L2 + 參數 0.001
      - 實務上：L1 / L2 較常使⽤在非深度學習上，深度學習上效果有限

### 重要知識點複習

- 雖然圖像化更直覺，但是並非量化指標且可視化不容易，故深度學習的觀察指標仍以損失函數/誤差為主
- 對於不同資料類型，適合加深與加寬的問題都有，但加深適合的問題類型較多
- 輸入特徵的選擇影響結果甚鉅，因此深度學習也需要考慮特徵⼯程
- 批次⼤⼩越⼩ : 學習曲線越不穩定、但收斂越快
- 學習速率越⼤ : 學習曲線越不穩定、但收斂越快，但是與批次⼤⼩不同的是，學習速率⼤於⼀定以上時，有可能不穩定到無法收斂
- 當類神經網路層數不多時，啟動函數 Sigmoid / Tanh 的效果比 Relu 更好
- L1 / L2 正規化在非深度學習上效果較明顯，⽽正規化參數較⼩才有效果

### 參考資料

- [深度学习网络调参技巧](https://zhuanlan.zhihu.com/p/24720954)



# 初探深度學習使用Keras

學習機器學習(ML)與深度學習(DL)的好幫手

## Keras 安裝與介紹

> - 初步了解深度學習套件 : Keras
> - 知道如何在⾃⼰的作業系統上安裝 Keras

### Keras 是什麼?

- 易學易懂的深度學習套件

  - Keras 設計出發點在於容易上⼿，因此隱藏了很多實作細節，雖然⾃由度稍嫌不夠，但很適合教學
  - Keras 實作並優化了各式經典組件，因此即使是同時熟悉TensorFlow 與Keras 的老⼿，開發時也會兩者並⽤互補

- Keras包含的組件有哪些?

  - Keras 的組件很貼近直覺，因此我們可以⽤ TensorFlow PlayGround 體驗所學到的概念，分為兩⼤類來理解 ( 非⼀⼀對應 )
  - 模型形狀類
    - 直覺概念：神經元數 / 隱藏層數 / 啟動函數
    - Keras組件 : Sequential Model / Functional
      Model / Layers
  - 配置參數類
    - 直覺概念：學習速率 / 批次⼤⼩ / 正規化
    - Keras組件 : Optimier / Reguliarizes /
      Callbacks

- 深度學習寫法封裝

  - TensorFlow 將深度學習中的 GPU/CPU指令封裝起來，減少語法差異，Keras 則是將前者更近⼀步封裝成單⼀套件，⽤少量的程式便能實現經典模型

- Keras的後端

  - Keras 的實現，實際上完全依賴 TensorFlow 的語法完成，這種情形我們稱 TensorFlow 是 Keras 的⼀種後端(Backend)

- Keras/TensorFlow的比較

  |          | Keras        | Tensorflow                     |
  | -------- | ------------ | ------------------------------ |
  | 學習難度 | 低           | 高                             |
  | 模型彈性 | 中           | 高                             |
  | 主要差異 | 處理神經層   | 處理資料流                     |
  | 代表組件 | Layers/Model | Tensor / Session / Placeholder |

### 安裝方法

請參考以下兩篇文章

- [新手初体验：Tensorflow-gpu1.8环境搭建与CPU比较（Win10+虚拟环境+实测结果）](https://zhuanlan.zhihu.com/p/38223869)
- [windows 10 64bit下安装Tensorflow+Keras+VS2015+CUDA8.0 GPU加速](https://www.jianshu.com/p/c245d46d43f0)

### 重要知識點複習

- Keras 是將 TensorFlow 等後端程式封裝並優化，易學易⽤的深度學習基礎套件
- 安裝 Keras ⼤致上分為四個步驟 : 依序安裝 CUDA / cuDNN / TensorFlow / Keras，只要注意四個程式間的版本問題以及虛擬環境問題，基本上應該能順利安裝完成

### 參考資料

- [Keras: The Python Deep Learning library](https://keras.io/)
- [keras安装和配置指南](https://blog.csdn.net/luoming1994130/article/details/52103645)



## Keras embedded dataset的介紹與應⽤

> - 了解 Keras 內建的 dataset
> - 如何使⽤ CIFAR10 做類別預測

### CIFAR10

- CIFAR10 small image classification
- Dataset of 50,000 32x32 color training images, labeled over 10 categories, and 10,000 test images.

```python
from keras.datasets import cifar10
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
```

### CIFAR100

- CIFAR100 small image classification
- Dataset of 50,000 32x32 color training images, labeled over 100 categories, and 10,000 test images.

```python
from keras.datasets import cifar100
(x_train, y_train), (x_test, y_test) = cifar100.load_data(label_mode='fine')
```

###  MNIST database

- MNIST database of handwritten digits
- Dataset of 60,000 28x28 grayscale images of the 10 digits, along with a test set of 10,000 images.

```python
from keras.datasets import mnist
(x_train, y_train), (x_test, y_test) = mnsit.load_data() 
```

###  Fashion-MNIST

- Fashion-MNIST database of fashion articles
- Dataset of 60,000 28x28 grayscale images of 10 fashion categories, along with a test set of 10,000 images. This dataset can be used as a drop-in replacement for MNIST. 

```python
from keras.datasets import fashion_mnsit
(x_train, y_train), (x_test, y_test) = fashion_mnsit.load_data()
```

### Boston housing price

- Boston housing price regression dataset
- Dataset taken from the StatLib library which is maintained at Carnegie Mellon University.
- Samples contain 13 attributes of houses at different locations around the Boston suburbs in the late 1970s. Targets are the median values of the houses at a location (in k$).

```python
from keras.datasets import boston_housing
(x_train, y_train), (x_test, y_test) = boston_housing.load_data()
```

### IMDB電影評論情緒分類

- 來⾃ IMDB 的 25,000 部電影評論的數據集，標有情緒（正⾯/負⾯）。評論已經過預處理，每個評論都被編碼為⼀系列單詞索引（整數）。
- 單詞由數據集中的整體頻率索引
  - 整數“3”編碼數據中第 3 個最頻繁的單詞。
  - “0”不代表特定單詞，⽽是⽤於編碼任何未知單詞
- 說明
  - path：如果您沒有本地數據（at '~/.keras/datasets/' + path），它將被下載到此位置。
  - num_words：整數或無。最常⾒的詞彙需要考慮。任何不太頻繁的單詞將oov_char在序列數據中顯⽰為值。
  - skip_top：整數。最常被忽略的詞（它們將 oov_char 在序列數據中顯⽰為值）。
  - maxlen：int。最⼤序列長度。任何更長的序列都將被截斷。
  - 種⼦：int。⽤於可重複數據改組的種⼦。
  - start_char：int。序列的開頭將標有此字符。設置為 1，因為 0 通常是填充字符。
  - oov_char：int。這是因為切出字 num_words 或 skip_top 限制將這個字符替換。
  - index_from：int。使⽤此索引和更⾼的索引實際單詞。

```python
from keras.datasets import imdb
(x_train, y_train), (x_test, y_test) = imdb.load_data(path=“imdb.npz”, 
                                                      num_words=None,
                                                      skip_top=0, 
                                                      maxlen=None, 
                                                      seed=113, 
                                                      start_char=1, 
                                                      oov_char=2, 
                                                      index_from=3)
```

### 路透社新聞專題主題分類

- 來⾃路透社的 11,228 條新聞專線的數據集，標註了 46 個主題。與 IMDB 數據集⼀樣，每條線都被編碼為⼀系列字索引

```python
from keras.datasets import reuters
(x_train, y_train), (x_test, y_test) = reuters.load_data(path=“reuters npz”, 
                                                         num_words=None,
                                                         skip_top=0,
                                                         maxlen=None,
                                                         test_split=0.2,
                                                         seed=113,
                                                         start_char=1,
                                                         oov_char=2,
                                                         index_from=3)
```

### 如何使用Keras dataset 做學習

- 適⽤於⽂本分析與情緒分類
  - IMDB 電影評論情緒分類
  - 路透社新聞專題主題分類
- 適⽤於 Data/Numerical 學習
  - Boston housing price regression dataset
- 適⽤於影像分類與識別學習
  - CIFAR10/CIFAR100
  - MNIST/ Fashion-MNIST
- 針對⼩數據集的深度學習
  - 數據預處理與數據提升

### 參考資料

1. [Keras: The Python Deep Learning library](https://github.com/keras-team/keras/)

2. [Keras dataset](https://keras.io/datasets/)

3. [Predicting Boston House Prices](https://www.kaggle.com/sagarnildass/predicting-boston-house-prices)

4. [Imagenet](http://www.image-net.org/about-stats)

   Imagenet數據集有1400多萬幅圖片，涵蓋2萬多個類別；其中有超過百萬的圖片有明確的類別標註和圖像中物體位置的標註。

   Imagenet數據集是目前深度學習圖像領域應用得非常多的一個領域，關於圖像分類、定位、檢測等研究工作大多基於此數據集展開。Imagenet數據集文檔詳細，有專門的團隊維護，使用非常方便，在計算機視覺領域研究論文中應用非常廣，幾乎成為了目前深度學習圖像領域算法性能檢驗的“標準”數據集。數據集大小：~1TB（ILSVRC2016比賽全部數據）

5. [COCO](http://mscoco.org/)

   COCO(Common Objects in Context)是一個新的圖像識別、分割和圖像語義數據集。

   COCO數據集由微軟贊助，其對於圖像的標註信息不僅有類別、位置信息，還有對圖像的語義文本描述，COCO數據集的開源使得近兩三年來圖像分割語義理解取得了巨大的進展，也幾乎成為了圖像語義理解算法性能評價的“標準”數據集。




## Keras Sequential API

> - 了解 Keras Sequential API
> - 了解 Keras Sequential API 與其應⽤的場景

### 序列模型

- 序列模型是多個網路層的線性堆疊。

- Sequential 是⼀系列模型的簡單線性疊加，可以在構造函數中傳入⼀些列的網路層：

  ```python
  from keras.models import Sequential
  from keras.layers import Dense, Activation
  model = Sequential([Dense(32, _input_shap=(784,)), Activation(“relu”)
  ```

- 也可以透過 .add

  ```python
  model = Sequential()
  model.add(Dense(32, _input_dim=784))
  model.add(Activation(“relu”))
  ```

### Keras框架回顧

![](https://lh3.googleusercontent.com/iFg9DGhNe0unCJaWaNzBXd0zH8PYJroNj3IevU9CQ6l0B98ySSxILtc-O--OSYuGpvQQgGSXEFCpRwVicQtPYjPJe66ab0wn-GuaiSSfxCyIMtcRc3yuxBMwGo3YVB6Lu8-gmo1fv-uTMZkJsEV-mnOuPBdCc-Wk84Z2tcAOSaA4YUMONlapCzj7mxA4kL9Ri2NLTHmgcrF3fRF5IUqmr-_fYY0P5qxdQOlRjuDKDFWnWzYhvFnuLnC-ZMFldiGihjLPledSwrUuwpcBjD4UI05ViQKixCG1QmEWQ4azMPsW_T-15dxBxNdO1a256yJJFLf9Nn4zep58Oc6FucTl6sekFcz3bkpyxbCe1mR-FxGFwB22ThoNnM1V2ftBEEN8W5kLk2wUcXW7frJ4qs7FvOGczcsucvPZ1VGWKzA-ZwFVLxVn0EY897TkrGp93C0rU2Jd9YYGzTZkxAc3F0fS8S9_V6l8TnjaZoHLVxsdEo3i207-diKILeyMaE4f-XN_GP8olxcMhzFOYAD_-LrkhuJJg2ZSRwO_ROBnpwk7iUNCL0cAApVUdzmwSt9ch3Q3QVB7lcSpt8RXwJQFLx75V_cmZZH4juoTrZ1WhvZz50YpJmzTXGDRTgSzIkdkXj3k-7WH6W1OeqgdHZ0EKeThLicQm23NbJSkVpwLLvIvfR9yL8DfVlxaEzmD5UftOzupkBYnnjri91YyjvXaNecXCEtLKJ-rELaaDQ0zCZoDyGfEQgI=w1305-h697-no)

### 指定模型的輸入維度

- Sequential 的第⼀層(只有第⼀層，後⾯的層會⾃動匹配)需要知道輸入的shape
  - 在第⼀層加入⼀個 input_shape 參數，input_shape 應該是⼀個 shape 的 tuple 資料類型。
  - input_shape 是⼀系列整數的 tuple，某些位置可為 None
  - input_shape 中不⽤指明 batch_size 的數⽬。

- 2D 的網路層，如 Dense，允許在層的構造函數的 input_dim 中指定輸入的維度。
- 對於某些 3D 時間層，可以在構造函數中指定 input_dim 和 input_length 來實現。
- 對於某些 RNN，可以指定 batch_size。這樣後⾯的輸入必須是(batch_size, input_shape)的輸入

### 常用參數

| 名稱       | 作⽤                                | 原型參數                                                     |
| ---------- | ----------------------------------- | ------------------------------------------------------------ |
| Dense      | 實現全連接層                        | Dense(units,activation,use_bias=True, kernel_initializer='glorot_uniform', bias_initializer='zeros') |
| Activation | 對上層輸出應⽤激活函數              | Activation(activation)                                       |
| Dropout    | 對上層輸出應⽤ dropout 以防⽌過擬合 | Dropout(ratio)                                               |
| Flatten    | 對上層輸出⼀維化                    | Flatten()                                                    |
| Reahape    | 對上層輸出 reshape                  | Reshape(target_shape)                                        |

- 前述流程 / python程式 對照

  ```pythno
  model = Sequential()
  model.add(Conv2D(64, 3,3), padding='same', input_shape=x_train.shape[1:]))
  model.add(Activation('relu'))
  model.add(Conv2D(128, (3,3)))
  model.add(Activation('relu'))
  model.add(MaxPooling2D(pool_size=(2,2)))
  model.add(Dropout(0.25))
  
  model.add(Flatten())
  model.add(Dense(512))
  model.add(Activation('relu'))
  model.add(Dropout(0.5))
  model.add(Dense(num_classes))
  model.add(Activation('softmax'))
  ```

  

### 重要知識點複習

- Sequential 序貫模型序貫模型為最簡單的線性、從頭到尾的結構順序，⼀路到底
- Sequential 模型的基本元件⼀般需要：
  1. Model 宣告
  2. model.add，添加層
  3. model.compile,模型訓練
  4. model.fit，模型訓練參數設置 + 訓練
  5. 模型評估
  6. 模型預測



### 參考資料

[Getting started with the Keras Sequential model](https://keras.io/getting-started/sequential-model-guide/)



## Keras Module API

> - 了解 Keras Module API
> - 了解 Keras Module API 與其應⽤的場景

### Functional API (or Model)

- Keras 函數式模型接⼝是⽤⼾定義多輸出模型、非循環有向模型或具有共享層的模型等複雜模型的途徑
- 定義復雜模型（如多輸出模型、有向無環圖，或具有共享層的模型）的⽅法。
- 所有的模型都可調⽤，就像網絡層⼀樣
  - 利⽤函數式API，可以輕易地重⽤訓練好的模型：可以將任何模型看作是⼀個層，然後通過傳遞⼀個張量來調⽤它。注意，在調⽤模型時，您不僅重⽤模型的結構，還重⽤了它的權重。

### 函數式API 與 順序模型

- 模型需要多於⼀個的輸出，那麼你總應該選擇函數式模型。
  - 函數式模型是最廣泛的⼀類模型，序貫模型（Sequential）只是它的⼀種特殊情況。
- 延伸說明
  - 層對象接受張量為參數，返回⼀個張量。
  - 輸入是張量，輸出也是張量的⼀個框架就是⼀個模型，通過 Model 定義。
  - 這樣的模型可以被像 Keras 的 Sequential ⼀樣被訓練



### 如何設定

使⽤函數式模型的⼀個典型場景是搭建多輸入、多輸出的模型

```python
from keras.layers import Input
from keras.models import Model
main_input = Input(shape=(100,), dtype='int32', name='main_input')
```



### 應用說明

- 利⽤函數式 API，可以輕易地重⽤訓練好的模型：可以將任何模型看作是⼀個層，然後通過傳遞⼀個張量來調⽤它。注意，在調⽤模型時，您不僅重⽤模型的結構，還重⽤了它的權重。
- 具有多個輸入和輸出的模型。函數式 API 使處理⼤量交織的數據流變得容易。
- 試圖預測 Twitter 上的⼀條新聞標題有多少轉發和點贊數
- 模型的主要輸入將是新聞標題本⾝，即⼀系列詞語。
- 但是為了增添趣味，我們的模型還添加了其他的輔助輸入來接收額外的數據，例如新聞標題的發布的時間等。
- 該模型也將通過兩個損失函數進⾏監督學習。較早地在模型中使⽤主損失函數，是深度學習模型的⼀個良好正則⽅法。



### 重要知識點複習

- 函數式API 的另⼀個⽤途是使⽤共享網絡層的模型。
  - 來考慮推特推⽂數據集。我們想要建立⼀個模型來分辨兩條推⽂是否來⾃同⼀個⼈（例如，通過推⽂的相似性來對⽤⼾進⾏比較）。
  - 實現這個⽬標的⼀種⽅法是建立⼀個模型，將兩條推⽂編碼成兩個向量，連接向量，然後添加邏輯回歸層；這將輸出兩條推⽂來⾃同⼀作者的概率。模型將接收⼀對對正負表⽰的推特數據。
  - 由於這個問題是對稱的，編碼第⼀條推⽂的機制應該被完全重⽤來編碼第⼆條推⽂（權重及其他全部）。

- Keras 函數式模型接⼝是⽤⼾定義多輸出模型、非循環有向模型或具有共享層的模型等複雜模型的途徑
- 模型需要多於⼀個的輸出，那麼你總應該選擇函數式模型。
- 函數式模型是最廣泛的⼀類模型，序貫模型（Sequential）只是它的⼀種特殊情況。
- 延伸說明
  - 層對象接受張量為參數，返回⼀個張量。
  - 輸入是張量，輸出也是張量的⼀個框架就是⼀個模型，通過 Model 定義。
  - 這樣的模型可以被像 Keras 的 Sequential ⼀樣被訓練

### 參考資料

[Getting started with the Keras functional API](https://keras.io/getting-started/functional-api-guide/)



## Multi-layer Perception多層感知

> - 理解多層感知器
> - 利⽤感知器寫個程式

- Multi-layer Perceptron (MLP)：MLP 為⼀種監督式學習的演算法
- 此算法將可以使⽤非線性近似將資料分類或進⾏迴歸運算

![](https://lh3.googleusercontent.com/hUy_C7_gox5ICoz3OWuWlVVF_6Jqn_h8q23c7DKi-qihr2UW4aA4zx76ffqPqGhNhlUVKF8bX_uTQfZVOY3VUCcpdpX6raxP8jjvhyAZT_BiuCpufnrGB8hbXCda9m4xeUXz_xcoehOBph66kHwO8zKZi3CLdShqdAO6yDpXhfXD7CX0h8TaMRaGyR59-QrJIdV7NQoGqA71Whe-GtH2R5kmawZWR1JjcD2hVEzoYVv8KAgL0DzGzZGvyd9qaDCXzkPMpcS_k8__PzCnvWbhscRejlXH5lhi30wqgD7IfD_UbVv5CDBA2rups6o0q1FlgbJxRA1X4izeTkGhlg3MKaHPoBDBWQVUsHyHTVld6AVD-uqdEdiTibWra2NhqCQuv3VWW4uIZ4OhRqEI3_su-IpqhqHt7bOu-tgRRLOYu1Y88WmqMk1NLQznqQ6S00TyezaIFxj9qug7pWOFTPN35Q9LfhAcS0JWQ-bZjEW2f6rMdcVloRflqW5AHXq9uSU4nxgUgzSeyD212JxjJq4d9qq2jv0Gswbcs6O9h2cSh7fBVb7gknnBuELBpb101-henaNfRSbqRs_iUqgnDZZ1o88q3SoGwjpjJl2rXC02qRL-Hns6bODzh9bGvsG51eiS5rp3fJyYZdBXp9FiOW6ewH_5O0mTxW_N41O5vbJcUcLm9DlELFbd9m4B8uIfmyP9IQhQeNS-pjP2Cchre90gJT8t=w396-h402-no)



- 機器學習- 神經網路(多層感知機 Multilayer perceptron, MLP) 含倒傳遞( Backward propagation)詳細推導

- 多層感知機是⼀種前向傳遞類神經網路，⾄少包含三層結構(輸入層、隱藏層和輸出層)，並且利⽤到「倒傳遞」的技術達到學習(model learning)的監督式學習，以上是傳統的定義。現在深度學習的發展，其實MLP是深度神經網路(deep neural network, DNN)的⼀種special case，概念基本上⼀樣，DNN只是在學習過程中多了⼀些⼿法和層數會更多更深。

  ![](https://lh3.googleusercontent.com/xVtqZygqGnU4NB4U5DI7x7DwftEP-NOsyfMLfQ7zPv1a2sb3cu1TndLdOvpaJL8gNixQeQaDhIgqrlRHEbjw10pMZ0OBGqER6kQQTv_7syK8N_5oLTwe38glL3DfdfDJdUp99-Gyg5R5_stliaYNgTtDml9ErTw5LJgQNrQZmQaA9hWUIN6-qfPZDNt4aI8p_Qv94AGVOg8dkvJzAyyt0rPvbF59hMZ3XxRxQopbcC_4PTcmzitCTFyqe3S6yNlouRVdpF2eaZOURn-Z6kFGcWwtzoJT_jfPFvJRepVJKxljUFe8WiAcF4c_jCCjDgAfJkrx709lvNWnaN9uRXqM2Mgio4uMiPA1YwZMw9Hox_sa5xUmFdqyylCbfS_aKyYJjacW8ky2bLcWxkPJ1mKHrbtVqJuVmH3GiRc-lL96D1zTXLSQts3AiuaqKbGEHLzpA6bdnBZuxWFggJqt_9X1c_MBUUoRIGT6NhAudGQ0SnW8U6jveJSvrN2Fl7eyNY6RToApANqR_DkthKs45flDQPnv020bD4z20IYCBbPOlswnxyJ3PUGB2_7eXMZB4xqCPaBJ4PKrU61x6bRUS-nabgs41TO7b0IqCkuyTiUq0-g4WCOBMcAKUU88ps8RK9rMb9Uphi5byS2Kyav_S-xIZzujooThWKsc4kqg-UdHNsxlaBm67dgaHrHXuPuUzURBMOmxRlL_ZvTvJEgcTNaiAsR4hmPCYLJWc_5kTY4Qp_Kt4Ag=w626-h294-no)

### 以NN的組成為例

![](https://lh3.googleusercontent.com/GnGBa_1pWSHeVGOgOY_tsmB5NG6YuL3RJGj7Z_dgbry9QOh1d6P1ni-eNFebNIZq9CK_YoS9nE9CaVu6VVgwHnG9d04TuhPKFucr8Jc_Zo2yA899tJlLIOnlI5thVFYarAy7K4ZtXufr-h5QKKmQIB7YeOo0Vmlu46C_Xl0I5QewePgFlkCEjQ2q6gvmsyJv9V2ZtItKKIRXLfflJ35NkjixOd5ylSSvOTLzOg_ww_gX24RiTnnens64-wJRZzhB_aMghCDMBqbLbBzrWo3ONdHUbNTKZXntJJEE_HitEQPq1S2LfqjfltUiOrh3lrDMuxv8wvrUGM1i3TxXj7lqdZkrdI4wwEQzTYnLYCjWjaoqVQdnADSS02aQ08Omv6AZvjg7rejeCaS1pCvpg72z-_ebWeismdZgy7euMeFutk0EfyVgTO4LYuNYBVTLLXe779TypM9ccd7K1pHtmeQegyO14dlkx7irtbMB_rvFjnx19XuPSZ2b_RTdhg3CJXMWiuWHRwvevJCibKFDkHkgvnImZEEGBh7ihbySvyLWaeas6aeoRZfOhyzsZ-O-1nkwDQQA05Y_yESMPCGIqjcbSlEUFerhhZAQc3z3CLOdJEwATyUtbjDFCmYvpU1ArA838q-hLOt84c2mKkVZPkkYH8qw-qQ1WUhxuhJ9HrlLyMa98p9PYrayBKwRZwoggtGWIOyYmUkYjn83AceNVd7GCbSa=w873-h385-no)

### Multi-layer Perceptron(多層感知器): NN 的結合

![](https://lh3.googleusercontent.com/eLjfhWJKXjRojUHbqqLb_kbKz_GetRM5K9QR8R1Rfj0IM9Fj6jf3FzFd5Wqt3fJY7UT-_MMSwHnUxl50TNyITdegGXOyuopEcl-d-Qo4YF6bkpLwq8lzb3Oe0NTyCH0Cz3ptxHvFQhhFNX-mIfolWjIXgLZ451WwECqXrmF2gCZlF4obsfEAP3lYdTuKJ_4iA36RHk-UgTaFSV7uvncxas1facXfYDurR9W4CzPuY0wykjp4OdIlDrj1nfDwIzNbsE7XpSvTOMJHiOsrSitNOLcLoGj0VxdnObi8NrrCUPg2LcxkcyfUPttdMMZCRcMj_to3K4_ADLnoxUrlWiWOr4S1an0CUpm24EWTly-3IiaHIxJVYsnYT0CRrxRzlhai2bVQUKUJHAWxBgMidQO0x69kcaeOkR8Ocx1XePamoeI2YihqRBqdzQB2K03HKjsMFXJ_Q7A3j-DilVwsFWc1w3GiG4PO9UekQCCRVj-XTKTnSrpHeDyEcx5Ufzf1uHePnAdf35-s8RU8ZZoUgxD5GbZhD7tbDZtGJFaoxKJNevao_tOY3n5W4Eq9mcDzHpKxPqzZ-Ar1XfTURfCrTPfefROKnMxQDg4-sLrepkvmobyyhG3d_syEJkJfYXyCx7lSYIU8Je0fhx76_MT6Ar-tCy-8dxO1kGcwZ-bO-ppXu7KfXBqUs6BnptK2BGiDpToeNk2ryBVxby3axOFbkl6C9-_B19BZMtTTwVPMuFSZyDQyA1U=w598-h372-no)

- **MLP優缺點**
  - 優點
    - 有能⼒建立非線性的模型
  - 可以使⽤ partial_fit​ 建立 realtime模型
  - **缺點**
    - 擁有⼤於⼀個區域最⼩值，使⽤不同的初始權重，會讓驗證時的準確率浮動
    - MLP 模型需要調整每層神經元數、層數、疊代次數
    - 對於特徵的預先處理很敏感



### 重要知識點複習

- 多層感知機其實就是可以⽤多層和多個 perception 來達到最後⽬的
- 在機器學習領域像是我們稱為 multiple classification system 或是 ensemble learning
- 深度神經網路(deep neural network, DNN)，神似⼈⼯神經網路的 MLP
- 若每個神經元的激活函數都是線性函數，那麼，任意層數的 MLP 都可被約簡成⼀個等價的單層感知器

### 參考資料

- [機器學習-神經網路(多層感知機 Multilayer perceptron, MLP)運作方式](https://medium.com/@chih.sheng.huang821/機器學習-神經網路-多層感知機-multilayer-perceptron-mlp-運作方式-f0e108e8b9af)

- [多層感知機](https://zh.wikipedia.org/wiki/多层感知器)



## 損失函數的介紹與應用

> - 了解損失函數
> - 針對不同的問題使⽤合適的損失函數

### 損失函數

- 機器學習中所有的算法都需要最⼤化或最⼩化⼀個函數，這個函數被稱為「⽬標函數」。其中，我們⼀般把最⼩化的⼀類函數，稱為「損失函數」。它能根據預測結果，衡量出模型預測能⼒的好壞
- 損失函數⼤致可分為：分類問題的損失函數和回歸問題的損失函數
  - Numerical Issues



### 損失函數為什麼是最小化

- 期望：希望模型預測出來的東⻄可以跟實際的值⼀樣
- 預測出來的東⻄基本上跟實際值都會有落差
  - 在回歸問題稱為「殘差(residual)」
  - 在分類問題稱為「錯誤率(errorrate)」

- 損失函數中的損失就是「實際值和預測值的落差」
- 在以下的說明中，y 表⽰實際值，ŷ 表⽰預測值



### 損失函數的分類介紹

- **mean_squared_error**

  - 就是最⼩平⽅法(Least Square) 的⽬標函數-- 預測值與實際值的差距之平方值。
  - 另外還有其他變形的函數, 如 mean_absolute_error, mean_absolute_percentage_error, mean_squared_logarithmic_error.

  $$
  \sum (\hat y - y)^2/N
  $$

  - 使⽤時機為處理 y 為數值資料的迴歸問題

  - Keras 上的調⽤⽅式：

    ```python
    from keras import losses
    model.compile(loss= 'mean_squared_error', optimizer='sgd')
    # 其中，包含 y_true， y_pred 的傳遞，函數是表達如下：
    keras.losses.mean_squared_error(y_true, y_pred)
    ```



- **Cross Entropy**
  - 當預測值與實際值愈相近，損失函數就愈⼩，反之差距很⼤，就會更影響損失函數的值要⽤ Cross Entropy 取代 MSE，因為，在梯度下時，Cross Entropy 計算速度較快。

  - 使⽤時機：

    - 整數⽬標：Sparse categorical_crossentropy
    - 分類⽬標：categorical_crossentropy
    - ⼆分類⽬標：binary_crossentropy

  - Keras 上的調⽤⽅式：

    ```python
    from keras import losses
    model.compile(loss= ‘categorical_crossentropy ‘, optimizer='sgd’)
    # 其中, 包含 y_true， y_pred 的傳遞, 函數是表達如下：
    keras.losses.categorical_crossentropy(y_true, y_pred)
    ```



- **Hinge Error (hinge)**
  
  - 是⼀種單邊誤差，不考慮負值，同樣也有多種變形，squared_hinge, categorical_hinge
  
    - 適⽤於『⽀援向量機』(SVM)的最⼤間隔分類法(maximum-margin classification)
  
  - Keras 上的調⽤⽅式：
  
    ```python
    from keras import losses
    model.compile(loss= ‘hinge‘, optimizer='sgd’)
    # 其中，包含 y_true，y_pred 的傳遞, 函數是表達如下:
    keras.losses.hinge(y_true, y_pred) 
    ```
  
    

- **⾃定義損失函數**

  - 根據問題的實際情況，定制合理的損失函數

  - 舉例：預測果汁⽇銷量問題，如果預測銷量⼤於實際銷量則會損失成本；如果預測銷量⼩於實際銷量則會損失利潤。

    - 考慮重點：製造⼀盒果汁的成本和銷售⼀盒果汁的利潤不是等價的

    - 需要使⽤符合該問題的⾃定義損失函數⾃定義損失函數為：
      $$
      loss = \sum nf(y_, y)
      $$

  - 損失函數表⽰若預測結果 y ⼩於標準答案 y_ ，損失函數為利潤乘以預測結果 y 與標準答案之差

  - 若預測結果 y ⼤於標準答案 y_，損失函數為成本乘以預測結果 y 與標準答案之差⽤

  - Tensorflow 函數表⽰為：

    ```python
    loss = tf.reduce_sum(tf.where(tf.greater(y, y_), COST*(y-y_), PROFIT*(y_-y)))
    ```

    

### 重要知識點複習

- 損失函數中的損失就是「實際值和預測值的落差」，損失函數是最⼩化
- 損失函數⼤致可分為：分類問題的損失函數和回歸問題的損失函數



### 參考資料

1. [TensorFlow筆記-06-神經網絡優化-損失函數，自定義損失函數，交叉熵](https://blog.csdn.net/qq_40147863/article/details/82015360)
2. [Hinge_loss](https://en.wikipedia.org/wiki/Hinge_loss)
3. [使用損失函數](https://keras.io/losses/)



## 激活函數的介紹與應⽤

> - 了解激活函數
> - 針對不同的問題使⽤合適的激活函數

- 激活函數定義了每個節點（神經元）的輸出和輸入關係的函數為神經元提供規模化非線性化能⼒，讓神經網絡具備強⼤的擬合能⼒

  ![](https://lh3.googleusercontent.com/y0MVNA4d2-mbKs7-sudJaUprzfYPbaETic2CF3NOAAdKrPhmxwUpLLN8Xu489mdR11Ah6XgTCm1z3xJODrZgvhiNbR-8JvQnebV6Rv8swA4l1bq3wnYnNZtH6Yveq-dcTjdyH7dCNMpqefgV83ZL5Ucj8lgJOtN1Hdk3u7qtHdcX7q6rSBC17pfKuvD1YyPz7BRbAPrBcFCyHGvMPyM1U5hl_-iRyJmvjugnIS7Aq02-oNGgCjd1OpXjRwNjnVHlH491kYPin6yJEswfXj_4OZocoFhaKheyTsbYFGOsD9No_NYK1EI3aIUx_A49IOUEtGvwabySVSwALw9DyPpaH_tPc4GqPugz2kIyKGe4Hp8SMDIqr5CQjKeNtnlsMtbIcGnJ2orBxpQ1qs7R0vLJn4iqJAkrCvgAHz0nrMUvG_jphpKl5IEsWQbtK1u0wEwZesLzb5ZjjyFp0N2-oH3o3gFAKW4eLIp0GNtyouWulrUqGNp3tsrXEel4bXKVMXZK45BRBNL5ley5egWblLBZuoBfsG0BQfY71dzTB9F-_hOxotIlKjTWBYVsUTcro8ym6cZ3IQfHoitje0T2Bl8VQBL7n3REhs4wcgZyW_r_TQExah7CZKiXI69Fjn_ExETqz7Yv6d47PVTvXMDzNOlN-vqgXxSA-zQo7Ryl89ETt2jfuEo7lIIEer7xD3EoNot7Se5oYirLcmMHd1CJVmAbKPAN=w951-h324-no)

- 輸出值的範圍
  - 當激活函數輸出值是有限的時候，基於梯度的優化⽅法會更加穩定，因為特徵的表⽰受有限權值的影響更顯著
  - 當激活函數的輸出是無限的時候，模型的訓練會更加⾼效



### 激活函數的作用

- 深度學習的基本原理是基於⼈⼯神經網絡，信號從⼀個神經元進入，經過非線性的 activation function
  - 如此循環往復，直到輸出層。正是由於這些非線性函數的反复疊加，才使得神經網絡有⾜夠的 capacity 來抓取複雜的 pattern
- 激活函數的最⼤作⽤就是非線性化
    - 如果不⽤激活函數的話，無論神經網絡有多少層，輸出都是輸入的線性組合

- 激活函數的另⼀個重要特徵是：它應該是可以區分
  - 以便在網絡中向後推進以計算相對於權重的誤差（丟失）梯度時執⾏反向優化策略，然後相應地使⽤梯度下降或任何其他優化技術優化權重以減少誤差



### 常用激活函數介紹

- **Sigmoid**
  - 特點是會把輸出限定在 0~1 之間，在 x<0 ，輸出就是 0，在 x>0，輸出就是 1，這樣使得數據在傳遞過程中不容易發散
  - 兩個主要缺點
    1. Sigmoid 容易過飽和，丟失梯度。這樣在反向傳播時，很容易出現梯度消失的情況，導致訓練無法完整
    2. Sigmoid 的輸出均值不是 0
  - Sigmoid 將⼀個 real value 映射到（0,1）的區間，⽤來做⼆分類。
  - 用於二分類的輸出層

$$
f(z) = \frac{1}{1+exp(-z)}
$$

![](https://lh3.googleusercontent.com/SkBS0YS1tjuUJeRiR8-VASachN3cLzjr1lfydbZB0qW77v1uRUEftA0LGJu2qYQHvqV07LMA3IZ9Tc3H_O1qs6dg3dLUViRsPv1LHuCBRsxNUklvs9WsCkmiS6e9dXpFyF_Otbf8b1SIJs3NnuacCW4Eh_gBb0-hmd3to_3uq6tAwABqNe_pcHJPYNhcj94o7qdea_SCfdaGdPMLIm_f2ttsuEIki8atLbg88ZYeeNdF8BTChgxs48yjZol6u6oa9rwFpYUzbmk3JuoRXwMsZi1mWxD5kAt73jhYFmQQxO9M3Km8FiGZPdLAOcCcc8brhMMcyUIA1K1qHPym9wMwVJSX3ccPZ8Byo8_ZJeSX99fmEGwwcR8r7iW3C4kqEnVH03OeEwXfDlLPqf2R7ZrTWK4rKPFN7xIbtx2ULINiDwwGFrO60QtzgW0IeLPZ84Ai_i8w9VYr3z-tSlsfjeyMJBz8m7PuQJ1yAGEhcwv5nAoVZjoMdakz5pGB0Y0PAe35mbPbcuA38_G7BoP53P79_l1nz6CxkHD5ujFwTD_NNyw3z26Pbtfj8JPVMA3c4FlfaSpEj4krHeAPx_RvR7LWXvk5AD2Eyt71r2pyip7K79k7iMEEg66FuYQzPRdL-H8xZAjkqpTr5aYSRe-WbvHZE8n9GjJwzDGcWgO2exvgZHI4nLmDtkF2_6aEeZecY9MFYCOgbwgcdCqKKBOcsCpq5Imw=w523-h293-no)

- **Softmax**

  - Softmax 把⼀個 k 維的 real value 向量（a1,a2,a3,a4….）映射成⼀個（b1,b2,b3,b4….）其中 bi 是⼀個 0～1 的常數，輸出神經元之和為 1.0，所以可以拿來做多分類的機率預測

  - 為什麼要取指數

    1. 第⼀個原因是要模擬 max 的⾏為，所以要讓⼤的更⼤。

    2. 第⼆個原因是需要⼀個可導的函數

  - ⼆分類問題時 sigmoid 和 softmax 是⼀樣的，求的都是 cross entropy loss

$$
\sigma (z)_j = \frac {e^{zj}}{\Sigma^K_{k=1}e^{ek}}
$$

![](https://lh3.googleusercontent.com/1NZ7mUCQSySzY-XNnopDqtCPbxbnilQHYDzWLzgxxMY9v5z6PKT4MiflECUuTiqPn55C6wZPjo1p-43T_bho8Vgx9t-WvDrRG8ekW8X-0HLWm1YyFRygXCQTuexky4DoyIw95jrulG5DCc18VNu7DV7KaQtDD2YryBAbPLvsARqezxxRLi4e7lQzNaVTclzXpO0SPhGGwrzZbbS59z7rmszmryVB_DUxU-5hm1AkEnXxyRAaW44XCw0xRNVV1bV-o7NEIuCJe-u3mxZp9cFCg8n71PGFc5OOKUfpRUntG2DbXpQaqPYon31Qbh9ICZPimO61fog4A0B_D8QUb5c3Ag0nTHpI3Ooeav0HN5YgpgEv56lbrrJS04IAdFxPuPpxfxuiHBTaKCqH7dsH2ptkIYL2lDlh3KMH36bwF5kl537yn2Gd6qr_w5qgOGkpm64E8dtWoOSl0P0SYATVPLOxqXZmBVYUicEpfwNlLAGnxq9MjvmZH32jCVv9Wxe5Vo5OvJVneDzFW-8fYLEp2JDHveGF0U215tQtgB5qU8ZliHR1tfkIsbJeMnihacuaFUiXS_ra3_r-Vj85JFaSRnqxSubqnKJoEtYDjMk7Ao8-OoFRHSN-TMJzB4Xlk4d-uXQg-fTOImsc9BdXPdSdcoFQmQWtZcpJMVCd_y41tzDZRgZDYZuVMljp72Jp_hezceQhd10kVqUp-DOJT7a2EYR2jD18=w456-h279-no)



- **Tanh(Hyperbolic Tangent)**
  - 也稱為雙切正切函數，取值範圍為 [-1,1]。
  - 在特徵相差明顯時的效果會很好，在循環過程中會不斷擴⼤特徵效果
  - 將輸入值壓縮到 -1~1 的範圍，因此它是 0 均值的，解決了 Sigmoid 函數的非 zero-centered 問題，但是它也存在梯度消失和冪運算的問題。
  - 幾乎所有場合都可以使用

$$
tanh(x)=2sigmoid(2x)-1
$$

![](https://lh3.googleusercontent.com/d_ri4ppN254PX19pxd26N923ywqAtqJml4NSfnP5iXTMzF4WX3ZPoB5jC1HDufZpjotntizIILl1XWigbNFaki0Tl_3nlUyAV9pT3G4y6FznIVOVj5ULNJ50MSYUg9K-_f7RrbPdvhV_Arps5FZ9gRTjEg3qDcDJ33p9qr9iXpGzC7NUPP13zz-h_TLJlOxqEcjrhEzp8YUmZAFY07gaHAO3dOq3YvbM3yQ39IjuKW__LR0GfaOSJQsNgUj9G11j_W9gy-_9YHGjbV7ZG2_Lh8XNS6p30cITuJnWeZXGfrIDwslsepnomLT8yJxbGiuvmtbXT-0SzICRx0oxzN55zcgjgzo5Hy738aXAK62ORPtq-lAETiVYo6LcJvx0Y8pmc5b2hHjKTIg2iDHBcWc_7YbA4e25-x6OgkwC1Q4cZTd0PevAcqqZLWIND4cE9Ti9-c7HD0J_Y3oePenzu5S2gJbooJa_rJrcULDoKNT1lHszuJ9bjgQYk7IHjQQL7DkNFN_YR5nKGax0KY7synIEzTvHsW_QovLsfFdvPfSFaNPJe9WQQM26nT5ItjiOWhwdDzHYMEuRvq47P0sgMCHWkVXTiw1Tsy27P9eQu7YH7K6DlpOSEplOSR0ftvsccEz9s0GT8qN8B0f9BvUlWVqEoIZOBhpp-QzFlBnTd4j3m4uLaT6flF__U1E73dC7RUdMftNHFSoAU1PXf_uJQd6XUncv=w465-h345-no)

![](https://lh3.googleusercontent.com/_Dzc4gX4yGc3cSOBwllc-hkyp-qzZTGHjO_AlJsfaPSdYF7oiuXrsWqBD0AuiULCKgBPoeh6pcsp6BsKUIX8WLUjG-br6dAIEH-842JfAoJRcnN88fXi1SQUBBvnFpGN7s17017Hbm6wybyENXkmZEAD_c17Nv91Hgt534CqZjtlye1jjJpE1CqdGgTb6Z3uu-h6y4JoRhYki9zpvDdVVjzlv9dkrhyFDwsc695OZNv4QAZD_sIT4DFPJQux03kxlM5MTl4g-jCHImfp2T-WWv8K_xJ5FeVAWWoCbhrn8Lecc74chkwLs98LvbH8fiwiyCKna_1Wkol2h6poLxzddCttEyRwEHprfzT_7fxUP_AwOIncpUAczFDrqS_kMLsuMk0S4FDV0_2SUVE-48O0g0DRC2Q_q4412Xk4mmW-yKMGsmSgPkkf7s3jrLz22iJzRp8hUBHJis-0R8-hP-7Q0Em8h9B-lt_Rhb8zGEukByeFTH-QGq5c-E9pNo7sjY_3yWj9Z_xZZygDjga5XwVBNPcmytKNLnL52ik8FEwGwytthoA4BoGVbTVoGA22eoQdR7_Tml7oZQf7W1xSO_8O6B7Ob-LgEFqL5-oHM_mrOd1firn7I8uWcz4_Su1OJ0cOn8uQHof7oNc3ILY5m5k9CBZLmP1dJ7RYAS6EUMLzo2lws3xsHX72d2yRLxYYDq9xlaHHOt0eFsG0_CAtHw03lxh_=w917-h303-no)

>  左邊是 Sigmoid 非線性函數，將實數壓縮到［0,1］之間。右邊是 Tanh 函數，將實數壓縮到［-1,1］。



- **ReLU**
  - 修正線性單元（Rectified linear unit，ReLU）
    - 在 x>0 時導數恆為1
    - 對於 x<0，其梯度恆為 0，這時候它也會出現飽和的現象，甚⾄使神經元直接無效，從⽽其權重無法得到更新（在這種情況下通常稱為 dying ReLU）
    - Leaky ReLU 和 PReLU 的提出正是為了解決這⼀問題
    - 二分類的問題選擇sigmoid,其餘預設選ReLU

$$
f(x) = max(0, x)
$$

![](https://lh3.googleusercontent.com/dwlrne9BJQWNSoyTSylprqyJzGIy5NRJNRtVl5-0LLTxRCXsttvGPRtkayLX3NF1Y0DtUcrnjH08beUrjyjd2Mas5PFCm94_b8dSxxSc6xmS3maqjDfZzxjzYJFTNcMnxMW6qCbiLW8m9aSxzNNtBsZ9FljvVCDQNswB-4Wdcu-nccEqOeuh-iGYGZDpCZid49DxHDIYA5pRm3jPmN5u1l8B0d_Odw0R-HWE2T_l_uZsbx0Ntz0w6QRu-br1zF_rXSd6onk_r6vwsv9XFeeT_wfEqbKLST2ex_OYTjLrmoOxLO_CjmjBOlvog1ChPM1QomrgiMdKO89CTooE5m3UvXGjOSrmi13qppOb2fOAfm0IEOOwfsxWMlUNwtpBpw0ELoQb3XvqHZ8ueZvWRWarDCdpe4FvOPao6_4QUdXT28KiZ9ml0GZYo-K-81MFlQvH4TPaMGRWl-aM1FXydCG_I8Ax9k_fD3O_jGf52_caSDhpFB11tLB4EF8Dz3unfDFXP_G1CLsZUVhZcTsu8-PIYjwbhmqRg8wqq7raATtGsLYxahXcOj5RZUtyLJ1dKywIQ97rXuu0OZZQx2seSvhJmzRsTe-JipucTn42bhw-ZL6eNeJyNqj6Zi5sKKk8f8_NKhYZ2p4ONiAyUnhtUgIjE69hVr3MgYJXjK8vENWxTV7R-Hi9fC6ls9u2kFMSPR9icFCxQlzPs9lZ-Yvfml-H99rG=w359-h306-no)

- **ELU**
  - ELU 函數是針對 ReLU 函數的⼀個改進型，相比於 ReLU 函數，在輸入為負數的情況下，是有⼀定的輸出的
  - 這樣可以消除 ReLU 死掉的問題
  - 還是有梯度飽和和指數運算的問題

$$
f(x) =\begin{cases}x\quad \quad \quad, x > 0 \\a(e^x-1),\quad x \leq  0\end{cases}
$$

![](https://lh3.googleusercontent.com/C4V51_Dhrzp8SMc7xxicp4WRDzkEPlPn99XwF_376qHKfpvz18PHYB6BpES9etSYzQgjAYdNqvmoNeci6JKgJKbNihTAnDY9n736XDM2KY6JL_CNvEzoQgn0DEQr6YORNBQ89xCRPaaJTDdFXNrTKaw7O-riyM1iyNVyWG0LBrXMjy2fIUTLed1hvh5cxUCiR8eqT7ZZoG-J51-lRHIds8JhhnMh1t3gtAVNzF77L30uxIw_C8ng9zk32sTsoResJr8_VA5r-ZvGdBcK36IyFSWBS9svDiG2qKUP4E_ga9PbbkTcsey6OUGGcT7pIRBHUFW-ANAjQWzFfp8sDrmqfPr6DvFlRjF8SwHL500lCZ0HQdF5pavTSM_pBFzeFIITWhR1ZwFgFe2pGJ271pmeAielpj4bE4gSnSiNZE9y43aGSmRNPZYZeGFOjRPPasXNqD_qiRwShNw3WfdBOy-3gZUsf0ToXm6NAhGFJ_qwqKu6zQgotx3cFNo0Ao8ERklAp8QH4wRFWlzWrAfaMy1UuKhUNJct-1Pzu5JlzJlPZMjQtQOHAgIM6Bdxa88U31mtrxi_BWpmOCe7rZbskwX4CZUPOv9jW_hBMcoVASYFUONMBTbwfT0mThjkcy7d-gCTKMsWf1o_-2uf0zSzZG1IKw-gLhMpmcI22GCtk8rmaQlqFBn4pKXdXvncTbcn6RCat2mfhHMl9X4XhfIXWqtdQLto=w440-h391-no)

- **PReLU**

  參數化修正線性單元（Parameteric Rectified Linear Unit，PReLU）屬於 ReLU 修正類激活函數的⼀員。

- **Leaky ReLU**

  當 α=0.1 時，我們叫 PReLU 為Leaky ReLU，算是 PReLU 的⼀種特殊情況

  > RReLU 以及 Leaky ReLU 有⼀些共同點，即爲負值輸入添加了⼀個線性項。

- **Maxout**
  - 是深度學習網絡中的⼀層網絡，就像池化層、卷積層⼀樣，可以看成是網絡的激活函數層
  - 神經元的激活函數是取得所有這些「函數層」中的最⼤值
  - 擬合能⼒是非常強的，優點是計算簡單，不會過飽和，同時⼜沒有 ReLU 的缺點
  - 缺點是過程參數相當於多了⼀倍

$$
f(x) = max(wT1x+b1, wT2x+b2)
$$

![](https://lh3.googleusercontent.com/Ls--crVs-P_V__x9aIQvY6ZOdd42oRCFIX393lRTN4p7jtqLrYCeWfheODskFNSVRb-weYTlq9dp2HmhYVY6nJ8XDBK5qoVcm1K_LurmBlAo45q612ZM5MyuqGozXKKhl8O_O0V1VIv4sHWpfJJCpl7Lovf9Z5JuQtdZ8PZ8lZ2oB9iXn5jrTMyLatGH2id10ri-qYpV8TcoucWt8cAexTW5M3ZdwVxMtHHGNbi6sMOAKYmFQD_mY7pE5CkqvbURDo7t3E8-jriE1-_mg7A7Z_Raft2Q-HESyFf78UEHkhBr3gVwT-eC0-OujAoDQVFMQ8xk0yXTmVHDWZPt09j8yZBzYGB5v1_1nJCf_HZvxX9QMCS2RvGyE8mi-NKhmrouJw9JJOg9fUWZZPR3E8rk7XhVVgBX67ROaZXO8aBeQZTTlwyxXtFxg3tx-Za1LCIZCLKjS_n7BX6PNjOwARiM075Lx2a65Tux9EqcChfgTmmFtCkNWYsZKhK7TDVg-FCbr724P0ynZ4WrWWSZDXebFm-qnNgjFMG9KtmaIDIzDNhnC_73pFwcnI-f9iAAeDWU7MwTDbYLml_duAK6qAqRQgyhLrsIHN6O9pw9SIGYc51g_LZvokKNp46LhQXZOGoshzBwS6ofPRHw3GboKZD--_FI1v27Az-avTw7FLiASuphSo7-7RDngllBVP2x5jgUAiKDKvWIrXX5yP4OMa4zAfvN=w575-h272-no)

### 如何選擇正確的激活函數

- 根據各個函數的優缺點來配置
  - 如果使⽤ ReLU，要⼩⼼設置 learning rate，注意不要讓網絡出現很多「dead」 神經元，如果不好解決，可以試試 Leaky ReLU、PReLU 或者Maxout
- 根據問題的性質
  - ⽤於分類器時，Sigmoid 函數及其組合通常效果更好
  - 由於梯度消失問題，有時要避免使⽤ sigmoid 和 tanh 函數。ReLU 函數是⼀個通⽤的激活函數，⽬前在⼤多數情況下使⽤
  - 如果神經網絡中出現死神經元，那麼 PReLU 函數就是最好的選擇
  - ReLU 函數建議只能在隱藏層中使⽤
- 考慮 DNN 損失函數和激活函數
  - 如果使⽤ sigmoid 激活函數，則交叉熵損失函數⼀般肯定比均⽅差損失函數好；
  - 如果是 DNN ⽤於分類，則⼀般在輸出層使⽤ softmax 激活函數
  - ReLU 激活函數對梯度消失問題有⼀定程度的解決，尤其是在CNN模型中。
- 梯度消失 Vanishing gradient problem
  - 原因：前⾯的層比後⾯的層梯度變化更⼩，故變化更慢
  - 結果：Output 變化慢 -> Gradient⼩ -> 學得慢
  - Sigmoid，Tanh 都有這樣特性
  - 不適合⽤在 Layers 多的DNN 架構

![](https://lh3.googleusercontent.com/x3CN8LTxGqX31Q4PX1ArsP4KTFLs4YHnV4rntTHfzJi96IR1QXt2n4LJz3pxU-Tr4XjZmFTppnQ_47VVA-uIeYdm3G7fjOh33YE2tU_BMb9uGhCMYZIXprGXy99MrsurSwlJyMlsgJzkKdefTyJeCereiNOjG30wT-AzFQXcvGnOOZDOKjJr-4gRvK_76kxUn2F8GDwS95pXtAQ97SEKKtMMHiVulST0k8MdZI1zy6dUqiisFvM1SgvgPbP0r-7xeZf5vs1jOgYg4sXrrJGLrgXla28jJ6EGRDby3NLyjesNm6nAzQoUiPP4hnp4zLvwtdyVxr-VmKzjGuk0gs62GzmUbqIdUXDtiA83mvxSVRkuzJrwVCZTHYtmpTW3woctaNs-7_UwsJ-ITvzu16TY3HfHBNHJY2uivi3RoEYHDroRe0tsrxonAMH5dtFYELcxaUVNxYCiPEyjg4utMR2NnTEAxmE69hDcB-W87Ppduc5eCBW3DU2aWfvg6J1WmOLwDOHg3NjU188XfVja5Vlv-8KRCqQYzOkjMNNoUTUFdlzhQIvCa4yAImSxqG-oSln1Wn98OnjRd6dUoFGm5rEMsmfi6y9UKwTO0AvVCYVa4xoRYY5b1I47TRs-XzfxNuL2snGrBpeG0o42oqpMlwv1FVc1H0rbFNyh8eb6ejeObUkpZtQBU0qXX1Q8tF1DgYb_jffmgz7YiHZK6xjZ-7G0cGYL=w565-h422-no)

### 前述流程 / python程式對照

- 激活函數可以通過設置單獨的激活層實現，也可以在構造層對象時通過傳遞 activation 參數實現：

  ```python
  from keras.layers import Activation,Dense
  model.add(Dense(64,activation=‘tanh’))
  ```

- 考慮不同Backend support，通過傳遞⼀個元素運算的Theano / TensorFlow / CNTK函數來作為激活函數：

  ```python
  from keras import backend as k
  model.add(Dense(64,activation=k.tanh))
  model.add( Activation(k.tanh))
  ```

  

### 重要知識點複習

- 激活函數定義了每個節點（神經元）的輸出和輸入關係的函數為神經元提供規模化非線性化能⼒，讓神經網絡具備強⼤的擬合能⼒

![](https://lh3.googleusercontent.com/OFWSVJT0xs502t__JzjL3jrZvUftkYv2S0lgUC75yz-6HKM3HBHh4DaF3uCXvOx0OSZcme06kuxAnQh2_sn8-NRaUi-PPNPU6HCppiVGpazjirnTu_XycxtHnSZxRrxrSr0OzoexgZ37-tZJKOja_bcsZrWC62dACETfRXftcOXp5_C-ECmTporBqhes_GgibpX8znHaWBhvA-f7B2S9bJzlfnZkFyVUYlLxjTvkguiFdjQ9P_e8URIHFXGV9CgAa4-TcQhM4GMpzgVU5nquGTAZ3eR80A00gZyCak0oPMuUo-Akpml-UFy47ATiuqDrs1OD_ZbGI3pYK62rcDmOHDH14uHAU0sUv-u7NBUWoBZa3FoLKYoxlqV4XbB0RYK_eZZmlvOlQ3TETKByuzrU3OafadAlyeWD2W_YKb2-hM8K7ExiEClcMSsbFPl9rZW2kUFVYIKPGT6kcsfY9jIUU1fCmwyyJU88s3KhTP8oyZlUomrMyHLaQ9vmF7k-B1Ouk_ICKYe-geRQHnkOQfFcGGBwINkJGX91wGDN_iP0gsUv2nseWcf48ApRiBblGLOPWbYDGgAL7ZLQ4SdKKZptdeguvEbzkP71xsQzywkBYEyuUB11bDZu4hpmskBpnNhtQFuWKzBSVDZxarnmrtb9d2XTYsaJEYznSs3mx9RspkjzIInpruvHKN18Jr4ltnChcihPTcYPNoMyjMzKyipc7Plk=w808-h386-no)

### 參考資料

1. [神經網絡常用激活函數總結](https://zhuanlan.zhihu.com/p/39673127)
2. [Reference 激活函數的圖示及其一階導數](https://dashee87.github.io/data science/deep learning/visualising-activation-functions-in-neural-networks/)
3. [Maxout Network](https://arxiv.org/pdf/1302.4389v4.pdf)
4. [Activation function](https://en.wikipedia.org/wiki/Activation_function)



## Gradient Descent 簡介

> - 了解 Gradient Descent 的定義與程式樣貌
> - 初步理解 Gradient Descent 的概念
> - 能從程式中 Fine Tune 相關的參數

![](https://lh3.googleusercontent.com/P2ufOXOvaucTCVxEprN6gsJw6ngC_BYQ8u0CwVGybjSX1_Y5jneX2iMz0UvVK3XjL5dE6O7TZM7wSK_xMwXfHQqdO9g1ZUkSclTis6Ig1KXiWj5tPxLgdN_aKyxoe6Fm-W7pSsGmcLtXMwrUEl3oBGr3VluJwrpP3m5nBPowY1PmUX1o-ruYWrz3WsWBJkJbmWzx9e-Bd0VNL5PS0n6D6ahyv85dR4fjZRAmjZG8eKENEQ5hBRVrt9SQN2PrbqYliwRpvViYyYy4mT9zbzqrsKstQ33jxbbButqxCWJQPZrLqY2ZIHpXl1rP2me5wZnBkk4TDOhfkbDEHX6XTO-IugoBGc4e3-PYAgcM4AV12PXkhZ2lLlxxdkCoLcaev3vNiiIoEnm10XMSKGQMxDq6mVafP2NCFrSj5g9OnFx4VyN4uDQ7SWoUnCTd11MvTrDk88sdkLqoGBUg0Puaye6lfrRPq1qbbC_psvhYy226FTThYVkiOy5YH7YKa4ebiXUBUzuokOFhiesDxp1esCPZ2o2m5rWW1za5RRh1OawWqNtpE8PZCh5ryTf5sNH5Bvht_81uDV_Ehwctza_MrpwoT5wT_wSLoOEaGEZ75RaRONUPqISCc_z80Dvyv4iUiKb3TYyBX6U4RSJI8D-xrfQZihSSx2BRQvQwjRGrZLXLZJQvUiwMEhzSOeME3WTjPwAaxBJh8UVyGynL9PZfKKPan64G=w1027-h427-no)

- 機器學習算法當中，優化算法的功能，是通過改善訓練⽅式，來最⼩化(或最⼤化)損失函數
- 最常⽤的優化算法是梯度下降
  - 通過尋找最⼩值，控制⽅差，更新模型參數，最終使模型收斂
  - wi+1 = wi - di·ηi, i=0,1,…
  - 參數 η 是學習率。這個參數既可以設置為固定值，也可以⽤⼀維優化⽅法沿著訓練的⽅向逐步更新計算
  - 參數的更新分為兩步：第⼀步計算梯度下降的⽅向，第⼆步計算合適的學習

### 學習率對梯度下降的影響

- 學習率定義了每次疊代中應該更改的參數量。換句話說，它控制我們應該收斂到最低的速度或速度。
- ⼩學習率可以使疊代收斂，⼤學習率可能超過最⼩值

![](https://lh3.googleusercontent.com/yzU1_yVv-CP0T3gGicaPdMjSO4Ppo9wOpgpWiZ7I_81vlGlzqrxQc9eW8EKTaUYKxEoz7KgF1ey1grQQSOmUbNe0G__lUO7BFw9Wwcd3Kdyao8LPkYw9Dp7a7VsNqpYrb-U6-DJ_EinUd9CQCl1ld6CIJI2n6RM_kJiwJ6xa0sY8DoOCEm5G_ABDiFyTyZOth-0V0m-2Ynnp4X2_al0Axku5X_CCTokrS3czLz3z3iHjVuaR_PUdaI1uTDwRLX6Uf39fn76akqB3-MjvzMnOdWcn0nqHQGcrywjbJeXu1U0My1Ltgf-51aEhzFhy2yM3NhYxtjgRKP-T3AUVTLPMWfmNYi7uC5dC2s3fEKNy_rtbNsfSXJ5mO1S_gxzKGn4awbPUEaR2lcvixJn9WS2X8neCa7AcuuG3Z7pUTRGOMZlgdHsnhTuGAZSWG6UVqUB2RVeAJDvKNj3x5qy5qf3WDk0p3sbwfzEfZVtmFqHsTl2E94X5qdU_AbGmmUh7vKS67ND5NcoiqkXlSULGxtv5MvlQhNlpMlbMisgmyTBjsxUK8JL4BH7ocAvjSv40O9wp7VdxY8kQmHvCbBgzdKd-kFtnkxg5XmYEQ1stCSr_9loJxPTO8WBAgfQXQGHv6Q-d6P1Mxf_eGVNCF2fUwxo-BTHEDbVgfJin6AOuTVPXAnzakc4MZ6xbcvtCtc2uWUdHtrg2d328wmnlH2WRFKpg-9qY=w1031-h276-no)

### 梯度下降法的過程

- ⾸先需要設定⼀個初始參數值，通常情況下將初值設為零(w=0)，接下來需要計算成本函數 cost
- 然後計算函數的導數-某個點處的斜率值，並設定學習效率參數(lr)的值。
- 重複執⾏上述過程，直到參數值收斂，這樣我們就能獲得函數的最優解

![](https://lh3.googleusercontent.com/CC-uYdn_fZGk1DvGv8T5nWD7RrZH4bUSlGOmoVkwDhp9Jc3rLzgJAplUvK3KtutQp-0fgi1QGd66g69nIJLTnRJvB_uC3ukERZU6cc-CP867UEQF1WxVOn7ueeuDfMHRWEDDA-0G-J_iH52FiH-_UB0Si6kYIXBcjPev-Bz-RQ3WEZid1CNGqmrfYbpC5jJnTjVhl7ObTUyMrnZ_wvVhFd1qHL8SOr_CmkOEs729q_JH5xYBZZCNUzicRZXfSOFp7dxxS6iTtfv3l54Qz1VvYpRdshhVqUdSHIjsKU79O7JsgvlvyL42_O-prcjfhvOnhRGe7dp0SN_jSu4Uq_lE4rc2W1CJcMTijyBirEV06qVGSoZU_ceKkPQWxU5OPZW7pbNmSdV7jFucDxmeeV3odIZn13MQgxHA-rAqJuZ-7Yonbfn54dse2K_9hXJp5_oLLDL3sJFTe-8tiMxBxFLNPv6gEgjs---3H26NzWjO45aNudxjMZcNY6GF_2rZSX8EexUWGSD6R8_LZQKv2oFzfNEf-dZhocYiQJ9D2XYz8W1RBLdzp5UBnkcCzC_u0OafQX4RBjX9lif0INkuJoLRzdVBssXAOF0psy3VaNikl8YjSNbPkfwS_8UKQ4-YXFoyD8cHu_Om0iWLA4ldqZHO0jMeEldrWIn0D0ygwCb8Os642Mm9VUmBUCMLemBri0wdjndESfcAunD5nll04dscG3oM=w757-h323-no)

### 怎麼確定到極值點了呢？

- η⼜稱學習率，是⼀個挪動步長的基數，df(x)/dx是導函數，當離得遠的時候導數⼤，移動的就快，當接近極值時，導數非常⼩，移動的就非常⼩，防⽌跨過極值點

![](https://lh3.googleusercontent.com/LB71izAdksVkRZbpJbVVFJgEgIqct32yMOt507joZoWxT_FZuR-eLDsfdpI2v5Ef8Xxs4xPqIRCh5xs9p7zRz60vF_rDqNcJgOSDIpLG84ywRWBwGCWoS34eLDN7McFlKqiiwgtR48jAjprHsVEnry5Qwxc23ybeEabEM71m7MSSmkBPzJe04jHF74N2rygeNx9gI2tmKe4CkJ7_OmGHp5kBSyFVOMIWw9ohist9hmv-IQ2d_5OWrIJcee77Ag_hW9f8xi86iQMmF7qPHP0DnUBQle3lfdZUfYCCRyCknsKC-CKxW90Z4Sj9Mm3TRxWOg1XlolifQIE_Ht7p_zUuRhkz01qR6J8S3ECDaKdrsKP_4zOKF0t93bPUGdMirN5R3uwNEdRwcpB5ztjjHV-Kh5yBzID-KWBLWRFLZ_WFcKlnPhor0CkRliQMfHitJUDz-6eDl56Vpqri5Be-oaOB_bWP-bjSN94G1UbC2fbBsNRM47qP1SZHIpNIsTmT6-LlciRrrTsmgxzBDGYAoszRezlPEsTu9XqyeR4g4bSfDFUldIGxSHyJaYeQZ54jXbKwC-TAV3-UsS1pjDUqvfr8kD3LXuZjxvu9Tp8J8tVVjyBF9Wf3iHluuerCRk14255h_mf9Tg9aHmdj8M3WKhtGTEpihvk9rxkb17gh5EZBAeITbzlmkHffnjGLP3lvthLCTGUKgoBs9ATD5agI_hvjZ0n7=w845-h376-no)

- Gradient descent never guarantee global minima
- Different initial point will be caused reach different minima, so different results
- 在訓練神經網絡的時候，通常在訓練剛開始的時候使⽤較⼤的 learning rate，隨著訓練的進⾏，我們會慢慢的減⼩ learning rate ，具體就是每次迭代的時候減少學習率的⼤⼩，更新公式
  - 相關參數:
    - decayed_learning_rate 哀減後的學習率
    - learning_rate 初始學習率
    - decay_rate 哀減率
    - global_step 當前的 step
    - decay_steps 哀減週期

- 使⽤ momentum 是梯度下降法中⼀種常⽤的加速技術。Gradient Descent 的實現：SGD, 對於⼀般的SGD，其表達式為
  $$
  X ← x -a * dx
  $$

- ⽽帶 momentum 項的 SGD 則寫⽣如下形式：
  $$
  v = \beta * v -a*dx 
  $$

  $$
  X ← x + v
  $$

  其中ß即 momentum 係數，通俗的理解上⾯式⼦就是，如果上⼀次的 momentum（即ß ）與這⼀次的負梯度⽅向是相同的，那這次下降的幅度就會加⼤，所以這樣做能夠達到加速收斂的過程



### 重要知識點複習

- Gradient descent 是⼀個⼀階最佳化算法，通常也稱為最速下降法。
- 要使⽤梯度下降法找到⼀個函數的局部極⼩值，必須向函數上當前點對應梯度（或者是近似梯度）的反⽅向的規定步長距離點進⾏疊代搜索。
- 梯度下降法的缺點包括：

  - 靠近極⼩值時速度減慢。

  - 直線搜索可能會產⽣⼀些問題。

  - 可能會「之字型」地下降
- avoid local minima
    1. 在訓練神經網絡的時候，通常在訓練剛開始的時候使⽤較⼤的 learning rate，隨著訓練的進⾏，我們會慢慢的減⼩ learning rate
       - 學習率較⼩時，收斂到極值的速度較慢。
       - 學習率較⼤時，容易在搜索過程中發⽣震盪
    2. 隨著 iteration 改變 Learning
       - 衰減越⼤，學習率衰減地越快。 衰減確實能夠對震盪起到減緩的作⽤
    3. momentum
       - 如果上⼀次的 momentum 與這⼀次的負梯度⽅向是相同的，那這次下降的幅度就會加⼤，所以這樣做能夠達到加速收斂的過程
       - 如果上⼀次的 momentum 與這⼀次的負梯度⽅向是相反的，那這次下降的幅度就會縮減，所以這樣做能夠達到減速收斂的過程



### 參考資料

- [知乎 - Tensorflow中learning rate decay](https://zhuanlan.zhihu.com/p/32923584)

- [機器/深度學習-基礎數學篇(一):純量、向量、矩陣、矩陣運算、逆矩陣、矩陣轉置介紹](https://medium.com/@chih.sheng.huang821/機器學習-基礎數學篇-一-1c8337179ad6?source=post_page---------------------------)

- [機器/深度學習-基礎數學(二):梯度下降法(gradient descent)](https://medium.com/@chih.sheng.huang821/機器學習-基礎數學-二-梯度下降法-gradient-descent-406e1fd001f?source=post_page---------------------------)

- [機器/深度學習-基礎數學(三):梯度最佳解相關算法(gradient descent optimization algorithms)](https://medium.com/@chih.sheng.huang821/機器學習-基礎數學-三-梯度最佳解相關算法-gradient-descent-optimization-algorithms-b61ed1478bd7?source=post_page---------------------------)
- [五步解析机器学习难点—梯度下降](https://zhuanlan.zhihu.com/p/27297638)



## Gradient Descent數學式

> 了解 Gradient Descent 的數學定義與程式樣貌

### Gradient梯度

- 在微積分裡⾯，對多元函數的參數求 $\partial$ 偏導數，把求得的各個參數的偏導數以向量的形式寫出來，就是梯度。
- 比如函數 f(x), 對 x 求偏導數，求得的梯度向量就是 ($\partial$f/$\partial$x)，簡稱 grad f(x)或者 ▽f(x)

$$
Function(x) = ydata = b+w*xdata
$$

### 最常用的優化算法 -梯度下降

- ⽬的：沿著⽬標函數梯度下降的⽅向搜索極⼩值（也可以沿著梯度上升的⽅向搜索極⼤值）

- 要計算 Gradient Descent，考慮

  - Loss = 實際 ydata – 預測 ydata 

    ​     = w* 實際 xdata – w*預測 xdata (bias 為 init value，被消除)

  - Gradient =  ▽f($\theta$) (Gradient = $\partial$L/$\partial$w)

  - 調整後的權重 = 原權重 – $\eta$(Learning rate) * Gradient
    $$
    w ←w- η  ∂L/∂w
    $$

    > 此處指每走⼀步即更新⼀次) 



### 梯度下降的算法調優

- Learning rate 選擇，實際上取值取決於數據樣本，如果損失函數在變⼩，說明取值有效，否則要增⼤ Learning rate

- ⾃動更新 Learning rate - 衰減因⼦ decay

  - 算法參數的初始值選擇。初始值不同，獲得的最⼩值也有可能不同，因此梯度下降求得的只是局部最⼩值；當然如果損失函數是凸函數則⼀定是最優解。

  - 學習率衰減公式

    - lr_i = lr_start * 1.0 / (1.0 + decay * i)

    - 其中 lr_i 為第⼀迭代 i 時的學習率，lr_start 為初始值，decay 為⼀個介於[0.0, 1.0]的⼩數。從公式上可看出：

      > decay 越⼩，學習率衰減地越慢，當 decay = 0時，學習率保持不變
      >
      > decay 越⼤，學習率衰減地越快，當 decay = 1時，學習率衰減最快

- 使⽤ momentum 是梯度下降法中⼀種常⽤的加速技術。
  x ← x − α ∗ dx (x沿負梯度⽅向下降)
  v = ß ∗ v − a ∗ d x
  x ← x + v

- 其中 ß 即 momentum 係數，通俗的理解上⾯式⼦就是，如果上⼀次的
  momentum（即ß ）與這⼀次的負梯度⽅向是相同的，那這次下降的幅度就
  會加⼤，所以這樣做能夠達到加速收斂的過程

![](https://lh3.googleusercontent.com/na5BUBlKpeVl5D7ka3yaYVZZnPlyD87vReJ0sag9DlARk0pBqPmwzcbM6XNf5njtnpYNzDls59s64kHCrbTaqmINw4K-N6DTtLFZeqP6lLFySMOFGKaAsnAJ3zn2czG504P9yqhPJvYKmUx0Z8FyDkK9tPywCq3Gnd97Vj_SubPwH22C1T_qdmcxJiY_7lb-KsHFFa86v2UsRbdRn3RKVBl588dHBrkWtQ-9SiPkNAH3IbatBTewW0CxZ2SvCuuPWOEqfD42fd8LNab9nOf--4vPqdpmyGU2JTHU5aJZU0wK9yHsE3y4mdBosrpmYXs4k8o5pVAA6YN6XmEKOPPU6P0PhlGB_B_x71-ZeNtJFJjLM_aLYK3gSEH2FhRjN9P13upQ3HBFd-I0D2p7ubVAdvOHNsKXB7r6P1QpaoeXuGTsCUBt5PjBCacwWOPolCUJRXk8QtNcX_hQUuMgsJiGdVV7Sr9qd6YL8UrRkmikbNtFCTtZi-22B9h-0wa6J6tzSmkYmFWPoXN8Tnmxqn6akj6PQ7HgWMsZYSMESfS439t5rGTuQgsVjfIpe_oUYEB5pPR9lxKHMFzqBKlcxjGCPtkXevIvVDHGKdUJuCToy7kK-mgrdKok7ihNAB1dEBMfS9UROA1kWJ4cVHZ-o0kgC3DY1b2MufUD08_sES0g3zfs6Zb_NX8W0opxR2mFGqxHeqjG2iuk5Nsz3U6ieuxs5goM=w924-h411-no)

### 重要知識點複習

- Gradient descent 是⼀個⼀階最佳化算法，通常也稱為最速下降法。
- 要使⽤梯度下降法找到⼀個函數的局部極⼩值，必須向函數上當前點對應梯度（或者是近似梯度）的反⽅向的規定步長距離點進⾏疊代搜索。
  - avoid local minima
    - Item-1：在訓練神經網絡的時候，通常在訓練剛開始的時候使⽤較⼤的learning rate，隨著訓練的進⾏，我們會慢慢的減⼩ learning rate
      - 學習率較⼩時，收斂到極值的速度較慢。
      - 學習率較⼤時，容易在搜索過程中發⽣震盪
    - Item-2：隨著 iteration 改變 Learning
      - 衰減越⼤，學習率衰減地越快。 衰減確實能夠對震盪起到減緩的作⽤
    - Item-3：momentum
        - 如果上⼀次的 momentum 與這⼀次的負梯度⽅向是相同的，那這次下降的幅度就會加⼤，所以這樣做能夠達到加速收斂的過程
        - 如果上⼀次的 momentum 與這⼀次的負梯度⽅向是相反的，那這次下降的幅度就會縮減，所以這樣做能夠達到減速收斂的過程

### 參考資料

1. [機器學習-梯度下降法](https://www.jianshu.com/p/31740cd2ca48)
2. [gradient descent using python and numpy](https://stackoverflow.com/questions/17784587/gradient-descent-using-python-and-numpy)
3. [梯度下降算法的參數更新公式](https://blog.csdn.net/hrkxhll/article/details/80395033)



## BackPropagation

![](https://lh3.googleusercontent.com/BSf6XzwhrnNABte9N5JstOCk2JQl9y5mxeyumymP2KO-QGfyWHgAQaYn7rBWs7sWRb9p2HjM1aVwHqbMCAuD_xYBy0CBPWa-jLx4669isgtLJBguWVVglGpr80iLQY_pyZLzvu8p7aEAXR5RYVIHnEYiP6GpnzO_R5UKPhETFCgzQKwpsWe2nyPxOnsl-3djyENkjN7nJKdyxZmFBqW7xCbS20F7hQ2LWTyS8tKl-GB84wA6AMyhXnaV9heUiVix1xFmBzITEMA3Q-_SKMvhfMLc8nCIiXXO44cRjxdmzlgi-q2UwQ2x6BxUvyfYH2oIR9erDfPY6C4DEfOOLfuEiUqcP3H_xy9sJiVj8sXM-NgMRS3x6mg_ZpQJXuRCYrx_54iDfnC26gdoFYZKlxcHpxtUbz_QAgY4BD9l_j_BeonBIuNe6kX-PCX1P9q-1wpJ1c1uyuNCzvf3vfBzuezZBDgzmv-BI1a616JYJugc0Ztb-7_NuwQOXkHSVBjnT-Uq-zGH0m6m04Xj8VkCZt8sZHJ_ZJADfmzWqsx81ifAtmT-4mojaZZ7QtbxROUrr_G8u13bPsmqzU12rdt6spJTD3wn2YjCJ4-8VqBB6wyXxNLjC28kqmFyE3HGaaw14NNhDoqIGWhLrpNzXSX8qi_2-nOEzXVtwbVb5OXdk9IgonTFKGQ87EDAn5QFhnnWGXDd0bloBvEc4zlMqypb8sBsF9Zt=w1240-h707-no)

> - 前⾏網路傳播(ForwardPropagation) /反向式傳播(BackPropagation )的差異
> - 反向式傳播BackPropagation的運作

### 何謂反向傳播

- 反向傳播（BP：Backpropagation）是「誤差反向傳播」的簡稱，是⼀種與最優化⽅法（如梯度下降法）結合使⽤的該⽅法對網路中所有權重計算損失函數的梯度。這個梯度會反饋給最優化⽅法，⽤來更新權值以最⼩化損失函數。
- 反向傳播要求有對每個輸入值想得到的已知輸出，來計算損失函數梯度。因此，它通常被認為是⼀種監督式學習⽅法，可以對每層疊代計算梯度。反向傳播要求⼈⼯神經元（或「節點」）的啟動函數可微

### 推導流程

![](https://lh3.googleusercontent.com/X7h72dPGnJkyEVh2FcaGVXPpWroXzN2c83A0FICa_H6cfZ0zo87rIQXzkRhYOeClI2Hltfff3N-FDh3tdwMmyY2dMkUQ_xdGkvgOI7OGqMo8GWpHLq8MS32NjpL-TfwV3YoBpSc4P2F7bFe1hzN3SlHRPA3eFrrbjqbcmYQWqRKF-kf1U2sjUcnkTqwezqpslaS_dvJUUSl2O05t4gHMXruC1leS5y4cLc4gyHpWvJW7aFWG-J-GZaSSyoGlT18XtzI_GHIphbUWZaDtMrVKw8QGe5JPKvatzf2EpH-JkIpDM_mcAaqfmZqvJ7OnkudJ0PgKPgk4JYdcBeAp8nLDcOC9_EyXKDcoP5Rr5VfMU650H2ANm3n-1JetXAO7NxE7_uAIei__GnbHhwnhr5jY3uuo6lVdtI8e49fiG0x9WtOiNqFcEx-U7e8qyOmhQGxQJhrjTWlM9X7jANtk_gVL7wMAl8sT12tjPILiniOdO-RA5a165dtUa7TQMaJOdhIYM4V25ChE-Vd8bplunqXlKGUkQA6BGkjljZC3q-ERPmgEroq_O0MVc0pgpWKM48_xYwchw6jOUAleSscs1Dm4yDT2F88klIZoHljIrRoIha5kqWqMcIb7rZMhJoEQzTjHY7oPoTD6r-1kOw0DGpX3_fL4JKPnTJD7_C2mJlSegj_FKHFmO8VXZ9wsSU3qxmsx2ziNMBwCWCfvo2a-pGoaItde=w660-h450-no)

### 重要知識點複習

- BP 神經網路是⼀種按照逆向傳播算法訓練的多層前饋神經網路
- 優點：具有任意複雜的模式分類能⼒和優良的多維函數映射能⼒，解決了簡單感知器
  不能解決的異或或者⼀些其他的問題。
  - 從結構上講，BP 神經網路具有輸入層、隱含層和輸出層。
  - 從本質上講，BP 算法就是以網路誤差平⽅⽬標函數、採⽤梯度下降法來計算⽬標函數的最⼩值。

- 缺點：
  - 學習速度慢，即使是⼀個簡單的過程，也需要幾百次甚⾄上千次的學習才能收斂。
  - 容易陷入局部極⼩值。
  - 網路層數、神經元個數的選擇沒有相應的理論指導。
  - 網路推廣能⼒有限。

- 應⽤：
  - 函數逼近。
  - 模式識別。
  - 分類。
  - 數據壓縮
- 流程
  - 第1階段：解函數微分
  - 每次疊代中的傳播環節包含兩步：
    - （前向傳播階段）將訓練輸入送入網路以獲得啟動響應；
    - （反向傳播階段）將啟動響應同訓練輸入對應的⽬標輸出求差，從⽽獲得輸出層和隱藏層的響應誤差。
  - 第2階段：權重更新
    - Follow Gradient Descent
    - 第 1 和第 2 階段可以反覆循環疊代，直到網路對輸入的響應達到滿意的預定的⽬標範圍為⽌。

### 參考資料

1. [Backpropagation](https://en.wikipedia.org/wiki/Backpropagation)
2. [BP神經網絡的原理及Python實現](https://blog.csdn.net/conggova/article/details/77799464)
3. [完整的結構化代碼見於](https://github.com/conggova/SimpleBPNetwork.git)
4. [深度學習-BP神經網絡(python3代碼實現)](https://blog.csdn.net/weixin_41090915/article/details/79521161)



## Optimizers

> - 了解 optimizers 的使⽤與程式樣貌
> - 理解 optimizers 的⽤途
> - 能從程式中辨識 optimizers 的參數特徵

### 什麼是Optimizer

- 機器學習算法當中，⼤部分算法的本質就是建立優化模型，通過最優化⽅法對⽬標函數進⾏優化從⽽訓練出最好的模型
- 優化算法的功能，是通過改善訓練⽅式，來最⼩化(或最⼤化)損失函數 E(x)
- 優化策略和算法，是⽤來更新和計算影響模型訓練和模型輸出的網絡參數，使其逼近或達到最優值

### 常用的優化算法

- Gradient Descent
  - 最常⽤的優化算法是梯度下降

    - 這種算法使⽤各參數的梯度值來最⼩化或最⼤化損失函數E(x)。

  - 通過尋找最⼩值，控制⽅差，更新模型參數，最終使模型收斂

  - 複習⼀下，前面提到的 Gradient Descent

    > wi+1 = wi - di·ηi, i=0,1,…
    >
    > - 參數 η 是學習率。這個參數既可以設置為固定值，也可以⽤⼀維優化⽅法沿著訓練的⽅向逐步更新計算
    > - 參數的更新分為兩步：第⼀步計算梯度下降的⽅向，第⼆步計算合適的學習

- Momentum

  > ⼀顆球從⼭上滾下來，在下坡的時候速度越來越快，遇到上坡，⽅向改變，速度下降

  $$
  V_t ← \beta V_{t-1}-\eta \frac{\partial L}{\partial w}
  $$

  
  - $V_t$:方向速度，會跟上一次的更新相關

    - 如果上⼀次的梯度跟這次同⽅向的話，|Vt|(速度)會越來越⼤(代表梯度增強)，W參數的更新梯度便會越來越快，
    - 如果⽅向不同，|Vt|便會比上次更⼩(梯度減弱)，W參數的更新梯度便會變⼩

    $$
    w ← w + V_t
    $$

    - 加入 $V_t$ 這⼀項，可以使得梯度⽅向不變的維度上速度變快，梯度⽅向有所改變的維度上的更新速度變慢，這樣就可以加快收斂並減⼩震盪

- SGD
  - SGD-隨機梯度下降法(stochastic gradient decent)

  - 找出參數的梯度(利⽤微分的⽅法)，往梯度的⽅向去更新參數(weight)
    $$
    w ← w - \eta \frac{\partial L}{\partial w}
    $$

    - w 為權重(weight)參數，
    - L 為損失函數(loss function)， 
    - η 是學習率(learning rate)， 
    - ∂L/∂W 是損失函數對參數的梯度(微分)

  - 優點：SGD 每次更新時對每個樣本進⾏梯度更新， 對於很⼤的數據集來說，可能會有相似的樣本，⽽ SGD ⼀次只進⾏⼀次更新，就沒有冗餘，⽽且比較快

  - 缺點： 但是 SGD 因為更新比較頻繁，會造成 cost function 有嚴重的震盪。

  ```python
  keras.optimizers.SGD(lr=0.01, momentum=0.0, decay=0.0, nesterov=False)
  ```

  - lr：學習率
  - Momentum 動量：⽤於加速 SGD 在相關⽅向上前進，並抑制震盪。
  - Decay(衰變)： 每次參數更新後學習率衰減值。
  - nesterov：布爾值。是否使⽤ Nesterov 動量。

  ```python
  from keras import optimizers
  model = Sequential()
  model.add(Dense(64, kernel_initializer='uniform', input_shape=(10,)))
  model.add(Activation('softmax’))
  
  # 實例化⼀個優化器對象，然後將它傳入model.compile()，可以修改參數
  sgd = optimizers.SGD(lr=0.01, decay=1e-6, momentum=0.9, nesterov=True)
  model.compile(loss='mean_squared_error', optimizer=sgd)
                       
  # 通過名稱來調⽤優化器，將使⽤優化器的默認參數。
  model.compile(loss='mean_squared_error', optimizer='sgd')
  ```



- mini-batch gradient descent
  - batch-gradient，其實就是普通的梯度下降算法但是採⽤批量處理。
    - 當數據集很⼤（比如有100000個左右時），每次 iteration 都要將1000000 個數據跑⼀遍，機器帶不動。於是有了 mini-batch-gradient——將 1000000 個樣本分成 1000 份，每份 1000 個，都看成⼀組獨立的數據集，進⾏ forward_propagation 和 backward_propagation。

  - 在整個算法的流程中，cost function 是局部的，但是W和b是全局的。
    - 批量梯度下降對訓練集上每⼀個數據都計算誤差，但只在所有訓練數據計算完成後才更新模型。
    - 對訓練集上的⼀次訓練過程稱為⼀代（epoch）。因此，批量梯度下降是在每⼀個訓練 epoch 之後更新模型。

  ![](https://lh3.googleusercontent.com/pSAfVKPxy0VBBeI_5sAFsNORpc6TocZpoZF6uEddMy1fwNyeG83nGh_jz0FRqQsKrfW4mt-30Yn1Refw5k026Lk_ZckL_5bchE90SOpO1xrlPd9bvp-rTxj27vsVjJfsTnb-v3TZ39YqPsUySoCyG5JfQwavcb7-YEBjwB37v6feLBp5LbLfL9eX8TkKpFQpcP5WLepnCDUsDsztOqqsBwlG2YnU8Bmnh_MOJSR67U3Q7bjPbwVP-Le78u-zhv6QZW0GmLip8ugjqXRVGKTkL_Z5xqlCRPiDUugTSZSioJheg_U070Qg5WK9VgechLxomLG6xiaTsQxzF7DG6XATFeXEe6Tvcm5wGPf22eO5tY3p-7APlGmZfeGls8CdxHw6sHzlk0Yki2rFqqwsUZ2yUEY9aCVMaXEnYq9y6-ptna4XAWkmeoyX_zBqyrzWKuVjgHRPUhOdM2MOyrH55Om8PwvWk-mIczqndAs46npxhoR_MNwDPPojp2sXtafG9njZgGWK2NcblGt60JWA2Isl_1FnwUqY_mbNyRivLUGJHA0kx-gux0SvJg3fRC-wbVIJyGdWQ4Ur53f06Vt5-CIKnvXiDBVkI6mXOZkXF_whZ47XzGLC15FCNxg2_zcrVrsnhtVjvjazRuNegMfKHNNiuACC_2HYN65yqWwvF0TyxXVeFr1aMnqw3xgnFDLQpr7hLcZ-ePdZreRkDTNVHY67vrPj=w354-h485-no)

- 參數說明
  - batchsize：批量⼤⼩，即每次訓練在訓練集中取batchsize個樣本訓練；
    - batchsize=1;
    - batchsize = mini-batch;
    - batchsize = whole training set
  - iteration：1個 iteration 等於使⽤ batchsize 個樣本訓練⼀次；
  - epoch：1個 epoch 等於使⽤訓練集中的全部樣本訓練⼀次；

  > Example:
  > features is (50000, 400)
  > labels is (50000, 10)
  > batch_size is 128
  > Iteration = 50000/128+1 = 391
  - 怎麼配置mini-batch梯度下降
    - Mini-batch sizes，簡稱為「batch sizes」，是算法設計中需要調節的參數。
    - 較⼩的值讓學習過程收斂更快，但是產⽣更多噪聲。
    - 較⼤的值讓學習過程收斂較慢，但是準確的估計誤差梯度。
    - batch size 的默認值最好是 32 盡量選擇 2 的冪次⽅，有利於 GPU 的加速
    - 調節 batch size 時，最好觀察模型在不同 batch size 下的訓練時間和驗證誤差的學習曲線
    - 調整其他所有超參數之後再調整 batch size 和學習率

- Adagrad
  - 對於常⾒的數據給予比較⼩的學習率去調整參數，對於不常⾒的數據給予比較⼤的學習率調整參數

    - 每個參數都有不同的 learning rate,

    - 根據之前所有 gradient 的 root mean square 修改
      $$
      \theta^{t+1} = \theta - \frac{\eta}{\sigma^t}g^t
      $$

      $$
      \sigma^t = \sqrt{\frac{(g^0)^2+...+(g^t)^2}{t+1}}
      $$

      > Root mean square (RMS) of all Gradient 

  - 優點：Adagrad 的優點是減少了學習率的⼿動調節

  - 缺點：它的缺點是分⺟會不斷積累，這樣學習率就會收縮並最終會變得非常⼩。

    ```python
    keras.optimizers.Adagrad(lr=0.01, epsilon=None, decay=0.0)
    ```

    - lr：float >= 0. 學習率.⼀般 η 就取 0.01
    - epsilon： float >= 0. 若為 None，默認為 K.epsilon().
    - decay：float >= 0. 每次參數更新後學習率衰減值

    ```python
    from keras import optimizers
    model = Sequential()
    model.add(Dense(64, kernel_initializer='uniform', input_shape=(10,)))
    model.add(Activation('softmax’))
                         
    #實例化⼀個優化器對象，然後將它傳入model.compile() , 可以修改參數
    opt = optimizers. Adagrad(lr=0.01, epsilon=None, decay=0.0)
    model.compile(loss='mean_squared_error', optimizer=opt)
    ```

- RMSprop

  - RMSProp 算法也旨在抑制梯度的鋸⿒下降，但與動量相比， RMSProp 不需要⼿動配置學習率超參數，由算法⾃動完成。更重要的是，RMSProp 可以為每個參數選擇不同的學習率。

  - RMSprop 是為了解決 Adagrad 學習率急劇下降問題的，所以
    $$
    \theta^{t+1} = \theta ^t - \frac{\eta} {\sqrt{r^t}}g^t
    $$

    $$
    r^t = (1-p)(g^t)^2+pr^{t-1}
    $$

  - 比對Adagrad的梯度更新規則：分⺟換成了過去的梯度平⽅的衰減平均值

    ```python
    keras.optimizers.RMSprop(lr=0.001, rho=0.9, epsilon=None, decay=0.0) 
    This optimizer is usually a good choice for recurrent neural networks.
    ```

    - lr：float >= 0. Learning rate.
    - rho：float >= 0.
    - epsilon：float >= 0. Fuzz factor. If None, 
    - defaults to K.epsilon().
    - decay：float >= 0. Learning rate decay over each update

    ```python
    from keras import optimizers
    model = Sequential()
    model.add(Dense(64, kernel_initializer='uniform', input_shape=(10,)))
    model.add(Activation('softmax’))
    #實例化⼀個優化器對象，然後將它傳入model.compile() , 可以修改參數
    opt = optimizers.RMSprop(lr=0.001, epsilon=None, decay=0.0)
    model.compile(loss='mean_squared_error', optimizer=opt) 
    ```

- Adam

  - 除了像 RMSprop ⼀樣存儲了過去梯度的平⽅ vt 的指數衰減平均值，也像momentum ⼀樣保持了過去梯度 mt 的指數衰減平均值,「 t 」：
    $$
    m_t=\beta_1m_t + (1-\beta_1)g_t
    $$

    $$
    v_t=\beta_2m_t + (1-\beta_2)g^2_t
    $$

  - 計算梯度的指數移動平均數，m0 初始化為 0。綜合考慮之前時間步的梯度動量。

  - β1 係數為指數衰減率，控制權重分配（動量與當前梯度），通常取接近於1的值。默認為 0.9

  - 其次，計算梯度平⽅的指數移動平均數，v0 初始化為 0。β2 係數為指數衰減率，控制之前的梯度平⽅的影響情況。類似於 RMSProp 算法，對梯度平⽅進⾏加權均值。默認為 0.999 

  - 由於 m0 初始化為 0，會導致 mt 偏向於 0，尤其在訓練初期階段。所以，此處需要對梯度均值 mt 進⾏偏差糾正，降低偏差對訓練初期的影響。

  - 與 m0 類似，因為 v0 初始化為 0 導致訓練初始階段 vt 偏向 0，對其進⾏糾正
    $$
    \hat m_t = \frac{m_t}{1-\beta_1^t}
    $$

    $$
    \hat v_t = \frac{v_t}{1-\beta_2^t}
    $$

  - 更新參數，初始的學習率 lr 乘以梯度均值與梯度⽅差的平⽅根之比。其中默認學習率lr =0.001, eplison (ε=10^-8)，避免除數變為 0。

  - 對更新的步長計算，能夠從梯度均值及梯度平⽅兩個⾓度進⾏⾃適應地調節，⽽不是直接由當前梯度決定

    ```python
    from keras import optimizers
    model = Sequential()
    model.add(Dense(64, kernel_initializer='uniform', input_shape=(10,)))
    model.add(Activation('softmax’))
    #實例化⼀個優化器對象，然後將它傳入 model.compile() , 可以修改參數
    opt = optimizers.Adam(lr=0.001, epsilon=None, decay=0.0)
    model.compile(loss='mean_squared_error', optimizer=opt) 
    ```

    - lr：float >= 0. 學習率。
    - beta_1：float, 0 < beta < 1. 通常接近於 1。
    - beta_2：float, 0 < beta < 1. 通常接近於 1。
    - epsilon：float >= 0. 模糊因數. 若為 None, 默認為 K.epsilon()。
    - amsgrad：boolean. 是否應⽤此演算法的 AMSGrad 變種，來⾃論⽂ 「On the Convergence of Adam and Beyond」
    - decay：float >= 0. 每次參數更新後學習率衰減值



### 如何選擇優化器

- 隨機梯度下降（SGD）：SGD 指的是 mini batch gradient descent 優點：針對⼤數據集，訓練速度很快。從訓練集樣本中隨機選取⼀個 batch 計算⼀次梯度，更新⼀次模型參數。
  - 缺點：
    對所有參數使⽤相同的學習率。對於稀疏數據或特徵，希望盡快更新⼀些不經常出現的特徵，慢⼀些更新常出現的特徵。所以選擇合適的學習率比較困難。
  - 容易收斂到局部最優 Adam：利⽤梯度的⼀階矩估計和⼆階矩估計動態調節每個參數的學習率。
  - 優點：
    1. 經過偏置校正後，每⼀次迭代都有確定的範圍，使得參數比較平穩。善於處理稀疏梯度和非平穩⽬標。
    2. 對內存需求⼩
    3. 對不同內存計算不同的學習率
- RMSProp：⾃適應調節學習率。對學習率進⾏了約束，適合處理非平穩⽬標和 RNN。
- 如果數據是稀疏的，就⽤⾃適⽤⽅法，如：Adagrad, RMSprop, Adam。
- Adam 就是在 RMSprop 的基礎上加了 bias-correction 和momentum，隨著梯度變的稀疏，Adam 比 RMSprop 效果會好。

### 重要知識點複習

- SGD（Stochastic Gradient Descent）隨機梯度下降法這種⽅法是將數據分成⼀⼩批⼀⼩批的進⾏訓練，但是速度比較慢。
- AdaGrad 採⽤改變學習率的⽅式
- RMSProp 這種⽅法是將 Momentum 與 AdaGrad 部分相結合
- Adam，結合 AdaGrad 和 RMSProp 兩種優化算法的優點。對梯度的⼀階矩估計（First Moment Estimation，即梯度的均值）和⼆階矩估計（SecondMoment Estimation，即梯度的未中⼼化的⽅差）進⾏綜合考慮，計算出更新步長。

### 參考資料

[An overview of gradient descent optimization algorithms](http://ruder.io/optimizing-gradient-descent/index.html)

[An overview of gradient descent optimization algorithms](https://arxiv.org/pdf/1609.04747.pdf)

[TensorFlow中的優化器](https://www.tensorflow.org/api_guides/python/train)

[keras中的優化器](https://keras.io/optimizers/)

[Adam优化器如何选择](https://blog.csdn.net/qq_35860352/article/details/80772142)

## 訓練神經網路的細節與技巧_Validation and overfit

> - 在訓練過程中，加入 Validation set
> - 檢視並了解 overfitting 現象

### 什麼是 Overfitting

過度擬合 (overfitting) 代表

- 訓練集的損失下降的遠比驗證集的損失還來的快
- 驗證集的損失隨訓練時間增長，反⽽上升

![](https://lh3.googleusercontent.com/ZyMV6_I4wI0eKi-E6uBTfIpR60EFGuNbHsMqhMaqno1cooNxN0fTXeVcqlCCwY5Re7RbvAhdKRnqTk6p7TMeSvnN2XuORYEcRBFxMMLg5TyNnrBc_Z_v-gWpC7NDhp_rHK0dcvXAPCD6k5zssWsxCCWaXJEbt2p1mxPV4l-HJ_EKj2uikR7AaQu1NWFOxvK8YpuSERDDJdgI15IsuxevUM6d5H4gW_jWCMQf0FAPLKlYiHXwLl9_sxmjnjf__ce_aLm4ZzaDDFGXvzJM0lEpP5jtDYIIRKvweoISwKwgMjVW8vmEpffZj4Efx9p8w2ZELxdI1PAqR1Ah6b_q70AggX0pu8EoMBbKHxgb9k5ZTiAUXqHNjiBDKiHifgZxF1M26-9jG9fllJIdmKRGM3dREAnlX4h9M31f0t4vruP6Dvqf6bxg_nJ6NXITxXh55Mw3cfIm10PMCfu4xc-lVoKd_-DTXtBCNk-LRyX_axDmb0Ua_NrYTir3C_95ewfH6AabtmVF9SMHC_y9pQVtx9B5C_ILvf5u3OnYYldjlbUsP1ENQlhba3AlMlaVHWROlHTGrQoBG6mttIlZqbw780eKBdA-P6mA670mcASksFY7_fwXqcEvCmgAooEdZfgIT4CXmzVmLHwpjACB41BRI0-Wsr9Sbt_I6W_aegIzFAARVhuYqbdmxS9Vnwq9y1ZJkiK-I_U-XUO_JR1QN34mJIuI6FgU=w528-h364-no)

### 如何檢視我的模型有沒有 overfitting

- 在 Keras 中，加入驗證集

  ```python
  model.fit(x_train, y_train, # 訓練資料
            epochs=EPOCHS, # EPOCHS
            batch_size=BATCH_SIZE, #BATCH_SIZE
            validation_data=(x_valid, y_valid), # 驗證資料
            shuffle=True) # 每epoch後，將資料順序打亂
  
  model.fit(x_train, y_train, # 訓練資料
            epochs=EPOCHS, # EPOCHS
            batch_size=BATCH_SIZE, #BATCH_SIZE
            validation_split=0.9, # 驗證資料
            shuffle=True) # 每epoch後，將資料順序打亂
  ```

  > 注意：使⽤ validation_split 與 shuffle 時，Keras 是先⾃ x_train/y_train 取最
  > 後 (1-x)% 做為驗證集使⽤，再⾏ shuffle

- 在訓練完成後，將 training loss 與 validation loss 取出並繪圖

  ```python
  train_loss = model.history.history['loss']
  valid_loss = model.history.history['val_loss']
  
  train_acc = model.history.history['acc']
  valid_acc = model.history.history['val_acc']
  ```

  ![](https://lh3.googleusercontent.com/aRcKSR_WJasIBDgHERPDSP2Lsj-FYplyPHzdhPhgu2Fu3I6WdAHAsRDk9_n6SwHXD2Koqy66RVuzf4req3PEUWY_ZFwFBoORZy62tkn_FJiuQBtO8BncqGY-SqZ61y3t_oFjzdkqhKWgxVT-wD8jpELgCdmkElw1Unx74ey6U1tGzsKGe1xhICNUfgxv7EwgsRF_ynSw0fcIzEoRc6DhFAvXE3mW315m9elVL5n8eXS5xGVN3roQ3yrnhBxX0BGDtF_GQBt9kvpvQCtwpT_-QAwpdqk39YCXUVsGlLPoabsz55qIkIq-oQbu1b62WQF7bc9aMGVXAa1rhx-a6oQc4Kvwyg3kbnaz09TddTjKke-M36Vu6xkfB4pfT-brCh_t8gwcMaSxbI0Xc_zUvgz1ai3tGVUrDN4fFZLtzqvqw5H2UnvGVs-au6gD6SElpEtGvyJ1aykTkwSYVoq1vbs0d07zKpsIWwO5GdPAJyx6ybUXK3AnWI5Rk8UTZzaEfzjpQkyl4nvqVzBLrTrJyatcWpWsiebw1F87iR37b8daGyXJn2roKGTaEHfo1ak_T2kOTVgHfnbn4czypgd3JWPiCcYj_N64hZ6f3TOVPHnokr-k64HVLZnSgrEqt8P95SK76hNDD74B8f-jpKW6thgjZS7E0ywn5D6hHOWWFh3pnOx2Y0p7Uy3U6UCjNBTjsbbxkPk4qbYyb8JAszwDWinrAIhx=w547-h386-no)

### 重要知識點複習

- Overfitting：訓練⼀個模型時，使⽤過多參數，導致在訓練集上表現極佳，但⾯對驗證/測試集時，會有更⾼的錯誤率。

- 在 Keras 的 model.fit 中，加入 validation split 以檢視模型是否出現過擬合現象。

  ![](https://lh3.googleusercontent.com/wySmre4Z4or2gnibj57qWRWZuo6kaADcnuD-2McBnAg-DuqT5LkPZhYFwL1Mrt6oBOv-WrAE8OOaxav5U_CQ1fKes3l63YoljkduOzXqhNnvhE2kyTc4TUx5hk6MRviqaESPetK865aHpt7sjyAkJ2uyLMSWZc6xmqkTrk-9pFSL1XN0y5jgi8SNOIWX78i5rqCSTiIAlrUoOzMQ2LHBqn0gUpEbADWv7toequIDS6Fhfsthpn1cjZ35MWTIKkpuulPgavdAZTp46EHr9j_GVhyLSTUnRJ1pgoL2tL9ErvYzzq38Y-_KdFpyhHNGu0q31lFuM8t8Dqcly-tiV-TFD9yLzsC77s7jpHNKT_W7r1835hcmTgsaUnSvqoWvEfGxBtljNNE3ntToIiHSg3ERkI3S5FfIvN1SgfyQqJpc_hqARwzkpuIj1JgTB2TnHxBig4H-dqtkcdNGUXV9Why1y7G1VAYN78urhVqXAMXkp1QANAftf-XXEOfGQ789JVbg5cRpa2cmVNUP6skO0f9dXv9y1b9PaQvxPSCZ-6oH0lZhvlMHbpRMYWZJJheLucmSz4TzRe6T9qMEBVRuccOP4hGkRjA97Q1NiGwQWb-D7XMlZ1OQ2DGsYIrvvNWIEKtQT1Z3r4B_PGrV85fZ5e1IWv62uj17mfJ6ThsaVWzgy0XwmVZt6nBMEFZBVGFZ0DJbNjCl6fnf6j0AJDoLCDC8BIPJ=w406-h340-no)

### 參考資料

- [Overfitting – Coursera 日誌](https://medium.com/@ken90242/machine-learning學習日記-coursera篇-week-3-4-the-c05b8ba3b36f)
- [EliteDataScience – Overfitting](https://elitedatascience.com/overfitting-in-machine-learning#signal-vs-noise)
- [Overfitting vs. Underfitting](https://towardsdatascience.com/overfitting-vs-underfitting-a-complete-example-d05dd7e19765)



## 訓練神經網路前的注意事項

> 在開始訓練模型前，檢查各個重要環節

### 訓練模型前的檢查

- 為何要做事前檢查

  訓練模型的時間跟成本都很⼤ (如 GPU quota & 你/妳的⼈⽣)

- 要做哪些檢查

  1. 使⽤的裝置：是使⽤ CPU or GPU / 想要使⽤的 GPU 是否已經被別⼈佔⽤?

     - nvidia-smi 可以看到⽬前可以取得的GPU 裝置使⽤狀態

       > windows作業系統的nvidia-smi在以下路徑，進入該路徑後輸入nvidia-smi即可使用
       >
       > ![](https://lh3.googleusercontent.com/Aih5DxOZU1a-woo1a6_VhqDnX__WD6LDjTYmkpi33vll6Xd-rZbSmkzwShlQD3aQ09uwNZOBReeZDRJzC3aSL283DXMyrJ7sTNq4EL37n67_K_fjpvvkW401dxkHMfmrONMUiHskQ8XOLRu_HbGAiR1Y0AzcluB8yRVV01NmT0jQ_q3I5GFnPEgE9do8-HykIOXJw95aGkrkbO9QSU1hY_5hR8dhHwf_UNtu6sdzBzKY-_vPj9spwC_hTP8Qw3IUBggnIlFw9zju1lBVNJSyICrquamYKsaJyh-iskKKzplILoF6QedfU30SF5m8v1c9A7FzKjslqt4Ct71bxWgLKO-PwDlkT7O_1VcjYUPidKMoo57BzoXHl6HVvEqX163Rlg-YKVVbo9RS55WW4hy1Vy_CoMXJL9d7iepMJNs_VLe8V3yLqxBQe7tvpFI6aOlDsmxP2LtB1zWYt5lul5URHamxeqcJiPD-0gteNAyaGzjjB0TDac3J4YPhajh8W5WYHVdIuu5bpY8joNOe92J8bFxkbvSK5w-kOxCSKY_DAahBLEYSEX_i5oqGHHDGDIPzb4Nuy7mOILMHDJNYEPHuMERoEiYv0Ye0PESUoQI22zodcG7xIUtcwesaU0-19Lbu66sEHeV0D06SvF5r40v47s3Wh-h2N-sguUUMk7UOvXPGYvBcNufXlWFuVkQvnlZqGxOJCvdILERI96cOO3s_tuhW=w653-h423-no)

  2. Input preprocessing：資料 (Xs) 是否有進⾏過適當的標準化?

     - 透過 Function 進⾏處理，⽽非在 Cell 中單獨進⾏避免遺漏、錯置

  3. Output preprocessing：⽬標 (Ys) 是否經過適當的處理?(如 onehotencoded)

     - 透過 Function 進⾏處理，⽽非在 Cell 中單獨進⾏避免遺漏、錯置

  4. Model Graph：模型的架構是否如預期所想?

     - model.summary() 可以看到模型堆疊的架構

  5. 超參數設定(Hyperparameters)：訓練模型的相關參數是否設定得當?

     - 將模型/程式所使⽤到的相關參數集中管理，避免散落在各處

### 參考資料

- [選擇 GPU 裝置與僅使用部分 GPU 的設定方式](https://github.com/vashineyu/slides_and_others/blob/master/tutorial/gpu_usage.pdf)
- [養成良好 Coding Style: Python Coding Style – PEP8](https://www.python.org/dev/peps/pep-0008/)
- [Troubleshooting Deep Neural Network – A Field Guide to Fix your Model](http://josh-tobin.com/assets/pdf/troubleshooting-deep-neural-networks-01-19.pdf)

## 訓練神經網路的細節與技巧_Learning rate effect

> - 了解 Learning Rate 對訓練的影響
> - 了解各優化器內，不同的參數對訓練的影響

### Learning Rate Effect 

如果 Learning rate (LR, alpha) 太⼤，將會導致每步更新時，無法在陡峭的損失⼭⾕中，順利的往下滑動；但若太⼩，則要滑到⾕底的時間過於冗長，且若遇到平原區則無法找到正確的⽅向。

![](https://lh3.googleusercontent.com/PTMBO-EvAdGWmYwCXXJwfog7zX3RKk1Eucro_zCNSdpsP0zL7wvHzJERYZXaAFctNBilQj1OWTDLhxWvIHrdZuyPfQ3DAMMIO9crEa5tGiBs_jf8hG2_CD4-8RoTSBCeyOMH2KfPWNgbOCxW7W6ivmwO4oAKyJc_nLJjA02c1533_ihyEDsH6W9MbO8oTXeY4N1UpIE6nfVGEeKeAWMU8pu0ytFzzVUk2HOOZxSjkq6Cy2qPGQAEelGGbGqGU8NGPU-aCiSGedGnkPr5-3uNqSTgDmScBuF8aS6YrgoHP8pqpVKjYUpNDpj-udf_7VxXBfuGlJb6pkeF0efnf42TOJ9mQZm3nB0uZA0gkzNcoc9oj4abMglGubk3oOEG_I4MaAHoMzJyvZNuIdkqDsWeOdPmJn-NzN_mdrfMzPoRL86x0ijqKv4m52DzlwKZcLMJaRGmvMzErs1-i3ZaDXkxlfRoqbV2G2_H84um4VNC-AVQ7brz-VFTy2wvf8s6K4GuRNkFGDckpffC3OTMsDK5S0Gjzj_wly3YsjN3JnD8lYtvg5OsPXjyVUCJOa_j8o9LnQ9DeUMG-CzrwpNTKhZ_8Ke7LO56-uDh2MQsgEXyehYnVDnzt5vnA7_EjYIoe_hCPo8BAKVsTuc8bE8vXzGRiONgOefRQP749yj5b4pSqKY6uM0gF3sqffvfVB8hfclnOnDRJALkgDWUrb60DJbCEDkU=w1029-h349-no)

### Options in SGD optimizer 

- Momentum：動量 – 在更新⽅向以外，加上⼀個固定向量，使得真實移動⽅向會介於算出來的 gradient step 與 momentum 間。
  
  - Actual step = momentum step + gradient step
  
- Nesterov Momentum：拔草測風向

  - 將 momentum 納入 gradient 的計算

  - Gradient step computation is based on x + momentum

    ![](https://lh3.googleusercontent.com/Y-oEhhWLyFGrfALnvn-9KpR_R23c8pDkE5h2KUxL1eNokjwC_LXAP0QoXW0cLIsi6Slrcc057wH45VU80m441RtY6VOWeBCBJfGnAEeGmonX0u9oBApYfvlUnah0NTpfLGMGW2XJ3vyVCE5nrMBMW_pACUAb24f1cvPdAOpo-IAJgbe4BGqt-cVST8a1FZlb_qYBwzY1Pwt2WrRytVIbfn5e1Z2l_zAtU-8lr4TlbCzRniWAGooX4aSaIAzdplXR4HczqOeAGbsyGMXDbGiTzluk7dJ7o3XF-pJGfQsXJYUdxV1H2NxxvI6CT0pAVUZTRV00VwnyEQd1AkkXzW8JRvdy_f1UOrqz86_fbhufezO927YgvRTyTOyW7NZp6rNEVhRyKJSsMRc3VvjV_As_KU_feCuJvoIaLq0vGWJlX1R6S87DgR6AyLP8D7FOn2Wkd8HyfSToumyOZ33FvgIkYKDkpYKSqdWYcOdUD0OMWSeI1OWDacOXv0_bP2W32nRp5w8ZblukMgEjN_uL3IFS2WS4-2lWH5nDh8i_LtvUdBPp26DR9s71SquD6iQul8RguwXd0HCqxBU2szbU-T2aKtZrfoFJlgKqErTVcZtHeGE7ux3dU3NaqRPqgXVXhBPOiApFkuAt2jzbxbNV1qonAropAYUSBLZwnTR3QV_Pw0OiCqvONY1MBrBxlmuYzsGqNeFH2gTrYIvtzCYMy6coFfsa=w1063-h301-no)

### 重要知識點複習

- 學習率對訓練造成的影響
  - 學習率過⼤：每次模型參數改變過⼤，無法有效收斂到更低的損失平⾯
  - 學習率過⼩：每次參數的改變量⼩，導致
    1. 損失改變的幅度⼩
    2. 平原區域無法找到正確的⽅向
- 在 SGD 中的動量⽅法
  - 在損失⽅向上，加上⼀定比率的動量協助擺脫平原或是⼩⼭⾕

### 參考資料

- [優化器總結 by Joe-Han](https://blog.csdn.net/u010089444/article/details/76725843)

## 優化器與學習率的組合與比較

> - 回顧不同的優化器
> - 回顧學習率對模型訓練的影響

### 參考資料

- [優化器總結 by Joe-Han](https://blog.csdn.net/u010089444/article/details/76725843)



## 訓練神經網路的細節與技巧_Regularization

> - 了解 regularization 的原理
> - 知道如何在 keras 中加入 regularization

### Regularization

- Cost function = Loss + Regularization
- 透過 regularization，可以使的模型的weights 變得比較⼩
- wi 較⼩ 
  ➔ Δxi 對 ŷ 造成的影響(Δ̂ y)較⼩ 
  ➔ 對 input 變化比較不敏感
  ➔ better generalization

```python
from keras.regularizers import l1
input_layer = keras.layers.Input(...)
keras.layers.Dense(units=n_units,
                   activation='relu',
                   kernel_regularizer=l1(0.001))(input_layer)
```

![](https://lh3.googleusercontent.com/6SLWFn1vxOrvvMds-C7SihSsG1dOJcYQJyyq-DhrLKHvpZtnLvF53qxQ_rB5xmO3AnPCM8U1RdmgBTBkkaeZQaf5cY2O1ycp4NsiCpHNP2d0APJn_1LC5dDWckhVzV2DrLsgNpgDTfwLwxdtRUgI9Rr79mOj9HBBsctvLcJs1NAl-OL2DnT0bCsxuLrdRV-cDFd3c906g7ca3z5pIK7N4p0f4bLossiMiFaOGc-x-GUsDYbdGK4K5Pe-cZcTNdHvH3NgDTwzwvF1IdVGNQd2WXkohn_1f1jOSauorkQo45WJgfh0HPap7gZ5nvTs5ZBT2uH48f014HFIIoWIjJiHB2AjxZPraFTPH1zTLjCLAUzn8Fx3H2x6Mijvp0TBSLnDJL7-JWxdj6CO4dVSkCmt7Qro-MTUIxzCLZMhbMpsU6MwpqpIn52CtmAIR9eWmNWsBbdapnk9j8QHfNcZ7My8Emmkv8CBY-xAMeUhEFXSCu7mm8SRc2rGp2Q3ZF2Q6dOjmArHZuOxMr_qu3F3pJW-hBNSpg_xhUhJIdJ9p5cPgaSxISK0Zn2ydhEXb_Rnk7_o7C-XzbfhOHd6nb0WtqsTNI90968DVrLHdGpK6hgAIfbFQxwe-4vQ2_obRn0GPazia1Emw2jpTuUPPMrb513kHvfl6EFizF1a31pHRGwgWtfNcASNDApzluYflAU_IklfGEAFGvnD_sOS8pIAPLcAcydt=w611-h472-no)

### 重要知識點複習

- Regularizer 的效果：讓模型參數的數值較⼩ – 使得 Inputs 的改變不會讓 Outputs 有⼤幅的改變。

### 參考資料

- [Regularization in Machine Learning](https://towardsdatascience.com/regularization-in-machine-learning-76441ddcf99a)
- [Machine Learning Explained：Regularization](http://enhancedatascience.com/2017/07/04/machine-learning-explained-regularization/)
- [機器學習：正規化 by Murphy](https://murphymind.blogspot.com/2017/05/machine.learning.regularization.html)

## 訓練神經網路的細節與技巧_Dropout

> - 了解 dropout 的背景與可能可⾏的原理
> - 知道如何在 keras 中加入 dropout

### Dropout

- 在訓練過程中，在原本全連結的前後兩層 layers ，隨機拿掉⼀些連結(weights 設為 0)
  - 解釋1：增加訓練的難度 – 當你知道你的同伴中有豬隊友時，你會變得要更努⼒學習
  - 解釋2：被視為⼀種 model ⾃⾝的ensemble ⽅法，因為 model 可以有 2^n 種 weights combination

![](https://lh3.googleusercontent.com/vkLYD68AKR-OcpD4tS2tCWvwt-6hZ4qy8MM6RajC2guOV4jWp_2_NtCIhBJRiuU9uDEfCiKweWeolok69vWGSgWruASv4mdzjEMAxrEp6aZ8hXGJQxrySirZOdT00_ol4qWArYMTNtVjCZWJiTyp631G5RSK6iraVun38qxiI8ksOXGsAHePv0iL5UGDZ8GYFx9gp4D-Ys1lRntokQ5x2NORUiFGaiH2cHUILjJadMTac9sCwxu6_z-Ac9O3FOBl7cCgKaPBFFugH6Fd9_lvfb8phe0335-8RR-lZlYd6oSR_wYzM8hd09WWby9iUt-vuPt_5nEac1HSB7zzrJ7Q9cQ8P62GIlMDsZW-dfkmE6sQihnfICkZnpDffzbgFNIpdgvLzTsJBXJjxnMWkle2oT9NF4em8IPOfr-sOeM_MSkVaIFJQrssCFOS2UAQQmIvemgiVNJHURzMxPmkkfYjR1BVLv4P5tRfT9VLLXnkjFhpjIE_qCTV5O4O0WOxJlldwLdYzYBSnRZhULusRD5BP-N9NTG4al82GVOdj4ftJJWxYBrASdbtirq53oShQk17VQPiURdVEHw5rnMFufmVyMXByXwZGdKewCAszDWGJfRTvJcYpX1OF16aGMNjI8JhZ5zzOwqWxtiQ0heGJ5O3JQ90STvVgLfFSaJKlk3mrYW-Afo_9HxzYJet4aq41hcZZcf1Sews-J_nY74jZ9MclN4o=w725-h334-no)

```python
from keras.layers import Dropout
x = keras.layers.Dense(units=n_units,
                       activation='relu')(x)
x = Dropout(0.2)(x)
# 隨機在一次 update 中，忽略 20% 的neurons間的connenction
```

### 重要知識點複習

- Dropout：在訓練時隨機將某些參數暫時設為 0(刻意讓訓練難度提升)，強迫模型的每個參數有更強的泛化能⼒，也讓網路能在更多參數組合的狀態下習得表徵。

### 參考資料

- [理解 Dropout – CSDN](https://blog.csdn.net/stdcoutzyx/article/details/49022443) 
- [Dropout in Deep Learning](https://medium.com/@amarbudhiraja/https-medium-com-amarbudhiraja-learning-less-to-learn-better-dropout-in-deep-machine-learning-74334da4bfc5)
- [Dropout: A Simple Way to Prevent Neural Networks from Overfitting](https://www.cs.toronto.edu/~hinton/absps/JMLRdropout.pdf)



## 訓練神經網路的細節與技巧_Batch normalization

> - 理解 BatchNormalization 的原理
> - 知道如何在 keras 中加入 BatchNorm

### Regularizatioan

- 對於 Input 的數值，前⾯提到建議要 re-scale

  - Weights 修正的路徑比較會在同⼼圓⼭⾕中往下滑

  ![](https://lh3.googleusercontent.com/u4ozWUFeEgyfpSYmG1I1ablnk-E-Y1wAsED9CKgjt6Vb3qNQUb0YngMngoUtBrJlrGf_BnzOAU3EppM9LEsIG8zoszH8zBEmADAio2smH37kc3JsC1vt3bDYiHcU0gCgmoaf2WArBjH8J0TNKJe6zYVG8vpgWi9P_piV2M3vQiXJZkFufNfvDGD0aKGp8q_kLIGzqkgZu6hXGYtAmrRjsbcaKVE27R_AdPbz-zLbxlSmgMW8uy76rjYAXsfvm47OQ5NSS5Mtni_KcBD3RO995G02SA4IJZgjjA7701DJcFSVVSj5UoKUdJxcoTv42XmdM1gl7kO3Ml86x9NPJKcbE9Oo6vhrHP1gOqezvPlaM_Ie1edyK76ynh2yRDEOA72R41NEUJ1lP6UPo5TJO_1IgJU3EIDRcd_1TcHviFbcs41h-CiUsmn3cH3T-1t0FWU1Pe_8TJbkmvFoLgW79NYE_Nc9zSqiIMxrUQEQhmz651IVUH8nHDvnl02Uj74eyslBsTD4N17SClimh5Xn2icfSIvJXAZoeW3stGxGeTQyOE0lWP6co0Fjeksb1jqMGjPkI-56z3vVCuf0_JBql-U9dW7wlqHM5q6x4J4pi2xNaV10mAt4SW0VHXyMOrMeJS1Y3cwwGPx2vQcVIMYDtokXHKDXFcORZ5Iwg6AUlvcQH-i6npaFoaVIrG_fLm8nowA2Qxu3cEr3EfESPJJ4L8AuY_ye=w925-h309-no)

- 只加在輸入層 re-scale 不夠，你可以每⼀層都 re-scale !!

### Batch Normalization

- 每個 input feature 獨立做 normalization
- 利⽤ batch statistics 做 normalization ⽽非整份資料
- 同⼀筆資料在不同的 batch 中會有些微不同
- BN：將輸入經過 t 轉換後輸出
  - 訓練時：使⽤ Batch 的平均值
  - 推論時：使⽤ Moving Average
- 可以解決 Gradient vanishing 的問題
- 可以⽤比較⼤的 learning rate加速訓練
- 取代 dropout & regularizes
  - ⽬前⼤多數的 Deep neural network 都會加

```python
from keras.layers import BatchNormalization
x = keras.layers.Dense(units=n_units,
                       activation='relu')(x)
x = BatchNormalization()(x)
```

### 重要知識點複習

- Batch normalization：除了在 Inputs 做正規化以外，批次正規層讓我們能夠將每⼀層的輸入/輸出做正規化
- 各層的正規化使得 Gradient 消失 (gradient vanish)或爆炸 (explode) 的狀況得以減輕 (但最近有 paper對於這項論點有些不同意)



### 參考資料

- [為何要使用 Batch Normalization – 莫凡 python](https://morvanzhou.github.io/tutorials/machine-learning/ML-intro/3-08-batch-normalization/)
- [Batch normalization 原理與實戰 – 知乎](https://zhuanlan.zhihu.com/p/34879333)
- [Batch Normalization： Accelerating Deep Network Training by Reducing Internal Covariate Shift](https://arxiv.org/pdf/1502.03167.pdf)
- [深度學習基礎系列（九）| Dropout VS Batch Normalization? 是時候放棄Dropout了 深度學習基礎系列（七）| Batch Normalization](https://www.itread01.com/content/1542171910.html)



## 正規化/機移除/批次標準化的組合與比較

> 請結合前⾯的知識與程式碼，比較不同的regularization 的組合對訓練的結果與影響：如 dropout, regularizers, batchnormalization 等

- 練習⽬標
  - 請同學將前三⽇的 Regularization ⽅式加以組合，並觀察對訓練造成的影響
    - Regularizers : 隱藏層內的 L1 / L2 正規化
    - Dropout : 隨機省略神經元輸出的正規化
    - Batch-normalization : 傳遞時神經元橫向平衡的正規化
- 注意事項
  - 與 Day 80 類似，建議同學⾃由發揮，解答只是其中⼀種寫法，僅供參考。

### 參考資料

- [An overview of gradient descent optimization algorithms](http://ruder.io/optimizing-gradient-descent/)
- [優化器總結 by Joe-Han](https://blog.csdn.net/u010089444/article/details/76725843)



## 訓練神經網路的細節與技巧_EarlyStopping

> - 了解如何在 Keras 中加入 callbacks
> - 知道 earlystop 的機制

### EarlyStopping

- 假如能夠早點停下來就好

- 在 Overfitting 前停下，避免 model weights 被搞爛

- 注意：Earlystop 不會使模型得到更好的結果，僅是避免更糟

  ![](https://lh3.googleusercontent.com/-6BtDcbtyb79-m8O_YBqrawpJDfBAZafriKFlMdxm9VVhvtCZE3vb37HZivlfknuQfAM5CTGXEp7NPiFrsNL0kNoSKX1CqUTbrWxNSJucy8HeRwplqnH5IcHE6UFqj2Urblk62LEutx7NvHa3dolFFaXGQgI60e79y5Lp3r-WK2bkKWchbtfQZt1F0U5t0Z83hg8qxPt8SGJtUNSADvLtvVV0T479skMhDv5VZVE2OIwgD7s3J8lnSPKh9LOdKcjcHaE9Gff-ewiK5OoLwZYkiHmOt_vti9EuJV2ryHzV2Etlp0V5sn1MvK7v2F2S96Zr44Lc3Yl2k_IGtyZHzxIc6mVN6TEKIYFS80D7S24_nGmE2JtOVerPQ_nBEaMyXdjkD9CgFaF9pNZtKR4cuDcV1dykEhOZxPLMR2g6C3HAqso2LZ9jdjQyaKRLttfdZ_bFU5V7713-NtdM9pWgHFNilGPQPJenHcD3G7gTvabmJRy6ip9nGi5KDLMfQPmyvVJG7Xl_H2K8OBy46dvwYLqepHpIDRAtF9_N6uzfJiNBVxuABeF68ETU0V-V_UyZQJU4TkypzKnSJXMyw0B_I8Oohcs3DuY4OznyjXiEIi6N6OTNR94FsdDEigrrFBjtyahpl9ZybNFEOVsZBg85ASBBd7v1PJ3cMfxJz5yjoNpaNDecWZtuca0rNceNZ-yfrE6grpI2juaJpVaiMzXIOSCke2i=w589-h428-no)

### EarlyStopping in Keras

```python
from keras.callbacks import EarlyStopping
earlystop = EarlyStopping(monitor='val_loss', # waht to monitor
                          patience=5, # epochs to wait
                          verbose=1) # print information

model.fit(x_train, y_train,
         epochs=EPOCHS,
         batch_size=BATCHIZES,
         validation_data=(x_test, y_test),
         shuffle=Ture,
         callbacks=[earlystop])
```

### 重要知識點複習

- Callbacks function：在訓練過程中，我們可以透過⼀些函式來監控/介入訓練
- Earlystopping：如果⼀個模型會overfitting，那 training 與 validation 的表現只會越差越遠，不如在開始變糟之前就先停下

### 參考資料

- [Keras 的 EarlyStopping callbacks的使用與技巧 – CSND blog](https://blog.csdn.net/silent56_th/article/details/72845912)

## 訓練神經網路的細節與技巧_ModelCheckPoint

> - 了解如何在訓練過程中，保留最佳的模型權重
> - 知道如何在 Keras 中，加入 ModelCheckPoint

### Model Check Point

- 為何要使⽤ Model Check Point?
  - ModelCheckPoint：⾃動將⽬前最佳的模型權重存下

- 假如電腦突然斷線、當機該怎麼辦? 難道我只能重新開始?
  - 假如不幸斷線 : 可以重新⾃最佳的權重開始
  - 假如要做 Inference :可以保證使⽤的是對 monitor metric 最佳的權重

### ModelCheckPoint in Keras

```python
from keras.callbacks import ModelCheckpoint
checkpoint = ModelCheckpoint('model.h5', # path to save
                             monitor = 'val_loss', # target to monitor
                             verbose = 1, # print information
                             save_best_only = True # save best checkpoint)
                             
model.fit(x_train, y_train,
         epochs=EPOCHS,
         batch_size=BATCH_SIZE,
         validation_data=(x_test, y_test),
         shuffle=True,
         callbacks=[checkpoint])
```

### 重要知識點複習

- Model checkpoint：根據狀況隨時將模型存下來，如此可以保證
  - 假如不幸訓練意外中斷，前⾯的功夫不會⽩費。我們可以從最近的⼀次繼續重新開始。
  - 我們可以透過監控 validation loss 來保證所存下來的模型是在 validation set表現最好的⼀個

### 參考資料

- [How to Check-Point Deep Learning Models in Keras](https://machinelearningmastery.com/check-point-deep-learning-models-keras/)
- [ModelCheckpoint – Keras github](https://github.com/keras-team/keras/blob/master/keras/callbacks.py#L633)

## 訓練神經網路的細節與技巧_Reduce Learning Rate

> - 了解什麼是 Reduce Learning Rate
> - 知道如何在 Keras 中，加入 ReduceLearningRate

### Reduce Learning Rate

- Reduce Learning Rate: 隨訓練更新次數，將 Learning rate 逐步減⼩
  - 因為通常損失函數越接近⾕底的位置，開⼝越⼩ – 需要較⼩的Learning rate 才可以再次下降

- 可⾏的調降⽅式
  - 每更新 n 次後，將 Learning rate 做⼀次調降 – schedule decay
  - 當經過幾個 epoch 後，發現 performance 沒有進步 – Reduce on plateau

![](https://lh3.googleusercontent.com/KsS-dlezf2E2VPokBOWBpgw1fYvWC-1Vtvj0rI4Xwv7X4f1zXa-GdQpmg5esDz7xlUIeXN8bAQrDPquMoK64B_6uFnJyM4OryKbiLWhh-uQ_KpHiA2wq-s-jxAjfNkiWJae2j-FlIU0SwD7xkM33iQ9hjm5rFqrZHqI3yWxbv6ACkucG6c7vGleBRuvCM5pLWOpLtRTbHYd8B0B81LfnAaMzlgfuTlbLxbqHnFOQMhZDbvOK8BaqOMAfIAOTIPSM6tV4HONZJCnfKOWA6GvAeWo_WLIv9nDoLHgLIo0I7m9tOMQz-FlluWBq9LrmmumU67eqwSdWP-5-Yexl40dDNbeJJ0kDrNpeC2-FWoeAjgiBe7K2A_YtcT76q7DbmMC41wh3U05KI9S71IW5TgmzxJkwLr7-2-ckKfSxaU5qAh68sqSKnJ4tTyVJAG5Dv69m6LopX761cYsvFOTvspnLZeaisKEnJOciwv92xqOozHwNHfpRlf8x9h6g9VHjSc-MCGaMBqPCJafNM_wecvBQyXMBbL4IryuKxHDn8XgYIUKjYMGU6tuB_uBlJRaJbNKpxdrczwXuxNB5NbLfIEfB8qCYYRvh_VAwjsyrgdLUe3SySYnBxOCm8nSdswAQgDa1PiXbVlAcJ4HF8MuoNlH8xXNVOzHEow1GsegTxzODlA7Lg-t7WPNmFjCVI5FueNjHXoXsLNiO0ZqA-IdjCaLL5Wo_=w976-h323-no)

### Reduce Learning Rate on Plateau in Keras

```python
from keras.callbacks import ReduceLROnPlateau
reduce_lr = ReduceLROnPlateau(factor=0.5,
                              min_lr=1e-12,
                              monitor='val_loss',
                              patience=5,
                              verbose=1)

model.fit(x_train, y_train,
          epochs=EPOCHS,
          batch_size=BATCH_SIZE,
          validation_data=(x_test, y_test),
          shuffle=True,
          callbacks=[reduce_lr])
```

### 重要知識點複習

- Reduce learning rate on plateau：模型沒辦法進步的可能是因為學習率太⼤導致每次改變量太⼤⽽無法落入較低的損失平⾯，透過適度的降低，就有機會得到更好的結果
- 因為我們可以透過這樣的監控機制，初始的 Learning rate 可以調得比較⾼，讓訓練過程與 callback 來做適當的 learning rate 調降。

### 參考資料

- [Learning Rate Schedules and Adaptive Learning Rate Methods for Deep Learning](https://towardsdatascience.com/learning-rate-schedules-and-adaptive-learning-rate-methods-for-deep-learning-2c8f433990d1) 
- [Why Reduce Learning Rate so quickly WILL fail](https://stats.stackexchange.com/questions/282544/why-does-reducing-the-learning-rate-quickly-reduce-the-error)
- [Keras source code of reduce-learning-rate](https://github.com/keras-team/keras/blob/master/keras/callbacks.py#L906) 

## 訓練神經網路的細節與技巧_撰寫自己的callbacks 函數

> - 學會如何使⽤⾃定義的 callbacks
> - 知道 callbacks 的啟動時機

### Callbacks

- Callback 在訓練時的呼叫時機
  - on_train_begin：在訓練最開始時
  - on_train_end：在訓練結束時
  - on_batch_begin：在每個 batch 開始時
  - on_batch_end：在每個 batch 結束時
  - on_epoch_begin：在每個 epoch 開始時
  - on_epoch_end：在每個 epoch 結束時

- 前幾天所提到的 Callbacks， 都是在 epoch end 時啟動 (也是最常使⽤的狀況)

### Custom callbacks in Keras

- 在 Keras 中，僅需要實作你想要啟動的部分即可
- 舉例來說，假如你想要每個 batch都記錄 loss 的話

```python
from keras.callbacks import Callback
class My_Callback(Callback):
    def on_train_begin(self, logs={}):
        return
    
    def on_train_end(self, logs={}):
        return
    
    def on_epoch_begin(self, log={}):
        return
    
    def on_epoch_end(self, log={}):
        return
    
    def on_batch_begin(self, batch, logs{}):
        return
    
    def on_batch_end(self, batch, logs={}):
        return
```

### 重要知識點複習

- Callbacks 可以在模型訓練的過程中，進⾏監控或介入。Callbacks 的時機包含
  - on_training_begin
  - on_epoch_begin
  - on_batch_begin
  - on_batch_end
  - on_epoch_end
  - on_training_end

### 參考資料

- [Keras 中保留 f1-score 最高的模型 – 知乎](https://zhuanlan.zhihu.com/p/51356820)
- [How easy is making custom Keras Callbacks](https://medium.com/@upu1994/how-easy-is-making-custom-keras-callbacks-c771091602da)
- [Keras Callbacks — Monitor and Improve Your Deep Learning](https://medium.com/singlestone/keras-callbacks-monitor-and-improve-your-deep-learning-205a8a27e91c)



## 訓練神經網路的細節與技巧_撰寫自己的 Loss function

> 學會如何使⽤⾃定義的損失函數

### Custom loss function

- 在 Keras 中，除了使⽤官⽅提供的 Loss function 外，亦可以⾃⾏定義/修改 loss function

- 所定義的函數
  - 最內層函式的參數輸入須根據 output tensor ⽽定，舉例來說，在分類模型中需要有 y_true, y_pred
  - 需要使⽤ tensor operations – 即在 tensor 上運算⽽非在 numpy array上進⾏運算
  - 回傳的結果是⼀個 tensor



### Custom loss function in Keras

```python
import keras.backend as K
def dice_coef(y_true, y_pred, smooth):
    # 皆須使⽤ tensor operations
    y_pred = y_pred >= 0.5
    y_true_f = K.flatten(y_true)
    y_pred_f = K.flatten(y_pred)
    intersection = K.sum(y_true_f * y_pred_f)
    
    # 最內層的函式 – 在分類問題中，只能有y_true 與 y_pred，其他調控參數應⾄於外層函式
    return (2. * intersection + smooth) / (K.sum(y_true_f) = K.sum(y_pred_f) + smooth)

# 輸出為 Tensor
def dice_loss(smooth, thresh):
    def dice(y_true, y_pred):
        return -dice_coef(t_true, y_pred, smooth, thresh)
    return dice
```

### 重要知識點複習

- 在 Keras 中，我們可以⾃⾏定義函式來進⾏損失的運算。⼀個損失函數必須
  - 有 y_true 與 y_pred 兩個輸入
  - 必須可以微分
  - 必須使⽤ tensor operation，也就是在 tensor 的狀態下，進⾏運算。如K.sum …

### 參考資料

- [如何自定義損失函數 – CSDN blog](https://blog.csdn.net/A_a_ron/article/details/79050204)
- [How to use a custom objective function for a model – keras github](https://github.com/keras-team/keras/issues/369)
- [Issue for load model if using custom loss function – keras issue](https://github.com/keras-team/keras/issues/3977)

## 傳統電腦視覺與影像辨識

> - 了解⽤傳統電腦來做影像辨識的過程
> - 如何⽤顏⾊直⽅圖提取圖片的顏⾊特徵

- 影像辨識的傳統⽅法是特徵描述及檢測，需要辦法把影像像素量化為特徵（特徵⼯程），然後把特徵丟給我們之前學過的機器學習算法來做分類或回歸。

![](https://lh3.googleusercontent.com/obqRVJFHA5BjE2xl8veo5Ctmjw8NHmekXAUyRy-uJVPEYtaYDFTekBzvuqdfH06VdvIOSv_d4pqBPiN3oe5_sopPH2eZ6QLCbdUZ4Yfc_pKuObFRzXugdXKnGQelbr-4i2_baD6p8xOWziehQjZty6zaMZAh5JVdCqKA3K2uxMtXKc0qX3NVK46Xq5Xh5YCkr21j6dUdbsE0PVSWBUlyxy1Boz3v6_NXUc36IBbsQumtdYH1ttv8o_1Fr7D13pugYNpb1ge8pXl2nAcLdSwlCxhuKgJYFTZAP5YeNq_wiRCj4uMwf__Ex0IR6PoWXk2QpnChaKfXXyB7lGHgVnn_k1t9VE-oW8bimk2FaRe5tF8kj902RiJA4KQJ2xv3oxPi8fXYSWHQXlyE2kNTKFx90mvLlwgUs8KJSUx9ZNL1YUAO6Dyb9cB6stCm1D0K342zQrFh3t8GM-lsDdmMkLjFfrici4w02u8nWiXrnjKYaHF7vCrem5RsvpVh4Kg5gUfOaHoTpHVV6BVCqw8bd4MTMtDqpY-0HnMFT8_CLGHfBitP6M57iN0RALSD9XblGl7X-LDaP40oMhsw7edzoLX5Kr3kR99D-VAQ3BIjHSsI2lH-bErzeYrVE2_hUG66gZRX9JqSwqeoYtkboZRZ_-7y4Rlze6DoNbG_xYXtX8IVABwk3_M8B-PW4vBt7U3EOZnHKmUNH2vtpdeGgAjj-EbjH4OQ=w1013-h226-no)

### 傳統電腦視覺提取特徵的⽅法

- 為了有更直觀的理解，這裡介紹⼀種最簡單提取特徵的⽅法

  - 如何描述顏⾊？
  - 顏⾊直⽅圖

- 顏⾊直⽅圖是將顏⾊信息轉化為特徵⼀種⽅法，將顏⾊值 RGB 轉為直⽅圖值，來描述⾊彩和強度的分佈情況。舉例來說，⼀張彩⾊圖有 3 個channel， RGB，顏⾊值都介於 0-255 之間，最⼩可以去統計每個像素值出現在圖片的數量，也可以是⼀個區間如 (0 - 15)、(16 - 31)、...、(240 -255)。可表⽰如下圖

  ![](https://lh3.googleusercontent.com/jA_qqEPxxF2wmpYIlWpgg2t3eJpFgjaqfUABumhFitJXkNHbGMrZx4EDr3Muk9LNUej5JFxsNa9AXsgURxjnCRY80_qJsqg6YTKg1SjIPC4p9RnSHtyu8OpBJuw9KJiO7uhLTUtx5zGrMs37LgAMvGI6QuCeKgnkWBusUspqS6isUMdWia4hYoQ-IAwYoqh7IK1RpYFQtoZ8vR_xxMTMh34mTGUz0r39y1EE2gU4aFu0bkGnbcvTkCTauVDJM1VKhcq6As8nwV--SYa7sqPWqmq7apdgVQhrq_JVJVhPEQQqqAU5DtZ3s2OpvGXOdbXp0Q8kFs71WSxFHZqRY6Q73FNUCTfvzM0pPMvhDuqM2nQfoMVwRXfI-uUhcFIhLRgHErG4y65FOuZk_Mu6uR5Eyg_MZV1Kc-GjHSTBUB0HsGzBBw01FGIdW1N8yTDfr5bJVPAq3bbmThP35SQmJBFjWlHFhWklwi0q-LxVJD8yiCkUQwTvRbHWdmY9EdFXmsA8_NTfeKWYNmik39wZiljHDQs49KwZ9DAhsqR8P5AWmG9nW1Kn8x_z8DZgl-ibiIpd3S_hNx2JTQEKNBzsU1NzlzXp70iXmt0CGLlmf_lk-2HqByuS7D37GAdaQ-rJqPXhyXT_dAf4wxM-5jyZGFBWGBVhvQ5Z9zj5yKYZf8aOZSNlVIHS4hbLt8WT7RE67kHOw9rJdHQe4jJb6KMT7XsZ8OhN=w623-h485-no)

### 重要知識點複習

- 傳統影像視覺描述特徵的⽅法是⼀個非常「⼿⼯」的過程， 可以想像本⽇知識點提到的顏⾊直⽅圖在要辨認顏⾊的場景就會非常有⽤
- 但可能就不適合⽤來做邊緣檢測的任務，因為從顏⾊的分佈沒有考量到空間上的信息。
- 不同的任務，我們就要想辦法針對性地設計特徵來進⾏後續影像辨識的任務。

### 參考資料

1. [圖像分類 | 深度學習PK傳統機器學習](https://cloud.tencent.com/developer/article/1111702)
2. [OpenCV - 直方圖](https://chtseng.wordpress.com/2016/12/05/opencv-histograms直方圖/)
3. [OpenCV 教學文檔](https://docs.opencv.org/3.0-beta/doc/py_tutorials/py_tutorials.html)
4. [Udacity free course: Introduction To Computer Vision](https://www.udacity.com/course/introduction-to-computer-vision--ud810)

## 傳統電腦視覺與影像辨識_HOG

> - 體驗使⽤不同特徵來做機器學習分類問題的差別
> - 知道 hog 的調⽤⽅式
> - 知道 svm 在 opencv 的調⽤⽅式

- 嘗試比較⽤ color histogram 和 HOG 特徵分類 cifar10 準確度各在 training 和 testing data 的差別

### 重要知識點複習

- 靠⼈⼯設計的特徵在簡單的任務上也許是堪⽤，但複雜的情況，比如說分類的類別多起來，就能明顯感覺到這些特徵的不⾜之處
- 體會這⼀點能更幫助理解接下來的卷積神經網路的意義。

### 參考資料

1. [Sobel 運算子 wiki](https://zh.wikipedia.org/wiki/索貝爾算子)
2. [基於傳統圖像處理的目標檢測與識別(HOG+SVM附代碼)](https://www.cnblogs.com/zyly/p/9651261.html)
3. [知乎 - 什麼是 SVM](https://www.zhihu.com/question/21094489)
4. 程式碼範例的來源，裡面用的是 mnist 來跑[https://github.com/opencv/opencv/blob/master/samples/python/tutorial_code/ml/py_svm_opencv/hogsvm.py]
   - [範例來源裡使用的 digit.png 檔案位置](https://raw.githubusercontent.com/opencv/opencv/master/samples/data/digits.png)
5. [支持向量机(SVM)是什么意思？](https://www.zhihu.com/question/21094489)

6. [第十八节、基于传统图像处理的目标检测与识别(HOG+SVM附代码)](https://www.cnblogs.com/zyly/p/9651261.html)



# 深度學習應用卷積神經網路

卷積神經網路(CNN)常用於影像辨識的各種應用，譬如醫療影像與晶片瑕疵檢測

## 卷積神經網路 (Convolution Neural Network, CNN) 簡介

> - 了解甚麼是卷積神經網路 (Convolution Neural Network)
> - 了解甚麼是卷積 (Convolution) 的原理

### 卷積神經網路的強大

CNN 模型能夠輕易辨別這兩隻狗的品種

![](https://lh3.googleusercontent.com/PB1LpVR3fz5zoyhhvN0Sb5Y8-z6VvNiQonj54UpwnHE3_NQAFzB4X-t_BpbNL_ArUb8n6MYKVT2TB9aTfi3vAukqYtmt1I_y6GT02bb-SN9SV-eENIkD8zXdIuof6JRmc2_bn9Swg95gM-EkvPZ0-jQ8nY_vobZQ4uaYYjWpVIGL9yycEcT9iVbs3mpz1sbYD4eCooMIZeTS8m1SyslqtvNRWC4dj74rzN4ftG61fYhTRC6Au7iKfwfVyxqkO3NDMFhngdR0GMe6qkg6_Efu9NwoRTnEJ9VdHUOIGGgSC34iRr3BEwN7sa9LZyobqXelrZ9RZPGkETsdHPlxA8mtodJQCCZAGQWU3JFzbHgDq1F6Nj86zdhkkgBCPAmp_Wvz6LQDhZtMUv-_4P_fm9ng8v6mlcKP9F3l8vLLQCRDnSAnB7jg0qcG_kVVMiz_qTyZeuJcSEkRLKvEYtpqES9w741O1yr6PKFyL6qk05pZifAMIenxQBiTRaX2nhaNa48iGqxAwVuR8JYZZ-yBaEDl_ZTX--pi-7hvp2TP_iLtHPYaWrbLKVPBcr0VZD9Ve0d0v_G8MujXWsInya8ZXbq0_UVSlMNBzj70LbN2dtTNraldHs1x3Gz0_XIf-OStRysIcVjSmzeq6m80eOcz2QpiyzQtZjeTzl7bjwQ5M4vutRvmhcFya6p_d_LF7-MheJgBy6l4XGgrWDD-GKqOY6Y2wkhK=w995-h432-no)



### CNN 在圖像辨識競賽中超越⼈類表現

ImageNet Challenge 是電腦視覺的競賽，需要對影像進⾏ 1000 個類別的預測，在 CNN 出現後⾸次有超越⼈類準確率的模型

![](https://lh3.googleusercontent.com/AKEXo44HiPQSSegThAi-zlP8IY9ZtLFWnJ34ta1KN8h2KvKy0E7MsnNlUTBBo4I0kVdaAVONSD6-pBNRZ8fwF3T6Vto1ReObC0tA1tV_CPphZFhj8MViSEHDcTEJ_7s8th6t22_jNwGiOTsDw1igzojJpvVWh3U3zS8fIQVR7vI_OqnZJ5IxWjKlswuSTTLzYSg_MgqP1bnpsBvJxHD_Y6mSQU3pq4SKz2fbnfQsMlTdPvosXQm_xju-SDUAuNeoTMQCw8gUlxu8cPR-oI09iNy-M4-fNtO8SR8LHF2A5jBaBmbNO4uqDs1gx5E-lj-evjDJ7IEN9Pk-pXB7XNMMAmkj712N56Oqt4HScwoB8f4yEs16CeO9KrpI0kLKQBTN5NqH1omLBb09fBwyr12ANcBoPizXAcwi5D28N3-YGaysW-A8HT_caxWBAvvPR9eHRfQ9JGBN27qAGwcCbx3Yv5wPdwh6vRYyP8VXCDjtumyfcc93DpZ1x6V9RYpXz4Pur18k91cHCmjJrfLTikQloy6luHDcYcrqUOyIP-wEehePa3DTBWI9wk9h-3rQjUn81zo9kvtmvnrkWJZTH8IDfFZBQEwbF3UJETkK2J8fVBZmuiwKTVp3iWM97FIe8hEBQ4AhlIqxnhNjzcGHbM91un7DSjwXb_8fkZUpuZqdO3cm0UEdo1E9ig6hB0rx6-_MbB2gO_lhmpdatU3TLfc4wN4j=w543-h343-no)

### 卷積是甚麼？

- 卷積其實只是簡單的數學乘法與加法
- 利⽤濾波器 (filter) 對圖像做卷積來找尋規則
- 下圖的濾波器是⼀個斜直線，可⽤來搜尋圖像上具有斜直線的區域

![](https://lh3.googleusercontent.com/7hV42k3RMTEUNyZSC1CSfXpo6hAz6lBds2X38nCnSxgJHnWZUhedUkg82mkbgtcArguG9S9iVEtnK2tJmDXb7rO25G4ibyvw_XfKAdqQMQCosGbQYS1B0UlDSLN8eIhCSrV6pY3TvnqmkTVNbiwSKa6L4XHUKZOY-BdWkqpHtydi09NIwtfTu8CFNcvKr6FWCYm46D-P4NcizrPgd8wtTAAJtldG2RiY7FZsPP9Tay5H6yHqqPhzkWy3Vz2Y7hyOBwRgAvxTS68fsnRfR53fI0F18m1AfnyacUFqNQQxWkdLK_oiLqkwg_fl5ELoWViE1xNyD1vG47qUnkgNCJQz5KKSIHwnhMsqvkZaS67T98xuIsGjKG0YvCgVeZ6HEPihioYspvRkyFQezcAEF4pR5-veWBQGplwlBfvm8yr73PttpZA51DIzNMB6572cLBiYt8OtDOOzESLTsrjwtbAg0Ba2r3Kn4oTZYFcMEl31Klhkkc-YfjqOWCH8r_1QT-mBJZ0VaiEIodprSjDvIErW8mQLsKiHByNvxe3eGVVuaUjGlzVHGE15Gb9l2aBxoaTdg4WFWABFfciYZDAy_yegRLxOwNctBvIRW2bh3hhuW4lFVUY-60w7_iz12_NIO5lPIi-SVARiXUsqqICnuS2G7FFng_K2QT9ChIOGeZoQqYoBwy9_OEEl7QitZkimhemyDirK9arXqmHkeyHBq7fqUjZp=w896-h389-no)

- 紅⾊的數字就是濾波器 (filter)，可以看到是⼀個 2x2 的矩陣(值為 [[0, 1], [1, 2]])

- 卷積是將影像與 filter 的值相乘後再進⾏加總，即可得到特徵圖(Feature Map)

  ![](https://lh3.googleusercontent.com/wDvPQf4hn-vQumTV0R03llmPObn3vRswzuMJiFKr-lQgaW8os1mJuF-HQY4OSwTB0Bx3qbk0GswsRdGASaEGA75XWHYcs_7xdBXv0g5i_O67tRD9gpEoJT8ctxhGGakfuuT3EPFgyiLXskNTs1I3dLRI_iamqfIVi_Okam97qBgYeY_aQT1D7mGHkDnbKI7PbFxoRYYq5LIcew8USRLI5SzATrAKhha77RoUj5vBK2QykouQjhbC0ktxthihhwjnr_hfazGoH8N8ISfqfTlXVluQPSy8XkXrQFDoTu0vifZsehNCBv4wGvJoOgROxWnNe_kRZnkRU4Va1p88MBUFpSelaVJlsdFD56ciItLCYMYhR0asX8HSQcSEw74rSTxFuukKQ95y2uWS59a2SoPB1xYCVGcWxHswi_DyPuQ-zxjAdnaELLf1WLqSTFA4uKi_3fQPh-rYKThmc1j3p7QmLYHbuxkCUo20_5jRWenJjIG9w-E4J1md7doB64WDmtq8B4_lG46lpi19lv6Fkl3evx3NpuZIIEbOGygOyaao7BucASVKaa_jIaJ7Lx96H-xSEKfyj3ZfaM-8IgxyLKwZPbyn6cb523C3oBiTqCGpvdLbT7Sn63Zoa7qMANfKgAeBOIhYw20luGR1-QhuC3VscNpT1j7zJXpIbWOQHOjnQ6uE-3c8fQyrRrxRhSkdUch0O4ozes2NJY-Xk5Xy7kQoWaeC=w783-h346-no)

- 透過卷積，我們可以找出圖像上與濾波器具有相同特徵的區域

- 下圖可以看出，兩個不同濾波器(filter) 得到的特徵圖 (Feature map) 也不相同

  ![](https://lh3.googleusercontent.com/jDdKuAdis-N9WpvHChgR-7c5SvkEzxAg8sZC7pAxLDGuCmJYt1V2rr-QZ33RYwNVqnNIsRwSt-ekVaSiW1nPKEe88AHBzX66sl3W3UvcaN3fusPTE1JnW2c1ocyQObbt7pGpybpnE-yaL2xz0kg40_Ui-yZnN5nX3JQKmJj6PxvYNJ_IYF-eoDWX0ZXn8u7up0K7aD1AYhia7y2DBoH0qXiLOSAXCRxCxr2xJQhndEOd-7S8co1lBa_NvANDhVuFdmW5lfStCFop1uq1mDr2ioAP1r2R_uqtX8J5Jyo0kqte00_8VlvJ-fVazut4JmYGvlBj9SQCM6u_hHB4SJwGnP1mlBnPehScx1f7ltYbt_kTBVXu-D6Ku8W8ErasCpi_AGfobDYOUk1bbgSs-VY9lo3Hmisa4HRvVn1kEy0WJDLti_FxaWO81Wv2PU_UsSpHMzxRiEPbwoCcVJkMNaRBVKPTRcaytxlErGWSm9HtiLgO7ryT_JAKhg3e17Oi-JSzol_Skwx-MyITnZP9w4I-_ako3mjbEXOIlI_BUTmeaL0yPzTpFeY2Mvz8cwm5eQmgQwxU-vf7YA57ht2VCy0KuOkx5T425DpKTSnPzPDdhFfJgEy20TDDueGmbv9qhINGEfMgyARJAUvEOH2gQ2dslKrcnBbH61Zas8qWuXWI2DKg6StuxeQFZtT4KLlEKvC-amNeFpkLxPxO4Uw4JAA0n4R5=w902-h399-no)

### 濾波器 (filter)

- 我們已經了解濾波器是⽤來找圖像上是否有同樣特徵
- 那濾波器 (filter) 中的數字是怎麼得來的呢?
- 其實是透過資料學習⽽來的! 這也就是 CNN 模型中的參數 (或叫權重weights)
- CNN 會⾃動從訓練資料中學習出適合的濾波器來完成你的任務 (分類、偵測等)

### 濾波器 (Filter) 視覺化

- 透過⼀層⼜⼀層的神經網路疊加，可以看到底層的濾波器再找線條與顏⾊的特徵，中層則是輪廓與形狀 (輪胎)，⾼層的則是相對完整的特徵 (如⾞窗、後照鏡等)

  ![](https://lh3.googleusercontent.com/TL-Uar2ZnG9ksRMP1ujhOzs4A8nM0vVaNypSOdcg9N-Ktc2N2gZa8M79gkcTM95bnOmYrsw3MMCi50bTP4U2dVcISYVlofD_AuXAEEEBk3NHzjLS15kyHLI9IMe4BwDf2_gea0eBYfavSs4aAsIY14f6iQ6cmQ9EGxxUVB6REbUrQAklxkzUnIUeMxjbUpFKF4BmHWIudo1y6fi2TJ39oNQphWg8lz6WXY9ymKkoG7p2iJewOR5SX7kh-qDUjGC4UXXkA4HcOoreX4Ik7VQmVPO6b2o07TbhyA_j8tmNH1GvxJQUZLkDaAHVPUBGRtmssZZ5kc0t5wQOfalFqseGIAmEI_q8dAWjH848NkhtqthMhymzGZRprtxiRwh-pV6iJp0yaq3LNywhjTHzJCTu9uAv6j6abgbbHDkjLEZVEqlaMpp9DlO10ze1wpQUVSjwIgCB4OUc5YE4Gw0NZ7QKD5Az-VGIy9zOS389SvH9MoGM2ssMlcqPgeLo3Sd-ac_eL3SuBI6BMeuOzY_6bkqt5yJcxEAJZeWcNAF4AgJLabU_UzXwjEHbrxTb6jF6_ai0-cB7GXTdkZJtbUQClxiKvgyMSWB3XmDBhUJj74-03mCve60pfbpze4_BpV4erdOG3bVfxz7ArudyNUdAUTV6GP8hJCJxTL5eVy-tBakYizy3iNoM_WLFTTaG3fhGEjWEeSmp_9ZsNHUbPYyPV14B8EsE=w667-h432-no)

### 重要知識點複習

- 卷積神經網路⽬前在多數電腦視覺的任務中，都有優於⼈類的表現
- 卷積是透過濾波器尋找圖型中特徵的⼀種數學運算
- 卷積神經網路中的濾波器數值是從資料中⾃動學習出來的

### 參考資料

- [ILSVRC 歷屆的深度學習模型]([https://chtseng.wordpress.com/2017/11/20/ilsvrc-%E6%AD%B7%E5%B1%86%E7%9A%84%E6%B7%B1%E5%BA%A6%E5%AD%B8%E7%BF%92%E6%A8%A1%E5%9E%8B/](https://chtseng.wordpress.com/2017/11/20/ilsvrc-歷屆的深度學習模型/))
- [卷積神經網路原理 - 中文](https://brohrer.mcknote.com/zh-Hant/how_machine_learning_works/how_convolutional_neural_networks_work.html)
- [CNN for beginner’s guide](https://adeshpande3.github.io/A-Beginner's-Guide-To-Understanding-Convolutional-Neural-Networks/)



## 卷積神經網路架構細節

> - 介紹 CNN
> - 說明 CNN 為何適⽤於 Image 處理
> - 卷積層中的捲積過程是如何計算的？為什麼卷積核是有效的？

### 深度神經網路的特例–CNN（卷積網路）

- 傳統的DNN（即Deep neural network）最⼤問題在於它會忽略資料的形狀。
  - 例如，輸入影像的資料時，該 data 通常包含了⽔平、垂直、color channel 等三維資訊，但傳統 DNN 的輸入處理必須是平⾯的、也就是須⼀維的資料。
  - ⼀些重要的空間資料，只有在三維形狀中才能保留下來。
    - RGB 不同的 channel 之間也可能具有某些關連性、⽽遠近不同的像素彼此也應具有不同的關聯性
- Deep learning 中的 CNN 較傳統的 DNN 多了 Convolutional（卷積）及池化（Pooling） 兩層 layer，⽤以維持形狀資訊並且避免參數⼤幅增加。
- Convolution 原理是透過⼀個指定尺⼨的 window，由上⽽下依序滑動取得圖像中各局部特徵作為下⼀層的輸入，這個 sliding window 在 CNN 中稱為稱為 Convolution kernel (卷積內核)
- 利⽤此⽅式來取得圖像中各局部的區域加總計算後，透過 ReLU activation function 輸出為特徵值再提供給下⼀層使⽤

### 卷積網路的組成

- Convolution Layer 卷積層

- Pooling Layer 池化層

- Flatten Layer 平坦層

- Fully connection Layer 全連接層

  ![](https://lh3.googleusercontent.com/qzEb-nbGYufLKkRCC9Qbep2gf19FUfFe4AyEoyheoo8B_XFiPLnMOhvCch9l0fAXjRTuTQ1-4Eq1Cmg8D86JFsxiizMvostFdeYPFnn9p1nLHZWPxV_6QvaXExOXbIgQ-26wpgsj7Q_VOH0-B0SW1Bot4evlcjedIcurhXPyxpLSMLn6ZVSfCu8kFjK0ZHnIGMakpsrtP8Ce7B3bbEpZNVF3N7ev6-nNLpD1pRAcU_kQnJ52PGBfPXUHYBC7mwYYdMOXBS87KyFPGfjIHkseAHu7bxHHYCXEKDDMDWPQmB1UT0e9p-hvS2M8Fn0l5R8hj73VZu9hug85Ze0dGk8ptqiGNmjnap6bRqJORp3CzciWjWBXHY2x7zCMvUZaZip3iMhb814U18XNjz30GgF0bsVMIyUwBtWPsaM64KOVN2YlCTaRRcreIAfUi3T6z2kEn9YGHmOkSRJkhNJdvhCgqpq_hgACQCBCdwGLm5Z1YdTnV4mowXPyOxVB3jRHQBL6hBEiSgjC5EHkdhqazl_XVUGFTrldJMHJrYDOWpyPvY3aqkQYgygVs3O-5trpg8k1FFpxdzO0iqKUvdWL0aqSQTUV036eTs5fYJkxFDaedAelCzv-lcq3X6S2u3J12rDjFlghDhmmgggImJ5OrXMVA0wTt1660hU7EIAc8hGmaD34kWBx91bNeFPeda5l4d4BBNwHZa9bVdnAQVhWSRDEE2ff=w987-h288-no)

### 卷積如何做

![](https://lh3.googleusercontent.com/drvULzuFu6GPIrUM-Y40spO1zI2SBFJuq-bMCmMzAffHSirXtmPSxom4N3cBOwqNx8ebFSoYeoqsLgeb8G56WtnQ8QOSX_sNimWHZhwnfJPt_yBEEH_hMEmg35kTwp4eermyfSupIuwtfI3RS1D0qbl-DHfNGQz1VRHa0YIkLRkn4op0ea6L-_aLvsWZN8owMTaIabUtpV3O4ZZOHFMjFcItztRq4z2lI53YhWRYWWI7UnF5P8PP_UJ_PhC_EaBHn82UAldpP5m8RWnvZLFLMMPMZahGkLnxFyLdrE0npKUTVlZAny4aaaAxX8gRVExOfF3MhKf3cpVppwi8YkVWlfrNGDYgTUU2qW0Mqhw0h0W4sjoMEqP5pbvK1ey6YSSFqSWZ5Cs0964kWxNB3njxjXhBXm-wnUPPIHQvRVDUwkvmaLmE-tMLci-ZyLuYc0-Wm8M8he19mQfJW2NQssg4pI0luYV11pjka3hewx808MPZ7xFyKiPvMUGdBbOisWlgR7WA8nwG6vnzm1hU9X8oJudJ1_JUkkVqho2cjmzE0IQhufdJ_tZW5iuF_th58GpTZOgMaegjO5UzTTxBTRV2GKLKeGOolvqs7UeLA5aebzCS0_cbWcL2Fzf--ZXxDUlMTmYVRjjxH2C614NpYQKk2qLoPsUrdJYlHOH6mCWBuuZ5LmoaEOd1lkYkwYdAa9xdaTcoGB6ZAzXE6n3BvhDDxeap=w910-h341-no)

![](https://lh3.googleusercontent.com/tmLoLUsX-x_RyWoUd2g4-ho2lhGxjn5cKYd09jq-gLzYSswkP0ctdZvekMhLNgHykJ2wrBHZ5OOskTPJrnxMKyjHWm_e654zS1R8nR6JrPUnFmYRcyA760XU00B8RRPiWol3T6F6h0xaMI1aYH81mL4yq0rrddIuNlVxOoXustYwavYNlklkhvrE5SgbWYuT80GWcoJItij42boY3NAHj--j8LZzeEl-kvVaqUcORc0fNmw4TyUae6-VS44DYRt7BZrQH50RmFBC3tmJpNegNqgzlmFDta7CgppZPf5vVTnACIkDrs9y90xGsf2gHxjFNt2gdU7oCWy7z-pYpCI6vEldjrjvKd2j23TMkyi1ipVwhOpNcVue0w9oca9Chu10RBuvDXjN4GQuWXTrbHb79rTjfeYv9Zdttj1XnLd8fGt_e5rTihREvgK9BCsQtzALriSNAYipVm_ND8mqB-paKPP6nEQFrYlqtkKBpk6osQa9vOOMjoDMDK2wcnxWLTN5jvuVKAYd2MetQipQJqId6sBtKTL-PXZq4h5Jbpqr8OHYIGWYZcybkpZf7yNuDepV7V5bkaiA8EU-NDW3hPjxXPYbrAq3q4iIJeazDsGO-7Gfh5nBRMm4UG72rdb7dFyz-57oJKToPgNP8YbXi0aYKrPLnluQa8sN4CIobWgFGWvLinJVnD53byW026RzdxG5pVL2UnB2e_J_ZO2UDwxWRdjb=w905-h452-no)

![](https://lh3.googleusercontent.com/Wd6Y94qHSuh62CaN8nwbnm4y7ofu4n6heYDvK6pDe1xAOmcJsMtdSXCcTY4EnmRepERiH0tjwr2YZPly8dZmfXOmmntWMDaOI1pQglEFvysZjH1FIqOGt_g3QVKqv8A7SEegqZS9z3nCLAQSaBWOHujtlBxBEGlw0jVxuQ32gsjgqSd3n9c1Be12UkFkGVmMmvpiO24UsfVi6cr0WD0XZ6SiD3JjTuGouDgnQ1nhQBspYI2JEx8Nmdd4E2pc1ZLq7sBLXj4keclkSC6NFSKZGNIXZltRR2-XRY5HQbNFXEK2mS37JQ3iQs4Mwk4Z5Hp4smX66uwCiMirvS9_RSRAi5gAuPBxx3DT07lGk83nkBmseOb90KyPkUFkFPqksUenfFSFHcuEn_NWMTq3kIdkJrivfsLHEWKC1VwFekl9kq_5Ym_H47mvjO7BOEeKfn34C8fqS9Ac1xfAd2vV2bIIlsHoPJSecbX8RQtfytrnwH_Ci6i5AbEsSTu1oRmfSvQdJy2DizRsguoxfIFbXz5CpSOKqZ6ehGpRGDP0ma7Evo0iXQ77_4ouniLqNyuTNRD9YHukBlfK4tx03YKsjgmgY_cnlenKDyydZtYBkAI_5bLFxLyc1W7UYjWwihehhTRcbiQzgfhUO8PI84D5XFuCTMyFbmf7uy-wrByqDoLe94pUzDu4kQY3Ex7AbnFPte8UqjUc1zIQoaW38wrEchiT_Wm6=w934-h470-no)

### Pooling Layer_池化層

- Pooling layer 的功能很單純，就是將輸入的圖片尺⼨縮⼩（⼤部份為縮⼩⼀半）以減少每張 feature map 維度並保留重要的特徵，其好處有：

  - 特徵降維，減少後續 layer 需要參數。
  - 具有抗⼲擾的作⽤：圖像中某些像素在鄰近區域有微⼩偏移或差異時，
    對Pooling layer 的輸出影響不⼤，結果仍是不變的。
  - 減少過度擬合 over-fitting 的情況。

  ![](https://lh3.googleusercontent.com/uIwV6uJAbcMLEsyIhMiDeXKZLk86vLlzw8t-eOtKhQLMH9HVivx0eO1qXUOhZ3Jx2DonHb9Oj2BvO2n82rcceeUswAkHw_4iXpQyK16r9KOqRh-kM_tCeXenYXigzWA0c7EsWnbcfGaj5KJoF9CbZwNIViqToygjCrGJNQLwJZMXZS8T5_Pu3mKslSc2LIJQHvCVukRgvdJ03zlx9eiNAUiNBtFkWUv7Zw-HWpwyX2bWW0hxJen2NBSsZj96gwJ860O7pFbR_ZSrdY7eSBEkw_0arfooc2waJvisKjurGMEJrR7sjBe-gof5UoOfDHbibV9HRqutNOQ6pGeCPztNIH9GJ5svGpMEF7hBfg1BTNjkCJMa15hKTpOLIBnYHWZVwObEANBy1-xpH63I-8BpXWkpGh_PRBJvikpejgjdHor3-CTkxC6zkTxaPCvgBJhV2AgGeDrEa0wbMNxjbvjdCR-Wl4ZkrJEm2BhU2U3vwX1jJOsTFz4C9A58oTANEpscvvgxrn20Az6_qvFxDYipFGe6inLeYE1_gBDyitXxyAqIKolY_k9pwJAc41CIihkSAtAUcDBXknFt5X42RlURrgx8NAbqBm5LeXcr1XLO0JGIugJCSYTBOhJb_OowHyfShTaGCy9Bo-fWBPXKJ-Ecu-gH-ljo1oun24hUpTFvzpeyxtFSdtPotP22TcATOcgh0sFM7s3D2ZLvNaCBH_iWf0_u=w811-h203-no)

### Flatten_平坦層

Flatten：將特徵資訊丟到 Full connected layer 來進⾏分類，其神經元只與上⼀層 kernel 的像素連結，⽽且各連結的權重在同層中是相同且共享的

![](https://lh3.googleusercontent.com/OrQ0qDtyYpv-q2IFxDRmN12AzF6bPwghdXZan2BaVmjiYKCTV2-RQcq1wbxseojea0tFlbEjjArH5xzM99UQAseh-lAJyga56I41cEStFx2mm986kM41iLH2V1FIoYyas9uzCsj2qUEq_6_af95ExlPUOg6bBthGfmM3abXz3GIIvxPqoK_gcZFbPTv8TniDkmD_vww11J2l5wvfGes7WYFcyAFEDuLzQVfBJpOdcBZvdxOH0xr4XyoGR-aDaX5LB49s0hI2d0WvfUPaeNKS03EDGrJcyOEwB2wMho0rMsPEAZfQUhxv8jDOkSgW21TA8Ju600SoDE23drbg0E2yNHfhkEHKslJpsRafE9KkyvLMc_q8738gb_4jGXm55hhHuseBx7-s27EEVwlCUyoReQsKufDhkjeQC12lqbVmTVGEPcgtW-OKMeK1ZF13LqykxR3_w2YfZfKA_QxwhdnBvWXrdXvqGyzHHvxA5tk5B-DCD0OZ5jU1A8Ni9q_ojiGkBIocrPU5BQMFEDawJ0GknKC-fKxWsk4_R4jFVBiR2rSOMFEMNufUfGs0wYllzKCM5nfJoxrKM2jISnNBqgJUisXFGoKwA-LkhmrUgCQkb2jwSXQ-dBbTgBKZq4yur8cc0HRMs7JvGuYD6_EfeVLsdvIaxQolIRd6ciJaXl_BF3omjrhCYuTfbPASdfo94a0s4IIYFzWyA_YBdvfbF8-PkQdX=w790-h229-no)

![](https://lh3.googleusercontent.com/n_wK6hmlxuSdv_U0cenKX6XuqD9vba5H5CG0FPvzKAUJb1bTx9evLtQ_0Xmjaw9jghr1XgvORvtoVOjs5sK4Wng1wnJ6JG-kYKu1m2wBN9tLebKOVqHi50u9F07Bm1s5aH8PHmjTPnG7kKbg5uWxQZSBQDdPJxfqscciLhQ1Iak8DGaKhDD7dBpaNGDJBU6EXAadf9zsbZaaWD6IClBfghjdh-Xk9CMexD6YRbO39ULGgIi70cMasejSaYhtruHsxhH-iu2ytm4vvtFsJrZ9FGmjan6Aahc8AcNYgmhbpfFpzBVS2MIna1BkvwEkRMBJJSsU2lKCDhx9IvWK5ni3RVHL6zuqspt0Zn5FOvBR-SEeEzY7WHcwjH8f7o4vv-09xbkwy8frvfGd0P3_gp0R7OHMPr7LgWnbykkbO6drzgcQ01L4Rv_wg4QsCeF88Rro0th2HHqHCBR91jfG52aO2MZjJ6mtMV5dbqSqLz8SPz1ZxsVE_cNjACI3s5cqCB1eAgT4F3H5_4H4yhLB6QVqfoSxkYUEA4ZgUxeqzfh-uAAHE6FBtu05LH18W4DGIwAdfk8x5naIwnhzTk5GqYf923HT8h6p-yu0bmghw-SeX7bOWDjEl0S95Sv8s-4XgLFPWaGG6TZYnz1FfrrRYtvintYVeQJ_WHND4tb4SSYCu2r9xy54niIIFVZpkCIlA63vAkmd4gfPpDUVM5Rtqz2fj2zZ=w412-h312-no)

### Fully connected layers_全連接層

- 卷積和池化層，其最主要的⽬的分別是提取特徵及減少圖像參數，然後將特徵資訊丟到 Full connected layer 來進⾏分類，其神經元只與上⼀層 kernel的像素連結，⽽且各連結的權重在同層中是相同且共享的

  ![](https://lh3.googleusercontent.com/8IPH6bO4KWocQuLqdgxc660jX5FxHj-THiYaUobADhuxINrPusKV1iHzEujQlRjHQBOJq_SLU7itcX-AE2KRtH3tUyI2RmdquC6v1mwojJ-nGUmZIcT-dcQSCyxNdiXcYUAMCG_tnMQT9ZnZ28gbFzDVyAvbbStV8P81PqnCKC0IBmGnWR0Fpj9f9I4sg_5lAgHPo_fFwZQoYSdM1bCOcdfvAS8T6T-6LjZHOG5h1RS-SJHciSfS6hvz9V5C5AuZyqRzZNnkztb4yrFKGGybndfYAA5es4KOtlE87TnuqMtpoHMqhE4Oeh74_BlMcP27b1tB0dEjSaLujPK736NtdCfRpjbullrAXelXQik1e10E5erPVayMeE6C2CqEH2abYE5cyJny5dA4DE_7VqV41g-B1nuvNIzHVGE2J1KOAaupRAAEC-K2v8_8js7ydqs5jQiYTij8vzVFBWi3SGvA3UrYqNrZP5QoWSxxOic-HJMD0KG1JiOfBZLUUiyeIZ3z6Qp459SfIl72ZVYH0HoyQUc_aM1Jwkw193qp8iqiYfLlgrAjPzr24qkfcTcvY5xfp9I_hhjqhDrJ9RhMk_JvfDVySEPDjtfPIYqOHuWG7e7dnyVJWAZT89gPPut48siFO_opocmODFeLa8Ud-lgd1prX9GIhxnq5s89iSOsfxuwNHkJW6fHiDa4r8U4hz2dBvHGTob26WxyrXbd0_KAJ-uuy=w794-h274-no)

### CNN in Keras

```python
# 卷積層
## 建立一個序列模型
model = models.Sequential()
## 建立一個卷積層，32個內核，內核大小3x3，輸入影像大小28x28x1
model.add(layers.Conv2D(32, (3,3), input_shape=(28, 28,1)))

# 池化層
model.add(MaxPooling2D(2,2)) ## 池化引數：劃分的尺寸
## 建立第二個卷積層，池化層，注意，這裡不需要再輸入input_shape
model.add(layers.Conv2D(25, (3,3)))

# 平坦層
model.add(Flatten())
# 投入全連線網路與輸出
model.add(Dense(output_dim=100))
model.add(Activation='relu')
model.add(Dense(output_dim=10))
model.add(Activation('softmax'))
```

![](https://lh3.googleusercontent.com/PAKVFBDKSIEMBVtizp4aMYPw_F6MC-zv1cLI4EdccDJykF3szfEjyeAmAX4we-tMSoC4VMPi1nwqd3A9a75lnyygbUtkcbiZ7s_7zSSl32vXovCtFQ9Xki2DYPGKhiRb8uyUmst-rX8NFFbl_7YfyafWyUJA12Kw8NOkgGGtgHn44SvZqMWqi3kOFBBhKnfgJyRLxQTuX0j3yurvCSC2rnfkU6pDjeIIzZ3o-A3mWEdRzSwmRD-wVWbW4P_mY9Qkir3F_hC-3lxSZX_EXBpQIAP7kcX1mOCWWqyBnTL39C6jrtAsbsRez6KJz9z9lGfTjlzzm8vOKbzO3Z2QeFErbw1jV_2kNUgdguWZgfwW6Xw1jKwhefGQzBcmaSNkaac_mddQbOzZo6HOeNuiYy_LYAJFsm8wra2JbSio878ccHHRGUnXeXrKrdzi8v7bBPVE1fRhwDpM0sKrgufwh7FOWvRAdh3SOjIcVplNBSRBXKHk0cPo-vGWvuNfa-CBPv_4bUmFlrABzTL9pvibEugSYQElx6QzCEJxXAfTeS7_1XqisvMa8lR-m9MTj5HYBw0VP4fAkCxqoeLZX4YhWhOwzxTj2cSOMkTXXjnu0RGcS9h5AWw2kIL30frh1ELnJB2DFXDZjZ9283-GK1LNBXvnaAQm-1TbR2S7PLB1lwlRoJWxU47Lhk511Jxt075ZpsFgkX566UaDGpSTqVJpwBCyBNno=w827-h163-no)

### 重要知識點複習：What is Convolution

- 卷積是圖像的通⽤濾鏡效果。
  - 將矩陣應⽤於圖像和數學運算，由整數組成
  - 卷積是通過相乘來完成的
  - 像素及其相鄰像素 矩陣的顏⾊值
  - 輸出是新修改的過濾圖像
- Kernel 內核 (or 過濾器 filter)
  - 內核（通常）很⼩ ⽤於的數字矩陣 圖像卷積。
  - 「不同⼤⼩的內核」包含不同的模式 數字產⽣不同的結果
  - 在卷積下。 「內核的⼤⼩是任意的」但經常使⽤ 3x3 
- input 上的變化
  - 單⾊圖片的 input，是 2D， Width x Height
  - 彩⾊圖片的 input，是 3D， Width x Height x Channels
- filter 上的變化
  - 單⾊圖片的 filter，是 2D, Width x Height
  - 彩⾊圖片的 filter，是 3D, Width x Height x Channels 但2個 filter 的數值是⼀樣的
- feature map 上的變化
  - 單⾊圖片，⼀個 filter，是 2D, Width x Height 多個 filters，Width x Height x filter 數量彩⾊圖片，也是如此

## 卷積神經網路_卷積(Convolution)層與參數調整

>- 了解卷積神經網路(CNN )中的 卷積 (Convolution)
>- 卷積 (Convolution) 的 超參數(Hyper parameter )設定與應⽤

### 卷積 (Convolution) 的超參數(Hyper parameter)

- 卷積 (Convolution) 的 超參數(Hyper parameter )
  - 內核⼤⼩ (Kernel size )
  - 深度(Depth, Kernel的總數)
  - 填充(Padding)
  - 選框每次移動的步數(Stride)

### 填充或移動步數(Padding/Stride )的用途

- RUN 過 CNN，兩個問題

  - 是不是卷積計算後，卷積後的圖是不是就⼀定只能變⼩?

    - 可以選擇維持⼀樣⼤

  - 卷積計算是不是⼀次只能移動⼀格?

- 控制卷積計算的圖⼤⼩ - Valid and Same convolutions
  - padding = ‘VALID’ 等於最⼀開始敘述的卷積計算，圖根據 filter ⼤⼩和 stride ⼤⼩⽽變⼩
    - new_height = new_width = (W—F + 1) / S 
  - padding = ‘ Same’的意思是就是要讓輸入和輸出的⼤⼩是⼀樣的
    - pad=1，表⽰圖外圈額外加 1 圈 0，假設 pad=2，圖外圈額外加 2 圈 0，以此類推

## 卷積神經網路_池化(Pooling)層與參數調整

> - 了解 CNN Flow
> - 池化層超參數的調適

![](https://lh3.googleusercontent.com/LX3-xNVpk-hMOLsVbnf5vW2HRgw7uRKJSRK3v9Z9hwsWVwsZ9ZyDzVAsU6ytCSC_kXJhGCWMq0HEbxG_mWU09mvykvB4y7WO1SlMcNZ3IENPJnic_Z_4mb4qMRzcS9GEzzNwbzrvnkwu0VLqpBe_PEZK8OR_6_IPUKi3olhvAEqw3hQS8k-Oclc5U7jDJDRj7GHWGS-azYiRe74UNqMcS3LTijK39g--JYUt4PxiLX5fP-nliTmMgE5SVcq1sMYg_9iaI85pyNJKacO1xhMS8DQsJNW4d7_X_8FHXTTYSBiMCJ_cXtZWVc4kb0eVZSRIDQn2wrmjD5rHwTPs6BR1oZP2e0cvTwt5imCbb23i-kaPTd34IDYLCAegcO6E9M7mejCZWy3M4s4abVVwo9QrKe6r_PJ9XMgyovxcE8zfx9uSbL8UcKCipj2rOd9tHdP6Gj4SGwH63BQQtq99JFyrs6uWw9MZMY5B5qzIREO0wNFQYT8Fs-0oizsCnAfwi-vehaFtGhLeS6h4-mp6Kt3tSVsPyBoEScmAU7zLqkIXfwYWbLYsTw0Ib2qIzKBRu8eQyl8m7yGFT6Uc8YR1HRVZAXvNZgryAQlwlUXcmDn2X2c1anxN5X07S9XoSjOHR6gKFNJ7XnPUORWzbqWQCBbs7NxxwSOPdUVBosmAinkk0KNaOJRUpsBrxTnWASVHhTZcjt_dW6AzyDdgODKoyfe6Ls8v=w737-h186-no)

### 池化層(Pooling Layer) 超參數

- 前端輸入feature map 維度：W1×H1×D1
- 有兩個hyperparameters：
  - Pooling filter 的維度- F,
  - 移動的步數 S,
- 所以預計⽣成的輸出是 W2×H2×D2:
  - W2=(W1−F)/S+1W2=(W1−F)/S+1
  - H2=(H1−F)/S+1H2=(H1−F)/S+1
  - D2=D1

![](https://lh3.googleusercontent.com/tds-tymj8KzB55rCs1HcCIaFzqRDWWq0hwDiRlPi2S48evhu-AU1tzsgcz5V0iRMnSPGwC8BTYYSjIxrbYBa_GZy7yxl06LRxETEL6zvKkkAgD0-tzhc5uvlomfR2Ydk2R30OXptd6BE_C2XX-DHZBRUdL08QlsAFsnViHLusNIy9N8S5iyh5QkF7hJApg3jGtESkUzsQYfCTgRsoLx9ACUY2sizihaX-NdV-jqiAOIqaEEA_-t_W9fx60IpxZruHdiivMCOCz7Ew6mLeUnqUbBpJi2vuCiDH5cSkgZBCkE_6oYpNI8N45yAVaBvMKC5-1XsJndaqyerw2HWspaDYC2WUqwUwn_2vu-JNJ_gcnMS2yI3X-ooBRr4Vhc6jGaU7r9Bq3FFH4yFD-p-ZyaVpdxSSAMZV9i6ZTQ3SZJfosQqyRXBaASu3kGa6qaCQBswZSw2gcn2UeNE2Yd5y1ejRf16ts4kGDb_wZLHoOuorrlY2_5i8eFpQP3RSDd1PzQIbd3pJWFqDuy2viYJUlRWiFQN55TdY4YB5ft-miAOriR9JdAM3_n3z31D9UGgWWzzLwX8pynu2LItmnNtWwjydVSc7vg9lswj7Dg-B8_60NKzgARyOvi2Qd4jIxlXrTnM0Oo8LZMtRfx0LAsDRDpuIDvSY0zdyLoeMPgbtTevgIC9P1KZQYfHFmAwyas6hbN8pHcPv5fndINB--ThyfbTs8p_=w469-h365-no)

### 池化層(Pooling Layer ) 常用的類型

- Pooling Layer 常⽤的類型:
  - Max pooling (最⼤池化)
  - Average pooling (平均池化)

![](https://lh3.googleusercontent.com/XtPOc_RTqvqc9p1TmNbXz5euRhKczvRLb2gGtrw3ElsrG6GVDbwAVvuJgBSSnQMsm_VLet-_43IohnbKSwG_oy4zShFL3yOHpCFMAqNFfIWFvxkRN3GeVeayhR8Muuh2P838clh5tdlI4oxWD1Q3xY6X_52bIa94TeWMX4T1pJJTqH7oO-tBNF_-Epi0-KoDZO4Gi1OfEcK-GZe_aeEJxplghz_-_IVZYH-YLOyLwelXEbknuN9vZjDrtwCOUpxKRJhI7K4Je2jGwM3pbbhQgTM1GdE_nnTrs6gQwsFQtI268ev0sPqaqscZEhZvHP_9W4XcFTQUBv9DrwNTMmOe1hTUW70_AUw4wjtJtJLXFLJqNfsmnaGHZeGi0lftRoWFJ_UlkBh9MlncxsA5zoPP4Tkvm2dOdViCTDnu39v3UepGimgMiUj4TNnzmhjv3_7ZZtBX_ujGNkOLrNf9-TR-HXXiMfAGFSgXjsKxedBGlPX4ftOQMS0TF7sXqmue_WuMjuQYvUd874A-unFBON-7o__d6Zbu0nM_Naa9u9WZQ1iUUR0Ocyz02e5IQ7BluRcEZ-Vt2oZ3R0viXMIIY0htJIo6PhNIfOmm4ugvT6_zcgEYVgrOwK0kGy3ErQWMC3cqJ51yIHIhF_QHI5Jenai3QbdwPrUxzHp-Gne4YQiprgkJoLwgAXzj4j7nUopgh68Xr1G4aiR1PlZAFx8VW08aRRoj=w702-h304-no)



### 池化層(Pooling Layer) 如何調用

```python
keras.layers.MaxPooling2D(pool_size=(2, 2), strides=None,
padding='valid', data_format=None)
```

> - pool_size：整數，沿（垂直，⽔平）⽅向縮⼩比例的因數。
>   - (2，2)會把輸入張量的兩個維度都縮⼩⼀半。
> - strides：整數，2 個整數表⽰的元組，或者是”None”。表⽰步長值。
>   - 如果是 None，那麼默認值是 pool_size。
> - padding："valid"或者"same"（區分⼤⼩寫）。
> - data_format：channels_last(默認)或 channels_first 之⼀。表⽰輸入各維度的順序
>   - channels_last 代表尺⼨是(batch, height, width, channels)的輸入張量，
>   - channels_first 代表尺⼨是(batch, channels, height, width)的輸入張量。

### 重要知識點複習：Convolution 跟 Pooling

- 卷積神經網路(CNN)特性
  - 適合⽤在影像上
    - 因為 fully-connected networking (全連接層) 如果⽤在影像辨識上，會導致參數過多(因為像素很多)，導致 over-fitting(過度擬合)
    - CNN 針對影像辨識的特性，特別設計過，來減少參數
    - Convolution(卷積) : 學出 filter 比對原始圖片，產⽣出 feature map (特徵圖, 也當成image)
    - Max Pooling (最⼤池化)：將 feature map 縮⼩
    - Flatten (平坦層)：將每個像素的 channels (有多少個filters) 展開成 fully connected feedforward network (全連接的前⾏網路)
  - AlphaGo 也⽤了 CNN，但是沒有⽤ Max Pooling (所以不同問題需要不同model)
- Pooling Layer (池化層) 適⽤的場景
  - 特徵提取的誤差主要來⾃兩個⽅⾯：
    1. 鄰域⼤⼩受限造成的估計值⽅差增⼤；
    2. 卷積層超參數與內核造成估計均值的偏移。
  - ⼀般來說，
  - average-pooling 能減⼩第⼀種誤差，更多的保留圖像的背景信息
  - max-pooling 能減⼩第⼆種誤差，更多的保留紋理信息

![](https://lh3.googleusercontent.com/rP0CKzIqgSC_yC3_GfAjTMPK2GboGrgtECSxtoBZZMYsZaujyzVWGlA-wUflCYP7O1wnDUI-ijrwEkmcW9zm90mqWXEIdsF-qY3CEGQ9yF-NADQeuK5-y1qHcgRIazo4IXSv0hcr8XJslKMJR_46pGBPHeuHD7PVNQjOe3EYDongCIADAnAEbkhF6AJoSkdJ-j7J7I1H1s3lDJ4z-dheClmVBP9VeO6_4O1ZFlnXglKQdcnauR8C87NLeLfIgnHcKIztN6OJAR6q2fZCpthNNzhbwjkBxXiNBiAjScwI0i45PqWO3YebNtRO2mgO5sEqwR_hGxdJKXKLmF04h3E7W9tKM1F3gMnRwwX0Mr6y7EskmKGMbBqaMTbuCTi_7r6E1jwlMyqHfL5HUydbaZPp59eYdkcRUj0x1GMThuT4RN9484rLRFGAP5RO7cCZqsu_oKefS7foU3g2wzMyIloWbROG-3Xv02Qm7Lt-PcMBZNSDNwocB9CpSpNCpiLDv_nYIGGwhkjXdDkZZ9nSIC6TEkEH7MH_X8uSczLRJtmTpz-tBt6xTsIWTNC52-G6RQ6EzDxpg6_GkAtTfuRl5zezNj5la02kRBhCkLDoNvmv9uxFlm0U5FdvWNYmpLSalMG9kkkQytMK6kof7aXAXLdT1fPDJ2ZEF2TldGxZQGAM2EjwYEORW-Fr0hUUBKBILHCIFz88r4whfii14F5MaUzIJ0-k=w867-h401-no)

### 參考資料

- [Convolutional Neural Networks (CNNs / ConvNets)](http://cs231n.github.io/convolutional-networks/)
- [3D Visualization of a Convolutional Neural Network](http://scs.ryerson.ca/~aharley/vis/conv/?source=post_page---------------------------)

## Keras 中的 CNN layers

### 卷積層 Convolution layer

- 卷積神經網路就是透過疊起⼀層⼜⼀層的卷積層、池化層產⽣的。

- 影像經過卷積後稱作特徵圖 (feature map)，經過多次卷積層後，特徵圖的尺⼨ (width, height) 會越來越⼩，但是通道數 (Channel) 則會越來越⼤

  ![](https://lh3.googleusercontent.com/kioKIV7Wkw_pJIhRZFxmA30OLMM3iBgVnyQFoqBDXBHATcUb521vp-bsj6vXvjMkcj4BHRGy3uYZULESUmwofhZUDJIuozsSykRFLpsld1IMftw56eWcW4BryHcYn9mqINjRbDeA-S_feRQlgdXO885xqZvouTpDm3oHr6EK3GF3NwTu3CEhPNEHIfcsPtjCQeRv6VWYI2oRyT51RjXE9g6A2GmXDOrGdi7nvwJjwFRGvgWD4cu9BagO_A1AUaXQHzeUnJIpZIMrX1_v2MjKhX_yC0wmtS1ZB-QRtIdOZY7Sj9W-QE_yEukkKjC9E0MfUU5DXuE0CNV93LBbbLTAkqqlHCj4Ye2QoA3RykHjj6dFIafvxZU11uLJYJB7XgkO73tAZEXV11K79nyy_Yor-JrCAM1Wn1XAPa2cY6SJ0CXSa48bgL5eghXeDIs0L3twWdzAsfiJ8yW6RH44voFuCOkrevlq2jrRXUPfnx97788wf1tlXpOj1S5K8NBIxZ6Rp0yju2C4qFXgXoI1NeUZiAZW0izc3Eo4eTXPBQiJzUyTm3Uj1VrjOyGFh_ql5haPf6V-LCwP8gjQcmdLe77Qni6Moz7Fngj9qHmxkbct9_IVwPhypfFO4RZhZ5S6Ppk0zlEjx1qDjJSaj9tgqF2cEzcLDP-q1TajzyeAU5oaQArK_89tPHdex7ev2pl_6NCtwTRQyfXwO1-S-8BMMuPJehpJ=w920-h441-no)

### Keras 中的 CNN layers- Conv2D

```python
from keras.layers import Conv2D
feature_maps = Conv2D(filters=128,
                      kernel_size=(3,3),
                      input_shape=input_image.shape)input_image)
```

上⽅的程式碼先 import Keras 中的 Conv2D，接下來對 input_image 進⾏2D 卷積，即可得到我們的特徵圖 feature maps

> - filters：濾波器的數量。此數字會等於做完卷積後特徵圖的通道數，通常設定為 2 的 n 次⽅
>
> - kernel_size：濾波器的⼤⼩。通常都是使⽤ 3x3 或是 5x5
>
> - input_shape：只有對影像做第⼀次卷積時要指定，之後 Keras 會⾃動計算input_shape
>
> - strides：做卷積時，濾波器移動的步長。此處的 stirides 就是 1 (⼀次移動⼀格)
>
> ![](![](https://lh3.googleusercontent.com/_JgUxL4UM9Jf7h1N3LHCFHLeGmEmJODQh3jPY6egML_qhQjfiBvXXCzk8vs_wRpo7NPqWCcfiOKqEmmRsQfBLGA2t-r1w-3RoZ7K9vXt4egLu8kkxUHmQdibpscPbDIHc2WPu3-sLO_qjMRvbwblX0hHqouwsBbNi1hCEx6K4WimO06fvO6ptH_JVJsGBRDKiRRCqvN_7gcMfnP5_ZLparMnXS5wnkqRfOZEvGeeJ5aPilZWEJHES2ZbBZJn3ipPbk1UUlpDiX1n_pbOqW2tcF7E8SLedkH9XNCxBrJuaiJfyBs1K-BCv0a-M-7zcKXFz4UjCPhKdEnZbGQ75nQ0fCKMPeEjOMsKXwmR5tOEQu8dfazocia9PbiiQqYJxFWCsjyBcALxagLfpJThEjSHdarzW6qBoEVw0uiiZ-_WPB9PWy1v6WIust7JMuvlGvmoe7udFAQBBt_lg6D5B6EpXyuY1Qu-6EhXYd8LNpDwZIhSWAGZbsHMVL2PAEb5IJfSGFusFxlNZjpyy-RQpPbVQ8siZhj8tlU99Ny2ASDYJ0D3LJmJ_kayMKs-ZUyFOZjB2f6QJO-0gtmCrDhldmAazNOt4OGXd-c26L2O207Sj5cd_MxPJKbtQOo3da41r0qX5D53Ec9ubCeMU12AS8iSKRqhDzhNTpAdNy2Iyy42xs8KlwoiOK6xxLhPUZgiB7PnkBYXOk56eDwcY_Et5dFAlqxG=w526-h384-no)
>
>   - padding：是否要對輸入影像的邊緣補值。此處的 padding=same (邊緣補⼀層 0)，稱為 same 的原因是因為做完 padding 再卷積後，輸出的特徵圖尺⼨與輸入影像的尺⼨不會改變
>
> ![](https://lh3.googleusercontent.com/0JU7Xm8kOZceKP9PUq3y9MEcl-H37D9YnnRgtk0IqX4h7HViShrKFZbCDyDmBVDRLVC5VCY0KZpmOfUV5NfX_97tMNof7eFPyIwgh-YbMf-opgOyAN1TEW_709hTZV5Oan0dBkovUnYjInmMDsNT759xEihco6KpdyuNnct8CFf3quoAWCP4jkd0BZL1nMMAXPFXPAJZXwmt7vV_jHRlO7rpryhMXmy8ImtSIJiZKOSfHUMMKpK3lsuRo0JnSeZh8lR4YOyU-WqhiJuQIXYlu7j1PQVR4q2MtEy5tWKQctHbKqJTJ2QfXM6uTCxeidlPnlAINXZS2uPrxJPaQLXMa4bYVU1yneekPsacU_9c_jBjjlHNnHsvvY7lHYg7vXR5jcB3mP5EuPSHugJ56_BIgVzqh90mgTK7Nl_WxT5PA21M-PlOtMDHkYI8hahuovvCF6Vhyx2YkRKk8cj1l7dxUSwCbHdE_7DJXFQhiaAdkBXKSaoyHxPQipgQV6qOgzt8hgtsfmi0zMWWi2txzdZpYJWa-qO6xtuIbxle0kfXHtE8nAdJJAZIu2J6qqP8XeijPbAUb-0uGOWH-e8fFfyBVMFQEWydLRXhSzfXSbYHRQxpftktsHHZatVUKhYJykU0u-VMYl3yTeh8GnDns_tFR7Hy0_04QMMbzNeilDlC1WyvMIptpB2LGzKqsiHkven_OFly2S9JaXW_kz-1VsiGh0nM=w666-h294-no)

### Keras 中的 CNN layers- SeparableConv2D

- 全名稱做 Depthwise Separable Convolution，與常⽤的 Conv2D 效果類似，但是參數量可以⼤幅減少，減輕對硬體的需求
- 對影像做兩次卷積，第⼀次稱為 DetphWise Conv，對影像的三個通道獨立做卷積，
  得到三張特徵圖；第⼆次稱為 PointWise Conv，使⽤ 1x1 的 filter 尺⼨做卷積。兩次卷積結合起來可以跟常⽤的卷積達到接近的效果，但參數量卻遠少於常⾒的卷積
  更多資訊可參考[Xception](https://www.icode9.com/content-4-93052.html)

![](https://lh3.googleusercontent.com/Jdlzq6xZeHISkNEROatbJVFljJwhBqHjRAl0oad9vjG3xTbkGHrqrG_AH5tRPpMNsj4U1MtdBSocbpFDDb9uH_u_QJa9o5zxgSvb3JIk1GXaGv-e-X184M9IF5JnGhUFTBfxA6ntHl2HJ8f32P_oJzFEm_pkN4OOIEaEgKnBYkUBtzm5KZ19ZTofn_DL33rN9Ibf8OeQwEBSnjDAUFOS5VbZ89DLXMOQ2mbG1SmvV-RFbOmp6MzCOEGtyeWrTrRrF1azbgRI9pl-eBokIAXe8UFL4s1vSijxqGfCiS64Ff4cMLmIzsNXQIMBw39YKT6f-Q_ROtM2fOTj3Es2P4lI4jdxzyuPwM3xXbeCmSJ6OvMDTLLXOme8OXyDZBDDu2ltuYgrijTvyeUfAE4rRMA5Dm6fMTqoP_PTfWSh1EYngz_wXCGEV3WUSo7s99iSa4NRSjPz7-wq_w8W8Gjm0wjoIuV2eENqJwSvf_U4vLi8MxqBg9ahfHS3khsWS29ogkXxNBDGpGtxF-6opHwn9AlJiUTYZD27XG5VkB7Ga5xSVfktVpuDH2Zk3ejI4AOxOJ-kVyZbxbFJ3mflJCJngGvVgo6-RUY6vYjHEwpd5f5TgSiFsd9uo1xPYfDKZJX51sGLiwtunxwuUXi3usPQl_SdfoAdvqwYd5EXXCTPE7KDrfWqQTWasbSvyTyOxLpCn00_jaFaSNYaBmHNZouQJqk1TKiW=w903-h229-no)

- 以下說明 SeparableConv2D 中的參數意義
  - filters, kernel_size, strides, padding 都與 Conv2D 相同
  - depth_multiplier : 在做 DepthWise Conv 時，輸出的特徵圖 Channel 數
    量會是 filters * depth_multiplier，預設為 1，上⾴的簡報即為 1 的DepthWise Conv 

### 參考教材

[ML Lecture 10: Convolutional Neural Network](https://www.youtube.com/watch?v=FrKWiRv254g)

## 使用 CNN 完成 CIFAR-10 資料集

> Coding 練習⽇

### Cifar-10

- 如同先前課程中的 Scikit-learn.datasets，深度學習的影像資料集以 MNIST(⼿寫數字辨識) 與 Cifar-10 (⾃然影像分類) 作為常⾒

- Cifar-10 是 10 個類別，影像⼤⼩為 32x32 的⼀個輕量資料集，非常適合拿來做深度學習的練習

  ![](https://lh3.googleusercontent.com/LLJ77yR3iEnd0eL7lNLDMT6g_k1ismemoKzCkbzBzX43ZA5Rvu6Td-_09F0yjrlOYvr7M9bp-mwEqvUs8ekxEf0NX1NASKU8zOlJrb0Y3RWMUMOvM9AqGsGyq3rtdAylOIot22flxzbI85ssGk9aRiQ6qCfb-Uv32_OEjPNw5yG71VA3Y84SsGFhbXsbCBzUxn6u3wrzwggvCu5xjI4RgzJoWlYlXoF0Ua-k6hI_aoE7uTByWBiLxbka7wu5MtLbK76A4TUssCViNZ3JoG6SVRUdqQl4JK3fBoFH05yjn8K0PFXOQ4K5FOs-RHdV4eYTlKIQenLbifMTFzyqz7MenPa97Jgjy9t6PApdWL-Cqt65OZvLol5ztUOqED7hEtMrSnGAZufybzkPAXyke8ib15XmNkkcKrWejg7uQIlSk0DrxSehLOeZS1gGcVPMUttyCTcXYE_fsthGDZxapnHEHKcLSSW9RuQByxyQxkLgzMWPagCShq9oZQ49jGa5IVFsxwvwMBEmVsf2P38bYTCu620-tyjwuMeHInWUzToQ_dH8ypbZj6OPnZ9nGJMzN3_rzCvleGTqp0HWrIytzkDVoVE0EoNnOPYoMI1RBPisngvMnrEzjwGWGuKkhbmgnlqxg6GzY91ZTekJ3O-ydkmLJG4h8DIgV03e8beeKOx577B8UtbFpOJC4ynkioPL_DgNnxPd3z9U5eoaxTBSHKUVUfi6=w606-h444-no)

## 訓練卷積神經網路的細節與技巧_處理大量數據

### 處理大量數據

- Cifar-10 資料集相對於常⽤到的影像來說是非常⼩，所以可以先把資料集全部讀進記憶體裡⾯，要使⽤時直接從記憶體中存取，速度會相當快
- 但是如果我們要處理的資料集超過電腦記憶體的容量呢？桌上電腦的記憶體多為 32, 64, 128 GB，當處理超⼤圖片、3D 影像或影片時，就可能遇到 Out of Memory error

### 批次 (batch) 讀取

- 如同訓練神經網路時，Batch (批次) 的概念⼀樣。我們可以將資料⼀批⼀批的讀進記憶體，當從 GPU/CPU 訓練完後，將這批資料從記憶體釋出，在讀取下⼀批資料
- 使⽤ Python 的 generator 來幫你完成這個任務！
- Generator 可以使⽤ next(your_generator) 來執⾏下⼀次循環假設有⼀個 list，其中有 5 個數字，我們可以撰寫⼀個 generator，⽤next(generator) 會⾃動吐出 list 的第⼀個數字，再⽤第⼆次 next 則會吐出第⼆個數字，以此類推
- 將原本 Python function 中的 return 改為yield，這樣 Python 就知道這是⼀個Generator 囉

## 訓練卷積神經網路的細節與技巧_處理小量數據

### 處理小量數據

- 實務上進⾏各種機器學習專案時，我們經常會遇到資料量不⾜的情形，常⾒原因：
  - 資料搜集困難或是成本極⾼
  - 資料標註不易
  - 資料品質不佳
- 除了繼續搜集資料以外，資料增強 (Data augmentation) 是很常⾒的⽅法之⼀

### 資料增強_Data augmentation

- 其實就是對影像進⾏⼀些隨機的處理如翻轉、平移、旋轉、改變亮度等各樣的影像操作，藉此將⼀張影像增加到多張

  ![](https://lh3.googleusercontent.com/5ek7O9ksulsQPCfqPCtHmP0bXTt0FLZMrzE2oiCzlMRMNXvjTkimxDYFWbRqm8Roqg76wUSnYq8hg5-YVGppwOIPrm6cTPb6TtwZJlR8h57-b-Zq5rfeQLCXHL6_VhoYLp3XyB3ydBups_0meEgzzZntjx084hw4gUnPd96nFveYvZvKoOqOs3B747BJ6guByqRWW0Ff_W8H-exhHl66TUkwDa45YW4OuPLovSEa8D8F4815AAhrVilFK6EM_15ZKXKxNtGbZI6FegDTvi31o30IaEplAaUK9aovyFWE7Sm86datbCpYPrw72JYLb3vHPR300SpRxasoA1RGmqShxk-CMnE1_vwg_34aMt_57DZWa4nFUTQ8IncBO3IgnfeucrTUh62YgWVpSiteRh5LzxDoqPw3O_rxLUp8Q9LGjip08DPNSDrwOPr3pemqrguAHsJovaSeoEN0uIF2fX7UOdAjCXKLxn8jomfQVAjbp0ipBeIXHVQd3XqFs0GsQC_hoR3x4obZPuQPogWv_Z8y1M99-NaE3QbAdSNEhz1ogGzwM39bPueSGdevLKuxr_xHMCF4UOagFsn27gQzEURFK_ZN1ET5rkQOTivfJMfdQFGAIQTCYOiVTPm7GpNmtATYmTDR5xRuRxxu7-mGzfCT4rGTkoMwJrTKT7gW7Y-Qg0Nz3vMDtXpnArCVEqQiCQLz_Gl6Ddks3CdreTJK_g7o-bye=w929-h422-no)

- 資料增強並非萬靈丹！

- 適度的資料增強通常都可以提升準確率。選⽤的增強⽅法則須視資料集⽽定

  - 例如⼈臉辨識就不太適合⽤上下翻轉，因為實際使⽤時不會有上下顛倒的臉部

- 另外需特別注意要先對資料做 train/test split 後再做資料增強！否則其實都是同樣的影像，誤以為模型訓練得非常好

### Data augmentation in Keras

- Keras 提供許多常⽤的資料增強函數⽅便使⽤

  ```python
  from keras.preprocessing.image import ImageDataGenerator
  
  Aug_generator = ImageGenerator(rotation_range=30,
                                width_shift_range=0.1,
                                height_shift_range=0.1)
  ```

  > 以上的 Generator 會對圖片做隨機的旋轉正負 30 度，垂直&⽔平 平移 10% pixels 

- 如同名稱顯⽰的，這是⼀個 Generator，要使⽤next(generator) 來取出做完資料增強的影像

  ```python
  Augmented_image = next(Aug_generator)
  ```

### 補充資料

- [Keras ImageDataGenerator 範例與介紹](https://zhuanlan.zhihu.com/p/30197320)

- 若你覺得 Keras 官⽅的資料增強函數太少，也可以使⽤這個非常紅的套件: imgaug，有非常多實作好的影像增強函數，使⽤⽅法也與 Keras ⼀樣，⼗分⽅便

  ![](https://lh3.googleusercontent.com/-5RXZ1tDFyWe3l581HhGA06mLCMLJBiO41fIpJx6OQBZ-NUT4xy48hVHV__A8Q4NqrgQRQ22Hz_lf3saXKVNxtwlg4X8Jq6XjDW8F0Rg7Mwg0G8JvBtmSbyR31QCRmmQZf9cFM-gT8krVVOgGZmWcOY1M3JsrUOPmvAno3nu0zqMEg4KqSBoJJ1UAcuz-TrS-OTXgTlv2wHt52VBkDsVss3RUSuwAq_klOsSRk1Uxz7bgnltB8iQ78lj6gYYJsuhtWZQY55pjiHoVGIV9fIk28EcymIM4Yb_bhOSP4dubBSX8VAUQLDND_PQICIv9zqOQzKg4nKofW_3disemji_wWv8MY1dm1MTFvvU4OcqxbJQMboGBQVaIKT4QWwmkDbX9XJ3jq3tUfnrOMA9SaHUnFjOH0ibSGfiM4r2Dj8BJEoM02MnzY098Lfyt_lMT-OmJCTIhXFttFQy9S1Qq6c1oVmAl9-xvEaXD4BEugopEp24BDYgVm7Ej7WKK8m9zE2U-Piuyf8QUKIytRmHXFF2PajnyfrAO0Gry5YlBitgvpPyC4revo5tN9XMx7CJAjcwJxfuD99y4G_gLt5tg-t1DNWQA-im0T4pCLhf6ajutH998LVw5RySPvVa3WA659ymhDJSSIhoc_xMEZyuB7d12DqLoi2SJSb4SLhDixUTNRgDF4tjmE-vlu8rGC6S6pkAx19usdy1BoqSaWxDYXT2ZBvd=w694-h363-no)

## 訓練卷積神經網路的細節與技巧_遷移學習 transfer learning

### 遷移學習, Transfer Learning

- 資料量不⾜時，遷移學習也是很常⾒的⽅法
- 神經網路訓練前的初始參數是隨機產⽣的，不具備任何意義
- 透過在其他龐⼤資料集上訓練好的模型參數，我們使⽤這個參數當成起始點，改⽤在⾃⼰的資料集上訓練！

### 為何可以用遷移學習？

- 記得前⾯ CNN 的課程有提到，CNN 淺層的過濾器 (filter) 是⽤來偵測線條與顏⾊等簡單的元素。因此不管圖像是什麼類型，基本的組成應該要是⼀樣的
- ⼤型資料集訓練好的參數具有完整的顏⾊、線條 filters，從此參數開始，我們訓練在⾃⼰的資料集中，逐步把 filters 修正為適合⾃⼰資料集的結果。

### 參考大神們的網路架構

- 同學們對於要疊幾層 CNN，filters 數量要選擇多少？Stride, Pooling 等參數要設定多少？這些應該都很疑惑。
- 許多學者們研究了許多架構與多次調整超參數，並在⼤型資料集如ImageNet 上進⾏測試得到準確性⾼並容易泛化的網路架構，我們也可以從這樣的架構開始！

### 注意

- 以下的程式碼提供給有 GPU 且具有較⼤影像尺⼨資料集的同學參考，若沒有 GPU 的同學可以直接看本⽇的 jupyter notebook 程式碼學習即可
- Cifar-10 並不適合直接使⽤ transfer learning 原因是多數模型都是在ImageNet 上預訓練好的，⽽ ImageNet 影像⼤⼩為 (224,224,3)，圖像差異極⼤，硬套⽤的結果反⽽不好

### Transfer learning in Keras: ResNet-50

```python
from keras.application.resnet50 import ResNet50
resnet_model = ResNet50(input_shape=(224, 224, 3),
                       weights='imagenet', pooling='avg',
                       include_top=False)
```

- 我們使⽤了 ResNet50 網路結構，其中可以看到 weights='imagenet'，代表我們使⽤從 imagenet 訓練好的參數來初始化，並指定輸入的影像⼤⼩為 (224,224,3)
- pooling=avg 代表最後⼀層使⽤ [Global Average pooling](https://blog.csdn.net/Losteng/article/details/51520555)，把 featuremaps 變成⼀維的向量
- include_top=False 代表將原本的 Dense layer 拔掉，因為原本這個網路是⽤來做 1000 個分類的模型，我們必須替換成⾃⼰的 Dense layer 來符合我們⾃⼰資料集的類別數量

```python
last_featuremaps = resnet_model.output
flatten_featuremap = Flatten()(last_featuremaps)
output = Dense(num_classes)(flatten_featuremap)

New_resnet_model = Model(inputs=resnet_model.input, outputs=output)
```

- 上⼀⾴的模型我們已經設定成沒有 Dense layers，且最後⼀層做 GAP，使⽤resnet_model.output 我們就可以取出最後⼀層的 featuremaps

- 將其使⽤ Flatten 攤平後，再接上我們的 Dense layer，神經元數量與資料集的類別數量⼀致，重建立模型，就可以得到⼀個新的 ResNet-50 模型，且參數是根據 ImageNet ⼤型資料集預訓練好的

- 整體流程如下圖，我們保留 Trained convolutional base，並新建 New classifier(Dense 的部分)，最後 convolutional base 是否要 frozen (不訓練) 則是要看資料集與預訓練的 ImageNet 是否相似，如果差異很⼤則建議訓練時不要 frozen，讓 CNN的參數可以繼續更新

  ![](https://lh3.googleusercontent.com/V6DMVj5pAEL-4xYjWM8nR6wHbNKwtkr4dkfrkPSqhYeXFWpROWklf-OswBNFCWIYurKOL0hmrplua7rREzBzS68ReNTqYE4beK1EUBOkkVfSTRvcIUvDuXsXMoTBJJ9DdEtScjwc8xKGNjv0E2_37p4kldSPdWcQSyPePQCRy9cEVDAxkgFtarJh2q1QIY6ntCxvjsY3DS-zxD0bs-FnUCUOS3y-OIqQkNrCy1X5oJffUq6r4YujDJ61Z-Foq4alG90aqBeo2b9sSn-53hmSHXoZRHbHfxJCJaQxD45p-7fexqxt7wnfpCw5mlWS7KlsvE7eXvxIG0X_3rM2arEBm7tTexnhh6UpgAdA3yYX1-iNg2zWplTvhZPgrqhLmNr3OxiooQ9m0gNdEeFnZWkdgeXUtdho8efB5BAbh0bquY0fu1BxBRcOSkV1ntH_RHo0xL6fDm74SzT-cv8ECwvEQE719LXAqfs_pdaEL4blW1ble-QaneNOwn2sW5KLngtdls2JpBcuGQS7tD2lb8AX3xwyQDnz7maZ1tpVvncy0EmpWXcRaBDiIIFaPNBD9ss-rdjmJW7SizST3dGvpPLD6qZ0xeGPAGEDhyf0AXYZFvKp6Jf4yJNKAtyNDjDFx0yZgu1Y87eFmrY-O-Lf7x_MmN9FVYlQ_MfO_7MU6z0U_npQL95ZYNR23b0UiCcpRXPfrA6u5uTUcYEQrdksLsN4V0O1=w731-h494-no)

### 參考資料

1. [Keras 以 ResNet-50 預訓練模型建立狗與貓辨識程式](https://blog.gtwang.org/programming/keras-resnet-50-pre-trained-model-build-dogs-cats-image-classification-system/)
2. [Img feature extraction with pretrained Resnet](https://www.kaggle.com/insaff/img-feature-extraction-with-pretrained-resnet)

# 其他資料

- [我知道你会用Jupyter Notebook，但这些插件你都会了吗？](https://mp.weixin.qq.com/s/ipcV07BHWTIajx3LWfNWiw?fbclid=IwAR037b-x3TlM-OYQTpTKY1FZNm8wG6WX5B-RgwZNH49xrCjBvjfFlWhhON8)