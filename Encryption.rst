****************************
Encryption in SD-dev
****************************


Encryption extension on SD::  

    SD                     =======c function ============================             SD
    encrypt call: 
    Pass Text              => libsodium encrypt => Hex or Base64 Encode => returned data (Hex or B64 encoded)

    decrypt call:
    Pass encoded text      => Hex or Base64 Decode => libsodium decrypt => return text
    
    Keys and Salts are all passed / returned as encoded strings (so we can save them in SD)
    
    Example
    
    Encrypt call:
    
    encrypted_text = SDENCRYPT(Data,KeyToUse,Encoding)
    where:
        Data = string to encrypt
        KeyToUse = should be crypto_secretbox_KEYBYTES bytes (currently defined as 32 bytes / 256 bits) string (see notes in SD_EncodeHX)
        Encoding =  one of: 
                            SD_EncodeHX   - The key and returned encrypted text are Hex Encoded (passed key is a 64 Hex Character               
                                            string which will be converted to 32 bytes, encrypted text will be a Hex Character String 
                                            2X the length of the passed string to encrypt.
                            SD_Encode64   - The key and returned encrypted text are Base64 Encoded 
    Decrypt call:
    
	
    decrypted_text = SDDECRYPT(Data,KeyToUse,Encoding)
    where:
        Data = encrypted string
        KeyToUse = should be crypto_secretbox_KEYBYTES bytes (currently defined as 32 bytes / 256 bits) string **
        Encoding =  one of: 
                            SD_EncodeHX   - The key and passed encrypted text are Hex Encoded (passed key is a 64 Hex Character               
                                            string which will be converted to 32 bytes, encrypted text will be a Hex Character String 
                                            2X the length of the returned decrypted text. 
                            SD_Encode64   - The key and passed encrypted text are Base64 Encoded  	
	
	
	

    See the simple test program SD_ENCRYPT and SD_ENCRYPT_B64 in sdsys/BP 
    

Why libsodium ?

From https://doc.libsodium.org/
    Sodium is a modern, easy-to-use software library for encryption, decryption, signatures, password hashing, and more.
    
I originally planned to use openssl, but with multiple recommendations on only using it for encryption if you know what you are doing, (and I don't) I figured it was better to use the "encyption for dummies" approach. 

To use this extension you need to install libsodium-dev and libsodium23 (Ubuntu):
    apt install libsodium-dev libsodium23 

Encryption, Decryption and encoding using libsodium package

https://doc.libsodium.org/

This routine (and doc) is based on information found here:

https://doc.libsodium.org/secret-key_cryptography/secretbox

https://github.com/jedisct1/libsodium/blob/master/test/default/secretbox_easy2.c

https://doc.libsodium.org/password_hashing/default_phf

we encrypt using crypto_secretbox_easy::


    int crypto_secretbox_easy(unsigned char *c, const unsigned char *m,
                        unsigned long long mlen, const unsigned char *n,
                        const unsigned char *k)

    The crypto_secretbox_easy() function encrypts a message m whose length is mlen bytes, with a key k and a nonce n. 
    k should be crypto_secretbox_KEYBYTES bytes (currently defined as 32 bytes / 256 bits) and n should be crypto_secretbox_NONCEBYTES bytes.
    c should be at least crypto_secretbox_MACBYTES + mlen bytes long.
    This function writes the authentication tag, whose length is crypto_secretbox_MACBYTES bytes,
    in c, immediately followed by the encrypted message, whose length is the same as the plaintext: mlen.
    Functions returning an int return 0 on success and -1 to indicate an error.

    REM:
      A 128-bit (16 byte) key can be expressed as a hexadecimal string with 32 characters.
        It will require 24 characters in base64.
      
      A 256-bit (32 byte) key can be expressed as a hexadecimal string with 64 characters.
        It will require 44 characters in base64.

    Note we return the encryption output with the nonce appended to the end
    Rem to encode either base64 or hex before returning to SD!

we decrypt using crypto_secretbox_open_easy::

    int crypto_secretbox_open_easy(unsigned char *m, const unsigned char *c,
                             unsigned long long clen, const unsigned char *n,
                             const unsigned char *k);

    c is a pointer to an authentication tag + encrypted message combination,
    as produced by crypto_secretbox_easy().
    clen is the length of this authentication tag + encrypted message combination.
    Put differently, clen is the number of bytes written by crypto_secretbox_easy(),
    which is crypto_secretbox_MACBYTES + the length of the message.
    The nonce n and the key k have to match those used to encrypt and authenticate the message.
    The function returns -1 if the verification fails, and 0 on success. 
    On success, the decrypted message is stored into m.

If the user wishes to use a password for encryption / decryption we need to generate a key for it.
The project recommends using crypto_pwhash to convert a password to a key, but to be reproducible the routine requires:

The salt to be know
 
Values for opslimit and memlimit

https://doc.libsodium.org/key_derivation and https://doc.libsodium.org/password_hashing/default_phf

To do this we will probably use function::

    int crypto_pwhash(unsigned char * const out,
                unsigned long long outlen,
                const char * const passwd,
                unsigned long long passwdlen,
                const unsigned char * const salt,
                unsigned long long opslimit,
                size_t memlimit, int alg);
                 
    The crypto_pwhash() function derives an outlen bytes long key from a password passwd whose length is passwdlen
    and a salt whose fixed length is crypto_pwhash_SALTBYTES bytes. 
    passwdlen should be at least crypto_pwhash_PASSWD_MIN and crypto_pwhash_PASSWD_MAX.
    outlen should be  at least crypto_pwhash_BYTES_MIN = 16 (128 bits) and at most crypto_pwhash_BYTES_MAX. 
    The salt should be unpredictable. randombytes_buf() is the easiest way to fill the crypto_pwhash_SALTBYTES bytes of the salt.

Keep in mind that to produce the same key from the same password, the same algorithm,
the same salt, and the same values for opslimit and memlimit must be used.

Therefore, these parameters must be stored for each user? / application? / or ??. 



