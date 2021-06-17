## ElasticSearch - term 和 match 的差別
- ES 中的 term 和 match 牽扯到了分詞器、mapping、倒排索引等，如果不熟悉相關知識，請先看 ElasticSearch - index mapping（5.x以上） 這篇文章
    - term 是直接把 field 拿去查詢倒排索引中確切的 term
    - match 會先對 field 進行分詞操作，然後再去倒排索引中查詢

- 具體實例
    - 假設有一個字段 nickname，存放的類型是 text，因此當新增一筆文檔時，內容會被分詞器分詞，然後才儲存進倒排索引
    - 假設插入了一筆文檔，其中 "nickname": "1 hello"，分詞過後變為 1、hello，因此倒排索引中儲存的是兩筆索引 1 和 hello，而不是一筆索引 1 hello
    - 使用 match 做查詢
        - "match": "1-hello" : 成功，因為 match 把 1-hello 分詞成 1、hello，而 1 和 hello 都存在在倒排索引中，所以不只能夠查到，_score 還很高
        - "match": "1" : 成功，1 被分詞過後還是 1，而 1 存在在倒排索引中
        - "match": "hello how r u" : 成功，match 把 hello how r u 分詞成 hello、how、r、u，而 hello 存在在倒排索引中
    - 使用 term 做查詢
        - "term": "2222-hello : 失敗，倒排索引只有 1、hello，並沒有 2222-hello
        - "term": "hello" : 成功，因為倒排索引中有 hello
        - "term": "1 hello" : 失敗，雖然 1 hello 和我們當時插入的數據一模一樣，但是因為倒排索引在建立索引時把原始的數據分詞了才儲存進索引，裡面存的是 1 和 hello，並沒有存放 1 hello，因此查詢失敗
