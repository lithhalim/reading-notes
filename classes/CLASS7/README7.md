# JSON Web Tokens

### WHAT IS JSON WEB TOKEN?

##### JSON Web Token (JWT) is an open standard (RFC 7519) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. This information can be verified and trusted because it is digitally signed. JWTs can be signed using a secret (with HMAC algorithm) or a public/private key pair using RSA.

Let’s explain some concepts of this definition further.

- Compact: Because of its size, it can be sent through an URL, POST parameter, or inside an HTTP header. Additionally, due to its size its transmission is fast.
- Self-contained: The payload contains all the required information about the user, to avoid querying the database more than once.

### WHEN SHOULD YOU USE JSON WEB TOKENS?
##### These are some scenarios where JSON Web Tokens are useful:

- Authentication: This is the typical scenario for using JWT, once the user is logged in, each subsequent request will include the JWT, allowing the user to access routes, services, and resources that are permitted with that token. Single Sign On is a feature that widely uses JWT nowadays, because of its small overhead and its ability to be easily used among systems of different domains.
- Information Exchange: JWTs are a good way of securely transmitting information between parties, because as they can be signed, for example using a public/private key pair, you can be sure that the sender is who they say they are. Additionally, as the signature is calculated using the header and the payload, you can also verify that the content hasn’t changed.

### WHICH IS THE JSON WEB TOKEN STRUCTURE?
JWTs consist of three parts separated by dots (.), which are:

- Header
- Payload
- Signature
Therefore, a JWT typically looks like the following.

xxxxx.yyyyy.zzzzz

Let’s break down the different parts.

### Header
The header typically consists of two parts: the type of the token, which is JWT, and the hashing algorithm such as HMAC SHA256 or RSA.

For example:
```javascript
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### Payload
The second part of the token is the payload, which contains the claims. Claims are statements about an entity (typically, the user) and additional metadata. There are three types of claims: reserved, public, and private claims.

### Signature
To create the signature part you have to take the encoded header, the encoded payload, a secret, the algorithm specified in the header, and sign that.

For example if you want to use the HMAC SHA256 algorithm, the signature will be created in the following way.

