---
{"dg-publish":true,"permalink":"/sys-admin/recovering-from-a-pacman-interrupted-upgrade/","dgPassFrontmatter":true}
---

```bash
pacman -Qk 2>/dev/null | grep -v ' 0 missing files' | cut -d: -f1 |
    while read -r package; do
        sudo pacman -S "$package" --overwrite "*" --noconfirm
    done
```