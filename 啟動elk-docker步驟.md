- 步驟如下
vscode shift + p 搜尋 ssh , 新增10.1.10.19, 密碼!QAZ2wsx 
1.      登入 10.1.10.19，帳密私下給
2.      cd dev-devops/elk-cluster
3.      sudo sysctl -w vm.max_map_count=262144  # 重開機就要重下
4.      docker-compose up -d