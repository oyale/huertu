---
{"type":"web","url":"https://www.plothost.com/kb/configure-mod-security-detection-only/","dg-publish":true,"permalink":"/sys-admin/security-plan/mod-security/configure-mod-security-for-detection-only/","dgPassFrontmatter":true}
---

Do you want to use ModSecurity in a transparent mode? Meaning that no actions will be performed? You can use the SecRuleEngine command. The syntax is:

```
SecRuleEngine On|Off|DetectionOnly
```

> The possible values are:  
> **On**: process rules  
> **Off**: do not process rules  
> **DetectionOnly**: process rules but never executes any disruptive actions (block, deny, drop, allow, proxy and redirect)
> 
> ModSecurity Wiki

For the transparent mode, where only no disruptive actions will be applied, use:

```
SecRuleEngine DetectionOnly
```

Put this directive at the top of the ModSecurity configuration file.

If you want to have a log, add:

```
SecAuditLog <path to the log file>
```

```
SecAuditLog /tmp/modsec_audit.log
```

Don’t forget to restart your web server.

![modsec logo](https://www.plothost.com/wp-content/uploads/2021/11/modsec-logo.webp)



**References:**  
[Modsecurity wiki](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual-(v2.x)#secruleengine)