## 本地ftp服务器配置以及工具类的使用

1. ftp连接配置文件

```properties
#ftp配置文件
ftp_address=localhost
ftp_port=21
ftp_username=mike
ftp_password=123
ftp_basepath=/file

image_base_url=ftp://mike:123@localhost/file/images
```

2. 将该properties文件交由spring管理，便于数据注入实体bean

```xml
<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
              <property name="locations">
                     <list>
                            <value>classpath:datasource.properties</value>
                            <value>classpath:ftp.properties</value>
                     </list>
              </property>
       </bean>
<!--以上为多properties写法，如果只有一个等同于如下写法-->
<!--<context:property-placeholder location="classpath:datasource.properties"/>-->
```

3. ftpconfig文件及属性注入

```java
package com.zte.zshop.service.ftp;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

/**
 * Author:helloboy
 * Date:2019-06-05 22:10
 * Description:<描述>
 */
@Component
public class FTPConfig {

    @Value("${ftp_address}")
    private String ftp_address;

    @Value("${ftp_port}")
    private String ftp_port;

    @Value("${ftp_username}")
    private String ftp_username;

    @Value("${ftp_password}")
    private String ftp_password;

    @Value("${ftp_basepath}")
    private String ftp_basepath;

    @Value("${image_base_url}")
    private String image_base_url;

    public String getFtp_address() {
        return ftp_address;
    }

    public void setFtp_address(String ftp_address) {
        this.ftp_address = ftp_address;
    }

    public String getFtp_port() {
        return ftp_port;
    }

    public void setFtp_port(String ftp_port) {
        this.ftp_port = ftp_port;
    }

    public String getFtp_username() {
        return ftp_username;
    }

    public void setFtp_username(String ftp_username) {
        this.ftp_username = ftp_username;
    }

    public String getFtp_password() {
        return ftp_password;
    }

    public void setFtp_password(String ftp_password) {
        this.ftp_password = ftp_password;
    }

    public String getFtp_basepath() {
        return ftp_basepath;
    }

    public void setFtp_basepath(String ftp_basepath) {
        this.ftp_basepath = ftp_basepath;
    }

    public String getImage_base_url() {
        return image_base_url;
    }

    public void setImage_base_url(String image_base_url) {
        this.image_base_url = image_base_url;
    }
}

```

4. FTPUtils工具类，直接调用将文件写入ftp服务器指定位置

```java
package com.zte.zshop.service.ftp;

import org.apache.commons.net.ftp.FTP;
import org.apache.commons.net.ftp.FTPClient;
import org.apache.commons.net.ftp.FTPReply;

import java.io.IOException;
import java.io.InputStream;

public class FTPUtils {

  /**
     *ftp上传图片方法
     *@param ftpConfig  由spring管理的FtpConfig配置，在调用本方法时，可以在使用此方法的类中通过@AutoWared注入该属性。由于本方法是静态方法，所以不能在此注入该属性
     *@param picNewName 图片新名称--防止重名 例如："1.jpg"
     *@param picSavePath 图片保存路径。注：最后访问路径是 ftpConfig.getFTP_ADDRESS()+"/images"+picSavePath
     *@return 若上传成功，返回图片的访问路径，若上传失败，返回null
     *@throws IOException
     */
  public static String pictureUploadByConfig(FTPConfig ftpConfig,String picNewName,String picSavePath,InputStream inputStream) throws IOException{

    String picHttpPath = null;
    boolean flag = uploadFile(ftpConfig.getFtp_address(), ftpConfig.getFtp_port(), ftpConfig.getFtp_username(),ftpConfig.getFtp_password(), ftpConfig.getFtp_basepath(), picSavePath, picNewName, inputStream);

    if(!flag){
      return picHttpPath;
    }

    //picHttpPath = ftpConfig.getFTP_ADDRESS()+"/images"+picSavePath+"/"+picNewName;
    picHttpPath = ftpConfig.getImage_base_url()+picSavePath+"/"+picNewName;
    System.out.println("==="+picHttpPath);
    return picHttpPath;
  }

  /**
     * Description: 向FTP服务器上传文件
     * @param host FTP服务器hostname
     * @param username FTP登录账号
     * @param password FTP登录密码
     * @param basePath FTP服务器基础目录
     * @param filePath FTP服务器文件存放路径。例如分日期存放：/2015/01/01。文件的路径为basePath+filePath
     * @param filename 上传到FTP服务器上的文件名
     * @param input 输入流
     * @return 成功返回true，否则返回false
     */
  public static boolean uploadFile(String host, String ftpPort, String username, String password, String basePath,
                                   String filePath, String filename, InputStream input) {
    int port = Integer.parseInt(ftpPort);
    boolean result = false;
    FTPClient ftp = new FTPClient();
    try {
      int reply;
      ftp.connect(host, port);// 连接FTP服务器
      // 如果采用默认端口，可以使用ftp.connect(host)的方式直接连接FTP服务器
      ftp.login(username, password);// 登录
      reply = ftp.getReplyCode();
      if (!FTPReply.isPositiveCompletion(reply)) {
        ftp.disconnect();
        return result;
      }

      // 检查上传路径是否存在 如果不存在返回false
      boolean flag = ftp.changeWorkingDirectory(basePath+filePath);
      if (!flag) {
        // 创建上传的路径 该方法只能创建一级目录，在这里如果/home/ftpuser存在则可创建image
        boolean b = ftp.makeDirectory(basePath+filePath);
        System.out.println("aaa-->"+b);
      }
      // 指定上传路径
      ftp.changeWorkingDirectory(basePath+filePath);
      //设置上传文件的类型为二进制类型
      ftp.setFileType(FTP.BINARY_FILE_TYPE);
      ftp.enterLocalPassiveMode();//这个设置允许被动连接--访问远程ftp时需要
      //上传文件
      if (!ftp.storeFile(filename, input)) {
        return result;
      }
      input.close();
      ftp.logout();
      result = true;
    } catch (IOException e) {
      e.printStackTrace();
    } finally {
      if (ftp.isConnected()) {
        try {
          ftp.disconnect();
        } catch (IOException ioe) {
        }
      }
    }
    return result;
  }
}
```

5. service具体使用中的其他工具类 StringUtils

```java
package com.zte.zshop.uitls;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Random;

/**
 * Author:helloboy
 * Date:2019-05-18 15:02
 * Description:<描述>
 */
public class StringUtils {
	/*依据时分秒设置文件名，避免重名*/
    public static String renameFileName(String fileName){
        //获取文件名的后缀
        int dotIndex = fileName.lastIndexOf(".");
        String suffix = fileName.substring(dotIndex);

        //重构新的文件名，格式为：年月日时分秒+（1-100）的随机数+后缀
        return new SimpleDateFormat("yyyyMMddHHmmss").format(new Date())+
                new Random().nextInt(100)+suffix;
    }
	/*获取设置目录名*/
    public static String generateRandomDir(String filename){
        int hashcode = filename.hashCode();
        int dir1 = hashcode & 0xf;//得到名称为1-16的一级目录
        int dir2 = (hashcode & 0xF0) >> 4;//得到名称为1-16的一级目录
        return "/"+dir1+"/"+dir2;
    }
}

```

6. serviceImpl中的具体操作,上传图片文件部分

```java
//获取重构文件
        String fileName = StringUtils.renameFileName(productDto.getFileName());
        //获取文件路径
        //String filePath = productDto.getUploadPath()+"\\"+fileName;
        String picSavaPath = StringUtils.generateRandomDir(fileName);
        //将文件上传（即将文件从本地拷贝到文件服务器中）
        String filePath="";
        try {
           // StreamUtils.copy(productDto.getInputStream(), new FileOutputStream(filePath));
            filePath = FTPUtils.pictureUploadByConfig(ftpConfig,fileName,picSavaPath,productDto.getInputStream());
        } catch (IOException e) {
                throw new FileUploadException("文件上传失败"+e.getMessage());
        }
//此后只需要将返回的filepath写入数据库在前端调用即可
```

7. 重要前提：ftp服务的使用需要依赖ftp指定jar包

```xml
<!--ftp服务  start-->
    <dependency>
      <groupId>commons-net</groupId>
      <artifactId>commons-net</artifactId>
      <version>3.3</version>
    </dependency>
    <!--ftp服务  end-->
```

