# 工程快照

这里存放 EasyEDA Pro 本地工程文件的**只读快照**，用于防止源文件被误删导致设计丢失。

## 文件

- `esp32-s3-eink-hardware.eprj2` —— 工程本体（SQLite 数据库），固定文件名，每次快照覆盖更新，历史版本由 git 保存。

## 源文件位置（不在本仓库内）

```
C:\Users\Admin\Documents\LCEDA-Pro\projects\esp32-s3-eink-hardware.eprj2
```

EasyEDA Pro 半离线模式的本地工程都放在这个目录，**不在本 git 仓库里**。所以本目录的快照是这个工程唯一进版本库的副本。

## 如何还原

1. 关闭 EasyEDA Pro 客户端（避免文件占用与覆盖冲突）
2. 把本目录的 `.eprj2` 复制回上面的源文件位置，覆盖同名文件
3. 重新打开客户端，工程即恢复为快照时的状态

## 更新流程

每完成一个阶段性成果（例如画完一个功能模块）后：

1. 在 EasyEDA Pro 中 `Ctrl+S` 保存
2. 用只读方式复制到本目录（先校验是有效 SQLite 再覆盖）
3. `git add snapshots/ && git commit && git push`

`.gitattributes` 已把 `*.eprj2` 标为 `binary`，不做换行转换。
