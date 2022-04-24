---
layout: post
title: 왜 (__name__)을 Flask class로 넘길까?
date: 2022-04-24 21:34:00
description: passing (__name__) into Flask Class
tags: flask python sql
categories: learnings
---

<span style=" font-family: 'Gothic A1', sans-serif;font-size: 25px; font-weight: 500;">[app = Flask(__name__)]</span>

<span style="font-weight: 500;">Flask를 사용하게 되면 Flask(**name**)이라는 놈을 선언하게 된다. 무슨 역할을 하는 놈인지 한 번 알아보았다.</span>

```python
from flask import Flask

app = Flask(__name__)
```

<p style="font-weight: 500;"><br>
<span style="font-size: 1.25rem;">Flask에 (__name__)을 인자로 넘겨준다.</span>
<br><br>
<span>__name__ 은 모듈의 이름을 지칭한다.</span>
<br><br>
<span>만약, 당신의 test.py 모듈의 위치가 최상위 디렉토리(directory)에 위치한다면</span><br>
<span>__name__ = test 이렇게 된다.</span><br><br>
    
<span>그 모듈의 위치가 my_package 라는 패키지 안에 들어있다면</span><br>
<span>__name__ = my_package.test 가 되는 것이다.</span><br><br>
    
<span style="font-size: 1.25rem;">예외 상황:</span><br><br>
<span>1. __init__.py라는 명칭을 가진 패키지 모듈일 경우</span><br><br>
<span>만약, 당신의 모듈이 my_package/__init__.py 의 경로를 가지고 있다면,</span><br>
<span>__name__ = my_package 이렇게 된다.</span><br><br>
    
<span>2. 메인 모듈 안에 있을 경우</span><br><br>
<span>만약, 당신이 python interpreter를 메인 모듈에서 구동한다면 그 땐,</span><br>
<span>__name__ = __main__ 이 되는 특수한 상황이 발생한다.</span>
<br><br><br>
</p>

<h2>왜 그렇게 될까?</h2>

Flask의 첫 argument(인자)는 “import_name”이라고 불리운다. 그리고 이 “import_name”을 곧 “애플리케이션 패키지의 이름"이라고 설명하고있다.

이 **“import_name”**의 존재 이유는 크게 3가지로 정리할 수 있다:

- 파일시스템 안에서 리소스를 찾기 위해
- 확장자를 통해 디버깅 정보를 개선하기 위해
- 그 외 아주 많은 이유로 인해(..?)

그럼 이게 다 뭔 소리일까?

<h3><br>우선,</h3>

여기서의 **‘리소스'**란 애플리케이션 구동에 필요한 **‘추가적인 파일들'**을 의미한다. 예를 들어, static 또는 template 파일들 말이다.

**작동원리는 다음과 같다.**

1. Flask는 “import_name”으로 넘겨진 인자를 받는다.
2. 그 인자는 import된 패키지의 명칭이다.
3. 그리고 Flask는 이 명칭을 이용해 애플리케이션 안의 경로를 찾아들어가 동일한 명칭을 가진 모듈을 파악한다.
4. 그렇게 경로를 알아내게 되면, Flask는 static 또는 template 디렉토리 명칭(directory names)을 사용해(append) 그 파일들을 취득한다.

flask/helpers.py 모듈 안에는 이미 get_root_path()라는 함수가 들어있다. 이 함수를 이용하면, 당신의 애플리케이션 안에 존재하는 각 패키지들의 혹은 모듈의 경로를 찾아낸다.

```python

# 예를 들어 'microblog'라는 애플리케이션에 get_root_path() 사용해보자.
from flask.helpers import get_root_path

# the app package
get_root_path('app')
'/home/miguel/microblog/app'

# the flask package
get_root_path('flask')
'/home/miguel/microblog/venv/lib/python3.8/site-packages/flask'

# the config.py module
get_root_path('config')
'/Users/mgrinberg/Documents/dev/python/microblog'

# the app/models.py module
get_root_path('app.models')
'/Users/mgrinberg/Documents/dev/python/microblog/app'

```

위에서 볼 수 있듯이 두 가지 경우로 나타난다.

1. Flask()의 **“import_name”**으로 애플리케이션의 “패키지 이름"을 넘겨주면, Flask는 **패키지의 위치**를 찾아낸다.
2. Flask()의 **“import_name”**으로 애플리케이션의 “모듈 이름"을 넘겨주면, Flask는 **모듈이 들어있는 패키지의 위치**를 찾아낸다.

<h2><br>다음으로,</h2>

Flask는 “instance floders”라는 매우 모호한 개념을 가지고 있다. 정의에 따르면, “instance floders”는 소스컨트롤의 지배를 받지 않는 설정파일(configuration files)를 저장할 수 있는 특수한 폴더라고 한다.

재밌는 건(말이라도 이렇게 해야..), “instance folders”의 위치를 추적하는 것 또한 **“import_name”**이라는 것. 즉, **“import_name”**을 이용해 instance의 위치를 찾고, 그곳에 subdirectory를 추가(append)하는 것이다.

이런 특성 때문에 root_path와 instance_path 둘 다 Flask constructor 를 통해 덮어쓸 수 있다.

<h2><br>Flask 확장자를 통한 디버깅 정보 개선</h2>

Flask extension(확장자)들 중 **“import_name”**을 사용하는 건 **Flask-SQLAlchemy**라고 한다.

Flask-SQLAlchemy는 **get_debug_queries()**라는 함수를 가지고 있는데 이것이 하는 일은

`“collects and logs all the queries that are issued during the life of a request.”` 라고 한다.

내 멋대로 해석해보자면, 요청을 수행하는 시간 동안 발생한 모든 쿼리에 대해 기록을 남기고 수집하는 것이라고 이해할 수 있다.

기록하는 요소들 중에는 쿼리가 발생한(issued) 애플리케이션의 소스코드 상 위치도 있다고 한다.

<h2><br>결론<br></h2>
    
<p><span>여기서 기억해두면 쓸모 있을만한 개념은 다음으로 정리할 수 있을 것 같다.</span>
<br>
<br>
<span>1. 내 .py 모듈이 최상위 디렉토리에 위치할 땐, (**name**)이 모듈의 이름과 일치한다.</span><br>
<span>2. 내 .py 모듈이 어떤 패키지 안에 위치할 땐, (**name**)이 패키지.모듈의 이름과 일치한다.</span><br>
<span>3. Flask(**name**)를 하는 이유는 결국, Flask에게 나의 패키지의 경로를 알려주는 것이다. 즉, 모듈의 이름을 인자로 받아 그걸 근거로 경로를 추적해 결국 모듈이 “담긴" 패키지의 위치를 알아낸다.</span>
<br><br>
<span>이렇게 오늘은 Flask를 사용할 때 보게 되는 Flask(__name__)에 대해 알아보았다. 이런 지식이 언젠가 나에게 실용적인 도움이 되었으면 좋겠다.</span><br><br>
</p>
