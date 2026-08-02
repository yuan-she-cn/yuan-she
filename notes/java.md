# Java

## 远程调试

### 开启远程调试

**jib-maven-plugin**

```bash shell
-e JAVA_TOOL_OPTIONS="-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005" \
-p 5005:5005 \
```

### IDE 连接调试

**VS Code**

1. 打开运行调试面板（Ctrl+Shift+D）
2. 创建 launch.json 文件（create a launch.json file）
3. 选择 Java
4. 添加配置（configurations[]）

```json
{
  "name": "远程调试",
  "type": "java",
  "request": "attach",
  "hostName": "127.0.0.1",
  "port": 5005
}
```

5. 选择 远程调试，点击 启动（运行调试面板顶部）
