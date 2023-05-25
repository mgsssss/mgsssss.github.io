---
layout: single
title: Django environ
categories: django
tags: 
 - django
toc: true
---

# Django-environ package를 활용한 환경변수 관리

Django를 사용하다 보면 git에 settings.py을 올려야 하는 경우가 있다.
이때 settings.py에 정의된 SECRET_KEY, db 정보가 유출될 우려가 있다.
정말 좋은 방법은 Django-environ package를 활용하는 것이다.

.env file에 중요한 정보를 적어놓고 settings.py에서 read 해서 쓰면
환경별로도 관리 할 수
```bash
pip install django-environ
```

해당 명령어를 python 가상환경에서의 terminal 작성하면 된다.

그리고 .env file을 생성한다. 나의 경우 manage.py 와 같은 경로에 .env file 을 생성했다.

```
#.env
DEBUG=True
SECRET_KEY=secret_key
ALLOWED_HOSTS=localhost
```
이런식으로 생성하고 settings.py에서 .env 파일을 read 해서 사용해야 한다. 이제 settings.py를 수정하자.

```python
# backend/settings.py
from pathlib import Path
import os # 
import environ # import 해야한다.
BASE_DIR = Path(__file__).resolve().parent.parent

# 환경 변수를 불러 올수 있도록 세팅한다.
env = environ.Env(DEBUG=(bool, True)) 


# 환경 변수가 있는 file을 지정해 줘야한다.
environ.Env.read_env(
    env_file=os.path.join(BASE_DIR, '.env')
)

# 가져온 환경 변수를 사용한다.
SECRET_KEY = env('SECRET_KEY')

```

이제 settings.py 에서 중요한 정보는 모두 .env 파일로 빼서 관리 할 수 있다.
git에서 .gitignore 파일에 .env 파일을 추가해 git에도 올라가지 않도록 하자.

.env 파일을 사용하면 환경 별로도 정보를 변경해 줄 수 있어서 좋다.

Development Server -> Staging Server -> Productions Server

위의 세 환경에서도 .env 파일을 사용해 누군가 코드를 보더라도 중요 정보는 보지 못하도록 하자.

*간단하지만 정말 중요하다. 보안 사고는 언제 일어나도 이상하지 않기 떄문이다.*
