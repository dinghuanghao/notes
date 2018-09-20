# How To Use AWS



## AWS E2免费1年

申请AWS帐号后可获得一年的E2免费使用，具体的实例配置见官网。

值得注意的是，可免费使用一年的30G EBS（Elastic Block Store），而且是SSD。可将这30G用于重要数据的中转（EBS和实例是分离管理的）。



## EBS

https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/ebs-using-volumes.html#create_file_system_step

数据可直接通过SFTP上传到EBS，但是速度有点慢（可能是XFTP没有开代理），也可以使用S3进行上传，然后转到EBS中。



## S3

S3的价格： https://amazonaws-china.com/cn/s3/pricing/



使用S3的分段上传功能： https://amazonaws-china.com/cn/blogs/china/uploading-using-s3/



创建存储桶后，可以对其设置一系列的存储桶策略，如果要支持分段上传文件那么需要进行如下配置：

List操作只能针对buket对象，而Get操作只能针对buket/*，因此reousrce需要有两个，或者是分别写两个不同的Statement对象。

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:AbortMultipartUpload",
                "s3:GetObject",
                "s3:ListBucketMultipartUploads",
                "s3:ListMultipartUploadParts",
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::usbuket/*",
                "arn:aws:s3:::usbuket"
            ]
        }
    ]
}
```



文件上传到S3出现了上传了3个小时然后连接超时上传失败的情况。而本地测试使用XFTP直接上传的速度和AWS CLI上传到EBS的速度差别不大。准备改为使用XFTP。

## AWS CLI

windows安装aws cli

+ 下载windows aws cli 安装包 https://s3.amazonaws.com/aws-cli/AWSCLI64.msi
+ 安装msi package
+ 通过python安装awscli， pip install awscli 
+ 在cmd中运行aws help检查安装结果



### proxy

在windows中，新增环境变量

```json
HTTP_PROXY=http://127.0.0.1:1080
HTTPS_PROXY=https://127.0.0.1:1080
```



### cmd

从EC2拷贝整个文件夹到S3：

 aws s3 cp local_path s3://bucket_name --recursive

拷贝单个文件：

aws s3 cp localpath s3://bucket/path



## EC2 P2.xlarge

https://zhuanlan.zhihu.com/p/25066187

单GPU，12GB显存



+ 可通过子网配置在定位到指定的趋于（保持和EBS一致）



使用起来比真实的GPU慢了不少，一开始以为是磁盘读取速度问题，后来发现EBS是SSD，且GPU使用率88%左右，与真实的GPU相差不大，因此应该还是虚拟化的问题。



## EC2 P3.2xlarge

P2的速度非常慢，而P3比本地的1080ti更快。使用竞价实例后1美元每小时。



## 使用技巧

+ 防止致的SSH断连而导致进程被关闭：ssh断连后进程不关闭
  + nohup [command] > log.txt &
+ 周期性查看gpu的使用情况
  + watch -n 1 nvidia-smi
+ 磁盘和实例必须在同一个区域才能挂载。创建实例的时候，可以通过配置子网的方式创建指定子网的实例。
+ 本地和实例间可通过google driver来传递数据