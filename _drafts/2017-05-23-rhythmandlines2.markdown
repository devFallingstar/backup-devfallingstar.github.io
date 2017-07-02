---
layout: post
title: "[C언어 리듬게임 만들기 2] - 게임의 구성 생각해보기"
date: "2017-05-25 19:31:00 +0900"
categories: ["Development", "Programming", "C", "OpenGL"]
---
## 앞서보기
저번 게시글에서 클래스의 구상을 다음과 같이 나누어 했었다.
1. GUI 출력을 담당하면서, 각종 입력을 처리하는 클래스
2. 입력을 처리해야할 때, 입력의 정확도에 대해서 처리하는 클래스
3. Note(리듬 게임 내의 건반)의 데이터 처리를 담당하는 클래스
4. Note의 GUI 처리를 담당하는 클래스

이번 게시글에서는 위 클래스 중에서도 1번 클래스, 즉 GUI의 출력 및 입력을 담당하는 클래스의 구현에 대해서 알아보자.

참고로, GUI의 출력 및 입력을 하기 위해서 사용되는 라이브러리는 OpenGL 라이브러리이며, 각자의 OS에 맞게 해당 라이브러리를 설치해주길 바란다.
Mac OS X는 기본적으로 설치되어있기 때문에, 추가적인 설치가 필요없다.

## 음악 ID 입력 받기
음악 id는 각 음악을 확인할 때 사용할 수 있는 음악 별 고유의 값이다.
1부터 시작하며, 1,2,3,4...과 같이 증가한다.
고유 id는 개발자가 정하기 나름이며, 더 좋은 방법이 있다면 그 방법으로 교체해도 된다.
하지만 확실한 모듈화와 구조화를 위해 일단은 1,2,3과 같이 일반적인 값을 입력받는 것으로 하자.

일단 파일을 처음 만들게 되면 다음과 같은 함수가 미리 만들어져 있을 것이다.
참고로 파일 이름은 GameSystem.c이다.
{% highlight c %}
// GameStart.c
int main(int argc, const char * argv[]) {
	return 0;
}
{% endhighlight %}

익히 아는 main함수이다.
없다면 적어주자.
모른다면 게임을 만드는 작업은 아직 무리일지도 모른다. 판단에 따라 뒤로가기를 눌러주자. c언어 혹은 프로그래밍의 기초를 배우고 오는 것이 편할 것이다.

이 함수가 실행되면 우리는 유저로부터 정수 값 하나를 입력받을 것이다. 변수 명이 code인 이 정수형 변수는 위에서 말했던 음악의 id를 의미한다.
{% highlight c %}
// GameStart.c
int main(int argc, const char * argv[]) {
  int code = 0;

	printf("Enter the code of music : ");
	scanf("%d", &code);

	return 0;
}
{% endhighlight %}

이런식으로 id의 값을 의미하는 code에 값을 입력했다면, 그 값으로 음악과 채보 데이터를 처리하게 된다.
{% highlight language %}
// GameStart.c
int main(int argc, const char * argv[]) {
  int code = 0;

	printf("Enter the code of music : ");
	scanf("%d", &code);

  if (code > 0) {
      playMusic(code);
  		musicSelect(code);
  }

	return 0;
}
{% endhighlight %}

가독성 및 구조화를 위해 playMusic()과 musicSelect()는 다른 클래스로 옮겨서 구현할 수도 있다.
실제 코드에서는 다른 클래스로 옮겼지만 여기서는 간단히 개념만 알아보기 위해 해당 내용은 각자의 구현에 맡기도록 하겠다.

먼저 선택된 게임을 플레이 하기위한 GUI를 출력해주는 함수인 musicSelect 함수에 대해서 알아보자.
## GUI 출력
{% highlight c %}
void musicSelect(int id) {
	glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGBA);
	glutInitWindowSize(1200, 800);
	glutCreateWindow("Rhythm And Lines - Evans");

	glutDisplayFunc(displayMainWindow);
	glutReshapeFunc(reshapeMainWindow);
	glutKeyboardFunc(keyboardMainWindow);
	glutKeyboardUpFunc(keyboardUpMainWindow);
	glutTimerFunc(30, timerMainWindow, 1);

	g_maxNotes = 2000;
	myTmpNotes = (TmpNote*)malloc(sizeof(TmpNote)*MAXSIZE);
	loadData(id);

	initMainWindow();
	glutMainLoop();
}
{% endhighlight %}

glutInitDisplayMode함수로 GLUT_DOUBLE과 GLUT_RGBA를 지정한다.
애니메이션을 위한 더블 버퍼링, 투명도를 더한 RGB 색상을 처리하기 위해서 해당 모드를 사용한다.

glutInitWindowSize함수로 창의 사이즈를 조절한다. 순서대로 width, height이다.
glutCreateWindow함수로 창을 생성함과 동시에 해당 창의 제목을 작성한다.

이하 glut함수들은 창의 출력이나 입력, 변환과 같은 이벤트를 처리해주기위한 콜백 함수이다.
화면 출력에 대한 displayMainWindow(), 화면 전환에 대한 reshapeMainWindow(), 키보드 키를 눌렀을 때 반응하는 keyboardMainWindow(), 키에서 손가락을 땠을 때 반응하는 keyboardUpMainWindow(), 마지막으로 매 주기마다 화면의 움직임(애니메이션)을 처리하기위한 timer 함수인 timerMainWindow()가 있다.

g_maxNotes는 하나의 채보가 가질 수 있는 최대 노트 수를 의미하며, loadData함수를 통해 미리 저장된 채보의 값을 전부 읽어온다.
모든 데이터를 로딩 했다면, 창의 초기화 함수를 실행하고, 그래픽을 표시해주기위한 loop에 진입한다.
