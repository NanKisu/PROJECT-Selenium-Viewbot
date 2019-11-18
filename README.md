# STUDY-20191117

## 1. 셀레니움 이란?
셀레니움(Selenium)이란 웹 응용 프로그램 테스트를 위한 휴대용 프레임 워크. 익스플로러, 크롬, 파이어 폭스 등 다양한 웹 브라우저로 웹사이트를 동작시키고 테스트 하기 위해 만들어진 도구이다. 하지만 브라우저를 자동으로 동작시키고 그 내용을 얻을 수 있다는 점에서 크롤링에도 사용되고 있다.

## 2. 셀레니움 실습
### 2-1) 실습 사전 준비
셀레니움은 Java로 만들어 졌지만 java뿐 아니라 python, ruby등 다양한 언어로 사용할 수 있다. 본 실습에서는 빠른 진행을 위해 python을 사용하겠다.
https://www.anaconda.com/distribution/#download-section 에서 파이썬을 설치하면 파이썬 뿐 아니라, 자주 사용하는 다양한 패키지 까지 함께 설치된다. 파이썬 설치가 끝나면 pip install selenium 명령어를 실행하여 selenium 파이썬 패키지를 설치해준다.

셀레니움을 브라우저를 동작시키는 플랫폼이므로 동작시킬 브라우저 드라이버를 다운받아야 한다. 본 실습에서는 크롬 브라우저를 사용하겠다. https://chromedriver.chromium.org/downloads 에서 크롬 드라이버를 다운받는다.

### 2-2) 셀레니움 실행 시켜보기
```py
from selenium import webdriver
browser = webdriver.Chrome(r'C:\Users\nankisu\Downloads\chromedriver_win32_78\chromedriver.exe')
browser.get('http://navar.com')
browser.close()
```
셀레니움으로 크롬을 동작시키기 위해 아까 설치한 크롬 드라이버의 파일 경로를 넘겨준다. 정상적으로 크롬 브라우저가 실행되고 네이버로 접속되는 것을 확인한다.

### 2-3) 셀레니움으로 크롤링하기
```py
from selenium import webdriver
from bs4 import BeautifulSoup

browser = webdriver.Chrome(r'C:\Users\nankisu\Downloads\chromedriver_win32_78\chromedriver.exe')
browser.get('http://naver.com')
soup = BeautifulSoup(browser.page_source, 'lxml')
keywords = soup.select(".PM_CL_realtimeKeyword_list_base .ah_k")
for keword in keywords:
    print(keword.text)
browser.close()
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
browser.close()
```
셀레니움은 애초에 웹서비스 테스트를 위해 만들어진 플랫폼이고, 브라우저를 실제 동작시키는 것과 다를 것이 없다. 다만 사람이 직접 동작을 수행히지않고 코드로 이를 대신한다. 그러므로 브라우저로 할 수 있는 모든 동작은 셀레니움으로 할 수 있다. ex) 클릭, 키 입력, 스크롤, js코드 실행, 쿠키 입력 등

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

## 3. AWS 등 클라우드 서버와의 조합 = 블로그 조회수 올리기
### 3-1) 개요
대학생 시절 서포터즈 같은 대외 활동을 지원하려고 하면, 본인의 블로그 주소를 적는 란이 있는 경우가 있었다. 아마 서포터즈와 같은 대외 활동은 회사 홍보와 대외 이미지 제고를 위한 목적이므로, 더 파급효과가 있을 지원자를 거르는게 목적이라고 생각 한다. 하지만 일 평균 조회수가 0에 수렴하는 블로그를 가지고 있다면 이는 매우 난감한 상황이고, 필자 역시 그러했다.

그래서 생각해낸 해결책이 selenium과 aws ec2의 조합이었다. selenium은 사실상 사람이 웹 브라우저를 동작시키는 것과 다르지 않고, aws ec2는 사실상 하나의 컴퓨터와 다르지 않다. 그러므로 ec2 서버에서 selenium으로 자신의 블로그에 접속하게 한다면 당연히 조회수가 올라간다.
 
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
wget https://chromedriver.storage.googleapis.com/78.0.3904.70/chromedriver_linux64.zip
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
browser = webdriver.Chrome('/home/ubuntu/chromedriver', options=options)
browser.get('https://naver.com')
print(browser.title)
browser.close()
```
