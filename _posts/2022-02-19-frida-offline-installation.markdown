---
layout: post
title:  "Frida Offline Installation"
date:   2022-02-19 17:30:00 +0900
category: Frida
---

* content
{:toc}

Frida를 인터넷 연결이 없는 상태에서 설치하는 방법을 설명한다.

프로젝트 투입 시 고객사의 환경에 따라 인터넷 연결이 제한되거나 원활하지 않은 상황에 처할 수 있기에 점검 도구인 Frida를 로컬에 존재하는 파일로부터 설치해야 하는 경우가 생길 수 있다.

## Download Python Packages

Frida 설치에 필요한 파이썬 패키지들을 다운로드 받는다.

```
$ python -m pip download frida-tools
Collecting frida-tools
  Downloading frida-tools-10.5.4.tar.gz (44 kB)
     |████████████████████████████████| 44 kB 3.6 MB/s
  Preparing metadata (setup.py) ... done
Collecting colorama<1.0.0,>=0.2.7
  Downloading colorama-0.4.4-py2.py3-none-any.whl (16 kB)
Collecting frida<16.0.0,>=15.0.0
  Downloading frida-15.1.17.tar.gz (11 kB)
  Preparing metadata (setup.py) ... done
Collecting prompt-toolkit<4.0.0,>=2.0.0
  Downloading prompt_toolkit-3.0.28-py3-none-any.whl (380 kB)
     |████████████████████████████████| 380 kB 10.9 MB/s
Collecting pygments<3.0.0,>=2.0.2
  Downloading Pygments-2.11.2-py3-none-any.whl (1.1 MB)
     |████████████████████████████████| 1.1 MB 12.4 MB/s
Collecting setuptools
  Downloading setuptools-60.9.2-py3-none-any.whl (1.1 MB)
     |████████████████████████████████| 1.1 MB 12.3 MB/s
Collecting wcwidth
  Downloading wcwidth-0.2.5-py2.py3-none-any.whl (30 kB)
Saved ./frida-tools-10.5.4.tar.gz
Saved ./colorama-0.4.4-py2.py3-none-any.whl
Saved ./frida-15.1.17.tar.gz
Saved ./prompt_toolkit-3.0.28-py3-none-any.whl
Saved ./Pygments-2.11.2-py3-none-any.whl
Saved ./wcwidth-0.2.5-py2.py3-none-any.whl
Saved ./setuptools-60.9.2-py3-none-any.whl
Successfully downloaded frida-tools colorama frida prompt-toolkit pygments wcwidth setuptools
```

pip의 `download` 명령을 사용하는 경우 패키지를 설치하지 않고 현재 위치에 저장한다.

```
$ pip list
Package    Version
---------- -------
pip        21.3.1
setuptools 60.5.0

$ ls
Pygments-2.11.2-py3-none-any.whl
colorama-0.4.4-py2.py3-none-any.whl
frida-15.1.17.tar.gz
frida-tools-10.5.4.tar.gz
prompt_toolkit-3.0.28-py3-none-any.whl
setuptools-60.9.2-py3-none-any.whl
wcwidth-0.2.5-py2.py3-none-any.whl
```

## Install Python Packages from Local

pip의 `-f`, `--find-links` 옵션을 사용하여 로컬에 존재하는 파일을 통해 패키지를 설치할 수 있도록 지정한다.

```
$ pip install --find-links=. frida-tools
```

Frida 설치가 잘 진행되다가 다음과 같이 frida의 `Python egg` 파일을 다운로드할 수 없다는 오류 메시지가 출력된다.

```
Installing collected packages: wcwidth, pygments, prompt-toolkit, frida, colorama, frida-tools
    Running setup.py install for frida ... error
    ERROR: Command errored out with exit status 1:
...
    looking for prebuilt extension in home directory, i.e. C:\Users\user/frida-15.1.17-py3.10-win-amd64.egg
    prebuilt extension not found in home directory, will try downloading it
    querying pypi for available prebuilds
    using default index URL: https://pypi.org/simple/
    downloading package list from https://pypi.org/simple/frida/
    unable to download it within 20 seconds; please download it manually to C:\Users\user/frida-15.1.17-py3.10-win-amd64.egg
```

오류 메시지에 출력된 URL에서 정해진 Frida의 `Python egg` 파일을 다운로드 받아, 현재 계정의 홈 디렉터리에 위치시키면 된다. 이는 Frida의 `setup.py`에서도 확인할 수 있다.

```python
egg_path = os.path.expanduser("~/frida-{}-py{}.{}-{}.egg".format(frida_version, python_version[0], python_version[1], os_version))
print("looking for prebuilt extension in home directory, i.e.", egg_path)

try:
    with open(egg_path, "rb") as cache:
        egg_data = cache.read()
except:
    egg_data = None

if egg_data is None:
    print("prebuilt extension not found in home directory, will try downloading it")
```

Frida의 `Python egg` 파일을 현재 계정의 홈 디렉터리에 위치시킨 후 다시 pip 설치 명령을 실행시키는 경우, 정상적으로 Frida를 설치할 수 있다.

```
$ pip install --find-links=. frida-tools
...
Installing collected packages: frida, colorama, frida-tools
    Running setup.py install for frida ... done
    Running setup.py install for frida-tools ... done
Successfully installed colorama-0.4.4 frida-15.1.17 frida-tools-10.5.4

$ frida --version
15.1.17
```

## References

[Installation - frida.re](https://frida.re/docs/installation/){:target="_blank"}

[frida/frida-python - Github](https://github.com/frida/frida-python){:target="_blank"}

[pip documentation - pip.pypa.io](https://pip.pypa.io/en/stable/cli/pip_download/){:target="_blank"}