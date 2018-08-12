## 前言

前几天在网上看到一篇文章《教你用微信每天给女票说晚安》，感觉很神奇的样子，随后研究了一下，构思的确是巧妙。好，那就开始动工吧！服务器有了，Python环境有了，IDE打开了...然而...然而...我意识到了一个非常严重的问题...没有女朋友 (T＿T)...  

微信开发已经活跃了很长时间了，在微信开发中有一个神奇的接口它叫**模板消息接口**，它可以根据用户的openid从服务端给用户推送自定义的模板消息，正因如此，我们可以利用这个特征在服务器端随时向用户推送消息（前提是该用户关注了该公众号）。  

总结出3点，1.模板消息的格式可以自定义，2.模板消息的内容可以自定义，3.模板消息发送的时间可以自定义。那么我们可以利用这些性质为自己做一款说**早安**的程序啦！

## 实验环境

1. 阿里云Linux服务器
2. Python环境

## 爱词霸每日一句API介绍

调用地址：`http://open.iciba.com/dsapi/`  
请求方式：GET  
请求参数：  

| 参数 | 必选 | 类型 | 说明 |
|----|----|----|----|
| date | 否 | string | 格式为：`2013-05-06`；如果`date`为，则默认取当天 |
| type | 否 | string | 可选值为`last`和`next`；以`date`日期为准的，`last`返回前一天的，`next`返回后一天的 |

返回类型：JSON  
JSON字段解释：  

| 属性名 | 属性值类型 | 说明 |
|----|----|----|
| sid | string | 每日一句ID |
| tts | string | 音频地址 |
| content | string | 英文内容 |
| note | string | 中文内容 |
| love | string | 每日一句喜欢个数 |
| translation | string | 词霸小编 |
| picture | string | 图片地址 |
| picture2 | string | 大图片地址 |
| caption | string | 标题 |
| dateline | string | 时间 |
| s_pv | string | 浏览数 |
| sp_pv | string | 语音评测浏览数 |
| tags | array | 相关标签 |
| fenxiang_img | string | 合成图片，建议分享微博用的 |


正常返回示例：
``` json
{
  "sid": "3080",
  "tts": "http://news.iciba.com/admin/tts/2018-08-01-day.mp3",
  "content": "No matter how hard we try to be mature, we will always be a kid when we all get hurt and cry. ",
  "note": "不管多努力蜕变成熟，一旦受伤哭泣时，我们还是像个孩子。",
  "love": "1966",
  "translation": "小编的话：这句话出自小说《彼得·潘》。岁月永远年轻，我们慢慢老去。不管你如何蜕变，最后你会发现：童心未泯，是一件值得骄傲的事情。长大有时很简单，但凡事都能抱着一颗童心去快乐享受却未必容易。",
  "picture": "http://cdn.iciba.com/news/word/20180801.jpg",
  "picture2": "http://cdn.iciba.com/news/word/big_20180801b.jpg",
  "caption": "词霸每日一句",
  "dateline": "2018-08-01",
  "s_pv": "0",
  "sp_pv": "0",
  "tags": [
    {
      "id": null,
      "name": null
    }
  ],
  "fenxiang_img": "http://cdn.iciba.com/web/news/longweibo/imag/2018-08-01.jpg"
}
```   

请求示例：  

**Python2请求示例**  
``` python
#!/usr/bin/python2
#coding=utf-8
import json
import urllib2
def get_iciba_everyday():
  url = 'http://open.iciba.com/dsapi/'
  request = urllib2.Request(url)
  response = urllib2.urlopen(request)
  json_data = response.read()
  data = json.loads(json_data)
  return data
print get_iciba_everybody()
```  

**Python3请求示例**  
``` python
#!/usr/bin/python3
#coding=utf-8
import json
import requests
def get_iciba_everyday():
	url = 'http://open.iciba.com/dsapi/'
	r = requests.get(url)
	return json.loads(r.text)
print(get_iciba_everyday())
```  

**PHP请求示例**  
```php
<?php
function https_request($url, $data = null){
  $curl = curl_init();
  curl_setopt($curl, CURLOPT_URL, $url);
  curl_setopt($curl, CURLOPT_HEADER, 0);
  curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, 0);
  curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, 0);
  if (!empty($data)) {
    curl_setopt($curl, CURLOPT_POST, 1);
    curl_setopt($curl, CURLOPT_POSTFIELDS, $data);
  }
  curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
  $output = curl_exec($curl);
  curl_close($curl);
  return $output;
}
function get_iciba_everyday(){
  $url = 'http://open.iciba.com/dsapi/'
  $result = https_request($url);
  $data = json_decode($result);
  return $data;
}
echo get_iciba_everyday();
```  


本接口（每日一句）官方文档：[http://open.iciba.com/?c=wiki](http://open.iciba.com/?c=wiki)
参考资料：[金山词霸 · 开发平台](http://open.iciba.com/?c=wiki)

## 登录微信公众平台接口测试账号

扫描登录公众平台测试号  
[申请测试号的地址 https://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=sandbox/login](https://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=sandbox/login)  
![](https://images2018.cnblogs.com/blog/1222343/201808/1222343-20180802001829278-1923495176.png)

手机上确认登录    
![](https://images2018.cnblogs.com/blog/1222343/201808/1222343-20180802001834438-98300541.jpg)

找到`新增测试模板`，添加模板消息    
![](https://images2018.cnblogs.com/blog/1222343/201808/1222343-20180802001839619-1135914488.png)

填写模板标题`每日一句`，填写如下模板内容    
``` conf
{{content.DATA}}

{{note.DATA}}

{{translation.DATA}}
```
![](https://images2018.cnblogs.com/blog/1222343/201808/1222343-20180802001845221-1785026324.png)

提交保存之后，记住该`模板ID`，一会儿会用到    
 ![](https://images2018.cnblogs.com/blog/1222343/201808/1222343-20180802001849583-1248996138.png)

找到`测试号信息`，记住`appid`和`appsecret`，一会儿会用到    
 ![](https://images2018.cnblogs.com/blog/1222343/201808/1222343-20180802001853944-1298462007.png)

找到`测试号二维码`。手机扫描此二维码，关注之后，你的昵称会出现在右侧列表里，记住该微信号，一会儿会用到（注：此微信号非你真实的微信号）    
 ![](https://images2018.cnblogs.com/blog/1222343/201808/1222343-20180802001858036-1194920342.png)


## 发送微信模板消息的程序

**本程序您只需要修改4个地方即可，请看注释**  

**Python2实现**  
``` python
#!/usr/bin/python2
#coding=utf-8
import json
import urllib2

class iciba:
	# 初始化
    def __init__(self, wechat_config):
    	self.appid = wechat_config['appid']
    	self.appsecret = wechat_config['appsecret']
    	self.template_id = wechat_config['template_id']
    	self.access_token = ''

    # 获取access_token
    def get_access_token(self, appid, appsecret):
    	url = 'https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=%s&secret=%s' % (appid, appsecret)
    	request = urllib2.Request(url)
    	response = urllib2.urlopen(request)
    	json_data = response.read()
    	data = json.loads(json_data)
    	access_token = data['access_token']
    	self.access_token = access_token
    	return self.access_token

    # 获取用户列表
    def get_user_list(self):
    	if self.access_token == '':
    		self.get_access_token(self.appid, self.appsecret)
    	access_token = self.access_token
    	url = 'https://api.weixin.qq.com/cgi-bin/user/get?access_token=%s&next_openid=' % str(access_token)
    	request = urllib2.Request(url)
    	response = urllib2.urlopen(request)
        result = response.read()
        return json.loads(result)

    # 发送消息
    def send_msg(self, openid, template_id, iciba_everyday):
    	msg = {
	    	'touser': openid,
	    	'template_id': template_id,
	    	'url': iciba_everyday['fenxiang_img'],
	    	'data': {
	    		'content': {
	    			'value': iciba_everyday['content'],
	    			'color': '#0000CD'
	    			},
	    		'note': {
		    		'value': iciba_everyday['note'],
	    		},
	    		'translation': {
		    		'value': iciba_everyday['translation'],
	    		}
	    	}
    	}
    	json_data = json.dumps(msg)
    	if self.access_token == '':
    		self.get_access_token(self.appid, self.appsecret)
    	access_token = self.access_token
    	url = 'https://api.weixin.qq.com/cgi-bin/message/template/send?access_token=%s' % str(access_token)
    	request = urllib2.Request(url, data=json_data)
    	response = urllib2.urlopen(request)
        result = response.read()
        return json.loads(result)

    # 获取爱词霸每日一句
    def get_iciba_everyday(self):
    	url = 'http://open.iciba.com/dsapi/'
    	request = urllib2.Request(url)
    	response = urllib2.urlopen(request)
    	json_data = response.read()
    	data = json.loads(json_data)
    	return data

    # 为设置的用户列表发送消息
    def send_everyday_words(self, openids):
    	everyday_words = self.get_iciba_everyday()
    	for openid in openids:
    		result = self.send_msg(openid, self.template_id, everyday_words)
    		if result['errcode'] == 0:
    			print ' [INFO] send to %s is success' % openid
    		else:
    			print ' [ERROR] send to %s is error' % openid

    # 执行
    def run(self, openids=[]):
    	if openids == []:
    		# 如果openids为空，则遍历用户列表
    		result = self.get_user_list()
    		openids = result['data']['openid']
    	# 根据openids对用户进行群发
    	self.send_everyday_words(openids)


if __name__ == '__main__':
	# 微信配置
	wechat_config = {
		'appid': 'xxxxx', #(No.1)此处填写你的appid
		'appsecret': 'xxxxx', #(No.2)此处填写你的appsecret
		'template_id': 'xxxxx' #(No.3)此处填写你的模板消息ID
	}
	
	# 用户列表
	openids = [
		'xxxxx', #(No.4)此处填写你的微信号（微信公众平台上你的微信号）
		#'xxxxx', #如果有多个用户也可以
		#'xxxxx',
	]
	

	# 执行
	icb = iciba(wechat_config)
	
	# run()方法可以传入openids列表，也可不传参数
	# 不传参数则对微信公众号的所有用户进行群发
	icb.run()



```  

**Python3实现**  
``` python
#!/usr/bin/python3
#coding=utf-8
import json
import requests

class iciba:
    # 初始化
    def __init__(self, wechat_config):
        self.appid = wechat_config['appid']
        self.appsecret = wechat_config['appsecret']
        self.template_id = wechat_config['template_id']
        self.access_token = ''

    # 获取access_token
    def get_access_token(self, appid, appsecret):
        url = 'https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=%s&secret=%s' % (str(appid), str(appsecret))
        r = requests.get(url)
        data = json.loads(r.text)
        access_token = data['access_token']
        self.access_token = access_token
        return self.access_token

    # 获取用户列表
    def get_user_list(self):
        if self.access_token == '':
            self.get_access_token(self.appid, self.appsecret)
        access_token = self.access_token
        url = 'https://api.weixin.qq.com/cgi-bin/user/get?access_token=%s&next_openid=' % str(access_token)
        r = requests.get(url)
        return json.loads(r.text)

    # 发送消息
    def send_msg(self, openid, template_id, iciba_everyday):
        msg = {
            'touser': openid,
            'template_id': template_id,
            'url': iciba_everyday['fenxiang_img'],
            'data': {
                'content': {
                    'value': iciba_everyday['content'],
                    'color': '#0000CD'
                    },
                'note': {
                    'value': iciba_everyday['note'],
                },
                'translation': {
                    'value': iciba_everyday['translation'],
                }
            }
        }
        json_data = json.dumps(msg)
        if self.access_token == '':
            self.get_access_token(self.appid, self.appsecret)
        access_token = self.access_token
        url = 'https://api.weixin.qq.com/cgi-bin/message/template/send?access_token=%s' % str(access_token)
        r = requests.post(url, json_data)
        return json.loads(r.text)

    # 获取爱词霸每日一句
    def get_iciba_everyday(self):
        url = 'http://open.iciba.com/dsapi/'
        r = requests.get(url)
        return json.loads(r.text)

    # 为设置的用户列表发送消息
    def send_everyday_words(self, openids):
        everyday_words = self.get_iciba_everyday()
        for openid in openids:
            result = self.send_msg(openid, self.template_id, everyday_words)
            if result['errcode'] == 0:
                print (' [INFO] send to %s is success' % openid)
            else:
                print (' [ERROR] send to %s is error' % openid)

    # 执行
    def run(self, openids=[]):
        if openids == []:
            # 如果openids为空，则遍历用户列表
            result = self.get_user_list()
            openids = result['data']['openid']
        # 根据openids对用户进行群发
        self.send_everyday_words(openids)


if __name__ == '__main__':
    # 微信配置
    wechat_config = {
        'appid': 'xxxxx', #(No.1)此处填写你的appid
        'appsecret': 'xxxxx', #(No.2)此处填写你的appsecret
        'template_id': 'xxxxx' #(No.3)此处填写你的模板消息ID
    }
    
    # 用户列表
    openids = [
        'xxxxx', #(No.4)此处填写你的微信号（微信公众平台上你的微信号）
        #'xxxxx', #如果有多个用户也可以
        #'xxxxx',
    ]

    
    # 执行
    icb = iciba(wechat_config)

    # run()方法可以传入openids列表，也可不传参数
    # 不传参数则对微信公众号的所有用户进行群发
    icb.run()



```

## 测试程序

在Linux上执行程序    
 ![](https://images2018.cnblogs.com/blog/1222343/201808/1222343-20180802001906633-791250923.png)

在手机上查看，已经收到了每日一句的消息    
![](https://images2018.cnblogs.com/blog/1222343/201808/1222343-20180802002321405-74749091.jpg)


## 部署程序

在Linux上设置定时任务
``` bash
crontab -e
```  
添加如下内容  
``` crontab
0 6 * * *    python /root/python/iciba/main-v1.0.py
```  
*注：以上内容的含义是，在每天`6:00`的时候，执行这个Python程序*
查看定时任务是否设置成功  
``` bash
crontab -l
```  

至此，程序部署完成，请您明天`6:00`查收吧！
效果图如下    
![](https://images2018.cnblogs.com/blog/1222343/201808/1222343-20180802061420747-1818912766.jpg)

