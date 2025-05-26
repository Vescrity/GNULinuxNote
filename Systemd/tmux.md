# Tmux 保活

一些 systemd 启动的桌面会话会在退出时将其启动的 tmux 也结束。需要这样启动：
```bash
systemd-run --user --scope ....
```

同时要为用户配置：
```bash
loginctl enable-linger
```
