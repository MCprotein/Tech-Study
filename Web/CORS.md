# CORS

- Cross-Origin Resource Sharing
- 추가 HTTP 헤더를 사용하여 한 출처에서 실행중인 웹 어플리케이션이 다른 출처의 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제. 웹 어플리케이션은 리소스가 자신의 출처(도메인, 프로토콜, 포트)와 다를 때 CORS HTTP 요청을 실행
- 보안상의 이유로 브라우저는 스크립트에서 시작한 CORS 요청을 제한한다. API를 사용하는 웹 어플리케이션은 자신의 출처와 동일한 리소스만 불러올 수 있으며, 다른 출처의 리소스를 불러오려면 그 출처에서 올바른 CORS 헤더를 포함한 응답을 반환해야 한다.

- 예시: https://domain-a.com의 프론트 엔드 JavaScript 코드가 XMLHttpRequest를 사용하여 https://domain-b.com/data.json을 요청하는 경우.

#### 출처

- https://developer.mozilla.org/ko/docs/Web/HTTP/CORS
- https://velog.io/@alli-eunbi/Node-js-%EB%B0%B1%EC%97%94%EB%93%9C-%EB%A9%B4%EC%A0%91-%EC%A7%88%EB%AC%B8-%EC%A0%95%EB%A6%AC#cors
