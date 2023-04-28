---
{"dg-publish":true,"permalink":"/sys-admin/color-tail-output-in-odoo-logs/","dgPassFrontmatter":true}
---

![Pasted image 20230301163617.png](/img/user/SysAdmin/attachments/Pasted%20image%2020230301163617.png)
### Create an alias for sed
```bash
alias ct='sed --unbuffered -e "s/\(.*INFO.*\)/\o033[32m\1\o033[39m/" -e "s/\(.*ERROR.*\)/\o033[31m\1\o033[39m/" -e "s/\(.*CRITICAL.*\)/\o033[33m\1\o033[49m/" -e "s/\(.*DEBUG.*\)/\o033[30m\1\o033[49m/" -e "s/\(.*WARN.*\)/\o033[35m\1\o033[49m/"'
```
Customize it by changing regular expressions and font and background colors.

**Fixed** part: `sed --unbuffered -e`
**Regex** part: `s/\(.*INFO.*\)/`
**Color** part: `\o033[32m\1\o033[39m/`, where the first part (until the `1` is for font color and second one is for background color)

#### Color codes
| Color | Font | Background |
| --- | --- | --- |
| Black | \\033\[30m | \\033\[40m |
| Red | \\033\[31m | \\033\[41m |
| Green | \\033\[32m | \\033\[42m |
| Orange | \\033\[33m | \\033\[43m |
| Blue | \\033\[34m | \\033\[44m |
| Magenta | \\033\[35m | \\033\[45m |
| Cyan | \\033\[36m | \\033\[46m |
| Light gray | \\033\[37m | \\033\[47m |
| Use default | \\033\[39m | \\033\[49m |


### Usage
```bash
tail [options] file | ct
```
## Sources
From Judith Roth @ [makandra](https://makandracards.com/makandra/476071-bash-how-to-use-colors-in-your-tail-output)

---

**ct stands for 'color this', 'color tail', 'confusion troll' or whatever. Don't use this alias if you plan to work on Flatcar Container Linux*