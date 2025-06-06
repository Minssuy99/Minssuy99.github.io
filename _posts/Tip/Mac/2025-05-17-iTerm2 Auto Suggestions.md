---
title: "[Mac] iTerm2 Auto Suggestions 자동완성 플러그인 설치 및 적용"
description: Installing and Applying the Auto Suggestions Plugin for iTerm2
author: Minssuy99
date: 2025-05-17 20:30:00 +0900
categories: [Tip, Mac]
tags: [Mac, iTerm2, Zsh]
pin: false
permalink: /posts/Mac-iTerm2-Auto-Suggestions/
# math: false
# mermaid: false
image:
  path: 'thumbnail.jpg'
  lqip: data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD/4QAiRXhpZgAATU0AKgAAAAgAAQESAAMAAAABAAEAAAAAAAD/2wBDAAIBAQIBAQICAgICAgICAwUDAwMDAwYEBAMFBwYHBwcGBwcICQsJCAgKCAcHCg0KCgsMDAwMBwkODw0MDgsMDAz/2wBDAQICAgMDAwYDAwYMCAcIDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAz/wAARCAAFAAoDASIAAhEBAxEB/8QAHwAAAQUBAQEBAQEAAAAAAAAAAAECAwQFBgcICQoL/8QAtRAAAgEDAwIEAwUFBAQAAAF9AQIDAAQRBRIhMUEGE1FhByJxFDKBkaEII0KxwRVS0fAkM2JyggkKFhcYGRolJicoKSo0NTY3ODk6Q0RFRkdISUpTVFVWV1hZWmNkZWZnaGlqc3R1dnd4eXqDhIWGh4iJipKTlJWWl5iZmqKjpKWmp6ipqrKztLW2t7i5usLDxMXGx8jJytLT1NXW19jZ2uHi4+Tl5ufo6erx8vP09fb3+Pn6/8QAHwEAAwEBAQEBAQEBAQAAAAAAAAECAwQFBgcICQoL/8QAtREAAgECBAQDBAcFBAQAAQJ3AAECAxEEBSExBhJBUQdhcRMiMoEIFEKRobHBCSMzUvAVYnLRChYkNOEl8RcYGRomJygpKjU2Nzg5OkNERUZHSElKU1RVVldYWVpjZGVmZ2hpanN0dXZ3eHl6goOEhYaHiImKkpOUlZaXmJmaoqOkpaanqKmqsrO0tba3uLm6wsPExcbHyMnK0tPU1dbX2Nna4uPk5ebn6Onq8vP09fb3+Pn6/9oADAMBAAIRAxEAPwD82praSW6hkWZo44t2+MAYlyMDJ6jHXipaKK9Q4z//2Q==
  # alt:
media_subpath: '/assets/img/posts/Tip/Mac'
---
<!--------------------------------------yaml-------------------------------------->
# _**zsh-autosuggestions**_
---

`Mac` 환경에서 `iTerm2` 와 `Zsh` 조합은 터미널 작업을 좀 더 편하게 만들어 준다.

특히 자주 사용하는 명령어나 이전에 입력했던 명령어를 다시 입력할 일이 많다면,

자동으로 명령어를 제안해주는 `zsh-autosuggestions` 플러그인을 설치해두면 상당히 유용하다.

이 글은 `zsh-autosuggestions` 플러그인을 설치하고 적용하는 방법을 정리한다.

<br>

## _**설치 방법**_
---

이 플러그인은 `Homebrew` 를 통한 방식과 `Git` 을 직접 클론하는 방식, 두 가지 방식으로 설치할 수 있다.

필자는 `2` 번째 방식으로 진행하였으나, 자신의 환경과 선호에 맞는 방법을 선택하면 된다.

<br>

### _**git clone 을 이용한 설치**_
___

> 이 단계를 진행하기 전, git 이 설치되어 있어야 한다.
{: .prompt-danger }

<br>

**1 .** `iTerm2` 를 실행한 뒤, 아래의 명령을 실행한다.

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```
{: file="iTerm2"}

<br>

**2 .** 설치가 완료된 뒤, 아래의 명령을 통해 `.zshrc` 파일에 접근한다.

```bash
vi ~/.zshrc
```
{: file="iTerm2"}

<br>

**3 .** 아래의 내용을 추가 및 저장 후, `iTerm2` 를 재시작 하면 적용된다.

* **`i` 키 입력** : 입력모드로 전환
* **`Esc` 키 입력** : 명령어 대기 상태로 전환
* **`:` `w` `q` 입력 후 `Enter`** : 저장 및 종료

`i → 내용 입력 → Esc → :wq → Enter`{: .filepath} 순서로 진행하면 된다.

```bash
# Which plugins would you like to load?
# Standard plugins can be found in $ZSH/plugins/
# Custom plugins may be added to $ZSH_CUSTOM/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# Add wisely, as too many plugins slow down shell startup.
plugins=(                    ## 수정
git                          ## 수정
zsh-autosuggestions          ## 수정
)                            ## 수정

source $ZSH/oh-my-zsh.sh
```
{: file="vi ~/.zshrc"}



<br>

### _**Homebrew 를 이용한 설치**_
___

**1 .** `iTerm2` 를 실행한 뒤, 아래의 명령을 실행한다.

```bash
brew install zsh-autosuggestions
```
{: file="iTerm2"}

<br>

**2 .** 설치가 완료된 뒤, 아래의 명령을 통해 `.zshrc` 파일에 접근한다.

```bash
vi ~/.zshrc
```
{: file="iTerm2"}

<br>

**3 .** 아래의 내용을 추가 및 저장 후, `iTerm2` 를 재시작 하면 적용된다.

* **`i` 키 입력** : 입력모드로 전환
* **`Esc` 키 입력** : 명령어 대기 상태로 전환
* **`:` `w` `q` 입력 후 `Enter`** : 저장 및 종료

`i → 내용 입력 → Esc → :wq → Enter`{: .filepath} 순서로 진행하면 된다.

> 반드시 `source $ZSH/oh-my-zsh.sh` 의 위에 작성하도록 한다.
{: .prompt-danger }

```bash
# Which plugins would you like to load?
# Standard plugins can be found in $ZSH/plugins/
# Custom plugins may be added to $ZSH_CUSTOM/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# Add wisely, as too many plugins slow down shell startup.
plugins=(git)

source /opt/homebrew/share/zsh-autosuggestions/zsh-autosuggestions.zsh # 추가

source $ZSH/oh-my-zsh.sh # 아래에 작성 X
```
{: file="vi ~/.zshrc"}

<br>

## _**Result**_
___

<KBD>→</KBD> 방향키를 누르면 해당 명령어가 자동완성 된다.

![img](2025-05-17-autosuggestions.jpg){: .rounded-10 .w-75 .shadow w='1200' h='630' }
_Applied_

<br>

## _**Reference**_
___

* [Eric Freese's GitHub - zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)