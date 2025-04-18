<h1 align='center'>7.1 JDBC 드라이버 매니저 인터페이스를 사용해서 MySQL과 연결하기</h1>

# 1. 원문 번역

> [원문 링크](https://dev.mysql.com/doc/connector-j/en/connector-j-usagenotes-connect-drivermanager.html)

  어플리케이션 서버(WAS) 외부에서 JDBC를 사용하려고 할 때는 `Driver Manager` 클래스가 DB 연결을 관리한다. 어떤 JDBC 드라이버를 활용해 연결을 시도할지 `Driver Manager`한테 명시해주어야 한다. 이것을 하는 가장 쉬운 방법은 `java.sql.Driver`인터페이스를 구현한 클래스의 `Class.forName()`을 사용하는 것이다. MySQL Connector/j에서는  이 구현체 클래스의 이름이 바로 `com.mysql.cj.jdbc.Driver`.이다. 이 방법을 사용하면 드라이버의 클래스 이름과 데이터베이스 연결에 필요한 파라미터들을 주입해주는 외부 환경 파일을 사용할 수 있게 된다. 

  다음에 오는 java 코드는 어떻게 어플리케이션의 main() 함수에서 MySQL connector/J를 등록할 수 있는지를 보여준다. 만약 밑의 코드를 테스팅한다면, 파일 경로 세팅이 끝나고 올바르게 설치된 connector를 가지고 있다는 것을 확실하게 만들기 위해, [Chapter 4, Connector/J 설치법](https://dev.mysql.com/doc/connector-j/en/connector-j-installing.html)을 먼저 읽기 바란다. 또한 MySQL이 외부 TCP/IP연결을 수용하도록 설정되었는지도 확실시 해라

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

// Notice, do not import com.mysql.cj.jdbc.*
// or you will have problems!

public class LoadDriver {
    public static void main(String[] args) {
        try {
            // The newInstance() call is a work around for some
            // broken Java implementations

            Class.forName("com.mysql.cj.jdbc.Driver").newInstance();
        } catch (Exception ex) {
            // handle the error
        }
    }
}
```

드라이버가 `Driver Manager`에 등록이 된 후에, `DriverManager.getConnection()`의 호출에 의해 특정 데이터베이스와 연결되어진 연결 객체를 얻을 수 있다.

예시, 























---

### 문법 정리

- `Specify to A(사람 - 간접 목적어) B(명사구 - 직접 목적어)`: A에게 B를 알려주다. 명시해주다.
  Specify [to the DriverManager] [which JDBC drivers to try…]