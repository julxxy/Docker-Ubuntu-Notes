# 为`typora`配置阿里云`oss`图床

![image-20210117015837940](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210117015837940.png)

```json
{
  "picBed": {
    "uploader": "aliyun",
    "aliyun": {
      "accessKeyId": "你的ak",
      "accessKeySecret": "你的sk",
      "bucket": "你的bucket名称",
      "area": "oss-cn-shanghai",
      "path": "你的bucket中的图片文件夹/",
      "customUrl": "https://${你的bucket名称}.oss-cn-shanghai.aliyuncs.com",
      "options": ""
    }
  },
  "picgoPlugins": {}
}
```
