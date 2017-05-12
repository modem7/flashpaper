# FlashPaper
A one-time encrypted zero-knowledge password/secret sharing application focused on simplicity and security. No database or complicated set-up required. 

## Demo
**Not** for production use, as this demo does not use SSL/TLS encryption (https://)

http://password.paglusch.com

## Requirements
* PHP 5
* Web server
* Linux
* OpenSSL

## Installation
Copy contents of this repository to document root of web server

*To increase security, disable access logging in your web server's configuration*

## Summary Of How It Works
### Submitting Secret
* Random 32-character password is created
* Submitted text is encrypted with password
* Password is hashed via SHA1
* File created in `secrets` directory. Name of file is the SHA1 of the random password
* Encrypted version of submitted text is stored inside of created file
* Password is Base64 encoded
* Retrieval URL is created by appending Base64 version of password to end
  * `https://site.com/k?=1a2b3c4d5a6b7c8d9a0b1c2d3a4b5c6d$`

### Retrieving Secret
* Base64 portion of URL is stripped from URL
* Decode Base64 string to get the decryption password
* Generate SHA1 of the password
* Look for file in `secrets` that is named the SHA1 that we just generated
* Get text from the file that we found and decrypt it with the password
* Return the decrypted secret text to user
* Delete the file