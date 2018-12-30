[tmux(tty multiplexer)](https://ko.wikipedia.org/wiki/Tmux)는 터미널에서 다중 터미널 세션으로 액세스 할 수 있게 해주는 도구입니다. 세션을 열거나 기존의 세션으로 접속이 가능하며, 윈도우를 pane 단위로 분할하는 기능을 제공하여 원격 서버에서 다수의 개발자와 터미널을 공유하고 싶을 때 사용하면 편리합니다. 잘 작성된 [cheatsheet](https://gist.github.com/henrik/1967800)가 있으니 헷갈릴 때 참고하면 좋습니다.

### 세션 열기

```bash
tmux -2

# 세션의 이름 지정
tmux -2 new -s my-session
```

-2 옵션은 터미널의 256 Color를 세팅하기 위한 옵션입니다. 이를 지정하지 않으면 vi 에디터의 colorscheme이 제대로 작동하지 않습니다. 매번 지정이 귀찮다면 아래 명령어로 shortcut을 등록할 수 있습니다.

```bash
echo "alias tmux='tmux -2'" >> ~/.tmux.conf
```

### 세션 닫기

tmux 세션에 접속한 상태에서 `ctrl + d`를 입력합니다. 만약 세션을 종료하지 않고 나오고싶다면, `ctrl + b`를 입력한 후 `d`를 입력합니다.

### 세션 리스트 출력

```bash
tmux ls

# 현재 접속 가능한 세션 출력
0: 1 windows (created Sun Dec  2 19:29:27 2018) [132x39]
my-session: 1 windows (created Sun Dec  2 19:29:48 2018) [132x39] (attached)
```

만약 세션에 다시 접속하고 싶다면 아래와 같이 합니다.

```bash
# 0번 session에 접속
tmux a -t 0
# my-session session에 접속
tmux a -t my-session
```

### 화면 분할 및 이동

화면 분할은 수직, 수평 분할이 가능합니다.

&nbsp;

수직 분할을 하고싶다면 `ctrl + b`를 입력한 후 `%`를 입력합니다.

&nbsp;
수평 분할을 하고싶다면 `ctrl + b`를 입력한 후 `"`를 입력합니다.

&nbsp;
화면이 분할된 상태에서 pane을 이동하고싶다면 `ctrl + b`를 입력한 후 이동하려는 방향의 화살표를 입력합니다.