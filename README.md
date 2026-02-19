# zt-report

`zt-report` is a shell script that builds a ZeroTier member status report with:

- alias/name
- node ID
- ZeroTier-assigned IP(s)
- online visibility
- authorization state
- time since last online (`days:hours:minutes`)
- client version

## Features

- Uses ZeroTier Central API for member metadata.
- Uses local `zerotier-cli` when available (`local+central` mode).
- Falls back to `central-only` mode when local daemon auth is not available.
- Marks currently online devices with `-` in the elapsed-time column.

## Requirements

- `bash`
- `curl`
- `jq`
- `column` (usually via `bsdmainutils`/`util-linux` depending on distro)
- `zerotier-cli` (optional for local+central mode)

## Setup

1. Place script in PATH (example):

```bash
mkdir -p ~/.local/bin
cp ./zt-report ~/.local/bin/zt-report
chmod +x ~/.local/bin/zt-report
```

2. Set ZeroTier Central token:

```bash
export ZEROTIER_CENTRAL_TOKEN="<your-central-api-token>"
```

Optional equivalents supported:

- `ZT_CENTRAL_TOKEN`
- `ZT_NETWORK_ID` (default network override)
- `ZT_ONLINE_WINDOW_SECONDS` (central-only online window; default `120`)
- `ZEROTIER_LOCAL_AUTHTOKEN` / `ZT_LOCAL_AUTHTOKEN` (local daemon auth token)

## Usage

Auto-detect network:

```bash
zt-report
```

Specific network:

```bash
zt-report <network_id>
```

## Local Auth Notes

On some Linux hosts, local `zerotier-cli` needs elevated access to `/var/lib/zerotier-one/authtoken.secret`.

If local auth fails, the script falls back to central-only mode when possible.

If you want local+central mode on such hosts, use either:

```bash
sudo --preserve-env=ZEROTIER_CENTRAL_TOKEN,ZT_CENTRAL_TOKEN ~/.local/bin/zt-report
```

or export local daemon token:

```bash
export ZEROTIER_LOCAL_AUTHTOKEN="$(sudo cat /var/lib/zerotier-one/authtoken.secret)"
zt-report
```

## Output Example

```text
ALIAS   NODE_ID      ZT_IPS         VISIBLE   AUTHORIZED   SINCE_LAST_ONLINE_D:H:M   CLIENT_VERSION
...     ...          ...            ...       ...          ...                         ...
```
