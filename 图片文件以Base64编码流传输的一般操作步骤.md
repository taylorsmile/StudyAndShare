### 图片文件以Base64编码流传输的一般操作步骤

```Java
package ImageTransfer;

import java.io.*;
import java.util.Base64;
import java.util.logging.Logger;

/**
 * @author TaylorSmile
 * @date 2021/1/13 19:14
 * @description 图片以流的形式传输
 */

public class ImageTransferUtil {

    private static Logger logger = Logger.getLogger(ImageTransferUtil.class.getName());

    /**
     * 图片传输
     * @param imageInfo
     * @param targetPath
     * @return
     */
    public static boolean generateImage(String imageInfo, String targetPath) {

        byte[] data = null;
        FileOutputStream fileOutputStream = null;

        if (imageInfo == null) {
            return false;
        }

        Base64.Decoder decoder = Base64.getDecoder();
        // 对加密的图片进行解密，并传输
        data = decoder.decode(imageInfo);

        try {
            fileOutputStream = new FileOutputStream(targetPath);
            fileOutputStream.write(data);
            fileOutputStream.flush();
            return true;
        } catch (FileNotFoundException e) {
            logger.info("路径文件不存在");
            return false;
        } catch (IOException e) {
            logger.info("图片传输失败");
            return false;
        } finally {
                try {
                    fileOutputStream.close();
                } catch (IOException e) {
                    e.printStackTrace();
            }
        }
    }

    /**
     * 获取需要传输的图片，并加密
     * @param imagePath
     * @return
     */
    public static String getImage(String imagePath) {
        InputStream inputStream = null;
        byte[] data = null;

        try {
            inputStream = new FileInputStream(imagePath);
            data = new byte[inputStream.available()];
            inputStream.read(data);
        } catch (FileNotFoundException e) {
            logger.info("路径文件不存在");
        } catch (IOException e) {
            logger.info("图片读取失败");
        } finally {
            try {
                inputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        Base64.Encoder encoder = Base64.getEncoder();
        return encoder.encodeToString(data);
    }
    
    public static void main(String[] args) {
        String imageStr = getImage("指定被上传的图片在本地的绝地路径");
//        System.out.println(imageStr); // base64加密编码后的数据信息
        System.out.println(generateImage(imageStr, "指定被存放解码后的图片的位置"));
    }
}
```