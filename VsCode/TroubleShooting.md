# 常见问题

### Go开发时，golang提示gopls currently requires one module per workspace folder

* 在`settings.json` 中添加如下配置：

  ```json
  "gopls": {
      "experimentalWorkspaceModule": true
  },
  ```

  