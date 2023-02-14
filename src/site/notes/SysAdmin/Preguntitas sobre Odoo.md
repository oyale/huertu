---
{"dg-publish":true,"topics":"Odoo","permalink":"/sys-admin/preguntitas-sobre-odoo/","dgPassFrontmatter":true}
---

- Why not use git when installing Odoo and its modules?
	- ➕ Cowboy code sucks, but it happens. At least, we'd have traceability.
	- ✅ We still can have pinned versions.
	- ❓ You're the same guy behind that sticker that said 'Cause with pip you can git…', huh?
		- Yes, that was me. And if I was mistaken, I'll feel morally obligated to issue a new (patch) sticker. I promise it.
---
-  Why not run two separate Odoo processes when testing and prod db's share machine?
	- ➕ This way we'd be able to restart only one database (useful when updating modules, for example)
	- ➖ Resources.
		- ➕ But… we can start the test process on demand. Or limit the resource consumption of the second instance.