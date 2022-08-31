
# INTRODUCCION
Algunos servidores (ahora la mayoria) requieren que la comunicacion con el cliente se realice de forma segura, es decir que los datos vayan encriptados y
que el cliente o el servidor o ambos se autentifiquen mediante un certificado. En el certificado va incluida la clave publica del cliente, de forma que
el servidor utiliza esta clave para encriptar los datos y enviarselos encriptados al cliente, el cual los desencripta utilizando la clave privada que ha sido generada junto al certificado
# SCAN

```
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
4040/tcp  open  yo-main
9009/tcp  open  pichat
54321/tcp open  unknown
```
Tenemos los  siguiente:
1. En el puerto 80 corre un servidor nginx que nos redirige al puerto 4040 
2. En el puerto 4040 hay un webserver TSL
3. Hay un servidor de certificados ssl que debe estar en el puerto 54321
4. El servidor localizado en el puerto 9009 emite los certificados

# PUERTO 9009

Utilizando netcat podemos conectar con el puerto 9009 donde obtenemos el certificado

> $ nc bedrock.thm 9009

```
-----BEGIN CERTIFICATE-----
MIICoTCCAYkCAgTSMA0GCSqGSIb3DQEBCwUAMBQxEjAQBgNVBAMMCWxvY2FsaG9z
dDAeFw0yMjA4MzExNTA4MDhaFw0yMzA4MzExNTA4MDhaMBgxFjAUBgNVBAMMDUJh
cm5leSBSdWJibGUwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQD1Ea6f
q+AZRNZ9ke8+nJ1T517YaVIzbD0SQJ4THqdN+7tXhquKzsbDMcungbWNQ1p9z3gN
5F6VWaQo5zkeY+FgCdFIEs4SkDfi5+yhE5Ose6hyVEYDizxcNXnAixDsuuoYVNs6
taoRckHPmrH++ALl99XzerhVgyq1uPwQ4dxVZbNHeXqV5HRPpF6zKzlctJhP/v4g
F4uggfMiYiefMiDPoUdkXHMwqOAzluhmBe97ioWSkq+pKhgcab4+VI//00e2P0De
Qv/ebwbcjvI5xF6YDLAiiReyuKRItNdG63j6h0Tl2I30w1GaRyNYucTq1yxKDdge
5JmfY2++4YTsasJlAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAIl4o3DJF6si4rE2
nIdg1gGt5MsV/kDqYYNgoMHUr05vylxKpDoAn9H6lgZDVg74ZlLOrYDpwUma4tiw
YMXqLiR2lQaQfyeJSYpJwr4vkhhAUCQuCdhBBjfg5jbkPsw2PkP8B4BHD222vDNl
52XAC3ZL9UGVkEKnV2FpBRkCF6qu2LoVsCDOAfaSVssaduBeoPAnHrZg3wtB/H/I
FsRiny6E4diGcwqB1VaQGc283MmjoRjxl7wzh1DN+AT7qNWtpidyzWFRGxXJ5086
CHd9tj2koYBUfed7LUf4zdh6+EEUYu9etek6rELCa+avsk44pvbFTXQaCA1OUWSC
FsyC/cU=
-----END CERTIFICATE-----
```
