---
layout: post
title: "[Java] JButton ActionListener를 통해 다른 클래스의 함수를 불러올 때, NullPointerException이 나는 경우"
slug: "[Java] 버튼으로 함수 실행시, NullPointerException"
date: 2016-11-20 03:34:00 +0900
categories: ["Development", "Java"]
---
네트워크 학기 과제를 하다가 만난 문제점이다
소켓 연결을 이용한 로그인과 채팅 시스템이 주요 기능이었는데, 특정 ActionListener를 통해 다른 클래스의 함수를 Call할 때, NullPointerException이 발생했다.

Client class에서 Server class쪽으로 메시지를 보내는 함수는 다음과 같았다.
{% highlight java %}
public void sendLoginRequest(String _ID, String _PW) throws IOException{
 this.ID = _ID;
 this.PW = _PW;
     
 if (ID.isEmpty() || PW.isEmpty() || (checkStringPattern(ID) == -1)){
  myLogin.wrongParam();
 }else{
  out.println(ID+"|"+PW);
 }
}
{% endhighlight %}

그리고 이 함수는 Login class 내에서 액션 리스너를 통해 실행이 되는 구조였다.
{% highlight java %}
loginBtn.addActionListener(new ActionListener(){
 @Override
 public void actionPerformed(ActionEvent e) {
  try {
   System.out.print(IDtxt.getText() +"  |||  "+PWtxt.getText());
   clnt.sendLoginRequest(IDtxt.getText(), PWtxt.getText());
  } catch (IOException e1) {
   e1.printStackTrace();
  }
 }
});
{% endhighlight %}

<b>이 부분이 문제였다다.</b>
위 액션 리스너를 통해서 Client의 Instance인 `clnt`의 함수 `sendLoginRequest()`를 실행할 때마다, NullPointerException이 발생했다.

메시지를 보내는 주요 코드 부분은 `out.println(ID+"|"+PW)`이 부분인데, 함수명이 잘못되었거나, 넘어가는 값이 이상했거나에 대한 가능성을 확인해보았지만 애꿎은 파라미터만 탓하는 상황이었다.

계속 살펴본 결과 `out` 객체에 눈이 가게 되었고, 요놈이 오류의 주범이라는 걸 알게 되었다.

Java와 같은 OOP 언어에서는 클래스를 만든 후, 해당 클래스에 접근하기 위해 클래스를 상속받는 Instance를 만들어야한다.
만약 A 클래스에 Integer x라는 변수가 있다고 쳐보자.
A 클래스에 대한 Instance K와 L은 서로 독립된 x의 변수를 갖고 있다. 즉, 공유된 값을 갖고있지 않다는 뜻이다.

이번 문제도 위와 같은 상황에 놓여진 문제였다.
메시지를 <b>보낼 때</b> 사용했던 `out` 객체와 처음 초기화를 진행할 때 사용했던 `out` 객체는 서로 다른 instance에서 초기화 됐다는 걸 알게되었고, `out` 객체를 static으로 선언해줌으로써 번거롭게 각 instance마다 초기화를 해주지 않고 사용할 수 있도록 코드를 수정했다.

다른 클래스의 함수를 호출할 때엔 그때 사용되는 모든 객체의 scope를 확실히 생각하도록 하자!