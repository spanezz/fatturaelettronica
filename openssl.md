# Setting up OpenSSL to work with smart cards

	apt install libengine-pkcs11-openssl

To sign using the smart card, one uses `openssl smime`, but I could not find
out how to have it [read a config file](http://opensc.1086184.n5.nabble.com/question-on-using-engine-pkcs11-with-openssl-smime-on-command-line-td13304.html)
yet. Let me know / send me a patch if you know a way.

Using the openssl shell:

	$ pkcs15-tool --read-certificate 01 > /tmp/cert01.pem
	$ openssl
	OpenSSL> engine -t dynamic -pre SO_PATH:/usr/lib/engines/engine_pkcs11.so -pre ID:pkcs11 -pre LIST_ADD:1 -pre LOAD  -pre MODULE_PATH:/usr/lib/x86_64-linux-gnu/opensc-pkcs11.so -pre VERBOSE
	OpenSSL> smime -sign -engine pkcs11 -keyform engine -inkey 1:1 -in file.xml -out file.xml.p7m -binary -signer /tmp/cert01.pem -outform DER -nodetach

Do verify the paths to the various `.so` files, as they may be different in
your system.

# Signing a fattura elettronica

I have logged into the [Infocamere](https://fattura-pa.infocamere.it/fpmi/service).

I have created an unsigned fattura elettronica.

I have clicked on "Sign" and choose manual signature.

I have downloaded the zip file and extracted the xml file.

I have signed it using the commands in the *Setting up OpenSSL to work with smart cards* section above.

I have uploaded the resulting p7m file.

Infocamere reported that the file was signed correctly.

I have submitted the fattura elettronica and it has been successfully sent. I
am now waiting to see if it gets accepted by the recipient, to complete the
cycle.


# Links

On using smart cards with OpenSSL:

 - http://ubuntuforums.org/archive/index.php/t-1447218.html
 - http://ubuntuforums.org/showthread.php?t=1557180
 - https://github.com/OpenSC/OpenSC/wiki/Engine-pkcs11-quickstart
 - https://www.mail-archive.com/openssl-users@openssl.org/msg43477.html
