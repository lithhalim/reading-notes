# Add Login Using the Authorization Code Flow
- You can add login to your regular web application using the Authorization Code Flow. To learn how the flow works and why you should use it, read Authorization Code Flow. To call your API from a regular web app, read Call Your API Using the Authorization Code Flow.

- To implement the Authorization Code Flow, Auth0 provides the following resources:

# Regular Web App Quickstarts: The easiest way to implement the flow.

Authentication API: If you prefer to build your own solution, keep reading to learn how to call our API directly.

- Following a successful login, your application will have access to the user's ID token and access token. The ID token contains basic user profile information, and the access token can be used to call the Auth0 /userinfo endpoint or your own protected APIs. To learn more about ID tokens, read ID Tokens. To learn more about access tokens, read Access Tokens.

You will request the user's authorization and redirect back to your app with an authorization_code. Then you will exchange the code for tokens.

# Prerequisites
Register your app with Auth0. To learn more, read Register Regular Web Applications.

Select Regular Web App as the Application Type.

Add an Allowed Callback URL of https://YOUR_APP/callback.

Make sure your application's Grant Types include Authorization Code. To learn more, read Update Grant Types.

Authorize user
To begin the flow, you'll need to get the user's authorization. This step may include one or more of the following processes:

Authenticating the user;

Redirecting the user to an Identity Provider to handle authentication;

Obtaining user consent for the requested permission level, unless consent has been previously given.

To authorize the user, your app must send the user to the authorization URL.
```javascript
Authorization URL example
to configure this snippet with your account
https://YOUR_DOMAIN/authorize?
    response_type=code&
    client_id=YOUR_CLIENT_ID&
    redirect_uri=https://YOUR_APP/callback&
    scope=SCOPE&
    state=STATE
Was this helpful?
```
/
# Parameters
Parameter Name	Description
response_type	Denotes the kind of credential that Auth0 will return (code or token). For this flow, the value must be code.
client_id	Your application's Client ID. You can find this value in your Application Settings.
redirect_uri	The URL to which Auth0 will redirect the browser after authorization has been granted by the user. The Authorization Code will be available in the code URL parameter. You must specify this URL as a valid callback URL in your Application Settings.

Warning: Per the OAuth 2.0 Specification, Auth0 removes everything after the hash and does not honor any fragments.
scope	Specifies the scopes for which you want to request authorization, which dictate which claims (or user attributes) you want returned. These must be separated by a space. To get an ID Token in the response, you need to specify a scope of at least openid. If you want to return the user's full profile, you can request openid profile. You can request any of the standard OpenID Connect (OIDC) scopes about users, such as email, or custom claims conforming to a namespaced format. Include offline_access to get a Refresh Token (make sure that the Allow Offline Access field is enabled in the Application Settings).
state	(recommended) An opaque arbitrary alphanumeric string your app adds to the initial request that Auth0 includes when redirecting back to your application. To see how to use this value to prevent cross-site request forgery (CSRF) attacks, see Mitigate CSRF Attacks With State Parameters.
connection	(optional) Forces the user to sign in with a specific connection. For example, you can pass a value of github to send the user directly to GitHub to log in with their GitHub account. When not specified, the user sees the Auth0 Lock screen with all configured connections. You can see a list of your configured connections on the Connections tab of your application.
organization	(optional) ID of the organization to use when authenticating a user. When not provided, if your application is configured to Display Organization Prompt, the user will be able to enter the organization name when authenticating.
invitation	(optional) Ticket ID of the organization invitation. When inviting a member to an Organization, your application should handle invitation acceptance by forwarding the invitation and organization key-value pairs when the user accepts the invitation.
login_hint	(optional) Populates the username/email field for the login or signup page when redirecting to Auth0. Supported by the New Universal Login experience.
As an example, your HTML snippet for your authorization URL when adding login to your app might look like:
```javascript
to configure this snippet with your account
<a href="https://YOUR_DOMAIN/authorize?
  response_type=code&
  client_id=YOUR_CLIENT_ID&
  redirect_uri=https://YOUR_APP/callback&
  scope=openid%20profile&
  state=xyzABC123">
  Sign In
</a>
Was this helpful?
```
/
# Response
If all goes well, you'll receive an HTTP 302 response. The authorization code is included at the end of the URL:
```javascript
HTTP/1.1 302 Found
Location: https://YOUR_APP/callback?code=AUTHORIZATION_CODE&state=xyzABC123
Was this helpful?
```
/
Request tokens
Now that you have an Authorization Code, you must exchange it for tokens. Using the extracted Authorization Code (code) from the previous step, you will need to POST to the token URL.

POST to token URL example
