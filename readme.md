# 181218 - Day2 ()

*로또 번호 크롤링*

```python
import requests
import random
from bs4 import BeautifulSoup as bs

url = "https://m.dhlottery.co.kr/common.do?method=main"
response = requests.get(url).text
soup = bs(response, 'html.parser') # html문서 파싱
document = soup.select(".prizeresult")[0] #slick-slide00 > div.prizeresult > div:nth-child(1) > span
numbers = document.select("span")

ns = []
for i in range(7):
  ns.append(int(numbers[i].text))
  
lotto = random.sample(list(range(1, 46)),7)

correct=[]
for i in ns:
  if i in lotto:
    correct.append(i)
print("나의 로또 번호 : {}".format(lotto))
print("당첨 로또 번호 : {}".format(ns))
print("{}개가 맞았습니다.".format(len(correct)))
```



*크롤링(네이버)*

```python
import requests
import time
from bs4 import BeautifulSoup as bs

today = time.strftime("%a").lower()
# 1. 네이버 웹툰을 가져올 수 있는 주소를 파악하고 url 변수에 저장한다.
url = "https://comic.naver.com/webtoon/weekdayList.nhn?week={}".format(today)

# 2. 해당 주소로 요청을 보내 정보를 가져온다.
response = requests.get(url).text

# 3. 받은 정보를  bs를 이용하여 검색하기 좋게 만든다.
soup = bs(response, 'html.parser')

# 4. 네이버 웹툰 페이지로 가서 내가 원하는 정보가 어디 있는지 파악한다.
# 오늘자 업데이트된 웹툰들의 각각 리스트 페이지, 웹툰의 제목 + 해당 웹툰의 썸네일(주소)
toons = []
li = soup.select('.img_list li')

# 5. 3번에서 저장한 문서를 이용해 4번에서 파악한 위치를 뽑아내는 코드를 작성한다.
for item in li:    
  toon = { 
    "title" : item.select('dt a')[0].text, #==item.select_one('dt a').text
    "url" : item.select('dt a')[0]["href"], #select는 요소가 1개여도 리스트 형식으로 저장
    "img_url" : item.select('.thumb img')[0]["src"]}
  toons.append(toon)
  
# 6. 출력한다.
print(toons)
```



*크롤링(다음)*

```python
import requests
import time
import json

today=time.strftime("%a").lower()
# 1. 내가 원하는 정보를 얻을 수 있는 주소를 url이라고 하는 변수에 담는다.
url = "http://webtoon.daum.net/data/pc/webtoon/list_serialized/{}?timeStamp=1545116996132".format(today)

# 2. 해당 url에 요청을 보내 응답을 받아 저장한다.
# 3. 구글신에게 python으로 어떻게 json을 파싱(dict형으로 변환)하는지 물어본다.
# response = requests.get(url).text
# document = json.loads(response) -> dict 형태로 받아짐
data = requests.get(url).json()
 
# 4. 내가 원하는 데이터를 꺼내서 조합한다.
toons = []
for i in range(len(data["data"])):
  toon = { "title" : data["data"][i]["title"], "url" : "http://webtoon.daum.net/webtoon/view/"+data["data"][i]["nickname"], "img_url" : data["data"][i]["pcThumbnailImage"]["url"]}
  toons.append(toon)
print(toons)
```

