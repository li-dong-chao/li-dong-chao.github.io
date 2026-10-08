---
title: C#与Python实现rsa加密解密
date: 2023-08-05
categories: ["编程"]
---

最近在项目中遇到一个需求，需要在客户端将数据加密，然后传到服务端进行解密。客户端逻辑是用 `C#` 实现的，然后服务端逻辑是用 `Python` 实现的，加密方式要求用rsa非对称加密。

由于 `C#` 使用的私钥、公钥文件和 `Python` 生成的私钥、公钥文件不同，所以需要实现两者的转化。这里记录一下两者的转化过程。

## C#实现rsa非对称加密

首先，可以使用 `C#` 创建私钥、公钥，并保存到 `xml` 格式的文件中（ `C#` 生成的rsa密钥是 `xml` 格式的）。代码如下：

```dotnet
using System;
using System.IO;
using System.Security.Cryptography;
using System.Text;
namespace TestConsoleApplication
{
    public class RSAHelper
    {
        // 创建rsa公钥、私钥
        public static void GenerateRSAKey(string rsaFile, string rsaPubFile)
        {
            var rsa = new RSACryptoServiceProvider(2048);
            // 创建私钥
            string a = rsa.ToXmlString(true);
            // 创建公钥
            string b = rsa.ToXmlString(false);
            // 保存公钥和私钥
            try
            {
                StreamWriter sw1 = new StreamWriter(rsaFile);
                sw1.WriteLine(a);
                sw1.Close();
                StreamWriter sw2 = new StreamWriter(rsaPubFile);
                sw2.WriteLine(b);
                sw2.Close();
            }
            catch (Exception e)
            {
                Console.WriteLine("Exception: " + e.Message);
            }
            finally
            {
                Console.WriteLine("Executing finally block.");
            }
        }
    }
    class Program
    {
        // 主程序
        static void Main(string[] args)
        {
            // 创建用于加密的公钥私钥
            string rsaFile = "rsa.xml";  // 保存私钥的文件
            string rsaPubFile = "rsa_pub.xml";  // 保存公钥的文件
            RSAHelper.GenerateRSAKey(rsaFile, rsaPubFile);
        }
    }
}

```

运行上面的代码，可以得到两个文件 `rsa.xml` 和 `rsa_pub.xml` ，这两个文件分别存储了私钥和公钥信息。

下面继续完善一下前面代码中的 `RSAHelper` 类，使其能够进行rsa加密。

```dotnet
using System;
using System.IO;
using System.Security.Cryptography;
using System.Text;
namespace TestConsoleApplication
{
    public class RSAHelper
    {
        // 这里的测试使用创建的公钥和私钥进行加密解密
        public static void Test(string rsaFile, string rsaPubFile, string dataToEncrypt)
        {
            try
            {
                var privateKey = ReadKeyFile(rsaFile);
                var publicKey = ReadKeyFile(rsaPubFile);
                var encryptedData = EncryptBase64(dataToEncrypt, publicKey);
                Console.WriteLine("加密后:\n{0}", encryptedData);
                var decryptedData = DecryptBase64(encryptedData, privateKey);
                Console.WriteLine("解密后:\n{0}", decryptedData);
            }
            catch (ArgumentNullException)
            {
                Console.WriteLine("Encryption failed.");
            }
            Console.ReadLine();
        }

        public static string ReadKeyFile(string filePath)
        {

            StreamReader fr = new StreamReader(filePath);
            return fr.ReadLine();
        }

        public static string EncryptBase64(string dataToEncrypt, string keyContent, bool doOAEPPadding = false)
        {
            var byteConverter = new UTF8Encoding();
            var bytesToEncrypt = byteConverter.GetBytes(dataToEncrypt);
            var data = Encrypt(bytesToEncrypt, keyContent, doOAEPPadding);
            if (data == null)
            {
                return null;
            }
            var b64Data = Convert.ToBase64String(data);
            return b64Data;
        }

        public static string DecryptBase64(string dataToDecrypt, string keyContent, bool doOAEPPadding = false)
        {
            var bytesToEncrypt = Convert.FromBase64String(dataToDecrypt);
            var data = Decrypt(bytesToEncrypt, keyContent, doOAEPPadding);
            var byteConverter = new UTF8Encoding();
            return byteConverter.GetString(data);
        }

        public static byte[] Encrypt(byte[] dataToEncrypt, string keyContent, bool doOAEPPadding = false)
        {
            try
            {
                byte[] encryptedData;
                using (var rsa = new RSACryptoServiceProvider())
                {
                    rsa.FromXmlString(keyContent);
                    encryptedData = rsa.Encrypt(dataToEncrypt, doOAEPPadding);
                }
                return encryptedData;
            }
            catch (CryptographicException e)
            {
                Console.WriteLine(e.Message); return null;
            }
        }

        public static byte[] Decrypt(byte[] dataToDecrypt, string keyContent, bool doOAEPPadding = false)
        {
            try
            {
                byte[] decryptedData;
                using (var rsa = new RSACryptoServiceProvider())
                {
                    rsa.FromXmlString(keyContent);
                    decryptedData = rsa.Decrypt(dataToDecrypt, doOAEPPadding);
                }
                return decryptedData;
            }
            catch (CryptographicException e)
            {
                Console.WriteLine(e.ToString());
                return null;
            }
        }

        public static void GenerateRSAKey(string rsaFile, string rsaPubFile)
        {
            var rsa = new RSACryptoServiceProvider(2048);
            // 私钥
            string a = rsa.ToXmlString(true);
            // 公钥
            string b = rsa.ToXmlString(false);
            try
            {
                StreamWriter sw1 = new StreamWriter(rsaFile);
                sw1.WriteLine(a);
                sw1.Close();
                StreamWriter sw2 = new StreamWriter(rsaPubFile);
                sw2.WriteLine(b);
                sw2.Close();
            }
            catch (Exception e)
            {
                Console.WriteLine("Exception: " + e.Message);
            }
            finally
            {
                Console.WriteLine("Executing finally block.");
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // 创建用于加密的公钥私钥
            string rsaFile = "rsa.xml";
            string rsaPubFile = "rsa_pub.xml";
            RSAHelper.GenerateRSAKey(rsaFile, rsaPubFile);
            // 使用公钥私钥对内容进行加密
            RSAHelper.Test(rsaFile, rsaPubFile, "你好，李银河");
        }
    }
}
```

## 公钥私钥的转化

由于 `C#` 生成的公钥私钥是 `xml` 格式的，与 `python` 使用的公钥私钥格式不同，所以需要对生成的公钥私钥进行转化。 转化的方式可以参考下面的步骤：

1. 保存 `XMLSec2PEM.java` 文件到本地；
该文件的内容如下：

```java
import java.io.*;
import java.math.BigInteger;
import java.security.*;
import java.security.spec.*;
import java.security.cert.*;
import java.security.interfaces.*;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import org.xml.sax.SAXException;
import org.xml.sax.SAXParseException;
import org.w3c.dom.*;
import org.w3c.dom.DOMException;

/**
 * Converts a private or public RSA key from the XML Security format (used also
 * in .NET) to the RSA key format in PEM format. 
 *
 * The output key is written on standard output.
 *
 * In case of private key, the output format is the "traditional" format of the 
 * key, i.e. NOT the newer PKCS#8 format. The output keys are unencrypted. To
 * convert to PKCS#8 and/or encrypt the private key, use openssl with the
 * -topk8 option: 
 *    openssl pkcs8 -topk8 -in privkey_rsa.pem -out privkey.pem
 * 
 * Created by matous.borak@platanus.cz, 2008.
 * See http://www.platanus.cz/blog/xml_key_to_pem_en.html
 *
 * Heavily inspired by the PvkConvert utility made by Michel Gallant, see
 *   http://www.jensign.com/JavaScience/PvkConvert/. Thanks for it!
 *
 */
public class XMLSec2PEM {

  private static final int PRIVATE_KEY = 1;
  private static final int PUBLIC_KEY = 2;
  private static final String[] PRIVATE_KEY_XML_NODES =  { "Modulus", "Exponent", "P", "Q", "DP", "DQ", "InverseQ", "D" };
  private static final String[] PUBLIC_KEY_XML_NODES =  { "Modulus", "Exponent" };

  public static void main(String args[]) {

    if (args.length != 1) {
      System.out.println("Usage:\n  java XMLSec2PEM <XMLSecurityRSAKeyValueFile.xml>");
      System.exit(0) ;
    }

    try {

      Document XMLSecKeyDoc = parseXMLFile(args[0]);
      System.out.print("Determining the key type: ");
      int keyType = getKeyType(XMLSecKeyDoc);
      if (keyType == PRIVATE_KEY || keyType == PUBLIC_KEY) {
        System.out.println("seems to be a " + ( keyType == PRIVATE_KEY ? "private" : "public" ) + " XML Security key");
      } else {
        System.exit(1);
      }

      System.out.print("Checking the XML file structure: ");
      if (checkXMLRSAKey(keyType, XMLSecKeyDoc)) {
        System.out.println("OK");
      } else {
        System.exit(1);
      }

      String pem = "";
      System.out.println("Outputting the resulting key:\n");
      if (keyType == PRIVATE_KEY) {
        pem = convertXMLRSAPrivateKeyToPEM(XMLSecKeyDoc);
        System.out.println("-----BEGIN PRIVATE KEY-----");
        System.out.println(pem);
        System.out.println("-----END PRIVATE KEY-----");
      } else {
        pem = convertXMLRSAPublicKeyToPEM(XMLSecKeyDoc);
        System.out.println("-----BEGIN PUBLIC KEY-----");
        System.out.println(pem);
        System.out.println("-----END PUBLIC KEY-----");
      }

    }

    catch (Exception e) {
      System.err.println(e);
    }
  }

  private static int getKeyType(Document xmldoc) {

    Node root = xmldoc.getFirstChild();
    if (!root.getNodeName().equals("RSAKeyValue")) {
      System.out.println("Expecting <RSAKeyValue> node, encountered <" + root.getNodeName() + ">");
      return 0;
    }
    NodeList children = root.getChildNodes();
    if (children.getLength() == PUBLIC_KEY_XML_NODES.length) {
      return PUBLIC_KEY;
    } 
    return PRIVATE_KEY;
  }

  private static boolean checkXMLRSAKey(int keyType, Document xmldoc) {

    Node root = xmldoc.getFirstChild();
    NodeList children = root.getChildNodes();
    String[] wantedNodes = {};
    if (keyType == PRIVATE_KEY) {
      wantedNodes = PRIVATE_KEY_XML_NODES;
    } else {
      wantedNodes = PUBLIC_KEY_XML_NODES;
    }
    for (int j = 0; j < wantedNodes.length; j++) {
      String wantedNode = wantedNodes[j];
      boolean found = false;
      for (int i = 0; i < children.getLength(); i++) {
        if (children.item(i).getNodeName().equals(wantedNode)) {
          found = true;
          break;
        }
      }
      if (!found) {
        System.out.println("Cannot find node <" + wantedNode + ">");
        return false;
      }
    }
    return true;
  }

  private static String convertXMLRSAPrivateKeyToPEM(Document xmldoc) {

    Node root = xmldoc.getFirstChild();
    NodeList children = root.getChildNodes();

    BigInteger modulus = null, exponent = null, primeP = null, primeQ = null, 
               primeExponentP = null, primeExponentQ = null, 
               crtCoefficient = null, privateExponent = null;

    for (int i = 0; i < children.getLength(); i++) {

      Node node = children.item(i);
      String textValue = node.getTextContent();
      if (node.getNodeName().equals("Modulus")) {
        modulus = new BigInteger(b64decode(textValue));
      } else if (node.getNodeName().equals("Exponent")) {
        exponent = new BigInteger(b64decode(textValue));
      } else if (node.getNodeName().equals("P")) {
        primeP = new BigInteger(b64decode(textValue));
      } else if (node.getNodeName().equals("Q")) {
        primeQ = new BigInteger(b64decode(textValue));
      } else if (node.getNodeName().equals("DP")) {
        primeExponentP = new BigInteger(b64decode(textValue));
      } else if (node.getNodeName().equals("DQ")) {
        primeExponentQ = new BigInteger(b64decode(textValue));
      } else if (node.getNodeName().equals("InverseQ")) {
        crtCoefficient = new BigInteger(b64decode(textValue));
      } else if (node.getNodeName().equals("D")) {
        privateExponent = new BigInteger(b64decode(textValue));
      }
    }

    try {

      RSAPrivateCrtKeySpec keySpec = new RSAPrivateCrtKeySpec (
        modulus, exponent, privateExponent, primeP, primeQ, 
        primeExponentP, primeExponentQ, crtCoefficient);

      KeyFactory keyFactory = KeyFactory.getInstance("RSA");
      PrivateKey key = keyFactory.generatePrivate(keySpec);
      return b64encode(key.getEncoded());

    } catch (Exception e) {
      System.out.println(e);
    }
    return null;
  }

  private static String convertXMLRSAPublicKeyToPEM(Document xmldoc) {

    Node root = xmldoc.getFirstChild();
    NodeList children = root.getChildNodes();

    BigInteger modulus = null, exponent = null;

    for (int i = 0; i < children.getLength(); i++) {

      Node node = children.item(i);
      String textValue = node.getTextContent();
      if (node.getNodeName().equals("Modulus")) {
        modulus = new BigInteger(b64decode(textValue));
      } else if (node.getNodeName().equals("Exponent")) {
        exponent = new BigInteger(b64decode(textValue));
      }
    }

    try {

      RSAPublicKeySpec keySpec = new RSAPublicKeySpec(modulus, exponent);

      KeyFactory keyFactory = KeyFactory.getInstance("RSA");
      PublicKey key = keyFactory.generatePublic(keySpec);
      return b64encode(key.getEncoded());

    } catch (Exception e) {
      System.out.println(e);
    }
    return null;

  }

  private static Document parseXMLFile(String filename) {
    try {
      DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
      DocumentBuilder builder = factory.newDocumentBuilder();
      Document document = builder.parse( new File(filename) );
      return document;
    } catch(Exception e) {
      System.err.println(e); 
      return null;
    }  
  }

  private static final String b64encode(byte[] data) {
    sun.misc.BASE64Encoder enc = new sun.misc.BASE64Encoder() ;
    String b64str = enc.encodeBuffer(data).trim();
    return b64str;
  }

  private static final byte[] b64decode(String data) {
    try {
      sun.misc.BASE64Decoder dec = new sun.misc.BASE64Decoder() ;
      byte[] bytes = dec.decodeBuffer(data.trim());
      return bytes;
    } catch (IOException e) {
      System.out.println("Exception caught when base64 decoding!" + e.toString());
    }
    return null;
  }
}
```

2. 编译java代码（注意：要求java版本为1.8，java版本过高可能会报错）

```shell
javac XMLSec2PEM.java
```

3. 转换 `xml` 到 `pem`

```shell
java XMLSec2PEM your_key.xml
```

4. 将上述输出的内容保存到文件 `rsa_key.pem`
5. 使用 `openssl` 工具对 `pem` 文件进行转化，否则 `python` 无法直接使用

```shell
openssl pkcs8 -topk8 -in rsa_key.pem -nocrypt -out rsa.key
```

6. 根据 `rsa.key` 文件，可以进一步使用 `openssl` 工具提取出公钥

```shell
openssl rsa -in rsa.key -pubout -out rsa_pub.key
```

执行完以上6步，便可以得到私钥 `rsa.key` 和公钥 `rsa_pub.key` 了。
`python` 使用上述两个文件进行加密解密即可。

## 使用Python进行rsa加密解密

```python
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_v1_5 as Cipher_pkcs1_v1_5
import base64

# 加密
message = "你好，李银河"
rsakey = RSA.importKey(open("rsa_pub.key").read())  # 这里使用前面生成的公钥
cipher = Cipher_pkcs1_v1_5.new(rsakey)  # 创建用于执行pkcs1_v1_5加密或解密的密码
cipher_text = base64.b64encode(cipher.encrypt(message.encode('utf-8')))
print(cipher_text.decode('utf-8'))

# 解密
cipher_text = cipher_text.decode('utf-8')
encrypt_text = cipher_text.encode('utf-8')
rsakey = RSA.importKey(open("rsa.key").read())  # 这里使用前面生成的私钥
cipher = Cipher_pkcs1_v1_5.new(rsakey)  # 创建用于执行pkcs1_v1_5加密或解密的密码
text = cipher.decrypt(base64.b64decode(encrypt_text), "解密失败")
print(text.decode('utf-8'))

```

## 参考链接

- <https://www.codercto.com/a/9090.html>
- <https://www.cnblogs.com/deliaries/p/13445277.html>
