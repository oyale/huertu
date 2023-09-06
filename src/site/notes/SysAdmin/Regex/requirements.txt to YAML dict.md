---
{"dg-publish":true,"topics":"Regex, Odoo, Python, YAML","permalink":"/sys-admin/regex/requirements-txt-to-yaml-dict/","dgPassFrontmatter":true}
---

```
^odoo12-addon-([[A-z0-9-]*).*=.*
```
Substitute with
```
    - $1
```
Now, replace the `-` with a `_`
