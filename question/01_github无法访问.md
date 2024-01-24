# github无法访问

通过配置hosts来避免DNS污染导致的无法访问github：

1. DNS查询，输入 github.com：[https://tool.chinaz.com/dns/](https://tool.chinaz.com/dns/)

   ![image-20240124125533357](images/image-20240124125533357.png) 

2. 修改系统的hosts文件：

   ```shell
   # 文件位置：C:\Windows\System32\drivers\etc\hosts
   # 添加记录
   140.82.121.4 github.com
   ```

3. 刷新DNS：

   ```shell
   # 在cmd窗口执行
   ipconfig /flushdns
   ```

