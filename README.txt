CS 255 Project 2

Group:

Thomas Davids (tdavids)
Alexander Atallah (aatallah)

Our SSL attack intercepts communications between the server and client, using the server's certificate to forge our own certificate which we then pass along to the client, pretending to be the server. We add the server's DN and the date to the certificate that we pass to the client, and use our own public key. This way, any message that the client sends back can be intercepted and read before it is passed along to the server.

To run the system, run the commands:

$ make
$ CLASSPATH=".:iaik_jce.jar" java mitm.MITMProxyServer -keyStore keystore -keyStorePassword 'keystorepass' -keyStoreAlias 'mykey' -outputFile log.txt

After these are run, while you are browsing webpages, the file log.txt is secretly logging all of your communication!

Short Answers:

1. Since the admin client has to transmit its password to the admin server, the attacker can intercept this and use it for his own purposes. More specifically, he can then transmit a message to the admin server, pretending to be a client. Since he has the proper password, the admin will authenticate him, and he can make commands as needed. The solution to this is pinning, where the browser is shipped with the public key. We can then encrypt a message to Google with Google's public key, and the attacker won't be able to intercept it.

2. Our password file just stores the hash of the password, so even if the attacker can read the file, assuming it's a strong hash function, it is computationally impossible for him to find our password. Normally, however, if he could write to the file, he could replace the hash of our password with the hash of a password of his choosing. This would give him access to the file. However, since we hard coded a salt into the bcrypt we used to generate the password hash, the attacker would not be able to guess the salt and thus could not come up with the hash for a password of his choosing. Thus our program is still secure under this condition.

3. The goal in preventing this attack is to prevent anyone from being able to get in between the client and the server. We could imagine a situation where the attacker has access to every communication the client sends out, and if he can read these, then he can send messages back pretending to be the necessary server. To prevent this problem, we can ship public keys with the browser. This way, the client can encrypt messages in a way that the attacker can't decrypt, which makes his ability to intercept communications useless.