### Python使用GMSSL包进行国密的加/解密、签名与验签功能的相关注意事项

1、python通过SM2加密后的密文，需要带上前缀为04的标识，上送给java解密（若java那里去掉了04标识则不需要加，正常是需要加的）  
2、对java通过SM2加密的数据，需要减去前缀为04的标识（若java那里去掉了04标识则不需要加，正常是需要加的）  
3、gmssl使用的公私钥为hex格式、如获取到的公私钥是pem格式的需要转换为hex格式，python具体转换方式代码如下：

 ```python
from base64 import b64decode

# pem私钥中获取hex格式公私钥
private_key_pem = "MIGTAgEAMBMGByqGSM49AgEGCCqBHM9VAYItBHkwdwIBAQQgWE7drmEguOqX6HG0XCdIu6W7YGWgWdnS4dCxPQUDTTKgCgYIKoEcz1UBgi2hRANCAASWKJNtdhAqoecB+dFqj6m/KC6ZwAaHKPOScWfOmPn0EwNMRTi1SYgHTxUqFBrEM3xBr9hAeZ0KUEELoA6sGSs0"
private_key_hex = b64decode(private_key_pem).hex()  # 将pem格式的字符串转换为hex格式
private_key = private_key_hex[72:136]  # 从hex中取私钥(私钥为pem格式转为hex后的字符串的72位到136位)
public_key = private_key_hex[-128:]  # 从hex中提取公钥(公钥为pem格式转为hex后的字符串的后128位)
```

上面代码得到的结果如下：
> 308193020100301306072A8648CE3D020106082A811CCF5501822D047930770201010420 前缀：72

> A19D9E2940F8217304245C4FD82706CF9CEE0ADC0305D10598835F20B9716E19 私钥：64

> A00A06082A811CCF5501822DA14403420004 中缀：57

>
7B1F812CA1F7F62A4F7B944E8A7DF09E2266E7FCDEF0CE5FD834417A15F0429BBFC5CB450A2264AA8932444CE6B9E680506BD68908619820E9FC5CB632A9EA23
公钥：128

```python
from base64 import b64decode

# pem公钥中获取hex格式公钥
public_key_pem = "MFkwEwYHKoZIzj0CAQYIKoEcz1UBgi0DQgAEex+BLKH39ipPe5ROin3wniJm5/ze8M5f2DRBehXwQpu/xctFCiJkqokyREzmueaAUGvWiQhhmCDp/Fy2MqnqIw=="
public_key_hex = b64decode(public_key_pem).hex()
public_key = public_key_hex[-128:]
```

上面代码得到的结果如下：
> 3059301306072a8648ce3d020106082a811ccf5501822d03420004 前缀：54

> 7b1f812ca1f7f62a4f7b944e8a7df09e2266e7fcdef0ce5fd834417a15f0429bbfc5cb450a2264aa8932444ce6b9e680506bd68908619820e9fc5cb632a9ea23 公钥：128