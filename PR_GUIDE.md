# Pull Request Guide

[中文说明](PR_GUIDE.zh-CN.md)

This guide explains how to request or update a free `baka.life` subdomain.

## Before You Submit

Make sure your requested subdomain is for research, education, learning, labs, open-source work, or another non-commercial technical purpose.

Do not submit records for abuse, phishing, spam, malware, impersonation, or illegal services.

## First-Time Registration

If this is your first PR, add a maintainer object and a domain object.

Maintainer file:

```txt
registry/mntner/YOURNAME-MNT
```

Maintainer content:

```txt
mntner: YOURNAME-MNT
descr: Your maintainer object

auth: github:your-github-username
```

Domain file:

```txt
registry/domain/your-name.baka.life
```

Domain content:

```txt
domain: your-name.baka.life
descr: My research or education project

mnt-by: YOURNAME-MNT

@ A 192.0.2.10
www CNAME @ proxied
```

First-time registration requires maintainer review.

## Updating An Existing Domain

After your maintainer object is merged, you may update domains managed by your maintainer.

Auto-merge is possible only when:

- The PR author is already registered in the base branch.
- The PR only changes direct files under `registry/domain/*`.
- The changed domains are managed by a maintainer authorized for the PR author.
- DNS validation passes.

Manual review is required when:

- You add or edit `registry/mntner/*`.
- You change workflow files or tooling.
- You change files outside `registry/domain/*`.
- You are registering for the first time.

## Record Format

General format:

```txt
<name> <type> <value...>
```

Supported types:

```txt
A AAAA CNAME TXT MX NS SRV CAA
```

Examples:

```txt
@ A 1.1.1.1
@ AAAA 2606:4700:4700::1111
www CNAME @ proxied
@ TXT "v=spf1 -all"
@ MX 10 mail.example.com
_sip._tcp SRV 10 5 5060 sip.example.com
```

## Validation Rules

- `domain:` must match the filename.
- `mnt-by:` must reference an existing maintainer.
- `A` must contain an IPv4 address.
- `AAAA` must contain an IPv6 address.
- `CNAME` must point to a domain name, not an IP address.
- `CNAME` cannot point to itself.
- `CNAME` cannot coexist with other record types at the same name.
- `MX` priority must be valid.
- `SRV` priority, weight, and port must be valid.
- `proxied` is only allowed for `A`, `AAAA`, and `CNAME`.

## Local Check

You can run validation locally if Go is installed:

```bash
go -C registryctl run . validate --root ..
```

Optional tests for maintainers:

```bash
go -C registryctl test ./...
```

## PR Status

GitHub Actions will comment on your PR with one of these results:

```txt
Registry format check failed.
Registry format check passed. This PR requires maintainer review.
Registry format check passed. This PR is eligible for auto-merge.
```

If validation fails, check the PR status comment. Parser and validation errors include filename, line number, and the original line.
