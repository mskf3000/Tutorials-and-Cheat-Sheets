## Apache - SSL Certificates

By Jack Szwergold

### Generate a CSR (certificate signing request).

    openssl req -nodes -newkey rsa:2048 -keyout www.example.com.key -out www.example.com.csr

Items to provide answers for the CSR with. The most important item is the `Common Name (CN)`; the rest is really clerical and for reference only:

- **Country Name (2 letter code) [AU]:** US
- **State or Province Name (full name) [Some-State]:** New York
- **Locality Name (eg, city) []:** New York
- **Organization Name (eg, company) [Internet Widgits Pty Ltd]:** Some Company
- **Organizational Unit Name (eg, section) []:** Some Company (Some More Info)
- **Email Address []:** email@example.com
- **Common Name (CN):** www.example.com

### Convert certificate to a `.pfx` (aka: `.p12`) package.

Convert a PEM certificate file and a private key to PKCS#12 (.pfx .p12) package file preferred by Windows setups:

    openssl pkcs12 -export -out www.example.com.pfx -inkey www.example.com.key -in www.example.com.crt -certfile GandiStandardSSLCA.pem

Extracting the key from the `.pfx` file:

    openssl pkcs12 -in www.example.com.pfx -nocerts -out test_www.example.com.key -nodes

Extracting the certificate from the `.pfx` file:

    openssl pkcs12 -in www.example.com.pfx -clcerts -nokeys -out test_www.example.com.crt

### A basic Apache config for an SSL certificate.

	<IfModule mod_ssl.c>
	  <VirtualHost *:443>
	    # 2013-10-26: Including common items in a common file for ssl & non-ssl.
	    include /etc/apache2/sites-available/www.example.com.common.conf
	
	    SSLEngine on
	    SSLCertificateFile /opt/ssl/cert-www.example.com.crt
	    SSLCertificateKeyFile /opt/ssl/www.example.com.key
	    SSLCertificateChainFile /opt/ssl/GandiStandardSSLCA.pem
	    SSLVerifyClient None
	
	    SSLProtocol all
	    SSLCipherSuite HIGH:MEDIUM
	
	    RequestHeader set X_FORWARDED_PROTO 'https'
	
	  </VirtualHost>
	</IfModule>

***

*Apache - SSL Certificates (c) by Jack Szwergold; written on September 19, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*