---
layout: post
title: 【个人自用】已经写好的python代码-防止重复造轮子
category: 笔记
tags: Python
keywords: Python
---
* content
{:toc}
一些已经写好的函数，可以直接拿来用的，防止重复造轮子


### 字符串是否包含中文

```
def is_contain_chinese(check_str):
    for ch in check_str:
        if u'\u4e00' <= ch <= u'\u9fff':
            return True
    return False
```

### 调用百度翻译
需要在<http://api.fanyi.baidu.com/>修改可访问的ip
```
import http.client,hashlib,json,urllib,random,time

def baidu_translate(content):
    appid = '20201013000588295'
    secretKey = 'pVgs7O_qj00uaQ0BcsVg'
    httpClient = None
    myurl = '/api/trans/vip/translate'
    q = content
    fromLang = 'en' # 源语言
    toLang = 'zh'   # 翻译后的语言
    salt = random.randint(32768, 65536)
    sign = appid + q + str(salt) + secretKey
    sign = hashlib.md5(sign.encode()).hexdigest()
    myurl = myurl + '?appid=' + appid + '&q=' + urllib.parse.quote(
        q) + '&from=' + fromLang + '&to=' + toLang + '&salt=' + str(salt) + '&sign=' + sign
 
    try:
        httpClient = http.client.HTTPConnection('api.fanyi.baidu.com')
        httpClient.request('GET', myurl)
        # response是HTTPResponse对象
        response = httpClient.getresponse()
        jsonResponse = response.read().decode("utf-8")# 获得返回的结果，结果为json格式
        js = json.loads(jsonResponse)  # 将json格式的结果转换字典结构
        dst = str(js["trans_result"][0]["dst"])  # 取得翻译后的文本结果
        print(dst) # 打印结果
        return dst
    except Exception as e:
        print('err:'+e)
    finally:
        if httpClient:
            httpClient.close()	
```

### list和dict互转
```
def list_to_dict(this_list):
    new_dict = {}
    for l in this_list:
        new_dict[l[0]] = l[1]
    return new_dict


def dict_to_list(this_dict):
    new_list = []
    for k,v in this_dict.items():
        new_list.append([k,v])
    return new_list
```


### pandas中数据特征转换

```
def pre_process(filename):
    df = pd.read_csv(filename)
    df_test = pd.read_csv('test_.csv')
    df.fillna('nan',inplace =True)
    df_test.fillna('nan',inplace =True)

    columns = ['appProtocol','tlsSni','tlsVersion','tlsSubject_C','tlsSubject_ST','tlsSubject_O',
                'tlsSubject_CN','tlsSubject_OU','tlsSubject_emailAddress','tlsIssuerDn_C','tlsSubject_L',
                'tlsIssuerDn_ST','tlsIssuerDn_O','tlsIssuerDn_L','tlsIssuerDn_CN','tlsIssuerDn_OU',
                'tlsIssuerDn_emailAddress']
    df1 = df[columns]
    np1 = np.array(df1,dtype='str')
    enc = preprocessing.OrdinalEncoder()
    enc.fit(np1)
    #print(enc.categories_)
    np2 = enc.transform(np1)
    #print(np2)
    df2 = pd.DataFrame(np2,columns = columns)
    df3 = pd.concat([df.drop(columns,axis=1),df2],axis = 1)
    print(df3)
    df3.to_csv("train_encode.csv",index=False)

    return df3
```