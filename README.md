## 🗉목차
[1. 개요](#1-개요)

[2. 주요 기능](#2-주요-기능)

[3. 구현](#3-구현)

[4. 개선 사항](#4-개선-사항)

## 1. 💬개요
채팅 프로그램 개발

<img src="https://img.shields.io/badge/Java-007396?style=flat&logo=OpenJDK&logoColor=white"/> <img src="https://img.shields.io/badge/sqlite-003B57?style=for-the-badge&logo=sqlite&logoColor=white"> <img src="https://img.shields.io/badge/Tcp&#47;Ip-orange?style=plastic&logo=tcp&#47ip&logoColor=white"/> <img src="https://img.shields.io/badge/androidstudio-3DDC84?style=for-the-badge&logo=androidstudio&logoColor=white"> <img src="https://img.shields.io/badge/sqlite-003B57?style=for-the-badge&logo=sqlite&logoColor=white">

## 2. 🔧주요 기능
<img src="https://github.com/user-attachments/assets/a6559926-8813-42d0-8857-ef9cfa36a1eb" width="600" height="240"/>

로그인 기능

데이터베이스에 존재하는 ID/PW와 일치할 경우 로그인 성공

<img src="https://github.com/user-attachments/assets/cd8ce812-08e7-4a83-afad-a5565bfecf35" width="300" height="200"/>

실시간 채팅 기능

## 3. ⌨️구현

사용자가 많아지더라도 중단 없이 채팅이 계속 가능하도록 하기 위해 멀티 스레드 사용
<details>
<summary>코드 펼치기</summary>

```
public void run() {
    String msg; 
    String[] rmsg; 
    status = true; 
    while(status) {
        try {          
            msg = inMsg.readLine();
            rmsg = msg.split("/");
            msgOut.append(rmsg[0] + ": "+rmsg[1] + "\n");      
            msgOut.setCaretPosition(msgOut.getDocument().getLength());
        } catch(IOException e) {                
            status = false; 
        }
    }
    System.out.println("[multiChatClient]" + thread.getName() + "�����"); 
}
```
클라이언트

서버로부터 메시지를 읽고

이를 화면에 출력
```
class ChatThread extends Thread {		
    String msg;
    String[] Rmsg;				
    private BufferedReader inMsg = null;
    private PrintWriter outMsg = null;

    public void run() {		
        boolean status = true; 
        System.out.println("##ChatThread start...");
        try {
            inMsg = new BufferedReader(new InputStreamReader(s.getInputStream()));
            outMsg = new PrintWriter(s.getOutputStream(), true);				
            while(status) {					
                msg = inMsg.readLine();					
                Rmsg = msg.split("/");										
                msgSendAll(msg);
            } 				
            this.interrupt();
            System.out.println("##"+this.getName()+"stop!!");
        } catch(IOException e) {
            chatlist.remove(this);
            System.out.println("���� �߻�!!");
        }
    }
}
```
서버

서버는 클라이언트의 연결을 수용하고, 각 클라이언트에 대해 ChatThread라는 스레드를 생성

클라이언트로부터 메시지를 읽고, 이를 다른 클라이언트에게 전송
</details>


## 4. 👨‍🔧개선 사항

회원 정보를 암호화하여 관리 해야할 필요가 있음
