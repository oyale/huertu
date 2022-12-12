---
{"type":"web","url":"https://scribe.citizen4.eu/building-goalwise/how-to-implement-modsecurity-waf-with-nginx-15fdd42fa3","dg-publish":true,"permalink":"/sys-admin/security-plan/mod-security/how-to-implement-mod-security-waf-and-nginx/","dgPassFrontmatter":true}
---

Almost a third of world’s websites use NGINX web server and this number is growing as we speak. The reason more and more organisations are choosing NGINX as the go to web server is simple. It delivers good performance and is lightweight but robust at the same time.

![](https://cdn-images-1.medium.com/fit/c/800/533/0*uPpnnhBPVhn9YakX)✍︎Photo by [Jordan Harrison](https://unsplash.com/@aligns?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

### Web Application Firewall (WAF)

What is a web application firewall and how is it different from a network firewall? A network firewall monitors and controls incoming and outgoing network traffic based on pre-determined rules. Usually a request that is a malicious request or an attack is going to look like proper traffic to a network firewall. There are next-gen network firewalls that are application aware but they come as part of the infrastructure stack and not the application stack. A web application firewall is going to protect against application level attacks as part of the application stack. As the application is developed and tested, the WAF policies and rules are going to be part of the process and is going to get deployed with the stack. WAF as part of Layer 7 or HTTP Layer security is going to inspect the HTTP traffic and depending on the rules is going to alert, log or block the request.

### When to use WAF?

1.  Compliance requirement.
2.  Buffer for development team. If there are any vulnerabilities found they can be mitigated at the WAF level immediately while the development team can change the code and patch the issue.

### What is ModSecurity?

This was developed back in 2002 as an Apache module by a sole author Ivan Ristić. Around 2012 the licensing for this module was changed and this allowed modules to be developed for servers like NGINX and IIS. As ModSecurity module has been around for a while now there has been different versions released for it. The latest version (at the time of writing this blog) v3 has considerable performance boost compared the older version. It is also available as dynamic module which means you don’t need to compile NGINX again with this module but just the module can be compiled and plugged into the web server.

![](https://cdn-images-1.medium.com/fit/c/774/692/1*Y2e-23K7JJCecu9lsQasew.png)✍︎Major difference in ModSecurity + Nginx architecture between v3 and earlier versions

### Installing ModSecurity v3

We will be delving into the details of installing this module on AWS EC2 with Amazon Linux. This blog assumes that you already have NGINX installed which is up and running and you have a basic knowledge of NGINX configuration files.

**Note:** NGINX version 1.11.5 or later is required.

### Prerequisite Packages

If you had compiled NGINX from source earlier on the server it is possible that all the packages are already present on the server.

```
yum install gcc make automake curl curl-devel httpd-devel autoconf libtool pcre pcre-devel libxml2 libxml2-devel
```

### Compile ModSecurity v3 module from source

There are two parts to code compilation. Compilation of ModSecurity code as a dynamic module for NGINX. Compilation of the connector which will link the dynamic module to the web server running. Because of the modular nature of ModSecurity project to plug it into different servers you just need to compile the corresponding connector for that server. There are separate connectors available for NGINX, Apache and IIS that I know of.

Compiling ModSecurity code

```
$ git clone --depth 1 -b v3/master --single-branch https://github.com/SpiderLabs/ModSecurity
```

```
$ cd ModSecurity 
$ git submodule init 
$ git submodule update 
$ ./build.sh 
$ ./configure 
$ make 
$ make install
```

```
During the process of installation it is safe to ignore the below message.
fatal: No names found, cannot describe anything.
```

```
Depending on the machine the compilation should be complete in about 12-15 mins.
```

Compile ModSecurity connector for NGINX as a dynamic module

```
$ git clone --depth 1 https://github.com/SpiderLabs/ModSecurity-nginx.git
```

Though only dynamic module is going to be compile complete source code of Nginx is required to compile the modules.

Determine which version of Nginx is installed and the source code for that specific version from the official site.

```
$ nginx -v
nginx version: nginx/1.14.1
```

```
Get the source code
$ wget http://nginx.org/download/nginx-1.14.1.tar.gz
$ tar -xvzmf nginx-1.14.1.tar.gz
```

Now that we have all the code lets compile the modules. There is a catch to compiling the module. The modules should be compiled with the same params as the nginx on your server was compiled. You will be able to list all the original configure arguments using `nginx -V` command.

```
$ cd nginx-1.14.1.tar.gz
$ ./configure <original configuration here> --add-dynamic-module=../ModSecurity-nginx
$ make modules 
$ cp objs/ngx_http_modsecurity_module.so /etc/nginx/modules
```

Couple of things to note in the above compilation.

-   You can remove the `--with-xxxxx-module=dynamic` options from the original configuration if the dependencies are not available.
-   When copying the shared object (.so) file you need to make sure you have copied it to the correct folder on your server. The path mentioned in the above command is general location but this may change based on the server configuration.

### Load ModSecurity dynamic module in Nginx

Add the following load\_module directive the top-level configuration file at **/etc/nginx/nginx.conf** file. It instructs NGINX to load ModSecurity dynamic module while loading the configurations.

```
load_module modules/ngx_http_modsecurity_module.so;
```

![](https://cdn-images-1.medium.com/fit/c/800/355/1*Sx3K9dtm8Cy9mYWJMc5jgA.png)✍︎Placing dynamic module before any section starts in the configuration.

If while loading the module you come across an error which is along the lines “Failed to load locate the unicode map file from: unicode.mapping” you can follow this [thread](https://github.com/SpiderLabs/ModSecurity/issues/1941). What basically you need to do is copy [this file](https://github.com/SpiderLabs/ModSecurity/blob/49495f1925a14f74f93cb0ef01172e5abc3e4c55/unicode.mapping) at **/etc/nginx/modsec/unicode.mapping**

### Configure ModSecurity module

It is cleaner to have a separate folder where all the modSecurity configuration files can be saved. Here we will be using a standard recommendation for configuration file from TrustWave SpiderLabs the current maintainer of ModSecurity project.

```
$ mkdir /etc/nginx/modsec 
$ wget -P /etc/nginx/modsec/ https://raw.githubusercontent.com/SpiderLabs/ModSecurity/v3/master/modsecurity.conf-recommended 
$ mv /etc/nginx/modsec/modsecurity.conf-recommended /etc/nginx/modsec/modsecurity.conf
```

By default the configuration in this file is set to **DetectionOnly** which means the malicious requests will be detected and logged but will not be dropped. Change the directive to actively drop requests. I would recommend doing this on a staging instance and testing the application before making it live on any production instance.

![](https://cdn-images-1.medium.com/fit/c/726/256/1*DwHA52GEkrbsq6c-carXSw.png)✍︎Configuring the engine to actively drop malicious request.

For purpose of this blog we will configure couple of simple rule. Put the following text in **/etc/nginx/modsec/main.conf**:

```
Include "/etc/nginx/modsec/modsecurity.conf"
```

```
# Basic test rule
```

```
SecRule ARGS:blogtest "@contains test" "id:1111,deny,status:403"
```

```
SecRule REQUEST_URI "@beginsWith /admin" "phase:2,t:lowercase,id:2222,deny,msg:'block admin'"
```

_Rule 1_ says if there is a query parameter named “blogtest” with a value “test” in it drop the request.

_Rule 2_ says if there is a URI which begins with admin drop the request.

In production environment you would probably want to have better rules which actually prevent malicious requests. [OWASP Core Rule Set](https://www.nginx.com/resources/admin-guide/nginx-plus-modsecurity-waf-owasp-crs/) is probably a good place to begin.

### Enabling ModSecurity Module

Till now all we have done is install the module, ask NGINX to load it and set up some configuration for the module. This does not mean that the servers that you have configured will now start making use of the configurations. We need to enable this module for the hosts that we want covered by adding the following two directives:

```
server {
    # Other server directives here
    modsecurity on;
    modsecurity_rules_file /etc/nginx/modsec/main.conf;
}
```

It is not necessary to enable this module for all locations on the server. You can also have a configuration which says only enable on certain paths while not on others. I am not sure why you would want to do that but it actually depends on your use case.

```
server {
    listen 443;
    # modsecurity on; # All traffic over 443 have ModSec on
 
    location / {
       proxy_pass http://127.0.0.1:8080;
       modsecurity off; # ModSec off by default
    }
```

```
    location /waf {
       proxy_pass http://127.0.0.1:8080;
       modsecurity on;
       modsecurity_rules_file /etc/nginx/modsec/main.conf;
       error_log /var/log/nginx/waf.log;
    }
}
```

**Testing the configuration**

Let’s run some curl requests to test our simple rules created earlier.

```
$ curl http://localhost/adminaccess
<html>
<head><title>403 Forbidden</title></head>
<body bgcolor="white">
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.14.1</center>
</body>
</html>
```

```
$ curl http://localhost/admin-login
<html>
<head><title>403 Forbidden</title></head>
<body bgcolor="white">
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.14.1</center>
</body>
</html>
```

```
$ curl https://localhost?blogtest=thisistestparam
<html>
<head><title>403 Forbidden</title></head>
<body bgcolor="white">
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.14.1</center>
</body>
</html>
```

![](https://cdn-images-1.medium.com/fit/c/800/53/1*ZueEEjlxMH-9Sl0yyMnNrQ.png)✍︎The request and the reason it is dropped being logged.

### Conclusion

There is no such thing as enough security. Having security at different layers mitigates the threat to a larger extent than having a single level of security. Web Application Firewalls acts as the last line of defence against a malicious attack. ModSecurity is an open source project which combines seamlessly with NGINX and also has the capability to apply OWASP core rule sets. This makes it a good place to start securing your applications.