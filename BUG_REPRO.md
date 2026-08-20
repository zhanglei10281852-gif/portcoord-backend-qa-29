# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

存储层查不到实体时向上返回了数据库原始错误，业务层无法识别统一的 not_found。请修复错误翻译链。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/portcoord-backend-qa-29
- 仓库地址：https://github.com/zhanglei10281852-gif/portcoord-backend-qa-29.git
- parent SHA：ba355a84c9302d07da7f3c6e597164b44119fb3a

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/portcoord-backend-qa-29.git bug-repro
cd bug-repro
git checkout --detach ba355a84c9302d07da7f3c6e597164b44119fb3a
go test ./internal/store -run "^TestStore_NotFoundTranslationFromStorage$" -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/store -run "^TestStore_NotFoundTranslationFromStorage$" -count=1
--- FAIL: TestStore_NotFoundTranslationFromStorage (0.01s)
    notfound_test.go:28: expected domain.IsNotFound, got scan declaration failed: sql: no rows in result set
FAIL
FAIL	portcoord/internal/store	0.015s
FAIL

```

stderr：

```text
warning: internal/store/concurrent_test.go has type 100755, expected 100644
warning: internal/store/notfound_test.go has type 100755, expected 100644
warning: internal/store/store_test.go has type 100755, expected 100644
warning: internal/store/concurrent_test.go has type 100755, expected 100644
warning: internal/store/notfound_test.go has type 100755, expected 100644
warning: internal/store/store_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/store -run "^TestStore_NotFoundTranslationFromStorage$" -count=1
--- FAIL: TestStore_NotFoundTranslationFromStorage (0.27s)
    notfound_test.go:28: expected domain.IsNotFound, got scan declaration failed: sql: no rows in result set
FAIL
FAIL	portcoord/internal/store	0.530s
FAIL

```

stderr：

```text
warning: internal/store/concurrent_test.go has type 100755, expected 100644
warning: internal/store/notfound_test.go has type 100755, expected 100644
warning: internal/store/store_test.go has type 100755, expected 100644
warning: internal/store/concurrent_test.go has type 100755, expected 100644
warning: internal/store/notfound_test.go has type 100755, expected 100644
warning: internal/store/store_test.go has type 100755, expected 100644

```

## 通过条件

在题面触发条件下，公开行为必须恢复且原始异常不再出现；定向命令 go test ./internal/store -run ^TestStore_NotFoundTranslationFromStorage$ -count=1、相关包测试、全量测试、race、vet 和 build 必须通过；不得删除或跳过测试，也不得绕过目标逻辑。
