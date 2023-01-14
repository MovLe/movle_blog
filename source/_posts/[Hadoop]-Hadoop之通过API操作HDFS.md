---
title: '[Hadoop]-Hadoop之通过API操作HDFS'
date: 2019-01-01 13:49:00
tags: Hadoop
categories: Hadoop
---
### HDFS客户端操作
### 一.IDEA环境准备
#### 1.修改$MAVEN_HOME/conf/settings.xml
```xml
  <!--本地仓库所在位置-->
<localRepository>F:\m2\repository</localRepository>

<!--使用阿里云镜像去下载Jar包，速度更快-->
  <mirrors>
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>        
    </mirror>
  </mirrors>

<!--本地配置JDK8版本-->
	<profiles>
		<profile>  
		  <id>jdk-1.8</id>  
		   <activation>  
		     <activeByDefault>true</activeByDefault>  
		     <jdk>1.8</jdk>  
		   </activation>  
			<properties>  
				<maven.compiler.source>1.8</maven.compiler.source>  
				<maven.compiler.target>1.8</maven.compiler.target>  
				<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
			</properties>
		</profile>
	</profiles>
```

#### 2.Maven准备,在在项目文件pom.xml文件中添加
```xml
<dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-common</artifactId>
            <version>2.8.4</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
            <version>2.8.4</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.8.4</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.16.10</version>
        </dependency>

        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.7</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/junit/junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
   </dependencies>

```


### 二. 通过API操作HDFS
#### 1.HDFS获取文件系统
(1)详细代码：

```java
     /**
     * 打印本地hadoop地址值
     * IO的方式写代码
     */
    @Test
    public void intiHDFS() throws IOException {
        //F2 可以快速的定位错误
        // alt + enter自动找错误
        //1.创建配信信息对象 ctrl + alt + v  后推前  ctrl + shitl + enter 补全
        Configuration conf = new Configuration();

        //2.获取文件系统
        FileSystem fs = FileSystem.get(conf);

        //3.打印文件系统
        System.out.println(fs.toString());
    }

```
#### 2.HDFS文件上传
(1)详细代码
```java
    /**
     * 上传代码
     * 注意：如果上传的内容大于128MB,则是2块
     */
    @Test
    public void putFileToHDFS() throws Exception {
        //注：import org.apache.hadoop.conf.Configuration;
        //ctrl + alt + v 推动出对象
        //1.创建配置信息对象
        Configuration conf = new Configuration();

        //2.设置部分参数
        conf.set("dfs.replication","2");

        //3.找到HDFS的地址
        FileSystem fs = FileSystem.get(new URI("hdfs://bigdata111:9000"), conf, "root");

        //4.上传本地Windows文件的路径
        Path src = new Path("D:\\hadoop-2.7.2.rar");

        //5.要上传到HDFS的路径
        Path dst = new Path("hdfs://bigdata111:9000/Andy");

        //6.以拷贝的方式上传，从src -> dst
        fs.copyFromLocalFile(src,dst);

        //7.关闭
        fs.close();

        System.out.println("上传成功");
}

```

#### 3.HDFS文件下载
(1)详细代码：

```java
     /**
     * hadoop fs -get /HDFS文件系统
     * @throws Exception
     */
    @Test
    public void getFileFromHDFS() throws Exception {
        //1.创建配置信息对象  Configuration:配置
        Configuration conf = new Configuration();

        //2.找到文件系统
        //final URI uri     ：HDFS地址
        //final Configuration conf：配置信息
        // String user ：Linux用户名
        FileSystem fs = FileSystem.get(new URI("hdfs://bigdata111:9000"), conf, "root");

        //3.下载文件
        //boolean delSrc:是否将原文件删除
        //Path src ：要下载的路径
        //Path dst ：要下载到哪
        //boolean useRawLocalFileSystem ：是否校验文件
        fs.copyToLocalFile(false,new Path("hdfs://bigdata111:9000/README.txt"),
                new Path("F:\\date\\README.txt"),true);

        //4.关闭fs
        //alt + enter 找错误
        //ctrl + alt + o  可以快速的去除没有用的导包
        fs.close();
        System.out.println("下载成功");
    }
```

#### 4.HDFS目录创建
(1)详细代码
```java
     /**
     * hadoop fs -mkdir /xinshou
     */
    @Test
    public void mkmdirHDFS() throws Exception {
        //1.创新配置信息对象
        Configuration configuration = new Configuration();

        //2.链接文件系统
        //final URI uri  地址
        //final Configuration conf  配置
        //String user   Linux用户
        FileSystem fs = FileSystem.get(new URI("hdfs://bigdata111:9000"), configuration, "root");

        //3.创建目录
        fs.mkdirs(new Path("hdfs://bigdata111:9000/Good/Goog/Study"));

        //4.关闭
        fs.close();
        System.out.println("创建文件夹成功");
    }
```
#### 5.HDFS文件夹删除
(1)详细代码

```java
    /**
     * hadoop fs -rm -r /文件
     */
    @Test
    public void deleteHDFS() throws Exception {
        //1.创建配置对象
        Configuration conf = new Configuration();

        //2.链接文件系统
        //final URI uri, final Configuration conf, String user
        //final URI uri  地址
        //final Configuration conf  配置
        //String user   Linux用户
        FileSystem fs = FileSystem.get(new URI("hdfs://bigdata111:9000"), conf, "root");

        //3.删除文件
        //Path var1   : HDFS地址
        //boolean var2 : 是否递归删除
        fs.delete(new Path("hdfs://bigdata111:9000/a"),false);

        //4.关闭
        fs.close();
        System.out.println("删除成功啦");
    }
```

#### 6.HDFS文件名更改
(1)详细代码：

```java
    @Test
    public void renameAtHDFS() throws Exception{
	// 1 创建配置信息对象
	Configuration configuration = new Configuration();	
	FileSystem fs = FileSystem.get(new URI("hdfs://bigdata111:9000"),configuration, "itstar");
		
	//2 重命名文件或文件夹
	fs.rename(new Path("hdfs://bigdata111:9000/user/itstar/hello.txt"), new Path("hdfs://bigdata111:9000/user/itstar/hellonihao.txt"));
	fs.close();
}
```

#### 7.HDFS文件详情查看
查看文件名称、权限、长度、块信息

```java
    /**
     * 查看【文件】名称、权限等
     */
    @Test
    public void readListFiles() throws Exception {
        //1.创建配置对象
        Configuration conf = new Configuration();

        //2.链接文件系统
        FileSystem fs = FileSystem.get(new URI("hdfs://bigdata111:9000"), conf, "root");

        //3.迭代器
        RemoteIterator<LocatedFileStatus> listFiles = fs.listFiles(new Path("/"), true);

        //4.遍历迭代器
        while (listFiles.hasNext()){
            //一个一个出
            LocatedFileStatus fileStatus = listFiles.next();

            //名字
            System.out.println("文件名：" + fileStatus.getPath().getName());
            //块大小
            System.out.println("大小：" + fileStatus.getBlockSize());
            //权限
            System.out.println("权限：" + fileStatus.getPermission());
            System.out.println(fileStatus.getLen());


            BlockLocation[] locations = fileStatus.getBlockLocations();

            for (BlockLocation bl:locations){
                System.out.println("block-offset:" + bl.getOffset());
                String[] hosts = bl.getHosts();
                for (String host:hosts){
                    System.out.println(host);
                }
            }

            System.out.println("------------------华丽的分割线----------------");
        }
```

#### 8.HDFS文件和文件夹判断

```java
    /**
     * 判断是否是个文件还是目录，然后打印
     * @throws Exception
     */
    @Test
    public void judge() throws Exception {
        //1.创建配置文件信息
        Configuration conf = new Configuration();

        //2.获取文件系统
        FileSystem fs = FileSystem.get(new URI("hdfs://bigdata111:9000"), conf, "root");

        //3.遍历所有的文件
        FileStatus[] liststatus = fs.listStatus(new Path("/Andy"));
        for(FileStatus status :liststatus)
        {
            //判断是否是文件
            if (status.isFile()){
                //ctrl + d:复制一行
                //ctrl + x 是剪切一行，可以用来当作是删除一行
                System.out.println("文件:" + status.getPath().getName());
            } else {
                System.out.println("目录:" + status.getPath().getName());
            }
        }
    }

```
### 三. 通过IO流操作HDFS

#### 1.HDFS文件上传

```java
    /**
     * IO流方式上传
     *
     * @throws URISyntaxException
     * @throws FileNotFoundException
     * @throws InterruptedException
     */
    @Test
    public void putFileToHDFSIO() throws URISyntaxException, IOException, InterruptedException {
        //1.创建配置文件信息
        Configuration conf = new Configuration();

        //2.获取文件系统
        FileSystem fs = FileSystem.get(new URI("hdfs://bigdata111:9000"), conf, "root");

        //3.创建输入流
        FileInputStream fis = new FileInputStream(new File("F:\\date\\Sogou.txt"));

        //4.输出路径
        //注意：不能/Andy  记得后边写个名 比如：/Andy/Sogou.txt
        Path writePath = new Path("hdfs://bigdata111:9000/Andy/Sogou.txt");
        FSDataOutputStream fos = fs.create(writePath);

        //5.流对接
        //InputStream in    输入
        //OutputStream out  输出
        //int buffSize      缓冲区
        //boolean close     是否关闭流
        try {
            IOUtils.copyBytes(fis,fos,4 * 1024,false);
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            IOUtils.closeStream(fos);
            IOUtils.closeStream(fis);
            System.out.println("上传成功啦");
        }
    }
```

#### 2.HDFS文件下载

```java
     /**
     * IO读取HDFS到本地
     *
     * @throws URISyntaxException
     * @throws IOException
     * @throws InterruptedException
     */
    @Test
    public void getFileToHDFSIO() throws URISyntaxException, IOException, InterruptedException {
        Configuration configuration = new Configuration();

        FileSystem fileSystem = FileSystem.get(new URI("hdfs://bigdata111:9000"),configuration,"root");


        FileOutputStream fos = new FileOutputStream(new File("/Users/macbook/TestInfo/downloadFileFromHdfsIO.txt"));

        Path readPath = new Path("hdfs://bigdata111:9000/aa.txt");

        FSDataInputStream fis = fileSystem.open(readPath);

        try {
            IOUtils.copyBytes(fis,fos,4*1024,false);
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            IOUtils.closeStream(fis);
            IOUtils.closeStream(fos);
            System.out.println("下载成功！");
        }
    }
```

#### 3.定位文件读取
(1)下载第一块
```java
     /**
     * IO读取第一块的内容
     *
     * @throws Exception
     */
    @Test
    public void  readFlieSeek1() throws Exception {
        //1.创建配置文件信息
        Configuration conf = new Configuration();

        //2.获取文件系统
        FileSystem fs = FileSystem.get(new URI("hdfs://bigdata111:9000"), conf, "root");

        //3.输入
        Path path = new Path("hdfs://bigdata111:9000/Andy/hadoop-2.7.2.rar");
        FSDataInputStream fis = fs.open(path);

        //4.输出
        FileOutputStream fos = new FileOutputStream("F:\\date\\readFileSeek\\A1");

        //5.流对接
        byte[] buf = new byte[1024];
        for (int i = 0; i < 128 * 1024; i++) {
            fis.read(buf);
            fos.write(buf);
        }

        //6.关闭流
        IOUtils.closeStream(fos);
        IOUtils.closeStream(fis);
    }
```

(2)下载第二块

```java
    /**
     * IO读取第二块的内容
     *
     * @throws Exception
     */
    @Test
    public void readFlieSeek2() throws Exception {
        //1.创建配置文件信息
        Configuration conf = new Configuration();

        //2.获取文件系统
        FileSystem fs = FileSystem.get(new URI("hdfs://bigdata111:9000"), conf, "root");

        //3.输入
        Path path = new Path("hdfs://bigdata111:9000/Andy/hadoop-2.7.2.rar");
        FSDataInputStream fis = fs.open(path);

        //4.输出
        FileOutputStream fos = new FileOutputStream("F:\\date\\readFileSeek\\A2");

        //5.定位偏移量/offset/游标/读取进度 (目的：找到第一块的尾巴，第二块的开头)
        fis.seek(128 * 1024 * 1024);

        //6.流对接
        IOUtils.copyBytes(fis, fos, 1024);

        //7.关闭流
        IOUtils.closeStream(fos);
        IOUtils.closeStream(fis);
    }

```
(3)合并文件
在window命令窗口中执行
type A2 >> A1  然后更改后缀为rar即可