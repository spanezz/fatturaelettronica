# Is there a way to verify signatures using free software?

If the signatures are just p7m encoded ssl signatures, I don't see why
not, especially because to verify a signature one is not supposed to be
in possession of the smart card, but only of public key material.

I gave this whole thing a try and much web searching, here is the
result.

Signed files seem to carry the public signing certificate with them. I
could see them with this:

	openssl pkcs7 -in file.xml.p7m -inform DER -text -print_certs

<http://www.agid.gov.it/certificati-0> allows you to download a signed
zipfile with all the issuer certificates. It also references [this EU
site](https://ec.europa.eu/information_society/policy/esignature/trusted-list/)
which sounds like interesting but I have not investigated.

I do not know how to get the CA certificate to verify the signature on
that list. I extracted the zipfile with this, skipping the verification
step:

	openssl smime -verify -inform DER -in LISTACER_20151113.zip.p7m -noverify > certs.zip

I could dump information from one of those certificates with this:

	openssl x509 -inform DER -in Infocert/CA_Infocert-ca-firma2.cer -text

I did a mass conversion to PEM with this:

	for i in *.cer *.crt
	do
		 openssl x509 -in $i -inform DER -out $i.pem -outform PEM
	done

I have no idea which of those files has the CA to verify my signatures,
so I tried using them all:

	cat *.pem > /tmp/ca.pem

And then I managed to successfully verify the signature:

	openssl smime -verify -inform DER -in file.xml.p7m -out verified.xml -CAfile /tmp/ca.pem

So it looks like there is a way.

The procedure I followed still misses how to get the CA certificate that
has been used to verify the zipfile with the list of CAs.
