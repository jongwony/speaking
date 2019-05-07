# Create Your Own CLI

AWS CLI 를 모티브로 만든 이야기

---

# Profile

최종원

![](../images/profile.png)

Data Engineer

- https://github.com/jongwony

- https://jongwony.pythonanywhere.com/about.html

---

# WTF?!

지금 보시는 이 화면도 만들었습니다!

```bash
pip install jw
jw md2ppt my_cli.md
```

---

# Why?

제 생산성을 높이기 위함입니다.

CLI 그룹 여러분들 마우스 저만 싫어하나요?

---

# Why?

1. 꼭 필요한 기능은 다른 모듈에 있다.

---

# Why?

1. 꼭 필요한 기능은 다른 모듈에 있다.

물론 파이프로 쓰고 계시겠죠!

```bash
$ VAR1=dev VAR2=sd cli1 --opt-1 --opt-2 | cli2 --opt-1 --opt-2 --opt-3 ...
```

---

# Why?

1. 꼭 필요한 기능은 다른 모듈에 있다.

물론 파이프로 쓰고 계시겠죠!

```bash
$ VAR1=dev VAR2=sd cli1 --opt-1 --opt-2 | cli2 --opt-1 --opt-2 --opt-3 ...
```

1. 자주 사용하는 기능은 한정되어 있음(매번 동일한 CLI 옵션)

---

# Why?

1. 꼭 필요한 기능은 다른 모듈에 있다.

물론 파이프로 쓰고 계시겠죠!

```bash
$ VAR1=dev VAR2=sd cli1 --opt-1 --opt-2 | cli2 --opt-1 --opt-2 --opt-3 ...
```

1. 자주 사용하는 기능은 한정되어 있음(매번 동일한 CLI 옵션)

```bash
$ aws help
```

모든 옵션을 다 쓸수도 없고 그렇게 하고 싶진 않을 것입니다.

---

# Bash Script!

스크립트를 짜봅니다.

```bash
#!/usr/bin/env bash

mysqldump -vvv -h$1 -P$2 -u$3 -p$4 --single-transaction --column-statistics=0 $5 $6 2>"$7" > "$8"
```

---

# Bash Script!

스크립트를 짜봅니다.

```bash
#!/usr/bin/env bash

mysqldump -vvv -h$1 -P$2 -u$3 -p$4 --single-transaction --column-statistics=0 $5 $6 2>"$7" > "$8"
```

special parameter로 유연하게 대처할 수도 있습니다.

```bash
#!/usr/bin/env bash

mysqldump -vvv --single-transaction --column-statistics=0 "$@"
```

https://stackoverflow.com/questions/5163144/what-are-the-special-dollar-sign-shell-variables

---

# Problem

스크립트가 제법 많아졌습니다.

저번에 짰던 user-data 스크립트 어디있더라...?

---

# Problem

스크립트가 제법 많아졌습니다.

저번에 짰던 user-data 스크립트 어디있더라...?

- 에이. 찾는거보다 새로 짜는게 빠르겠다.

---

# Problem

스크립트가 제법 많아졌습니다.

저번에 짰던 user-data 스크립트 어디있더라...?

- 에이. 찾는거보다 새로 짜는게 빠르겠다.

- GitHub repository도 점점 많아져서 관리가 되지 않음

---

# Side Project

여러분은 여러분이 만든 것을 얼마나 자주 다시 쓰시나요?

    - Google 번역 / 캘린더 / 할일 / 스프레드시트
    - ETL 쉘 스크립트들
    - 이미지를 CLI로 보기
    - CLI 로 파워포인트
    - .ssh/config 추가 스크립트
    - 짜잘한 스니펫 GitHub, Gist 업로드
    - vim 과 SQL connection 을 연결
    - JIRA Issue, Confluence 들어가기 귀찮고 느리다
    - Slack 이모티콘을 위한 GIF 전처리

---

# AWS CLI

- aws cli를 모티브로 만든 이야기

```
usage: aws [options] <command> <subcommand> [<subcommand> ...] [parameters]
To see help text, you can run:

  aws help
  aws <command> help
  aws <command> <subcommand> help
```

- AWS CLI는 서비스별로 묶어서 모든 서비스를 하나의 CLI로 사용가능합니다.
- 서비스별로 help 스트링이 따로 있다.

---

# AWS CLI

- GitHub aws cli를 뜯어보다.

저는 라이브러리를 그대로 차용하지는 않았습니다.

A 부터 Z 까지 분석하려고도 해보았지만 완전히 똑같이 만들 필요도 없고,

그러기엔 지금 필요한 기능을 못써서 너무 답답합니다.

그래도 간단히 어떻게 했나 라고 볼 필요는 있었습니다.

__main__ 함수를 찾는게 먼저였습니다.

이 스크립트가 들어있는 패키지를 아래와 같이 실행시켜 테스트로 쓰기 때문입니다.

```bash
git clone https://github.com/aws/aws-cli.git
python -m aws-cli.awscli
```

---

# python argparse

`main` 함수를 찾고,
object 를 상속하는 최상위 클래스를 찾고
클래스의 심장부 `main()`을 찾았습니다.

여기서 argparse 기본 라이브러리를 사용하는 방식처럼 `sys.argv` 를 파싱하기 시작합니다.

```
command_table()
parser.parse_known_args()
class CustomAction(Action):
    pass
```

위의 3가지 기능을 주로 사용하는데
argparse 라이브러리에 설명은 다 되어 있지만
기본만을 사용해도 그 깊이가 방대했습니다.

---

# As mine

당장 닭을 잡는 데에 탱크를 만들 필요는 없다고 봅니다.

Action 클래스를 상속해서 모듈화를 엄청 잘했다는 건 알았지만 애초부터 이럴 필요는 없었습니다.

AWS CLI 처럼 동작하기 위해 필요한 기능이 뭔지 기본 라이브러리를 좀 더 찾아봤습니다.

그 결과 subparser 가 딱 찾던 기능임을 알게 되었습니다.

https://docs.python.org/3/library/argparse.html#sub-commands

```
Many programs split up their functionality into a number of sub-commands,
for example, the svn program can invoke sub-commands
like svn checkout, svn update, and svn commit.
```

`git commit -m 'add README'` 처럼 동작한다고 아예 쓰여있습니다.

여기서 commit 을 sub command 라고 하고,

AWS CLI는 이 command 를 테이블 구조로 커스텀했다는 것을 알 수 있었습니다.

---

# As mine

subparser 를 동적으로 잘 생성시켜주기만 하면 제가 원하는 기능을 만들기에는 충분했습니다.

데코레이터를 추가만 하면 확장하기 좀 더 쉽게끔 했습니다.

```python
class BioParser:
    def __init__(self):
        self._parser = argparse.ArgumentParser()
        self._subparsers = self._parser.add_subparsers()
        self._subparser = None

    def register(self, f):
        @wraps(f)
        def wrapper(*args, **kwargs):
            self._subparser = self._subparsers.add_parser(f.__name__)
            result = f(*args, **kwargs)
            self._subparser.set_defaults(func=result)
            return result
        return wrapper

    def add_argument(self, *args, **kwargs):
        def decorator(f):
            @wraps(f)
            def wrapper(*args_, **kwargs_):
                self._subparser.add_argument(*args, **kwargs)
                return f(*args_, **kwargs_)
            return wrapper
        return decorator
```

뭔가 복잡해 보이지만 register 는 subparser 를 후킹해서 전후로 작업을 추가하고,

`add_argument`는 실제 라이브러리의 `add_argument`를 데코레이터로 추가시키도록 하는 작업입니다.

---

# As mine

위의 `register`와 `add_argument` 를 클로저로 만든 이유는

스택오버플로우를 잘 찾아보니 namespace 에다가 `func`라는 함수를
이전 코드의 `set_defaults` 로 나중에 동적으로 만드는 방식의 트릭을 쓸 수 있었습니다.

```python
class CLIInit:
    def main(cls):
        namespace = cls.bioparser.parse_args()
        if hasattr(namespace, 'func'):
            namespace.func(**vars(namespace))
        else:
            cls.bioparser.print_help()
```

자 그럼 이제 `func` 라는 클로저만 일괄적으로 만드는 규칙과,

---

# As mine

`func` 가 항상 인자로 딸려 들어오기 때문에 `**kwargs` 를 꼭 추가해야 하는 규칙만 주면 되겠네요.

```python
@bioparser.register
@bioparser.add_argument('content', nargs='?', type=str,
                        help='None(clipboard) or base64 string or filename')
@bioparser.add_argument('--inline', type=int, default=1,
                        help='1(default): inline, 0: download')
def imgcat(self):
    def func(content, **kwargs):
        if content is None:
            content = get_clipboard_image()
        print(iterm2_img_format(content, inline).decode())
    return func
```

이제 저는 제가 만들었던 스크립트의 `main` 함수만 잘 만들어서 붙이면 됩니다.

---

# As mine

mac 의 iterm2 터미널과, ubuntu guake 터미널에서는 screen-256color 를 사용해서 이미지를 출력해주는
`imgcat`이라는 bash 스크립트를 사용할 수 있습니다.

여기서 만들어주는 바이트 코드를 개조해서 만든 python 스크립트가 위의 `imgcat`의 예제인데요,

위와 같이 만들면 아래와 같은 명령어로 실행할 수 있게 됩니다.

```
#clipboard
jw imgcat
#filename
jw imgcat profile.png
```

PPT 만들면서 생각했는데 url 을 파싱하게 하는 기능을 추가해도 되겠네요.

---

# Preview

Vim 도 같이 쓰고 싶어요!

---

# Additional

vim ppt 플러그인도 있는데 굳이?
- 원하는 기능이 잇는 툴이 있어도 VIM 플러그인이 없어서(뭔가 부족).
> 사진을 뿌려주진 않더라구요.

꼭 있었으면 좋겠다 >>> 잘 쓰고 있는 기능을 버릴 수 없다.
- 에버노트에서 원노트 기능 찾지말고 조금 부족하더라도 만들어보자

그래서 하나씩 만들게 되었습니다.

---

# Additional

프로젝트를 공개로 하면서 회사 일에도 써먹기

> AWS SSM 으로 완전한 configuration 분리

```
aws ssm put-parameter --Name '/COMMON' --value "<secret>" --type String --profile <profile>
```

---

# !?

실제로 이 프로젝트는 vimgolf 에서 아이디어를 얻어
메모를 태그기반으로 저장하는 시스템을 만들어보기 위해 시작한 것입니다.
아직 vim 내부를 스크립트 하는건 시작도 못했지만요...

아직까지 지우지 못한 TODO

나는 파일에 태그를 달아서 검색을 용이하게 하는 것이 목적이다.
파일 검색은 fzf, find 등의 다른 솔루션으로도 해결 가능하다. 나는 태그와 내용을 검색하기를 원한다.
태그는 파일이름과 본문에 모두 없을 수도 있다.

TODO:
검색 및 태그 히스토리를 지정하여 자동 저장시킨다.
vim :wq에 git 을 후킹시킨다.
블로그로 퍼블리싱하는 방법도 생각해본다. ex: vimgolf
github star 레포의 태그도 연동하는 것도 괜찮은 듯

---

# Stay Motivated

- 프로그래밍으로서의 동기부여란
    - 알고리즘 문제만 풀던 시절...
    - Question: Python을 배웠는데 뭘 해야 할지 모르겠어요.
    - Answer: 뭘 하나 만들어보세요.
    - 내가 만든 스크립트의 사용성을 높여보자.

- 이 프로젝트엔 끝이 없습니다.
아직 저는 하고싶은 것이 많으며, 작은 목표를 그때 그때 달성할 뿐입니다.

---

# References

- https://docs.python.org/3/library/argparse.html#sub-commands
- https://github.com/aws/aws-cli
- https://stackoverflow.com/questions/49038616/argparse-subparsers-with-functions

# Sources

- https://github.com/jongwony/jw
- python 3.7 required

```
pip install jw
jw --help
```

---

감사합니다.
자료는 keynote 로도 만들어서 올릴 테니 걱정하지 않으셔도 됩니다. :)

---

- 각각 다른 cli를 만들었던 경험들
    - AWS 인스턴스 끄고 타입 올리고 스크립트 실행하고 다시 끄고 켜고...

