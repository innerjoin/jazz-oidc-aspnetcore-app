Setup Docs
================

# Setup Docs
Based on https://andrewlock.net/an-introduction-to-openid-connect-in-asp-net-core/
--> Ensure that you click the 'user secrets manager' link

IMPORTANT! JAS seems to have a bug in 6.0.3 whereas the JWT Url throws an NPE. I recommend to use 6.0.4 M5 instead!

## IM (Installation Manager)
Download: https://jazz.net/downloads/ibm-installation-manager/releases/1.8.6/agent.installer.win32.win32.x86_64_1.8.6000.20161118_1611.zip
Installation: Simply follow the wizzard

## JAS
### Download & Setup
Download from: https://jazz.net/downloads/rational-team-concert/releases/6.0.3/JazzAuthServer-offering-repo-6.0.3.zip
Follow the wizzard to install JAS

### Patch JAS
As for 6.0.3 and 6.0.4 RC1, there was an issue where JWK was not enabled by default. To fix this, please change the `openidConnectProvider` tag under `<JAS>\wlp\usr\servers\jazzop\appConfig.xml` to

> <openidConnectProvider id="jazzop" oauthProviderRef="JazzOP"
sessionManaged="true" signatureAlgorithm="RS256" jwkEnabled="true"/>

### Run JAS
 - open command line as administrator and navigate to the folder where JAS is installed
 - execute `start-jazz.bat`
 - Verify that the server is running:
	- https://host.example.com:9643/oidc/endpoint/jazzop/.well-known/openid-configuration
	- https://localhost:9643/oidc/endpoint/jazzop/.well-known/openid-configuration
 - Verify that the user registry is configured correctly:
	- https://host.example.com:9643/oidc/endpoint/jazzop/registration
	- https://localhost:9643/oidc/endpoint/jazzop/registration
 - 

### JAS Login
Installation of Jazz Authorization Server creates a directory that is named JazzAuthServer in the installation location. The JazzAuthServer_install_dir/wlp/usr/servers/jazzop/defaults directory includes sample files for both a basic, file-based user registry and an LDAP user registry.
 - The localUserRegistry.xml template file defines only the ADMIN and clmadmin users, with passwords the same as the user IDs.
 - The ldapUserRegistry.xml file is a template for an LDAP registry configuration.
[Source](https://www.ibm.com/support/knowledgecenter/SSJJ9R_6.0.1/com.ibm.jazz.install.doc/topics/t_jsasso_jas_user_mgmt.html)

### Install Certificate
 You will receive a Certificate warning while requesting JAS URLs. Accept the certificate exception, view the browsers certificate store, export the IBM certificate issued to localhost and install it into your local machine
 - Start > "Manage Computer Certificates" (also available in the control panel)
 - Right-click on "Trusted Root Certification Authoritites" > "All tasks" > "Import"
 - Browse to the crt file and then keep pressing "Next" to complete the wizard
 - Restart Docker for Windows
IF the user under which IIS runs is different from the local user, do the following:
1. Start > type mmc in the run prompt
2. This will bring up Microsoft Management Console.
3. Select File > Add/Remove Snap-In
4. Select Certificates > Add
5. Select Computer Account
6. Select Local Computer
7. Select Finsh and OK
[Source](https://answers.microsoft.com/en-us/windows/forum/windows_xp-security/certmgrmsc-add-certificate-for-all-users-on-a/8992e277-9ab8-439b-8559-226ae9518ea0)

DOCS:
https://www.ibm.com/support/knowledgecenter/SSEQTP_8.5.5/com.ibm.websphere.wlp.doc/ae/twlp_client_registration.html?cp=SSEQTP_8.5.5

HTTP POST:
https://localhost:9643/oidc/endpoint/jazzop/registration

**Headers**<br/>
Content-Type: application/json<br/>
Accept: application/json<br/>

Content:
```json
{ 
  "client_name":"OIDC Test",
  "scope": "ALL_SCOPES",
  "redirect_uris": [ 
      "http://localhost:7374/signin-oidc",
      "https://localhost:44391/signin-oidc"
  ],
   "grant_types":[
      "authorization_code",
      "client_credentials",
      "implicit",
      "refresh_token",
      "urn:ietf:params:oauth:grant-type:jwt-bearer"
   ],
   "response_types":[
      "code",
      "token",
      "id_token token"
   ],
   "application_type":"web",
   "introspect_tokens":true
}
```

**Response**
```json
{
	"client_id_issued_at": 1497594910,
	"registration_client_uri": "https://localhost:9643/oidc/endpoint/jazzop/registration/2a9c2623bde844c4b446e48d8a53c296",
	"client_secret_expires_at": 0,
	"token_endpoint_auth_method": "client_secret_basic",
	"scope": "ALL_SCOPES",
	"grant_types": [
	  "authorization_code",
	  "client_credentials",
	  "implicit",
	  "refresh_token",
	  "urn:ietf:params:oauth:grant-type:jwt-bearer"
	],
	"response_types": [
	  "code",
	  "token",
	  "id_token token"
	],
	"application_type": "web",
	"introspect_tokens": true,
	"client_id": "2a9c48231b6bb4c4b566eaad8a53c2d6",
	"client_secret": "9z6Ueq609xGIrVGq4562756ESvTmOkFEqye3eVS2c6GqFJqBLUrVbHFcVP88X",
	"client_name": "OIDC Test",
	"redirect_uris": [
	  "http://localhost:7374/signin-oidc",
	  "https://localhost:44391/signin-oidc"
	],
}
```