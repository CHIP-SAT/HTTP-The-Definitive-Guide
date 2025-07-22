

### 01장 - HTTP 개관
> 1장은 개요임, 책에서 소개할 모든 내용을 한 페이지 분량으로 요약해둠

##

### **HTTP: 인터넷의 멀티미디어 배달부**

JPEG, HTML, 텍스트 파일, MPEG 동영상, 음성 파일 등의 리소스들을 웹 브라우저 간에 주고받음

이때, `HTTP는 신뢰성 있는 데이터 전송 프로토콜을 사용하기 때문에` 데이터 전송 중 손상이나 꼬이지 않음을 보장한다고 하는데 TCP 얘기하는듯

웹 서버는 리소스를 관리하고 제공한다. 여기서 말하는 리소스는 위에서 언급한 정적 파일들, 혹은 라이브 영상 같은 동적 파일도 해당됨

<br>

### **미디어 타입 - MIME**

HTTP 헤더에는 `Content-type` 이 있고, 이는 전송할 값, 혹은 받을 값에 대한 타입을 명시하는 역할이다.

`MIME`(= **M**ultipurpose **I**nternet **M**ail **E**xtensions), 원래 이메일에서 유래된 기술로 HTTP에서도 추후에 채택했다고 한다. HTTP 0.9 버전에서는 없었다고 함

MIME 타입은 사선(/)으로 구분된 주 타입과 부 타입으로 이루어진 문자열 라벨이다.

- **HTML**로 작성된 텍스트 문서는 `text/html`
- **JPEG** 이미지는 `image/jpeg`
- **GIF** 이미지는 `image/gif`

아래는 토덕 프로젝트에서 실제로 사용하고 있는 방식이다.

<img width="847" height="264" alt="image" src="https://github.com/user-attachments/assets/0b67d09c-91bb-4ed5-b0d4-181920039feb" />

**일반 텍스트의 경우** 디코딩하여 `application/json`으로 보내고,

**S3와 같은 오브젝트 스토리지에는 이미지**를 업로드 하니까 `image/jpeg`로 HTTP 헤더를 구성한다.

이미지를 업로드하는데, image/gif를 사용하면 에러가 발생한다.

<br>

### **URI (Uniform Resource Identifier)**

URI는 URL + URN이다.

- URL의 L은 locator로, 리소스의 위치를 나타냄

- URN의 N은 Name이고 리소스에 대한 위치와 상관없는 절대이름을 의미하는듯 ?

<img width="2048" height="198" alt="image" src="https://github.com/user-attachments/assets/8052368c-0610-4a1e-aea9-e7dcc376fa33" />

처음 :// 앞에 스킴이 오고 우리는 매번 https를 사용하지만,

딥링크, 유니버셜 링크와 같은 것에서도 스킴이 사용된다.

아래는 토덕 딥링크 예시
`toduck://profile?userId=56`

<img width="1590" height="742" alt="image" src="https://github.com/user-attachments/assets/7ea810e5-5e7f-4000-ac63-8c6878a3f3fb" />

우리가 임의로 만들었어도 :// 앞을 스킴으로, 그 후에 오는 걸 호스트로 인식하며, ? 뒤에 오는 건 쿼리로 인식한다.

<br>

### HTTP 메소드

- **GET :** 리소스 조회, 바디가 없음
- **POST:**  요청 데이터 처리, 주로 등록에 사용
- **PUT :** 리소스를 대체(덮어쓰기), 해당 리소스가 없으면 생성
- **PATCH :** 리소스 부분 변경 (PUT이 전체 변경, PATCH는 일부 변경)
- **DELETE :** 리소스 삭제
- **HEAD :** GET과 동일하지만 메시지 부분(body 부분)을 제외하고, 상태 줄과 헤더만 반환


추후 학습내용

GET과 HEAD 차이 알아보기

PUT과 PATCH에는 멱등성 개념이 있는데, 추후에 공부해보면 좋을듯

<br>

### HTTP 상태 코드
> HTTP 응답에서 상태를 나타내는 코드

- **1xx**: 정보 응답 (예: 100 Continue)
- **2xx**: 성공 (예: 200 OK, 201 Created)
- **3xx**: 리다이렉션 (예: 301 Moved Permanently, 302 Found)
- **4xx**: 클라이언트 오류 (예: 400 Bad Request, 404 Not Found)
- **5xx**: 서버 오류 (예: 500 Internal Server Error, 502 Bad Gateway)

<br>

### HTTP 메시지

> HTTP 요청과 응답 메세지의 구조
> 

<img width="2048" height="849" alt="image" src="https://github.com/user-attachments/assets/e49741f3-db0b-4038-9403-0f71f16eaf8b" />

**시작줄**

요청의 경우 뭘 해야 하는지, 응답이라면 어떤 일이 발생했는지를 나타냄

**헤더**

시작줄 다음에 나타나는 것으로 0개 이상 올 수 있다.

키밸류로 구분하여 하나의 값으로 구성됨

**본문**

요청 및 응답에서 데이터를 삽입하는 공간

텍스트 뿐만 아니라, 이진 데이터를 포함할 수 있으므로 멀티 미디어도 담을 수 있다.

<br>

### TCP/IP 위의 HTTP 동작

1. `https://naver.com`의 URL에 대해 호스트 명을 추출함
여기서 호스트 명은 naver.com
2. 위 호스트 명에 대해 DNS를 통해 IP로 변환함
3. URL에 포트번호가 있으면 추출, 없으면 80으로 인식
4. 내 IP 주소와 포트번호, 목적지 IP 주소와 포트번호를 통해 TCP 연결
5. 맺어진 TCP 커넥션을 통해 HTTP 요청
6. 서버로부터 HTTP 응답 받기

<br>

### HTTP 프로토콜 버전

**HTTP/`0.9`**

- 1991년 당시 프로토타입
- GET 메소드만 지원하고, MIME 및 헤더 지원 X
- HTML 객체를 받아오기 위해 만들어진 것

**HTTP/`1.0`**

- 처음 널리 쓰이기 시작한 버전
- 헤더, 추가 메소드, 멀티미디어 처리 등이 가능해짐

**HTTP/`1.0+`**

- keep-alive 커넥션, 가상 호스팅, 프락시 연결 등 가능

**HTTP/`1.1`**

- 현재 HTTP 버전
- HTTP 설계의 구조적 결함, 성능 최적화 등에 집중함

**HTTP/`2.0`**

- 구글 SPDY(= 스피디) 프로토콜을 기반으로 설계함
- 여러 파일을 병렬로 전송 가능

<br>

### 웹 구성요소

> 지금 다루기엔 헤비하니까 패스
> 
- **`프락시`** - 클라이언트와 서버 사이에 위치한 HTTP 중계자
- **`캐시`** - 많이 찾는 웹페에지를 클라이언트 가까이에 보관하는 HTTP 창고
- **`게이트웨이`** - 다른 애플리케이션과 연결된 특별한 웹 서버
- **`터널`** - 단순히 HTTP 통신을 전달하기만 하는 특별한 프락시
- **`에이전트`** - 자동화된 HTTP 요청을 만드는 준지능적 웹 클라이언트
