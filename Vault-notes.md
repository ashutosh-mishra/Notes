
Vault is a tool for securely accessing secrets.

## Features :

* Secret Management
  * Dynamic secrets, Secure secret storage
* Encryption/Crypto as a Service
* Identity and access Management
  * Leasing and renewal, revocation


Security :
* Vault provides TLS security
* Not a FIPS compliant(Enterprise version has FIPs 140-2 conformant)
* User/Application need to pass token to access data. Request can use either root token or token generated after authentication. Token
    is similar to session_id.
* Supports single use token

Enterprise features :
* FIPS compliant
* Replication across datacentres
* HSM support
* Multi-factor authentication


By default vault starts in sealed state, APIs will work only if vault is unsealed. Unsealing is the process of constructing the
master key necessary to read the decryption key to decrypt the data. Once unsealed the standard ACL mechanisms are used for all requests. Unsealing is the process of constructing the master key necessary to read the decryption key to decrypt the data, allowing access to the Vault.

An encryption key is used to protect all the data, that key is protected by a master key. By default, Vault uses a technique known as Shamir's secret sharing algorithm to split the master key into 5 shares, any 3 of which are required to reconstruct the master key.

Before a client start interaction with vault it must authenticate itself with some authentcation backends. Upon authentication a token is generated, This token is similar to session_id in the website. Authentication works by verifying your identity and then generating a token to associate with that identity.

Vault never store master key on persistent location. It always get generated and remained in ram. That is the reason vault need to unsealed upon start.


## Architecture :
Vault has 3 components : Client(making the API request), Vault(Authenticate client and encrypt/decrypt data), Storage backend(Stores the data).


## Vault Internals :
1) Storage backend - Durable storage of encrypted data
2) Barrier - Any data that flows between vault and storage backend is encrypted while going out and verified and decrypted while coming in.
3) Secret backend - Responsible for managing secrets. Also responsible for dynamic secret generation. For ex
       When a "Web" secret is reas a new MySql user/password can be generated with a limited set of privilages 
       for the web server.
4) Audit backend - Responsbile for managing audit logs. Every request to vault and response form vault goes through audit backends.
5) Auth backend - Responsbile for authenticating user or application which are connecting to vault. Returns a client token
       after authentication which can be used in future requests. The authentication can either be user based or machine based. For user
       based authentication scenario, Vault provides username/password, token, github methods to authenticate. For machine scenario,
       Vault provides methods like approle, certificates to authenticate.
       Supports various authentication mechanisms user creds, github creds, public key, token etc.
6) Client token - similar to session id. Can set lease time at the time of token creation.
7) 


A storage backend is where Vault stores its encrypted data.
Secret backend provides secret to callers, whether ones that have
been set (e.g. the generic K/V store) or dynamically-generated ones
(like database passwords).

#### Transit backend(Secret backend)
Handles cryptographic functions on data in-transit. also known as Encryption as a Service
or Cryptography as a Service. User can configure vault to store encrypted data through storage backend.

Transit backend supports exportable property on the keys. If exportable property is set key can be exported outside else key
remains in the vault only but can be used to do crypto operations. Supports both symmetric and asymmetric key operations. transit can also sign and verify data; generate hashes and HMACs of data; and act as a source of random bytes

#### Cubbyhole backend(Secret backend)
Similar to k/v store but paths are scoped per token i.e. no token can access another token's 
    cubbyhole to read/write or to perform any operation. When the token expires, its cubbyhole is destroyed. Cubbyhole can be
    used with response wrapping to ensure trust.

#### Respone wrapping
When requested, vault will put the actual response into cubbyhole of a single-use token and
    return that token. When a response is wrapped, the normal API response from Vault does not contain the original
    secret, but rather contains metadata to the response-wrapping token.

Can be used with ttl and use-limit i.e. Can be used to store the permanent token for a temporary token with use
    limit of 2 and ttl of 1 hour.

You should seal your vault when a significant intrusion has been detected.



## Tokens
Token has properties like id, name, metadata, source_path, policies. Each token maintains the source path sued to
create the token. This is used in source based revokation. Child token can be created with subset of parent policies to drop
privilages. Use count can also specified at the time of token creation.
* Root token - Tokens that have root policy associated with it. Root tokens can perform any operations in vault. Root token gets created at the time of init or via another root token.
* Periodic token - Need to renew within configured TTL. Upon renewal set the ttl back to 0.




## Use cases :
1. As Secrets manager(creds, password, keys)
2. Vault as CA
3. Passing secrets to Container
4. Dynamic secret generation
    For Ex. User access with dynamic secrets Connects to vault, request for DB creds, Vault will generate DB creds on the fly  
    and hand it to user. In addition it can associate lease time for the creds.
5. Crypto operations :
    Encryption/Decryption, Sign/Verify, hash, hmac, random bytes generation.
6. With Strongswan
    Strongswan was configured to do authentication via client certificates, which is pretty straightforward.  If the connecting
    client has a valid certificate signed by a known authority (Vault) with an email address matching a particular pattern,
    access is granted.  No knowledge of users was needed ahead of time.  On the client side, a user would authenticate to Vault, 
    retrieve a short-duration (24 hours) certificate, and that was pretty much it.
7. 
  Due to Vault's flexible ACLs, other interesting use-cases are possible. For instance, one set of Internet-facing servers can
  be given permission to encrypt with a named key but not decrypt with it; a separate set of servers not directly connected to
  the Internet can then perform decryption.
8. Response wrapping

## Workflow :
* Administartor
  * Start vault
  * Unseal vault
  * Add credentials based on type of backends
  * Add Access Control policies to backend based on user or machine identifier
  * Enable an audit backend to enable logging
 
* User
  * User or machine logs into vault with the creds
  * Vault associates policy based on creds
  * User/Machine can fetch secrets from secret backend
  * vault destroys the secret if lease duration expires.
 
 
 1. User Flow
     ![User Flow](https://)
     
 2. Authentication sequence
     ![Authentication sequence](https://)
     
 3. Ssh Login Flow
     ![Ssh Login Flow](https://)
     

 

