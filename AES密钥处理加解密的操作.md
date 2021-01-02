### AES算法

* AES加密步骤：
    * 1.构造密钥生成器
    * 2.根据随机数算法和指定密钥长度(AES默认128位)来初始化密钥生成器
    * 3.产生AES专用密钥
    * 4.根据指定的AES密码器 "DEFAULT_CIPHER_ALGORITHM" 来创建加密密码器
    * 5.使用加密模式、AES专用密钥、密钥偏移量来初始化加密密码器
    * 6.使用加密器对内容(可指定编码集，如UTF-8)进行加密
    * 7.使用Base64编码格式(可保证字节数组编码前和解码后的数据一致性)对加密后的数据进行编码处理，并返回字符串形式


* AES解密步骤：
    * 1.构造密钥生成器
    * 2.根据随机数算法和指定密钥长度(AES默认128位)来初始化密钥生成器
    * 3.产生AES专用密钥
    * 4.根据指定的AES密码器 "DEFAULT_CIPHER_ALGORITHM" 来创建解密密码器
    * 5.使用解密模式、AES专用密钥、密钥偏移量来初始化解密密码器
    * 6.使用解密器对"使用Base64解码后的"内容进行解密
    * 7.解密后的数据以字符串(可指定编码集，如UTF-8)形式返回
    

```Java
package AESTest;

import javax.crypto.Cipher;
import javax.crypto.KeyGenerator;
import javax.crypto.SecretKey;
import javax.crypto.spec.IvParameterSpec;
import javax.crypto.spec.SecretKeySpec;
import java.security.NoSuchAlgorithmException;
import java.security.SecureRandom;
import java.util.Base64;
import java.util.logging.Logger;

public class AESUtil {

    private static Logger logger = Logger.getLogger(AESUtil.class.getName());

    // 自定义的密码
    public static String KEY = "A669D42F119s10BM5FB8";

    // 偏移量
    private static int offset = 16;

    // 加解密的算法类型
    private static final String KEY_ALGORITHM = "AES";

    // 指定AES密码器(算法为AES，模式为CBC，补码方式为PKCS5Padding)
    private static final String DEFAULT_CIPHER_ALGORITHM = "AES/CBC/PKCS5Padding";

    /**
     * AES加密
     * @param content
     * @return
     */
    public static String encrypt(String content) {
        return encrypt(content, KEY);
    }

    /**
     * AES解密
     * @param content
     * @return
     */
    public static String decrypt(String content) {
        return decrypt(content, KEY);
    }

    /**
     *
     * @param content 需要加密的内容
     * @param password 加密的密码
     * @return 返回Base64编码之后的加密数据
     */
    private static String encrypt(String content, String password) {
        try {
            // 创建初始向量iv用于指定密钥偏移量(可自行指定但必须是128位，因为AES是分组加密的，上一组加密的密文用作下一组的iv)
            IvParameterSpec ivParameterSpec = new IvParameterSpec(password.getBytes(), 0, offset);
            // 根据指定的加密器实例化cipher对象
            Cipher cipher = Cipher.getInstance(DEFAULT_CIPHER_ALGORITHM);
            // 使用加密模式Mode，根据AES专用密钥，密钥偏移量来初始化加密器
            cipher.init(Cipher.ENCRYPT_MODE, getSecretKey(password), ivParameterSpec);
            // 开始加密
            byte[] encryptResult = cipher.doFinal(content.getBytes("UTF-8"));
            // 使用Base64编码格式对加密后的二进制数组进行编码
            return Base64.getEncoder().encodeToString(encryptResult);
        } catch (Exception e) {
            logger.info(e.getMessage());
        }
        return null;
    }

    /**
     *
     * @param content 需要解密的内容
     * @param password 解密的密钥
     * @return 解密之后的内容
     */
    private static String decrypt(String content, String password) {
        try {
            // 创建初始向量iv用于指定密钥偏移量(可自行指定但必须是128位，因为AES是分组加密的，上一组加密的密文用作下一组的iv)
            IvParameterSpec ivParameterSpec = new IvParameterSpec(password.getBytes(), 0, offset);
            // 根据指定的加密器实例化cipher对象
            Cipher cipher = Cipher.getInstance(DEFAULT_CIPHER_ALGORITHM);
            // 使用解密模式Mode，根据AES专用密钥，密钥偏移量来初始化加密器
            cipher.init(Cipher.DECRYPT_MODE, getSecretKey(password), ivParameterSpec);
            // 开始解密
            byte[] decryptResult = cipher.doFinal(Base64.getDecoder().decode(content));
            // 返回解密后的结果
            return new String(decryptResult, "UTF-8");
        } catch (Exception e) {
            logger.info(e.getMessage());
        }
        return null;
    }

    /**
     * 根据指定密钥算法生成密钥
     * @param password
     * @return
     */
    private static SecretKeySpec getSecretKey(String password) {
        // 生成指定密钥算法生成器的keyGenerator对象
        KeyGenerator keyGenerator = null;
        try {
            keyGenerator = KeyGenerator.getInstance(KEY_ALGORITHM);
            SecureRandom random = SecureRandom.getInstance("SHA1PRNG");
            random.setSeed(password.getBytes());
            // 指定AES密钥的默认长度128位
            keyGenerator.init(128, random);
            // 生成密钥
            SecretKey key = keyGenerator.generateKey();
            // 转换成AES专用的密钥
            return new SecretKeySpec(key.getEncoded(), KEY_ALGORITHM);
        } catch (NoSuchAlgorithmException e) {
            logger.info(e.getMessage());
        }
        return null;
    }

    public static void main(String[] args) {
        // 加密前的内容
        String content = "Hello World,你好!";
        System.out.println(content);
        // 加密后的内容
        String encryptContent = AESUtil.encrypt(content);
        System.out.println(encryptContent);
        // 解密后的内容
        System.out.println(AESUtil.decrypt(encryptContent));
    }
}
```