# Pull Request 指南

[English](PR_GUIDE.md)

本指南说明如何申请或更新免费的 `baka.life` 子域名。

## 提交前

请确认你申请的子域名用于科研、教育、学习、实验室、开源项目或其他非商业技术用途。

请不要提交用于滥用、钓鱼、垃圾信息、恶意软件、冒充他人或非法服务的记录。

## 初次注册

如果这是你的第一个 PR，需要同时添加 maintainer 对象和 domain 对象。

Maintainer 文件：

```txt
registry/mntner/YOURNAME-MNT
```

Maintainer 内容：

```txt
mntner: YOURNAME-MNT
descr: Your maintainer object

auth: github:your-github-username
```

Domain 文件：

```txt
registry/domain/your-name.baka.life
```

Domain 内容：

```txt
domain: your-name.baka.life
descr: My research or education project

mnt-by: YOURNAME-MNT

@ A 192.0.2.10
www CNAME @ proxied
```

初次注册需要维护者 review。

## 更新已有域名

当你的 maintainer 对象已经合并后，你可以更新由该 maintainer 管理的 domain。

满足以下条件时可以自动合并：

- PR 作者已经在 base 分支注册。
- PR 只修改 `registry/domain/*` 下的直接文件。
- 被修改的 domain 由 PR 作者有权限的 maintainer 管理。
- DNS 校验通过。

以下情况需要人工 review：

- 新增或修改 `registry/mntner/*`。
- 修改 workflow 或工具代码。
- 修改 `registry/domain/*` 以外的文件。
- 初次注册。

## 记录格式

通用格式：

```txt
<name> <type> <value...>
```

支持类型：

```txt
A AAAA CNAME TXT MX NS SRV CAA
```

示例：

```txt
@ A 1.1.1.1
@ AAAA 2606:4700:4700::1111
www CNAME @ proxied
@ TXT "v=spf1 -all"
@ MX 10 mail.example.com
_sip._tcp SRV 10 5 5060 sip.example.com
```

## 校验规则

- `domain:` 必须和文件名一致。
- `mnt-by:` 必须引用存在的 maintainer。
- `A` 必须是 IPv4 地址。
- `AAAA` 必须是 IPv6 地址。
- `CNAME` 必须指向域名，不能指向 IP。
- `CNAME` 不能指向自己。
- `CNAME` 不能和同名其他记录共存。
- `MX` priority 必须有效。
- `SRV` priority、weight、port 必须有效。
- `proxied` 只允许用于 `A`、`AAAA`、`CNAME`。

## 本地检查

如果你安装了 Go，可以在本地运行校验：

```bash
go -C registryctl run . validate --root ..
```

维护者可选运行测试：

```bash
go -C registryctl test ./...
```

## PR 状态

GitHub Actions 会在 PR 中评论以下结果之一：

```txt
Registry format check failed.
Registry format check passed. This PR requires maintainer review.
Registry format check passed. This PR is eligible for auto-merge.
```

如果校验失败，请查看 PR 状态评论。解析和校验错误会包含文件名、行号和原始行。
