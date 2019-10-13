
# *GPG*
*Note: GnuPG and GPG refers to the same.*

GPG stands for Gnu Privacy Guard(GnuPG).

PGP stands for Pretty Good Privacy

GnuPG is an open-source implementation of PGP, which adhers to OpenPGP standard.
GnuPG allows to encrypt and sign the data and communications.

## GPG vs OpenSSL
GPG is different then OpenSSL.

OpenSSL is an open-source implementation of SSL and TLS protocols. Provides crypto functionality for encryption, decryption, signing, checksum. It takes input and provides output, it does not maintain any state between requests.
GPG serves as a key server, which provides same functionality as of OpenSSL along with key management system i.e. key generation and key maintenance. It runs as a server, which manages your private keys and others public keys.


To begin using gpg for encryption, need to create a key-pair:
> $ gpg --gen-key

You need to provide
- kind of key
- key size
- validity
- name and email address
- passphrase to secure the key

Import others key
```
$ gpg --import name_of_pub_key
$ gpg --delete-key "User name"
```

Public pgp server - https://pgp.mit.edu/

List keys
```
$ gpg --list-keys
$ gpg --list-secret-keys
```
Export a public key for distribution
```
$ gpg --export -a "Ash Mishra" > ashmishra_public.key
$ gpg --export-secret-key -a "Ash Mishra" > ashmishra_private.key
```

GPG supports both symmetric and asymmetric encryption


### Symmetric encryption
When not using key pair for encryption
- Encryption
```
$ gpg --symmetric secrets.txt
```
This will promt to enter a passphrase to complete the encryption. Same passphrase need to supply while decrypting the content.
This will create a new file with encrypted content secrets.txt.gpg

- Decryption
```
$ gpg secrets.txt.gpg
```
This will create a new file with decrypted content secrets.txt


### Asymmetric encryption
- Encryption

Encrypt message for a specific user
```
$ echo "a really interesting message" | gpg --armor --encrypt -r <specific user name/email>
```

Encrypt a file 
```
$ gpg --encrypt -u "Sender User Name" -r "Receiver User Name" somefile
```
- Decryption

Private key should be there to decrypt the message

```
$ gpg --decrypt encrypted_msg_file.gpg
$ gpg -d encrypted_msg_file.gpg
```


### Key signing
GPG allows us to sign a key. This enables us to establish a chain of trust i.e. If someone (Let say P1), sees we signed P2 key, and they trust us, then implicitly they will also trust P2. 
```
$ gpg --sign-key bob@example.org
```
Once signed, the keys can be exported and send it to the person(bob)
```
$ gpg --export --armor bob@example.org
```

Fingerprint of a key
```
$ gpg --fingerprint <user email>
```


**Configuration** - GPG Configuration will be maintained in ~/.gnu/gpg.conf


### Digital Signatures
In GPG there are 3 types of signatures:
1. cleansigned
2. detached
3. attached signatures

All 3 signatues generate hash corresponding to file content

1. **cleansigned**

   ``` 
   $ gpg --local-user Bob --clearsign secrets.txt
   ``` 

   Once we run above command, a new file secrets.txt.asc gets created, which contains both data of new file and signature of the content which was signed with Bob key

   Another user, Alice can verify the signature, if she has Bob's public key
   ```
   $ gpg --local-user Alice --verify secrets.txt.asc
   ```

   Issues with clearsigned signature are:
   a. It's not encrypted
   b. Original file get modified to include signature
   c. It's not compressed


2. **detached**

   As name suggests, in this method a new file gets generated which only contains signature. Generated file is compressed not encrypted.  

   This is better as the original file is not modified, but it imposes another conndition for verification.  
   To verify, both signature file and original file is required, so both file need to send to recipient.  

   ```
   $ gpg --local-user Bob --detach-sign secrets.txt
   ```
   This will generate a new file secrets.txt.sig

   ```
   $ gpg --local-user Alice --verify secrets.txt.sig
   ```

3. **attached signatures**

   This meahcnism enforces to verify the file before using the file.  
   This method is similar as clearesigned by attaching the signatiure in same file, but in encrypted form.

   ```
   gpg --local-user Bob --recipient Alice --encrypt --sign secrets.txt
   ```
   This will generate a new file secrets.txt.gpg

   ```
   gpg --local-user Alice --decrypt secrets.txt.gpg
   ```
   This results in original content of the file and signature verification at the end.

   ```
   $ gpg --decrypt secrets.txt.gpg
   gpg: encrypted with 2048-bit RSA key, ID F4E7D32FEC9CD6E6, created 2019-09-30
         "amishra <ashutosh.mishra@actifio.com>"
   1. my secrets
   
   2. my password
   
   gpg: Signature made Sun Oct 13 17:21:34 2019 IST
   gpg:                using RSA key 28B2D8D172D00EEFCEFAF74D40CD5DB0338BE019
   gpg:                issuer "ashutosh.mishra@actifio.com"
   gpg: Good signature from "amishra <ashutosh.mishra@actifio.com>" [ultimate]
   ```

