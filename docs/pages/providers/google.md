---
title: "Google"
---

# Google

Implements OpenID Connect.

For usage, see [OAuth 2.0 provider with PKCE](/guides/oauth2-pkce).

```ts
import { Google } from "arctic";

const google = new Google(clientId, clientSecret, redirectURI);
```

```ts
const url: URL = await google.createAuthorizationURL(state, codeVerifier, {
	// optional
	scopes // "openid" always included
});
const tokens: GoogleTokens = await google.validateAuthorizationCode(code, codeVerifier);
const tokens: GoogleRefreshedTokens = await google.refreshAccessToken(refreshToken);
```

## Get user profile

Add the `profile` scope. Optionally add the `email` scope to get user email.

```ts
const url = await google.createAuthorizationURL(state, codeVerifier, {
	scopes: ["profile", "email"]
});
```

Parse the ID token or use the `userinfo` endpoint. See [ID token claims](https://developers.google.com/identity/openid-connect/openid-connect#an-id-tokens-payload).

```ts
const tokens = await google.validateAuthorizationCode(code, codeVerifier);
const response = await fetch("https://openidconnect.googleapis.com/v1/userinfo", {
	headers: {
		Authorization: `Bearer ${tokens.accessToken}`
	}
});
const user = await response.json();
```

## Get refresh token

Set the `access_type` param to `offline`. You will only get the refresh token only in the first sign in.

```ts
const url = await google.createAuthorizationURL();
url.searchParams.set("access_type", "offline");
```
