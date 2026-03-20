<p>문제 상황
Spring Boot 프로젝트에 JWT 설정을 추가했는데 서버가 뜨지 않았다.
Could not resolve placeholder 'jwt.secret' in value &quot;${jwt.secret}&quot;
JwtTokenProvider에서 @Value(&quot;${jwt.secret}&quot;)로 값을 읽으려 했는데, 스프링이 해당 키를 찾지 못한 것이다.</p>
<p>원인
application.yml을 보니 이렇게 되어 있었다.
yamlserver:
  port: 8080</p>
<p>  jwt:
    secret: 'orda-secret-key...'
jwt가 server 블록 안에 들여쓰기되어 있었다. YAML에서 들여쓰기는 계층 구조를 의미하기 때문에, 스프링은 이 값을 server.jwt.secret으로 인식했다. 하지만 코드에서는 jwt.secret으로 읽으려 했으니 당연히 찾지 못한 것이다.</p>
<p>해결
jwt를 최상위 레벨로 이동했다.
yamlserver:
  port: 8080</p>
<p>jwt:
  secret: 'orda-secret-key...'
  access-token-expiration: 1800000
  refresh-token-expiration: 604800000
server와 같은 레벨, 즉 들여쓰기 없이 작성하니 정상적으로 값을 읽어왔다.</p>
<p>핵심 정리
YAML에서 들여쓰기는 단순한 포맷이 아니라 키의 경로를 결정한다.
작성 방식스프링이 인식하는 키server 블록 안에 jwtserver.jwt.secret최상위에 jwtjwt.secret
@Value(&quot;${jwt.secret}&quot;)로 읽으려면 jwt가 반드시 최상위에 있어야 한다.</p>
<p>느낀 점
에러 로그에 Could not resolve placeholder가 뜨면 가장 먼저 application.yml의 들여쓰기부터 확인하자. 오타보다 들여쓰기 실수가 더 찾기 어렵다.</p>