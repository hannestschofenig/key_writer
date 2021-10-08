# Key Writer

## Introduction

key_writer is an pplication for converting cryptographic keys for use with the PSA Crypto API. The current implementation focuses on the support for ECC keys. 

Here is a common use case. You have created an ECC key pair for use with your application. Now, you want to use these keys within your code that uses the PSA Crypto API. Unfortunately, the PSA Crypto API requires key to be important in a raw key format. Hence, you have to convert the keys first. 

The key_writer app uses a public or private key as input and then converts the provided key into a given output format. Here are the parameters: 

```
 usage: key_writer param=<>...

 acceptable parameters:
    mode=private|public default: none
    filename=%s         default: keyfile.key
    output_mode=private|public default: none
    output_file=%s           default: keyfile.pem
    output_format=pem|der|bin default: pem
```

The parameters 'mode' and 'filename' are dedicated to the input. For the output you can specify the mode (either a public or private key), the output file and the format of the output (pem, der, or binary). The binary format refers to the format accepted by the PSA Crypto API. 

Not all combinations are possible, however. While it is possible to give a private key as input and to let key_writer give you the private key or the public key in a selected format. It is obviously not possible to provide a public key and to expect key_writer to return the private key to you. Had I managed to provide such a functionality in key_writer, this program would be extremely popular. ;-)


## Example

This example shows how to use key_writer to do so. 

First, we use the OpenSSL commands to create a PEM encoded ECC private key with the NIST P256r1 curve. 

```
openssl ecparam -name prime256v1 -genkey -noout -out skR.pem
```

Next, we need to convert the key in PEM format into the raw key format using key_writer. The format used by the PSA Crypto API is documented in https://armmbed.github.io/mbed-crypto/html/ (or more specifically in this section https://armmbed.github.io/mbed-crypto/html/api/keys/management.html?highlight=import#c.psa_export_public_key).

We will invoke the key_writer utility to create two files, one for the public key and another one for the private key.

```
./key_writer mode=private filename=skR.pem output_mode=public output_file=pkR.bin output_format=bin
./key_writer mode=private filename=skR.pem output_mode=private output_file=skR.bin output_format=bin
```

If you want to include those keys in your application source code, as it is common for embedded devices, use the 'xxd -i' command, which formats the content of the key in C structure. 

## Building the Key Writer Application 

key_writer relies on Mbed TLS and therefore we have to clone the Mbed TLS repository first. 

```
git clone https://github.com/hannestschofenig/key_writer.git
cd key_writer
mkdir external
cd external
git clone https://github.com/ARMmbed/mbedtls.git
cd mbedtls
git checkout development_2.x
cd ..
cd ..
mkdir build
cd build
cmake ..
make 
```

The key_writer app should now be in your build directory. Follow the steps above to convert your first key. 
