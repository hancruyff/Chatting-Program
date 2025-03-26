# 💬 Chatting-Program
### **실시간 멀티 클라이언트 채팅 프로그램**
> Java 소켓 프로그래밍 및 멀티 스레드를 활용한 실시간 채팅 애플리케이션

<img src="https://img.shields.io/badge/Java-007396?style=flat&logo=OpenJDK&logoColor=white"/> 
<img src="https://img.shields.io/badge/SQLite-003B57?style=flat&logo=sqlite&logoColor=white"/> 
<img src="https://img.shields.io/badge/TCP/IP-orange?style=flat&logo=tcpip&logoColor=white"/>

---

## 🗉 목차  
- [1. 프로젝트 개요](#1-프로젝트-개요)  
- [2. 주요 기능](#2-주요-기능)  
- [3. 기술적 구현](#3-기술적-구현)  
- [4. 개선 사항 및 향후 계획](#4-개선-사항-및-향후-계획)  
- [5. 프로젝트 기여도](#5-프로젝트-기여도)   

---

## 1. 📌 프로젝트 개요  
**Chatting-Program**은 여러 사용자가 동시에 접속하여 실시간으로 메시지를 주고받을 수 있는 **멀티 클라이언트 채팅 프로그램**입니다.  
Java의 **소켓 프로그래밍**과 **멀티 스레드**를 활용하여 다수의 클라이언트가 동시에 원활하게 채팅할 수 있도록 구현되었습니다.  

### 📍 개발 목표  
- **TCP 기반 서버-클라이언트 통신**을 활용하여 안정적인 메시지 송수신 구현  
- 다수의 사용자가 **동시에 접속**하여 실시간으로 대화할 수 있도록 **멀티 스레드 처리**  
- **SQLite 데이터베이스**를 이용한 로그인 기능 구현 및 사용자 정보 저장  

---

## 2. 🔧 주요 기능  

### 2.1 로그인 기능  
- 사용자는 **ID/PW를 입력하여 로그인** 가능  
- **SQLite 데이터베이스**에 저장된 사용자 정보와 대조하여 인증  
- 인증 성공 시 채팅방으로 이동  

<img src="https://github.com/user-attachments/assets/a6559926-8813-42d0-8857-ef9cfa36a1eb" width="600" height="240"/>

### 2.2 실시간 채팅  
- TCP/IP 소켓 통신을 활용하여 서버와 클라이언트 간 **메시지 송수신**  
- 서버는 **모든 클라이언트에 메시지를 브로드캐스트**  
- 클라이언트 UI에서 실시간으로 채팅 내용 출력  

<img src="https://github.com/user-attachments/assets/cd8ce812-08e7-4a83-afad-a5565bfecf35" width="300" height="200"/>

### 2.3 멀티 클라이언트 지원  
- **멀티 스레드 기반 서버 구현**  
- 다수의 사용자가 접속해도 안정적인 채팅 가능  

---

## 3. ⌨ 기술적 구현  

### 3.1 멀티 스레드 기반 서버  
- 서버는 클라이언트의 연결을 수락하고, **각 클라이언트마다 개별 스레드를 생성**  
- 클라이언트로부터 받은 메시지를 **다른 클라이언트에게 브로드캐스트**  

```java
class ChatThread extends Thread {		
    private BufferedReader inMsg = null;
    private PrintWriter outMsg = null;
    
    public void run() {		
        boolean status = true; 
        try {
            inMsg = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            outMsg = new PrintWriter(socket.getOutputStream(), true);				
            
            while(status) {					
                String msg = inMsg.readLine();
                msgSendAll(msg); // 받은 메시지를 모든 클라이언트에게 전송
            } 				
        } catch(IOException e) {
            System.out.println("클라이언트 연결 종료");
        }
    }
}
```
### 3.2 클라이언트 메시지 처리  
클라이언트는 서버로부터 메시지를 수신하고 UI에 출력  

```java
public void run() {
    boolean status = true; 
    while(status) {
        try {          
            String msg = inMsg.readLine();
            msgOut.append(msg + "\n");      
        } catch(IOException e) {                
            status = false; 
        }
    }
}
```
### 3.3 로그인 인증 (SQLite 활용)
사용자의 ID/PW 정보를 SQLite에 저장

로그인 시 입력값과 데이터베이스 값을 비교하여 인증

```java
public boolean authenticateUser(String username, String password) {
    String sql = "SELECT * FROM users WHERE username=? AND password=?";
    try (Connection conn = this.connect();
         PreparedStatement pstmt  = conn.prepareStatement(sql)) {
        
        pstmt.setString(1, username);
        pstmt.setString(2, password);
        
        ResultSet rs = pstmt.executeQuery();
        return rs.next();  // 결과가 존재하면 로그인 성공
    } catch (SQLException e) {
        System.out.println(e.getMessage());
        return false;
    }
}
```
---
### 4. 👨‍🔧 개선 사항 및 향후 계획
🔹 개선 사항
✔️ 회원 정보 보안 강화

현재는 ID/PW를 평문으로 저장 → SHA-256 암호화 적용 예정

✔️ 채팅 기록 저장 기능 추가

기존 채팅 내역을 불러올 수 있도록 데이터베이스에 저장 기능 추가

✔️ UI 개선 (Java Swing 활용)

현재는 콘솔 기반 → GUI 환경 추가 개발 예정

✔️ 파일 전송 기능 추가

기본 텍스트 채팅 외에도 파일 공유 기능 추가

✔️ 메시지 암호화 기능 추가

AES-256 기반 메시지 암호화를 적용하여 보안 강화

### 5. 🏆 프로젝트 기여도
✔️ 소켓 프로그래밍을 이용한 서버-클라이언트 통신 설계 및 구현

✔️ 멀티 스레드 기반 안정적인 채팅 기능 개발

✔️ SQLite 기반 사용자 로그인 인증 시스템 구축

✔️ 코드 최적화 및 성능 개선 (불필요한 I/O 최소화)

✔️ 향후 보안 및 기능 개선 계획 수립

