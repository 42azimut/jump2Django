
click here 
* [장고 질문게시판](https://pybo.kr/pybo/question/list/qna/)
* [점프 투 장고 위키독스](https://wikidocs.net/book/4223)
* [git hub Source jump2Django](https://github.com/pahkey/djangobook)
___
# 1장 장고 개발 준비! 

## - 가상환경 설정 MacOS
```
- 가상환경 만들기
python3 -m venv mysite
- 가상환경 실행
source ./mysite/bin/activate
```

## - 기본앱 설정
- 가상환경에서 장고 설치하기
    - `pip install django==3.1.3`


## 장고 프로젝트 생성하기 
- mysite 디렉터리에서 django-admin이라는 도구로 장고 프로젝트를 생성하자. 
- 이때 config 다음에 점 기호(.)가 있음에 주의하자. 점 기호는 '현재 디렉터리를 프로젝트 디렉터리로 만들라'는 의미이다.
    - `django-admin startproject config .`

## 서버 구동하기
- `python manage.py runserver`
___

# 2장 장고의 기본 요소 익히기!
## 2-01 주소와 화면을 연결하는 URL 과 뷰
### 1) pybo앱 생성하기
`django-admin startapp pybo`
### 4) config/urls.py
- config/urls.py 파일은 페이지 요청 시 가장 먼저 호출되며, 요청 URL과 뷰 함수를 1:1로 연결해 준다.
- 뷰 함수는 아직 살펴보지 않았다. 뷰 함수는 화면을 보여 주기 위한 함수로, views.py에 있는 함수다. 
### 5) urls.py
- pybo에 슬래시 / 를 붙여 입력함! 슬래시를 붙이면 사용자가 슬래시 없이 주소를 입력해도 장고가 자동으로 슬래시를 붙여 준다! 슬래시를 암튼 붙여라! 
- 예) 웹 브라우저 주소창에 localhost:8000/pybo라고 입력하면 장고가 자동으로 localhost:8000/pybo/와 같이 슬래시를 붙여 준다.
### 7) pybo/views.py 작성
- index 함수의 매개변수 request는 장고에 의해 자동으로 전달되는 HTTP 요청 객체이다.
- request는 사용자가 전달한 데이터를 확인할 때 사용된다.
- HttpResponse는 페이지 요청에 응답할떄 사용하는 장고 클래사! 문자열을 전달하여 웹브라우저에 그대로 출력하게 함! 

## 장고 개발 흐름 정리! 매우 중요!!!
1. 웹 브라우저 주소창에 localhost:8000/pybo 입력(장고 개발 서버에 /pybo 페이지 요청).
2. config/urls.py 파일에서 URL을 해석해 pybo/views.py 파일의 index 함수 호출.
3. pybo/views.py 파일의 index 함수를 실행하고 그 결과를 웹 브라우저에 전달.

## URL 분리하기
1) config/urls.py 다시 살펴보기!
2) `path('pybo/', include('pybo.urls'))`는 pybo/로 시작되는 페이지 요청은 모두 pybo/urls.py 파일에 있는 URL 매핑을 참고하여 처리하라는 의미이다
```
* URL 매핑을 확인하는 과정
예)  만약 pybo/urls.py 파일에 path('question/create/', ...)가 추가 된다면 config/urls.py 파일과 pybo/urls.py 파일에 의해 최종 매핑되는 URL은 pybo/question/create/가 될 것이다.
```