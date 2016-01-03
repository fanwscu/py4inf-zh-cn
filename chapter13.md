# 第13章 Web Services

掌握了通过HTTP编写文档检索与解析程序之后，接下来为我们自己创建的文档，开发一种其他应用程序可调用的文档服务，这就不是一件很苦难的事情了。这里说的文档不是显示在网络浏览器中的HTML网页。

网络数据交换包括两种常见格式：可扩展标记语言XML（eXtensible Markup Language）和JSON（JavaScript Object Notation）。XML已经使用很长时间，适合于交换文档类型的数据。当程序彼此之间只需要交换字典、列表或其他内部信息时，一般会使用JSON。接下来分别介绍这两种格式。

## 13.1 可扩展标记语言XML

XML与HTML看起非常像，但XML比HTML的结构化程度更高。下面是一个XML文档示例：

```
<person>
  <name>Chuck</name>
  <phone type="intl">
     +1 734 303 4456
   </phone>
   <email hide="yes"/>
</person>
```

通常可以把XML文档想象成一个树结构，这样有助于理解。示例中顶层标签是person，其他标签是它的子节点，如phone。

![enter image description here](http://www.pythonlearn.com/html-270/book014.png)

## 13.2 XML解析

下面是一个简单的示例程序，对XML文件进行解析，从中提取一些数据元素：

```
import xml.etree.ElementTree as ET

data = "'
<person>
  <name>Chuck</name>
  <phone type="intl">
     +1 734 303 4456
   </phone>
   <email hide="yes"/>
</person>"'

tree = ET.fromstring(data)
print 'Name:',tree.find('name').text
print 'Attr:',tree.find('email').get('hide')
```

调用fromstring将XML的字符串表示转换为一棵XML节点树。当XML被视为一棵树，我们就有一系列方法来抽取XML中的数据片段。

使用find函数对XML树进行搜索，检索出匹配特定标签的节点。每个节点包含一些文本，一些属性（例如隐藏的）以及一些子节点。每个节点都可以成为节点树的顶点。

```
Name: Chuck
Attr: yes
```

XML解析器，例如ElementTree，可以处理许多XML有效性规则，让我们无需操心XML语法规则就可以抽取需要的XML数据片段。由于示例的XML结构过于简单，体现不出这一优势。

## 13.3 节点循环

通常，XML会拥有多个节点，我们需要写一个循环来处理所有节点。下面的程序使用循环找出user节点：

```
import xml.etree.ElementTree as ET

input = "'
<stuff>
    <users>
        <user x="2">
            <id>001</id>
            <name>Chuck</name>
        </user>
        <user x="7">
            <id>009</id>
            <name>Brent</name>
        </user>
    </users>
</stuff>"'

stuff = ET.fromstring(input)
lst = stuff.findall('users/user')
print 'User count:', len(lst)

for item in lst:
    print 'Name', item.find('name').text
    print 'Id', item.find('id').text
    print 'Attribute', item.get('x')
```

使用Python列表来表示XML树中user结构的子树， findall方法检索这个列表。然后，编写一个循环，检索出每一个user节点，打印出name和id的文本元素，以及user节点的x属性值。

```
User count: 2
Name Chuck
Id 001
Attribute 2
Name Brent
Id 009
Attribute 7
```

## 13.4 JavaScript对象标记 - JSON

JSON格式的灵感来自于JavaScript语言的对象与数组格式。Python的出现早于JavaScript，Python的字典与列表语法对JSON语言有一定影响。所以，JSON格式可视为Python列表与字典的组合。

上面的XML示例使用JSON格式来表示，如下代码所示，两者大致等价：

```
{
  "name" : "Chuck",
  "phone" : {
    "type" : "intl",
    "number" : "+1 734 303 4456"
   },
   "email" : {
     "hide" : "yes"
   }
}
```

你会注意到一些差异。首先，XML中“phone”标签有一个“intl”属性，在JSON里处理成键值对。另外，XML的“person”标签没有了，取而代之的是一组大括号。

由于JSON比XML功能少，所以JSON的结构比XML简单。但是，JSON的优势在于，它直接映射成字典与列表的组合。几乎所有编程语言与Python的字典和列表都存在某种程度上的等价关系，所以JSON是两个协作应用程序之间非常合适的数据交换格式。

与XML相比，JSON相对简单，正迅速成为应用程序之间数据交换格式的不二选择。

## 13.5 JSON解析

通过字典（对象）与列表的嵌套来构造我们需要的JSON。这个示例中用户的列表由键值对集合组成（也就是一个字典）。因此，我们有了一个字典列表。

在下面的程序中，我们使用内置的**json**库来解析JSON，读取其中的数据。仔细比较等价的XML数据和上面的代码，有一点必须提前知晓，JSON的细节较少。我们最终得到的列表包含用户信息，每个用户的信息是键值对集合。JSON的优点是格式简洁，缺点是自描述能力不强。

```
import json

input = "'
[
  { "id" : "001",
    "x" : "2",
    "name" : "Chuck"
  } ,
  { "id" : "009",
    "x" : "7",
    "name" : "Brent"
  }
]"'

info = json.loads(input)
print 'User count:', len(info)

for item in info:
    print 'Name', item['name']
    print 'Id', item['id']
    print 'Attribute', item['x']
```

从解析后的JSON与XML分别抽取数据的两段代码进行比较，你会发现json.loads()得到一个Python列表，通过for循环进行遍历，列表的每个数据项是一个Python字典，其中使用了Python索引操作符来抽取每个用户的各个字节。JSON经过解析后，我们就得到了原生的Python对象与结构。由于返回的数据就是简单的原生Python结构，就没必要使用JSON库继续深入解析JSON了。

程序执行结果如下，与上面的XML版本几乎相同。

```
User count: 2
Name Chuck
Id 001
Attribute 2
Name Brent
Id 009
Attribute 7
```

总之，Web Service的行业发展趋势是从XML转向JSON。由于JSON足够简单，能够直接映射到编程语言已有的原生数据结构，JSON的使用让解析与数据抽取变得更简单。但是，XML比JSON在自我描述方面更强，因此在某些应用程序中XML仍然有一定优势。例如，大多数文字处理器内部存储文档采用XML而不是JSON。

## 13.6 应用编程接口API

我们已经学习了通过超文本传输协议HTTP在应用程序之间交换数据，了解了使用XML与JSON在这些应用程序之间发送与接收复杂数据的表示方法。

下一步是使用这些技术在应用程序之间定义与签订“合同”。应用程序之间合同的一般名称是应用程序接口或APIs。当我们使用一个API，应用程序通常会提供一组可供其他应用程序使用的服务。应用程序发布的APIs（即“规则”）在访问服务时必须遵守。

当我们开始构建自己的应用程序，功能上要能访问其他应用程序提供的服务，这种方式称为面向服务的架构**SOA**（Service-Oriented Architecture）。SOA方式是应用程序使用其他应用程序服务的总称。非SOA方式是应用程序作为单一独立的程序，自身包含程序运行中所需的所有代码。

互联网应用中存在许多SOA实例。我们可以在一个网站上完成旅行机票与酒店预定以及租车等一系列活动。酒店数据没有存储在航空公司的计算机上。相反，航空公司的计算机与酒店的计算机签订服务合同，检索酒店数据，并呈现给用户。当用户使用航空公司的网站预订了一家酒店，航空公司的网站使用酒店系统的Web Service来完成这笔预订。当涉及整个交易的信用卡支付时，仍然会有其他计算机参与到这个过程。

![enter image description here](http://www.pythonlearn.com/html-270/book015.png)

面向服务的架构拥有许多优点，其中包括：（1）数据始终只存在一处，这对酒店预订这类情况来说特别重要，我们不希望出现多次提交；（2）数据的拥有者能够设置数据的使用规则。基于这些优点，SOA系统必须精心设计，以达到具备良好的性能与满足用户的需求。

应用程序通过网络发布一组可用的API服务，我们称之为Web Services。

## 13.7 Google地理编码Web Service

Google的Web Service非常优秀，让我们能充分利用其庞大的地理信息数据库。我们可以向Google的地理编码API提交诸如“Ann Arbor, MI”这样的地理搜索字符串。Google会根据搜索字符串返回最佳猜测，在地图上告诉我们想找的地方以及附近的地标性建筑。

地理编码服务是免费的，但有访问次数限制，不能在商业应用程序中无限制使用。如果有一些调查数据，其中被调查者在自由格式的输入框中输入了一个地址，你就可以使用这个API清洗这些数据，效果会很不错。

*当使用类似Google地理编码API的免费API时，你需要遵守这些资源的使用规定。如果有太多用户滥用API，Google会关闭或大幅度缩减这项免费服务。*

通过阅读这项服务的在线文档了解使用方法。不过，这个操作非常简单，可以直接在浏览器中测试，在地址栏输入如下URL：

```
http://maps.googleapis.com/maps/api/geocode/json?sensor=false &address=Ann+Arbor%2C+MI
```

粘贴到浏览器之前，确保是原始的URL，移除URL中的任何空格。

下面是一个简单应用程序，提示用户输入一个搜索字符串，调用Google的地理编码API，从返回的JSON中抽取信息。

```
import urllib
import json

serviceurl = 'http://maps.googleapis.com/maps/api/geocode/json?'

while True:
    address = raw_input('Enter location: ')
    if len(address) < 1 : break

    url = serviceurl + urllib.urlencode({'sensor':'false',
          'address': address})
    print 'Retrieving', url
    uh = urllib.urlopen(url)
    data = uh.read()
    print 'Retrieved',len(data),'characters'

    try: js = json.loads(str(data))
    except: js = None
    if 'status' not in js or js['status'] != 'OK':
        print '==== Failure To Retrieve ===='
        print data
        continue

    print json.dumps(js, indent=4)

    lat = js["results"][0]["geometry"]["location"]["lat"]
    lng = js["results"][0]["geometry"]["location"]["lng"]
    print 'lat',lat,'lng',lng
    location = js['results'][0]['formatted_address']
    print location
```

该程序获取输入的搜索字符串，将其作为合适的编码参数，构建URL，使用urllib从Google地理编码API检索文本内容。与固定的网页不同，这里得到的数据取决于发送的参数与Google服务器中存储的地理数据。

一旦获取到JSON数据，我们使用json库对其进行解析，做一些检查以确保收到良好的数据，然后抽取我们需要的数据。

程序执行结果如下（下面只展示了返回的部分JSON数据）：

```
$ python geojson.py
Enter location: Ann Arbor, MI
Retrieving http://maps.googleapis.com/maps/api/
  geocode/json?sensor=false&address=Ann+Arbor%2C+MI
Retrieved 1669 characters
{
    "status": "OK",
    "results": [
        {
            "geometry": {
                "location_type": "APPROXIMATE",
                "location": {
                    "lat": 42.2808256,
                    "lng": -83.7430378
                }
            },
            "address_components": [
                {
                    "long_name": "Ann Arbor",
                    "types": [
                        "locality",
                        "political"
                    ],
                    "short_name": "Ann Arbor"
                }
            ],
            "formatted_address": "Ann Arbor, MI, USA",
            "types": [
                "locality",
                "political"
            ]
        }
    ]
}
lat 42.2808256 lng -83.7430378
Ann Arbor, MI, USA
Enter location:
```

你可以下载<http://www.py4inf.com/code/geojson.py>和<http://www.py4inf.com/code/geoxml.py>两个文件，搞清楚Google地理编码API的JSON与XML之间的差别。

## 13.8 安全与API用法

通常情况下，你需要某种类型的API密钥才能访问服务提供者的API。这样设计的初衷是服务提供者想要知道谁在使用他们的服务，以及每个用户的使用情况。可能他们提供免费服务，但会根据服务层次收费，或者在特定时间段限制个体用户的请求数量。

在一些情况中，你一旦得到API密钥，在调用API时只需将密钥作为POST数据的一部分，或者作为URL的一个参数。

在另一些情况中，服务提供者为了增加请求来源的保险性，他们希望你使用共享密钥与密码方式发送加密的签名信息。互联网中签名请求普遍采用OAuth技术。有关OAuth协议详见<http://www.oauth.net>。

随着Twitter API越来越有价值，Twitter从免费公开的API转向每个API请求需要OAuth签名。值得庆幸的是，许多方便的OAuth库可以免费使用。你可以不必阅读技术规范和从零编写OAuth的实现过程。这些库的复杂性与丰富程度不一。OAuth网站提供各种OAuth库信息。

下面一段示例程序需要从<http://www.py4inf.com/code>下载三个文件twurl.py、 hidden.py、oauth.py和twitter1.py，把它们放在一个文件夹下。

为了能使用这些程序，需要一个Twitter账号，让你的Python代码作为Twitter的一个应用得以授权，创建key、secret、token与token secret。通过修改hidden.py，将这四个字符串赋予文件中合适的变量。

```
def auth() :
    return { "consumer_key" : "h7L...GNg",
        "consumer_secret" : "dNK...7Q",
        "token_key" : "101...GI",
        "token_secret" : "H0yM...Bo" }
```

Twitter的Web Service通过URL访问，如下所示：
https://api.twitter.com/1.1/statuses/user_timeline.json

所有的安全信息添加完毕之后，完整的URL如下：

```
https://api.twitter.com/1.1/statuses/user_timeline.json?count=2
&oauth_version=1.0&oauth_token=101...SGI&screen_name=drchuck
&oauth_nonce=09239679&oauth_timestamp=1380395644
&oauth_signature=rLK...BoD&oauth_consumer_key=h7Lu...GNg
&oauth_signature_method=HMAC-SHA1
```

如果想了解OAuth安全需求的各种参数含义，请阅读OAuth技术规范。

这个程序访问Twitter时，隐藏了文件oauth.py与twurl.py中所有复杂细节。我们只需简单设置hidden.py中的加密信息，然后把请求的URL发送给twurl.augment()函数，库代码会帮我们添加URL需要的所有参数。

程序（twitter1.py）检索了特定Twitter用户的时间线，以JSON格式返回为一个字符串。我们仅打印出字符串前250个字符。

```
import urllib
import twurl

TWITTER_URL='https://api.twitter.com/1.1/statuses/user_timeline.json'

while True:
    print "
    acct = raw_input('Enter Twitter Account:')
    if ( len(acct) < 1 ) : break
    url = twurl.augment(TWITTER_URL,
        {'screen_name': acct, 'count': '2'} )
    print 'Retrieving', url
    connection = urllib.urlopen(url)
    data = connection.read()
    print data[:250]
    headers = connection.info().dict
    # print headers
    print 'Remaining', headers['x-rate-limit-remaining']
```

程序运行结果如下：

```
Enter Twitter Account:drchuck
Retrieving https://api.twitter.com/1.1/ ...
[{"created_at":"Sat Sep 28 17:30:25 +0000 2013","
id":384007200990982144,"id_str":"384007200990982144",
"text":"RT @fixpert: See how the Dutch handle traffic
intersections: http:\/\/t.co\/tIiVWtEhj4\n#brilliant",
"source":"web","truncated":false,"in_rep
Remaining 178

Enter Twitter Account:fixpert
Retrieving https://api.twitter.com/1.1/ ...
[{"created_at":"Sat Sep 28 18:03:56 +0000 2013",
"id":384015634108919808,"id_str":"384015634108919808",
"text":"3 months after my freak bocce ball accident,
my wedding ring fits again! :)\n\nhttps:\/\/t.co\/2XmHPx7kgX",
"source":"web","truncated":false,
Remaining 177

Enter Twitter Account:
```

Twitter返回时间线数据的同时，还返回了HTTP响应头部中请求的元数据。特殊的头部x-rate-limit-remaining表明，在短时间切断之前我们还能发起的请求数量，这样就可以知道每次API请求中剩余的检索次数。

在下面例子中，我们检索一个用户的Twitter朋友，解析返回的JSON，抽取朋友信息。在解析和4格缩进的“工整打印”之后导出JSON文件。当需要抽取更多字段时，导出的数据可供我们解读。

```
import urllib
import twurl
import json

TWITTER_URL = 'https://api.twitter.com/1.1/friends/list.json'

while True:
    print "
    acct = raw_input('Enter Twitter Account:')
    if ( len(acct) < 1 ) : break
    url = twurl.augment(TWITTER_URL,
        {'screen_name': acct, 'count': '5'} )
    print 'Retrieving', url
    connection = urllib.urlopen(url)
    data = connection.read()
    headers = connection.info().dict
    print 'Remaining', headers['x-rate-limit-remaining']
    js = json.loads(data)
    print json.dumps(js, indent=4)

    for u in js['users'] :
        print u['screen_name']
        s = u['status']['text']
        print '  ',s[:50]
```

由于JSON是Python列表与字典的嵌套集合，我们可以组合使用索引操作与for循环操作，使用很少的Python代码遍历返回的数据结构。

程序运行结果如下（为方便页面显示，一些数据被压缩）：

```
Enter Twitter Account:drchuck
Retrieving https://api.twitter.com/1.1/friends ...
Remaining 14
{
    "next_cursor": 1444171224491980205,
    "users": [
        {
            "id": 662433,
            "followers_count": 28725,
            "status": {
                "text": "@jazzychad I just bought one .__.",
                "created_at": "Fri Sep 20 08:36:34 +0000 2013",
                "retweeted": false,
            },
            "location": "San Francisco, California",
            "screen_name": "leahculver",
            "name": "Leah Culver",
        },
        {
            "id": 40426722,
            "followers_count": 2635,
            "status": {
                "text": "RT @WSJ: Big employers like Google ...",
                "created_at": "Sat Sep 28 19:36:37 +0000 2013",
            },
            "location": "Victoria Canada",
            "screen_name": "_valeriei",
            "name": "Valerie Irvine",
    ],
    "next_cursor_str": "1444171224491980205"
}
leahculver
   @jazzychad I just bought one .__.
_valeriei
   RT @WSJ: Big employers like Google, AT&amp;T are h
ericbollens
   RT @lukew: sneak peek: my LONG take on the good &a
halherzog
   Learning Objects is 10. We had a cake with the LO,
scweeker
   @DeviceLabDC love it! Now where so I get that "etc

Enter Twitter Account:
```

在输出的最后，我们看到for循环读取了Twitter账号drchuck的5位新近朋友，打印出每位朋友的最近状态。返回的JSON中还有更多数据可用。此外，如果仔细查看程序输出，你会发现，特定账号的“找到他的朋友”与时间线查询在一个时间段的请求数量有不同的访问限制。

这些安全的API密钥让Twitter有充分信心认为，他们知道谁在使用他们的API以及数据使用情况。访问限制让我们可以做一些简单的个人数据检索，但不能用于构建每天有百万级API数据访问的产品。

## 13.9 术语

**API：**应用程序接口 —— 应用程序之间的合同，定义了两个应用组件之间交互的模式。

**ElementTree：**用于解析XML数据的Python内置库。

**JSON：**JavaScript Object Notation —— 基于JavaScript对象语法的结构化数据标识格式。

**REST：**表述性状态转移 —— 一种Web Service风格，通过HTTP协议提供应用程序间的资源访问。

**SOA：**面向服务的架构 —— 应用程序由跨网络连接的组件组成。

**XML：**可扩展标识语言 —— 结构化数据标识格式。

## 13.10  练习

**习题13.1** 修改<http://www.py4inf.com/code/geojson.py>或<http://www.py4inf.com/code/geoxml.py>文件，从检索到的数据中打印出2位字符的国家代码。添加错误检查，确保国家代码不存在时的异常处理。当程序正常工作了，搜索“Atlantic Ocean”，让程序可以处理不属于任何国家的地理位置。
