## STEP 1：注册并获取API Key

打开网站：[https://www.nuget.org/](https://www.nuget.org/)  注册账户

并获取appkey


## STEP 2：下载NuGet.exe

NuGet有个命令行工具：NuGet.exe;为了方便使用，请设置机器的PATH环境变量【exe所在目录】，将NuGet.exe的路径添加到PATH中。

在https://www.nuget.org/downloads页面点击下载即可


## STEP 3：开发自己的类库

开发完毕类库后，设置基础数据


## STEP 4：产生并修改nuspec

进入类库文件夹目录下，运行cmd以下命令：

```
nuget spec
```


这时候在当前类库产生一个“ Ydhp.Lib.Setting.nuspec ”文件。

```
<?xml version="1.0" encoding="utf-8"?>
<package >
  <metadata>
    <id>Ydhp.Lib.Setting</id>
    <version>1.0.0</version>
    <title>基础类库</title>
    <authors>liangfy</authors>
    <requireLicenseAcceptance>false</requireLicenseAcceptance>
    <license type="expression">MIT</license>
    <!-- <icon>icon.png</icon> -->
    <projectUrl>https://kown.ydaicloud.com/</projectUrl>
    <description>基础通用类库</description>
    <releaseNotes>Summary of changes made in this release of the</releaseNotes>
    <copyright>杭州银巅科技</copyright>
    <tags>字符串 配置 压缩 加密 文件</tags>
  </metadata>
</package>
```

## STEP 5：产生类库包

在类库当前目录下：

```
nuget pack Ydhp.Lib.Setting.csproj -Prop Configuration=Release
```

这时候当前目录会产生文件Ydhp.Lib.Setting.1.0.0.nupkg文件

## STEP 6：发布类库包

1、生成项目，进入bin\Release文件夹

```
cd bin\Release
```

2、运行以下cmd代码

```
dotnet  nuget push 报名称 -k Api秘钥 -s https://api.nuget.org/v3/index.json
```

```
dotnet  nuget push Ydhp.Lib.Setting.1.0.1.nupkg -k Api秘钥 -s https://api.nuget.org/v3/index.json
```



运行成功后，几分钟后，在地址：[https://www.nuget.org/account/Packages](https://www.nuget.org/account/Packages)  可以查看最新发布情况
