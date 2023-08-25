
- [TLS 1.2](https://tls12.xargs.org/)

## TLS 1.2

### 握手

第一次

1. Client: Hello
2. Server: Hello
3. Server: Certificate
4. Server: Key Exchange Generation
5. Server: Key Exchange
6. Server: Hello Done

第二次

7. Client: Key Exchange Generation
8. Client: Key Exchange
9. Client: Encryption Keys Calculation
10. Client: Client Change Cipher Spec
11. Client: Client HandShake Finished
12. Server: Encryption Keys Calculation
13. Server: Change Cipher Spec
14. Server: HandShake Finished

### 通信

1. Client: Application Data
2. Server: Application Data

### 结束

1. Client: Close Notify

## TLS 1.3


