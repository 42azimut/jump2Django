
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

## 개발 환경 설정
```
#!/bin/bash

cd /Users/...my root.../myproject
export FLASK_APP=pybo
export FLASK_ENV=development

source /Users/...my root.../venv/myproject/bin/activate
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

### * 장고 개발 흐름 정리! 매우 중요!!!
1. 웹 브라우저 주소창에 localhost:8000/pybo 입력(장고 개발 서버에 /pybo 페이지 요청).
2. config/urls.py 파일에서 URL을 해석해 pybo/views.py 파일의 index 함수 호출.
3. pybo/views.py 파일의 index 함수를 실행하고 그 결과를 웹 브라우저에 전달.

### * URL 분리하기
1) config/urls.py 다시 살펴보기!
2) `path('pybo/', include('pybo.urls'))`는 pybo/로 시작되는 페이지 요청은 모두 pybo/urls.py 파일에 있는 URL 매핑을 참고하여 처리하라는 의미이다
```
* URL 매핑을 확인하는 과정
예)  만약 pybo/urls.py 파일에 path('question/create/', ...)가 추가 된다면 
config/urls.py 파일과 pybo/urls.py 파일에 의해 최종 매핑되는 URL은 
pybo/question/create/가 될 것이다.
```
___

## 2-02 데이터를 관리하는 모델! 
`python manage.py migrate`

### 2) pybo.models.py 질문 모델 작성
- 글자수를 제한하고 싶은 데이터는 CharField 사용해야 한다.
- 글자수 제한 없이 사용하려면 TextField를 사용!
- 시간 관련 속성은 DateTimeField를 사용!

### 3) 답변모델 작성
- Answer 모델은 어떤 질문에 대한 답변이므로 Question 모델을 속성으로 가져와야 한다. 
- 어떤 모델의 속성을 가져올때믄 ForeignKey를 이용한다. 외래키는 다른 모델과의 연결을 의미!
- on_delete=models.CASADE 는 답변에 연결된 질문이 삭제되면 답변도 삭제된다!

### 4) pybo 앱 등록
`'pybo.apps.PyboConfig',`

### 5) pybo/app.py 피보 앱 만들떄 자동 생성됨! 
- 이 안에 PyboConfig 클래스가 있다.

### 6) migrate 테이블 생성
`python manage.py migrate`

### 7) makemigrations 테이블 작업 파일 생성하기
`python manage.py makemigrations`

### 10) migrate 실행하기
`python manage.py migrate`
```
python manage.py sqlmigrate pybo 0001
```

## 2-03 개발 편의를 제공하는 장고 Admin
### 1) 슈퍼유저 생성하기
`python manage.py createsuperuser`

### 3) Admin에서 모델 관리하기
- pybo.admin.py 에서 질문 모델 등록하자
`admin.site.register(Quesion)`

## 2-04 질문 목록과 질문 상세 기능 구현하기
### 2) 오류화면 구현하기!
    - `get_object_or_404()` 

### 3) 404 확인!

## 2-05 URL 더 똑똑하게 사용하기
### 1) URL 별칭 사용하기
```
urlpatterns = [
    path('', views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
]
```
- 이렇게 수정하면 실제 주소 /pybo/는 index라는 URL 별칭이, /pybo/2/는 detail이라는 URL 별칭이 생긴다.

### URL 네임 스페이스 사용하기
- pybo/urls.py 에 app_name 변수에 네임스페이스를 저장하면 됨!
- `app_name = 'pybo'`

### 2) 네임 스페이스 테스트 하면 오류 발생됨!

### 3) pybo/question_list.html 수정!
- 원인은 : 템플릿에서 아직 네임스페이스를 사용하고 있지 않기 때문이다.
- `url 'detail' 을  url 'pybo:detail' 로 pybo 등록수정!`

## 2-06 답변 등록 기능 만들기


### 답변 저장하고 표시하기!
### 1) 질문 상세 팀플릿 답변 등록 버튼 만들기!
 - `{% csrf_token %}` 유효성 데이터 검증

 ### 4) answer_create 함수 추가 > 여기가 어렵네! 
```
answer_create 함수의 question_id 매개변수에는 URL 매핑 정보값이 넘어온다. 예를 들어 /pybo/answer/create/2가 요청되면 question_id에는 2가 넘어온다. request 매개변수에는 pybo/question_detail.html에서 textarea에 입력된 데이터가 담겨 넘어온다. 이 값을 추출하기 위한 코드가 바로 request.POST.get('content')이다. 그리고 Question 모델을 통해 Answer 모델 데이터를 생성하기 위해 question.answer_set.create를 사용했다.

- request.POST.get('content')는 POST 형식으로 전송된 form 데이터 항목 중 name이 content인 값을 의미한다.
- Answer 모델이 Question 모델을 Foreign Key로 참조하고 있으므로 question.answer_set 같은 표현을 사용할 수 있다.
```

## 2-07 스태틱 화면 예쁘게 꾸미기
```
STATIC_URL = '/static/'
# ---------------------------------- [edit] ---------------------------------- #
STATICFILES_DIRS = [
    BASE_DIR / 'static',
]
```

## 2-08 부트스트랩으로 더 쉽게 화면 꾸미기
- 다운로드 및 파일 복사! 
- 압축파일내 경로 : bootstrap-4.5.3-dist.zip\bootstrap-4.5.3-dist\css\bootstrap.min.css
- 카피한 경로 : C:\projects\mysite\static\bootstrap.min.css

### 2) 질문 목록 템플릿에 부트스트랩 적용
```
<!-- ------------------------------- [edit] -------------------------------- -->
{% load static %}
<link rel="stylesheet" type="text/css" href="{% static 'bootstrap.min.css' %}">
<!-- ----------------------------------------------------------------------- -->
{% if question_list %}
(... 생략 ...)
```

## 2-09 표준 HTML과 템플릿 상속 사용해 보기
### 1) base.html 템플릿 만들기!
```
{% load static %}
<!doctype html>
<html lang="ko">
<head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <!-- Bootstrap CSS -->
    <link rel="stylesheet" type="text/css" href="{% static 'bootstrap.min.css' %}">
    <!-- pybo CSS -->
    <link rel="stylesheet" type="text/css" href="{% static 'style.css' %}">
    <title>Hello, pybo!</title>
</head>
<body>
<!-- 기본 템플릿 안에 삽입될 내용 Start -->
{% block content %}
{% endblock %}
<!-- 기본 템플릿 안에 삽입될 내용 End -->
</body>
</html>
```
### 2) question_list.html 수정하기
```
{% extends 'base.html' %}
{% block content %}
<!-- ----------------------------------------------------------------------- -->
<div class="container my-3">
    <table class="table">
        (... 생략 ...)
    </table>
</div>
<!-- ------------------------------- [edit] -------------------------------- -->
{% endblock %}
```
### 3) question_detail.html 수정하기
```
{% extends 'base.html' %}
{% block content %}
<!-- ----------------------------------------------------------------------- -->
<div class="container my-3">
    <h2 class="border-bottom py-2">{{ question.subject }}</h2>
    (... 생략 ...)
    </form>
</div>
<!-- ------------------------------- [edit] -------------------------------- -->
{% endblock %}
```
## 2-10 질문 등릭 기능 만들기
### 1) 질문 등록 버튼
`    <a href="{% url 'pybo:question_create' %}" class="btn btn-primary">질문 등록하기</a>`
### 2) url 매핑 추가!
`path('question/create/', views.question_create, name='question_create'),`

### 3) pybo/views/py 
-  QuestionForm 클래스는 질문을 등록하기 위해 사용하는 장고의 폼이다
- {'form':form}은 템플릿에서 폼 엘리먼트를 생성할때 사용!
```
form .form import QuestionForm
... 생략 ...
def question_craete(request):
    form = Question()
    return render(request, 'pybo/question_form.html', {'form':form})
```

## 3-01 내비게이션 기능 추가

## 3-02 게시판 페이징 기능 추가
- 장고 쉘에서 모델(데이터) 질문 글 등록 300개 입력이 안됨!

## 3-03 템플릿 필터 직접 만들기!

## 3-04 질문에 답글 수 표시

## 3-05 로그인, 로그아웃 기능 구현
- 장고에서 기능 제공! django.contrib.auth 앱이 있다.
- 이 앱은 장고 프로젝트 생성 시 mysite/config/settings.py에 자동으로 추가된다.
### 1) common 앱 생성하기! 
- 여러 앱들과 함께 사용할수 있도록 앱 단위로 로그인 구현이 아니고, 통용할수 있는 common 앱 만든다! 
- (mysite) c:\projects\mysite>django-admin startapp common

## 3-06
### 3) UserForm은 django.contrib.auth.forms 패키지의 UserCreationForm 클래스를 상속하고 email 속성을 추가했다.
- UserCreationForm 에는 다음 속성이 있다. username, password1, password2
- 기본 위 속성에 email 속성을 추가 하기 위해 상속한 UserForm 클래스를 만든다!
- 패스워드 검사는 is_valid 함수를 사용! 

### 4) 회원가입을 위한 signup 함수 정의
 ```
signup 함수는 POST 요청인 경우 화면에서 입력한 데이터로 새로운 사용자를 생성하고, GET 요청인 경우 common/signup.html 화면을 반환한다. 
POST 요청에서 form.cleaned_data.get 함수는 회원가입 화면에서 입력한 값을 얻기 위해 사용하는 함수이다. 
여기서는 로그인 시 필요한 아이디, 비밀번호를 얻기 위해 사용되었다. 
그리고 회원가입이 완료된 이후에 자동으로 로그인되도록 authenticate 함수와 login 함수를 사용했다.
 ```
 -  authenicate, login 함수는 django.contrib.auth 패키지에 있는 함수로 사용자 인증과 로그인을 담당!

 ### 5) 회원가입 템플릿 만들기
 
