## IAM Resources
- 유저, 그룹, 역할(role), 정책(policy),  identity provider objects(oauth2 사용 가입자??)에 관한 정보로 IAM의 개념에 포함된다.
- 위의 Resouce들을 더하고 빼고 수정할 수 있다.

## IAM Identities
- 식별 및 그룹화에 사용되는 IAM 리소스 객체.
- IAM Identitiy에 하나의 정책, policy를 부여(attatch)할 있습니다.
- 이 policy라고 불라는 정책에는 user, group, roles가 있습니다.

## IAM Entity
- AWS에서 Authentication에 사용하는 IAM 리소스 객체.
- IAM 사용자 및 역할이 데이터로 담겨 있다.

## Principals
- AWS 루트 계정, IAM 유저 계정,  IAM role을 사용하는 사용자나 어플리케이션을 뜻하고 해당 계정과 role을 통해 sign in 하고 AWS에게 request를 할 수 있다.
- federation 유저와 assumed roles을 모두 포함한다.
- 일상적으로 AWS를 사용할 때는 root 계정을 그대로 쓰기보다는 IAM Entity(user나 role)를 하나 생성하여

## Request
- 하나의 principal이(이하 P) AWS Management Consol,  AWS API,  AWS CLI을 사용할 때 principal은 AWS에게 request를 보낸다. 해당 request는 다음의 정보를 갖고 있다.
    1. Actions or operations: P의 행위(to perform)를 나타내는 정보. AWS Management Consol,  AWS API,  AWS CLI에서 수행될 P의 요청을 뜻 함.
    2. Resouces: P의 요청이 수행되는 Resouce 객체.
    3. Principal: 요청을 보내고자하는 IAM Entity를 사용하는 사용자 혹은 어플리케이션. principal에 대한 정보를 보면 이때 사용된 IAM Entity와 연관된 policy를 알 수 있다.
    4. Environment data: IP주소, 유저 agent, SSL, 시간 관련 데이터를 의미한다.
    5. Resouce data: 요청된  resouce관련 데이터. EC2 인스턴스의 DynamoDB 테이블 정보나 태그 관련 정보가 담겨 있다.
- AWS는 request정보들을 모아 request context에 담고 이는 해당 request를 AWS에서 실제로 처리할지를 결정하는데 사용된다(evaluate & authorize)

## Authentication
- 하나의 principal은 request를 보내기 위해 자신의 credential을 AWS로 전달하여 인증(Authentication)을 받아야 한다. S3, STS 같은 서비스들은 익명 사용자의 request도 수용한다. 하지만 이는 특수한 예외에만 사용된다.
- root 사용자가 인증을 받으려면 가입한 이메일과 패스워드로 로그인(sign in)을 해야한다. IAM user는 account ID나 alias를, 이후 user namerhk password를 제공해야 한다.
- AWS API, AWS CLI를 이용하기 위한 인증은 access key와 secret key가 필요하다. 추가 보안 정보가 필요한 경우도 있다.
- 예를 들어 MFA(Muti-factor-Authentication)같은 추가 인증을 말하낟..
- MFA는 쉽게 말해 네이버나 카카오에서 메일이나 메세지로 2차인증을 요구하는 것과 같은 개념을 AWS에서 적용한 것이다.

## Authorization
- 인증이 끝났으면 인가를 받아 AWS 서비스를 이용할 수 있다.
- AWS는 request context를 참고해 내부의 request를 확인하고 각 request 마다 적용된 policies를 체크한다.
- 이 policy의 내용을 보고 인가여부를 결정한다.
- 이 policy는 AWS에 JSON 문서로 저장되어 있고 이를 보면 인가 판단에 필요한 request를 보낸 principal의 entity 정보가 담겨 있다.
- policy의 타입은 여러가지가 있고 각 타입 허용되는 범위가 다르다
- identity-based policies만 보유하면 본인 계정에 있는 AWS resouce에 접근할 수 있다.
- 또한 Resource-based policies가 있다면 cross-account access를 이용할 수 있다.
- 만약 AWS가 request의 policy를 확인하다가 a single permissions policy인데도  denied action이 작성되어 있다 해당 request 자체가 거부된다.
- 이를 "explcit deny"라고 한다. 그 policy가 허용하는 적용 범위까지만 deny되지 않을 수 있다.
- single account의 request에 적용되는 검증 규칙은 다음과 같다.
  1. 기본적으로 모든 요청은 비허용(denied) 상태가 기본값이다. (반면 root 사용자의 credential을 통해 만드는 request는 따로 설정 안 해도 허용이 기본이다.)
  2. identity-based or resource-based 같은 policy의 특별한 요청이라면 허용이 된다.
  3. Organizations SCP, IAM permissions boundary 혹은 session policy일 경우에는 2번 예외를 또 뒤집는다.(denied)

## Actions or operations
- 인증, 인가가 통과된 request는 AWS에서 액션과 오퍼레이션을 작성한대로 수행할 수 있게 된다.
- 오퍼레이션에는 CRUD 같은 작업을 말한다. resouce를 가지고 해당 작업을 수행할 수 있는 것이다.
- 예를 들어 IAM 서비스는 약 40개의 user resouce관련 작업을 제공한다.
  - Ex.CreateUser, DeleteUser, GetUser, UpdateUse
- 어떤 principal이 operation을 하고자 한다면 principal의 policy에는 이에 필요한 액션을 포함해야 한다.

## Resources
- AWS가 허용을 하고 난 후에는 request가 관련 계정에 연관된 resouces를 통해 수행됩니다.
- 여기서 resouce란 어떤 데이터를 의미하는 것이 아닌 AWS에서 제공하는 서비스에 존재하는 객체를 의미하는데 EC2 instance, IAM user 객체, S3 bucket등을 말합니다.
- 각 서비스는 resouce를 통해 수행되는 액션들의 합(set of actions)을 정의해놓은 것을 말한다.
- 만약 어떤 한 resouce와 연관 없는 액션을 수행하고자 request를 만든다면 해당 request는 deny 될 것이다.
- 만약 IAM user를 삭제하는 request를 만들 때, 여기에 IAM group resource를 이용한다면 역시 deny될 것이다.
-
