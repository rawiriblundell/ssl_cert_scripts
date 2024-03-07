Most of the SSL certs that I look after are now ACME'd.  For a handful of customer provided certs, ACME is not an option.

I manage these certs using (internal only, and secured) git, a per-cert folder structure, openssl config files and these scripts.

I've sanitised the scripts a bit and generated a couple of example per-cert folders.

Note: The context for these certs is nginx reverse proxies.

Each certificate has a folder by the same name as the certificate's CN, and within that folder is a file called `openssl.cnf`.  This is the brains of the operation.
So to add another cert, for example, you could do something like

```
cp bar.contoso.com baz.contoso.com
cd baz.contoso.com
rm baz.contoso.com*
vi openssl.cnf
```

Change `openssl.cnf` to suit, and then run the process as described below.

The process is pretty simple:

```
./generate_csr foo.contoso.com
Generating DH parameters, 4096 bit long safe prime, generator 2
This is going to take a long time

[new dhparams is generated producing a bunch of output, this takes a while]

Generating a RSA private key
...........................+++++
..+++++
writing new private key to 'foo.contoso.com.key'
-----
Please send foo.contoso.com.csr to the appropriate place to be signed
```

Then I send the CSR off to the customer's cert team to sign and return.

I plonk the returned file into the given per-cert folder and run:

```
./sign_csr foo.contoso.com
Ensuring that all files are lowercase...
foo.contoso.com.cer appears to be in DER format, converting to PEM and continuing...
Line endings in foo.contoso.com.cer appear to be fine...
foo.contoso.com.cer appears to have passed validation tests...
foo.contoso.com.chained.crt created.
```

Probably `sign_csr` should be `validate_csr`.  Shrug.

Security note: Obviously don't store your certs in a public git repo!
