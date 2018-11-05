# NGINX+ with Okta for authentication

this github repo is: `https://github.com/tom-smith-okta/nginx-plus-oidc`

Use Okta as an OIDC authentication server to secure applications secured by NGINX+.

This repo is based on the [official NGINX+ OIDC library](https://github.com/nginxinc/nginx-openid-connect), where you can find fuller documentation.

Upon successful authentication, NGINX+ will receive an id token from Okta. NGINX+ will parse the id token and add some relevant user attributes as headers to the request it passes to the downstream application.

By default, this setup proxies a public web application: [http://okta-headers.herokuapp.com/](http://okta-headers.herokuapp.com/). The app just displays headers, so you can visit it now to see what it looks like without authentication and proxy by NGINX+. After you authenticate with Okta, the app will display the additional headers it has parsed from the Okta id token.

## Setup and installation

A sample nginx.conf file is included in this repo. These are some of the essential parameters that need to be updated to enable authentication vs. Okta. Update these example values with values from your own Okta tenant:

```
$oidc_jwt_keyfile     /etc/nginx/my_idp_jwk.json;
$oidc_authz_endpoint  "https://partnerpoc.oktapreview.com/oauth2/v1/authorize";
$oidc_token_endpoint  "https://partnerpoc.oktapreview.com/oauth2/v1/token";
$oidc_client          "0oagfbbn3gHhSxJWL0h7";
$oidc_client_secret   "{{my_client_secret}}";
```

There are of course many other parameters that you can adjust.

### Okta setup
To sign up for a free-forever Okta tenant visit [developer.okta.com](https://developer.okta.com/).

To set up your OIDC client in Okta, follow the quickstart guide [here](https://developer.okta.com/quickstart/#/okta-sign-in-page/nodejs/express) and follow the instructions for "Okta Sign-In Page Quickstart".

You need to add the redirect_uri (http://localhost:8126/redirect_uri) to your OIDC client, and your hostname (http://localhost:8126) to the Trusted Origins for your Okta tenant. I am just using 8126 as an example port; you can use any port you wish.

Assign the OIDC app to Everyone for now, and add a user to your Okta tenant so you can test authentication.

After you have updated your `nginx.conf` file with the settings from your Okta tenant, you can restart NGINX+ and requests to protected endpoints will be redrected to Okta for authentication.

### Authentication
When you authenticate, NGINX will parse the id token it receives from Okta, create some new headers, and load the downstream site, which just displays headers.
