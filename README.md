# PROJECT-20200615

## 1. 셀레니움 이란?
셀레니움(Selenium)이란 웹 응용 프로그램 테스트를 위한 휴대용 프레임 워크. 익스플로러, 크롬, 파이어 폭스 등 다양한 웹 브라우저로 웹사이트를 동작시키고 테스트 하기 위해 만들어진 도구이다. 하지만 브라우저를 자동으로 동작시키고 그 내용을 얻을 수 있다는 점에서 크롤링에도 사용되고 있다.

## 2. 셀레니움 실습
### 2-1) 실습 사전 준비
셀레니움은 Java로 만들어 졌지만 java뿐 아니라 python, ruby등 다양한 언어로 사용할 수 있다. 본 실습에서는 빠른 진행을 위해 python을 사용하겠다. https://www.anaconda.com/distribution/#download-section 에서 파이썬을 설치하면 파이썬 뿐 아니라, 자주 사용하는 다양한 패키지 까지 함께 설치된다. 파이썬 설치가 끝나면 pip install selenium 명령어를 실행하여 selenium 파이썬 패키지를 설치해준다.

셀레니움을 브라우저를 동작시키는 플랫폼이므로 동작시킬 브라우저 드라이버를 다운받아야 한다. 본 실습에서는 크롬 브라우저를 사용하겠다. https://chromedriver.chromium.org/downloads 에서 크롬 드라이버를 다운받는다.

### 2-2) 셀레니움 실행 시켜보기
```py
from selenium import webdriver
browser = webdriver.Chrome(r'C:\Users\nankisu\Downloads\chromedriver_win32_78\chromedriver.exe')
browser.get('http://navar.com')
browser.quit()
```
셀레니움으로 크롬을 동작시키기 위해 아까 설치한 크롬 드라이버의 파일 경로를 넘겨준다. 정상적으로 크롬 브라우저가 실행되고 네이버로 접속되는 것을 확인한다.

### 2-3) 셀레니움으로 크롤링하기
```py
from selenium import webdriver
from bs4 import BeautifulSoup

browser = webdriver.Chrome(r'C:\Users\nankisu\Downloads\chromedriver_win32_78\chromedriver.exe')
browser.get('http://naver.com')
news_header = soup.select("#NM_NEWSSTAND_HEADER .list_issue")[0]
print(news_header.text)
browser.quit()
```
셀레니움으로 동작시킨 브라우저의 html 소스를 BeautifulSoup에 넘겨주고 원하는 정보를 얻어낸다. 단순히 get request를 사용한 크롤링과는 다르게, 셀레니움을 사용하면 채팅과 같은 동적인 내용도 크롤링 할 수 있다. 

### 2-4) 셀레니움으로 다양한 동작 실행하기
``` py
from selenium import webdriver

browser = webdriver.Chrome(r'C:\Users\nankisu\Downloads\chromedriver_win32_78\chromedriver.exe')
browser.get('http://naver.com')
search_input = browser.find_element_by_css_selector('.input_text')
search_button = browser.find_element_by_css_selector('#search_btn')
search_input.clear()
search_input.send_keys("삼성SDS")
search_button.click()
browser.quit()
```
셀레니움은 애초에 웹서비스 테스트를 위해 만들어진 플랫폼이고, 브라우저를 실제 동작시키는 것과 다를 것이 없다. 다만 사람이 직접 동작을 수행히지않고 코드로 이를 대신한다. 그러므로 브라우저로 할 수 있는 모든 동작은 셀레니움으로 할 수 있다. ex) 클릭, 키 입력, 스크롤, js코드 실행, 쿠키 입력 등

참고) https://selenium-python.readthedocs.io/api.html

### 2-5) GUI 없이 브라우저 실행하기
``` py
from selenium import webdriver
from selenium.webdriver.chrome.options import Options  

options = Options()
options.headless = True
browser = webdriver.Chrome(r'C:\Users\nankisu\Downloads\chromedriver_win32\chromedriver.exe', options=options)
browser.get('https://naver.com')
print(browser.title)
browser.close()
```
셀레니움으로 브라우저를 자동화 시키고 나면 한가지 드는 생각이 있다. "어차피 내가 클릭하고 입력하는 것도 아닌데, 화면이 없어도 되지 않을까?". GUI는 사람에게는 편리하지만, 컴퓨터에게는 자원을 소모하는 행위일 뿐이다. 따라서 코드가 제대로 동작하는 것을 확인했다면, headless 옵션으로 GUI 없이 실행시키는 것이 좋다.

## 3. AWS 등 클라우드 서버와의 조합 = 트위치 
### 3-1) 개요
selenium은 사실상 사람이 웹 브라우저를 동작시키는 것과 다르지 않고, aws ec2는 사실상 하나의 컴퓨터와 다르지 않다. 그러므로 ec2 서버에서 selenium으로 자신의 트위치에 접속하게 한다면 당연히 시청자수가 올라갈 것이다. 그래서 이를 한번 검증해보고자 트위치 뷰봇을 만들어 보고자 한다.
 
### 3-2) AWS EC2 환경 세팅
AWS EC2, Ubuntu Server 18.04로 서버를 하나 생성하였고 서버의 환경세팅은 위에서 했던것과 다르지 않다. 윈도우에서 하던것을 우분투 서버에서 하는 것 뿐다.

먼저, 크롬을 설치한다.
```
wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
echo 'deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main' | sudo tee /etc/apt/sources.list.d/google-chrome.list
sudo apt-get update 
sudo apt-get install google-chrome-stable
```

그 다음으로 크롬 드라이버를 다운받는다.
```
sudo apt install unzip
wget https://chromedriver.storage.googleapis.com/83.0.4103.39/chromedriver_linux64.zip
unzip chromedriver_linux64.zip
```

이제 마지막으로 파이썬과 셀레니움 패키지를 설치하면 모든 준비가 끝난다.
```
sudo apt-get update
sudo apt-get install python
sudo apt-get install python-pip
pip install selenium
```

이제 AWS EC2에서 셀레니움을 실행할 모든 준비가 끝났다. 다음 코드를 작성하고 파이썬으로 실행시켜보자.
``` py
from selenium import webdriver
from selenium.webdriver.chrome.options import Options  

options = Options()
options.headless = True
browser = webdriver.Chrome(r'./chromedriver.exe', options=options)
browser.get('https://www.twitch.tv/{아이디}')
print(browser.title)
```

![조회수가 올라간 모습](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fk.kakaocdn.net%2Fdn%2FYFYOw%2FbtqESh3cgxs%2F8QxFr6Tv4gPG9pt7Jb6Bfk%2Fimg.png)

5개의 AWS ec2 서버로 위의 코드를 실행하자, 실제로 시청자 수가 5명이 증가한것을 확인할 수 있다. 이처럼 selenium과 AWS ec2의 조합으로 트위치 뷰봇을 만들어 보았다. 이론적으로 가능하다는 것이 확인되었지만, 이를 악용하고자 한다면 배보다 배꼽이 더 클것이다. 각각 ec2 서버의 유지비가 만만치 않을 것이고, 비용을 들여서 해도 적발될 가능성이 크다. 따라서 이 글을 단순한 학술적 호기심과 검증 정도의 의미로 보았으면 한다.
