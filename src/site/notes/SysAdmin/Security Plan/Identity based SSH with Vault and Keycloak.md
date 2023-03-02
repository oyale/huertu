---
{"created":"2023-03-02T23:00:09 (UTC +01:00)","tags":null,"source":"https://drpdishant.medium.com/identity-based-ssh-with-vault-and-keycloak-part-1-3-47ab2181ceae","author":"Dishant Pandya","dg-publish":true,"permalink":"/sys-admin/security-plan/identity-based-ssh-with-vault-and-keycloak/","dgPassFrontmatter":true}
---


[Identity based SSH with Vault and Keycloak. | Part 1/3 | by Dishant Pandya | Medium
](https://drpdishant.medium.com/identity-based-ssh-with-vault-and-keycloak-part-1-3-47ab2181ceae)
> ## Excerpt
> Part 1 of the comprehensive guide which focuses on individual aspects of the overall configuration. Following is the brief of what each part focuses on: You are using SSH, but are you using it right…

---
## Step by Step Guide for Configuring Vault + KeyCloak OIDC

![](https://miro.medium.com/max/1400/1*CZDZSiwJSQ5m0AY353icyw.gif)

SSH Signed Certificate Authentication | Check above slides [here](https://docs.google.com/presentation/d/1vEpBaS8643wr0xZ5jnZrzZ0eH_KIVuCWo9lxyNLXneI/edit?usp=sharing)

**Part 1 of the comprehensive guide which focuses on individual aspects of the overall configuration. Following is the brief of what each part focuses on:**

-   **Part 1 — Step by Step Guide for Configuring Vault + KeyCloak OIDC**
-   [Part 2 — Step By Step Guide for Configuring Vault SSH Secrets engine for Signed SSH Certificates](https://drpdishant.medium.com/identity-based-ssh-with-vault-and-keycloak-part-2-3-signed-ssh-certificate-c9fb2c4dde64)
-   Part 3 — Step By Step Guide for Configuring Vaut SSH Secrets engine for SSH OTP. (coming soon)

## Overview

You are using SSH, but are you using it right? Probably not! Chances are that you are using password based authentication or private key based authentication to access your servers. Although necessary to provide administrative access, these methods are non-reliable and insecure at scale.

Private Key based authentication is still widely popular, but many processes, applications and users access the servers, so that will end up with a long list of public keys in the authorized\_keys file of the server and it grows till it gets unmanageable, cleaned up and populated again. There’s need for an SSH Solution that is Dynamic, Scalable and Secure, something that uses Identity to Authenticate User, and authorize access against a set of policies, and most importantly, log the activity. We are going to use Hashicorp Vault to provide Authenticatation and Authorization user for SSH access, and Keycloak to demonstrate OIDC integration with Vault.

Use of identity to authorize SSH access will make it scalable and secure. You can simply allow/revoke access to set of servers by applying required set of policies to user, or simply delete the user when they leave the organization. It makes on-boarding of users even easier.

No more dangling private keys and long list if authorized public keys.

## Vault

Vault is a powerful secret management tool by Hashicorp, it provides api’s and tooling to store, manage and generate secrets securely. Its has built in secret engines for wide range of applications and services. SSH is one such powerful engine that we are going to use to dynamically generate signed certificates and OTP to access our SSH Hosts.

## Keycloak

KeyCloak or its enterprise downstream RedHat SSO is a powerful Identity Provider and Single Sign On Server by RedHat. Its found to be used by many large enterprises such as SalesForce for providing User Management, Authentication and Federation, including RedHat itself. We are going to use it to demonstrate OIDC authentication for vault.

## Setup

For this setup we are going to use vagrant to provision VM’s for Vault and SSH Host. Please make sure you have necessary packages installed for it to work.

## **Pre-Requisites:**

-   VirtualBox
-   Vagrant

Clone this [demo repository](https://gitlab.com/drpdishant/vault-ssh.git) to setup the test environment.

```
git clone https://gitlab.com/drpdishant/vault-ssh.git
```

## **Start Vagrant Machines:**

Go to the cloned directory and run vagrant up

```
cd vault-ssh && vagrant up
```

This will provision the VM’s for Vault Server, and 2 SSH Hosts.

1.  192.168.56.101 — Vault Server
2.  192.168.56.102 — Host 1
3.  192.168.56.103 — Host 2

## Configure Vault Server

Vault UI will be accesible on [https://192.168.56.101:8200/ui](https://192.168.33.11:8200/ui) , open it in browser to proceed with configuration.

**Initialize:**

On opening the ui for first time you’ll be taken to initialization page. Here you’ll need to generate master keys and root token. After initialization, vault is sealed, and Master Key Shares will be used to unseal it. Options are as following.

-   Key Shares: Number on Key Share to Split Master Key into.
-   Key Threshold: Number of unique key shared required to unseal vault. Cannot be greater than the number of Key Shares.

For our demo we’ll set both values to `1` . After clicking on `Initialize` masked Initial root token, and Master Keys will be displayed. Click on `Download keys` to download the json file containing keys and root token.

![](https://miro.medium.com/max/1400/1*t-CA8EXCgRq6UQ_fqyZCXg.gif)

**Unseal:**

Now we can proceed to unseal Vault using the key in the downloaded json.

Click on Continue to Unseal and Copy the master key part under the `keys` block in json.

![](https://miro.medium.com/max/1400/1*NIibCcZiF9M7mNfyFfIP-g.gif)

**Sign In:**

In the next step use the root\_token to sign in to vault.

![](https://miro.medium.com/max/1400/1*yAsebvw5zTsIgTuqlUn8nw.gif)

## Initialize KeyCloak Server

KeyCloak Server is available at [http://192.168.56.101:8080](http://192.168.56.101:8080/), open it in browser to login. Username and Password combination is `admin/admin`

1.  **Configure Vault Client in KeyCloak:**

Download the vault.json file containing client configuration from [here](https://gitlab.com/drpdishant/vault-ssh/-/raw/master/vault.json).

In **_KeyCloak Admin Console_**, Goto **_Clients_ > _Create_** . Import the downloaded vault.json to configure the vault OIDC client in KeyCloak.

![](https://miro.medium.com/max/1400/1*r5Y7iMrRDliCMtCQg8bfbA.gif)

2\. **Create a test user ‘testing’ in Keycloak:**

Go to _Users_ and click on add user, and create configure the user as follows:

-   **Username:** testing
-   **Email:** testing@example.com
-   **First Name:** Test
-   **Last Name:** User
-   **User Enabled:** On
-   **Email Verified:** On

![](https://miro.medium.com/max/1400/1*EJxv40NOklWhFzwDAYttgQ.gif)

Then go to the Credentials tab for the created user, and Set a new password for user i.e `testing123` and set ‘**Temporary**’ to ‘OFF’. We’ll use this user to login to vault using oidc method.

![](https://miro.medium.com/max/1400/1*aI9zWE2D8o22yMBLLYUBoQ.gif)

## **Create Admin Policy on Vault:**

**Access Vault Server and Login to Vault CLI:**

Get SSH Console into vault server vagrant machine by running

```
vagrant ssh vault
```

Configure `VAULT_SKIP_VERIFY` environment to skip tls verfication as we are using self signed TLS certificate in our demo environment.

```
export VAULT_SKIP_VERIFY=true
```

add it to `~/.bashrc` to set it automatically on startup. Now proceed with vault cli login. Use the vault root token generated during init.

```
export VAULT_TOKEN=s.WzCfvOHa0Dz1W11NkOHkFYLVecho $VAULT_TOKEN | vault login -Success! You are now authenticated. The token information displayed belowis already stored in the token helper. You do NOT need to run "vault login"again. Future Vault requests will automatically use this token.Key                  Value---                  -----token                s.WzCfvOHa0Dz1W11NkOHkFYLVtoken_accessor       qbWsjwwywUJU7Sw0CRoWHlSBtoken_duration       ∞token_renewable      falsetoken_policies       ["root"]identity_policies    []policies             ["root"]
```

**Create Admin Policy:**

Run the following command to create the admin policy, which we’ll use as default in our oidc configuration

```
vault policy write admin /vagrant/admin.hcl Success! Uploaded policy: admin
```

## **Configure OIDC Auth:**

1.  Enable OIDC Auth Method:

```
vault auth enable oidc
```

2\. Set KC\_DOMAIN, KC\_CLIENT\_ID and KC\_CLIENT\_SECRET to configure _oidc\_discovery\_url_ , _oidc\_client\_id_ and _oidc\_client\_secret_ respectively:

```
export KC_DOMAIN=http://192.168.56.101:8080/realms/master
```

-   To get values for KC\_CLIENT\_ID and KC\_CLIENT\_SECRET from KeyCloak vault client we previously created:

![](https://miro.medium.com/max/1400/1*IzeRb-3B6IUDhY6vK-7edw.gif)

-   For **KC\_CLIENT\_ID**, goto clients > vault > settings tab, and copy the client id

```
export KC_CLIENT_ID=vault
```

-   For **KC\_CLIENT\_SECRET,** goto clients > vault > credentials tab, and copy the Secret

```
export KC_CLIENT_SECRET=3ce2a23d-681e-4804-affe-a4214195a4d2
```

-   Once the variables are set run:

```
vault write auth/oidc/config \        oidc_discovery_url="$KC_DOMAIN" \        oidc_client_id="$KC_CLIENT_ID" \        oidc_client_secret="$KC_CLIENT_SECRET" \        default_role="default"
```

3\. Now we’ll create the _default_ role for oidc

```
export VAULT_UI=https://192.168.56.101:8200export VAULT_CLI=https://127.0.0.1:8250vault write auth/oidc/role/default \allowed_redirect_uris="${VAULT_UI}/ui/vault/auth/oidc/oidc/callback" \allowed_redirect_uris="${VAULT_CLI}/oidc/callback" \user_claim="email" \policies="admin"
```

4\. Once that is done we’ll use oidc to login to vault ui.

Open vault ui at [https://192.168.33.11:8200/ui,](https://192.168.33.11:8200/ui) under Method drop-down select **‘OIDC’,** leave **Role** blank and we are assiging default role, and click **Sign In.** You will be redirected to Keycloak Login Page, enter your credentials here, and Login.

Upon successful login, you will be taken to vault and logged as OIDC user testing, with admin policy attached which we have attached with the default OIDC Role. To know much more about Vault OIDC auth check this link.

![](https://miro.medium.com/max/1400/1*1otcVUaw3KVtkucPk85oiA.gif)

Login to Vault using Keycloak

Now that we are able to Successfully Login to Vault using KeyCloak, let’s conclude this guide, work is in progress for Part 2, will be soon publishing it.
