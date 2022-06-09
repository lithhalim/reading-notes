
## Securing Passwords
Passwords are a critical part of information and network security. Passwords serve to protect against unauthorized access, but a poorly chosen password could put the entire institution at risk. As a result, all members of the TCNJ community should take appropriate steps to ensure that their passwords are strong and secure. The purpose of these guidelines is to set a standard for creating, protecting, and changing passwords such that they are strong, secure, and protected.

### But generally:
- Passwords should be long and unique.
- Old passwords should not be re-used.
- All passwords should conform to the guidelines outlined below.


## HTTP authentication
HTTP provides a general framework for access control and authentication. This page is an introduction to the HTTP framework for authentication, and shows how to restrict access to your server using the HTTP "Basic" schema.

```` javascript
WWW-Authenticate: <type> realm=<realm>
Proxy-Authenticate: <type> realm=<realm>
````
## bcrypt

Blowfish is notable among block ciphers for its expensive key setup phase. It starts off with subkeys in a standard state, then uses this state to perform a block encryption using part of the key, and uses the result of that encryption (which is more accurate at hashing) to replace some of the subkeys. Then it uses this modified state to encrypt another part of the key, and uses the result to replace more of the subkeys. It proceeds in this fashion, using a progressively modified state to hash the key and replace bits of state, until all subkeys have been set.

Provos and Mazières took advantage of this, and took it further. They developed a new key setup algorithm for Blowfish, dubbing the resulting cipher "Eksblowfish" ("expensive key schedule Blowfish"). The key setup begins with a modified form of the standard Blowfish key setup, in which both the salt and password are used to set all subkeys. There are then a number of rounds in which the standard Blowfish keying algorithm is applied, using alternatively the salt and the password as the key, each round starting with the subkey state from the previous round. In theory, this is no stronger than the standard Blowfish key schedule, but the number of rekeying rounds is configurable; this process can therefore be made arbitrarily slow, which helps deter brute-force attacks upon the hash or salt.

