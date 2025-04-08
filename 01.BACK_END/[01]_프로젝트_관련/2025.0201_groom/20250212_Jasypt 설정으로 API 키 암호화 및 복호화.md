# 0. 왜 필요한가

외부 API나 DB, 미들웨어 연동을 위해서는 활용을 위해 약속한 secret-key를 들고 있어야 한다. 보통 개발자들은 설정파일에 그것을 몰아넣고, `@Value()`를 활용하여 그것들을 불러온다. 다음 사진과 같이 말이다.

![image-20250212191455490](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[02] Jasypt 설정으로 API 키 암호화 및 복호화/image-20250212191455490.png)

이런 secret-key들은 유출되면 해킹 등 프로젝트에 치명적인 위협을 준다. 그래서 개발자들은 항상 이것들에 대해 신경 쓰고, 인프라 팀 또한 Jenkins나 DockerHub에 키 리스트들을 따로 관리해야 해서 골치 아프다. 이를 해결하기 위해 Java의 라이브러리인 `Jasypt`를 활용했다.

# 1. Jasypt란? 

## (0) 정의

단방향 암호화 혹은 단일 키 양방향 암호화를 가능하게 해주는 라이브러리이다. 이 라이브러리의 강점이라 한다면 **Jasypt로 암호화한 대상을 특정하여 서버 실행 시 자동 복호화 시켜주는 기능**이다. 이것은 어떠한 원리로 가능할까?

## (1) 원리

- 0️⃣ Secret-key 암호화는 개발자들이 미리 다 해놓는다고 가정
- 1️⃣ ENC( ) 안에 암호화된 Key를 집어넣는다.
- 2️⃣ 서버 실행 시 설정 파일에서 'ENC('로 시작되는 문자열을 찾는다.
- 3️⃣ 해당 문자열을 찾으면 Jasypt에서 만든 암호화 문자열이라 판단하고 복호화를 진행한다.
- 4️⃣ 이후 복호화된 API Key를 서버 내에서 활용 가능 하다.

# 2. Jasypt config 설정

## (1) 의존성 삽입 & secret-key 설정 파일에 작성

```java
	implementation 'com.github.ulisesbocchio:jasypt-spring-boot-starter:3.0.5'
```

```java
jasypt:
  encryptor:
    password: spot-secret-key
```

### secret-key도 설정 파일에 적히면 안되는 거 아닌가요? 😭

그것도 맞다. jasypt를 위한 secret-key도 노출되면 안된다. 우리는 효율성 측면을 따져봐야 한다. API KEY 여러 개에 대한 유출 방지보다 **jasypt에 쓰이는 secret-key 하나만 유출에 대해 신경 쓰는 게 개발자 입장에서는 머리를 비울 수 있다.** 사과를 봉투 없이 손으로 끌어 안고 이동하는 거랑 사과 하나를 먹으면서 움직이는 것에는 큰 차이가 있다.  

## (2) Config 파일 설정

```java

@Configuration
public class JasyptConfig {

    @Value("${jasypt.encryptor.password}")
    private String key;

    @Bean(name = "jasyptEncryptor")
    public StringEncryptor stringEncryptor () {
        PooledPBEStringEncryptor encryptor = new PooledPBEStringEncryptor();
        SimpleStringPBEConfig       config = new SimpleStringPBEConfig();

        config.setPassword(key);	// 암호화에 쓰일 키 
        config.setAlgorithm("PBEWithMD5AndDES");// 암호화 알고리즘 - 양방향 개인 키 알고리즘을 써야 합니다.
        config.setKeyObtentionIterations("1000");	// 키 도출 매소드 반복횟수 
        config.setPoolSize("1");	// 암호화 연산 수행 시 인스턴스 풀 크기
        config.setProviderName("SunJCE");	// 사용할 보안 제공자 설정
        config.setSaltGeneratorClassName("org.jasypt.salt.RandomSaltGenerator"); // 솔트 방식: 동일한 값이 암호화되면 다른 결과를 만들도록 하기 
        config.setStringOutputType("base64"); // 암호화된 문자열의 출력 방식

        encryptor.setConfig(config);
        return encryptor;
    }
}

```

이렇게 config 파일을 설정하면, 이제 서버를 실행할 때마다, 설정 파일에 있는 "ENC("로 시작하는 문자열을 찾아서 설정에 기록된 key와 알고리즘으로 복호화를 한다.

# 3. 그러면 암호화는 어떻게 함? 

래퍼런스를 찾아봤지만, 자동 암호화에 대한 설명은 찾아보기 힘들다. 다들 개발자가 임의로 하는 듯 하다. 자동 암호화 방법에 대해서 찾아봤지만 GPT 마저 개발자가 직접 해야한다는 답변을 내놓았다. 따라서 

- 1️⃣ 암호화 매소드를 따로 만들어 서버 내부에서 secret-key 최초 발급 시 사용 하거나
- 2️⃣ 암호화 사이트를 활용해야 할 듯 하다.
- 

https://www.devglan.com/online-tools/jasypt-online-encryption-decryption

# 4. 테스트 코드 작성

필자는 팀원들이 쓸 수 있도록 테스트 코드를 활용해 **`개발자를 위한 임의 암호화 서비스`**를 만들었다.

## (1) 폴더 구조

![image-20250212193436038](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[02] Jasypt 설정으로 API 키 암호화 및 복호화/image-20250212193436038.png)

## (2) JasyptUtil

```java
package spot.spot.global.security.util;

import org.jasypt.encryption.StringEncryptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class JasyptUtil {
    private final StringEncryptor stringEncryptor;

    @Autowired
    public JasyptUtil(@Qualifier("jasyptEncryptor") StringEncryptor stringEncryptor) {
        this.stringEncryptor = stringEncryptor;
    }
    /*
     *  문자열을 Jasypt로 암호화 하는 매서드
     *  @Param plain Text (String) 암호화할 문자열
     *  @return ENC(암호화된 문자열)
     * */
    public String encrypt(String str) {
        return "ENC (" + stringEncryptor.encrypt(str)+ ")";
    }
}

```

spring container에  Bean으로 등록된  Encryptor 객체를 가져와 그것을 활용해 인수로 받은 문자열을 암호화 하는 코드이다.  

## (3) Test 폴더에 테스트 코드 환경 만들기

![image-20250212193625452](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[02] Jasypt 설정으로 API 키 암호화 및 복호화/image-20250212193625452.png)

resource가 또 필요한 이유는 Test 폴더 내부의 파일들은 main에 있는 설정 파일을 참조하지 못하기 때문이다. 내용물은 똑같다. 이제 위에서 만든 util을 활용해 단위 테스트로 암호화된 문자열을 받을 수 있는 코드를 작성해보자.

```java
package spot.spot.global.security.config;

import org.jasypt.encryption.pbe.StandardPBEStringEncryptor;
import org.jasypt.encryption.pbe.config.SimpleStringPBEConfig;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.TestPropertySource;
import spot.spot.global.security.util.JasyptUtil;

@SpringBootTest

public class IssueEncryptString {

    @Value("${jasypt.encryptor.password}")
    private String key;

    private JasyptUtil jasyptUtil;

    @BeforeEach
    void setUp() {
        // 암호화를 담당하는 암호화 객체 설정
        StandardPBEStringEncryptor encryptor = new StandardPBEStringEncryptor();
        SimpleStringPBEConfig config = new SimpleStringPBEConfig();
        config.setPassword(key);
        config.setAlgorithm("PBEWithMD5AndDES");
        encryptor.setConfig(config);

        jasyptUtil = new JasyptUtil(encryptor);
    }

    @Test
    @DisplayName("1. YAML에서 가져온 키로 암호화/복호화 테스트")
    void testEncryptionDecryption() {
        String str = "MySecretData";
        // 암호화 및 복호화 수행
        String encryptedText = jasyptUtil.encrypt(str);
        System.out.println(encryptedText);
    }

}

```

이를 실행 하면,

![image-20250212194422867](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[02] Jasypt 설정으로 API 키 암호화 및 복호화/image-20250212194422867.png)

잘 나온다.

# 5. 이번 프로젝트에 활용 하는가..?

이번 프로젝트에서는 활용하지 않기로 했다. 왜냐하면 **클라우드 전문가가 팀에 계시기 때문이다.** 이 대신에 인프라팀에서 API를 통해 키를 동적으로 서버에 공급해주는 valut를 활용한 서비스 아키텍처를 설계 및 구현할 예정이다. 
이를 활용하여 서버를 구성하는 법은 다음 주에 포스팅 하겠다.

**VALUT API 활용 아키텍처**

![image](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[02] Jasypt 설정으로 API 키 암호화 및 복호화/image.png)