---
{"dg-publish":true,"topics":"Odoo modules","permalink":"/odoo/non-oca-modules/modules/mail-server-filter/","dgPassFrontmatter":true}
---


![](https://www.odoo-wiki.org/assets/icon_oms_box-61bea3f9.png)

Filter incoming and outgoing mail servers by means of database names.

Technical name: `mail_server_filter`  
Repository: [https://github.com/Mint System/Odoo-Apps-Server-Tools/tree/16.0/mail-server-filter](https://github.com/Mint-System/Odoo-Apps-Server-Tools/tree/16.0/mail_server_filter)

## Use

### Set database filter for outgoing mail server

Navigate by _Settings > Technical > Outgoing Mail Server_ and open an entry. In the _Database Filter_ field, you can enter the name of the database or comment separated several database names. If a value is set on this field, when sending e-mails, it is checked whether the database name is contained in the outgoing mail server.


> [!TIP] 
> The filter is also used for the scheduled action to retrieve the available e-mails.



> [!WARNING] 
> If an email is sent but no outgoing mail server can be used, Odoo throws the error message `Connection failed (outgoing mail server problem)`.



### Set database filter for incoming mail servers

Navigate by _Settings > Technical > Incoming Mail Server_ and open an entry. In the _Database Filter_ field, you can enter the name of the database or comment separated several database names. If a value is set to this field, when e-mails are received, it is checked whether the database name is contained in the incoming mail server.


> [!TIP] 
> Other filters such as the _From Filter_ is checked after the _database filter_.

