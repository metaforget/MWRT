# MWRT — Agent Guide

## What This Repo Is

CI/CD configuration for building custom ImmortalWrt firmware. **No application source code.** Contains only Kconfig fragments, UCI defaults, and GitHub Actions workflows.

## Two Build Variants

| Variant | Workflow | Config | Tag Variable | Key Difference |
|---------|----------|--------|--------------|----------------|
| main | `mwrt-ci.yml` | `diffconfig` | `IMM_TAG` | Standard build |
| 2512 | `mwrt-2512.yml` | `diffconfig-2512` | `IMM_2512_TAG` | Adds Lua/LuCI compat layer (`liblua`, `luci-lua-runtime`, etc.) + `mihomo-meta` + timezone data |

## Key Files

- `diffconfig` / `diffconfig-2512` — Kconfig fragments copied to `.config` before `make defconfig`
- `99-custom` — UCI defaults script (sets root password, LAN IP `10.0.0.1`, disables flow offloading)
- `99-setip` — Interactive LAN IP change tool deployed to `/root/set-ip.sh`
- `.github/workflows/mwrt-ci.yml` — Main build workflow
- `.github/workflows/mwrt-2512.yml` — 2512 build workflow

## Build Flow (Both Variants)

1. Checkout this repo + `immortalwrt/immortalwrt` at the configured tag
2. Inject `LOGIN_PASSWD` into `99-custom` line 2 via `sed -i "2i LOGIN_PASSWD='$LOGIN_PASSWD'" 99-custom`
3. Copy `99-custom` and `99-setip` to `imm/files/etc/uci-defaults/`
4. Add custom feeds (bandix, nikki, openclash, luci-app-advanced) to `feeds.conf.default`
5. `scripts/feeds update -a && scripts/feeds install -a`
6. Copy `diffconfig` → `.config`, run `make defconfig`
7. `make download -j$(nproc)`, then `make -j$(nproc+1)`

## Gotchas

- `LOGIN_PASSWD` is injected at build time — `99-custom` in the repo has a placeholder, not the real password
- 2512 workflow disables `telephony` and `video` feeds; main does not
- Main workflow uses `ubuntu-22.04` runner; 2512 does aggressive disk cleanup first
- Artifact retention is 30 days
- All workflows are `workflow_dispatch` only (manual trigger)
