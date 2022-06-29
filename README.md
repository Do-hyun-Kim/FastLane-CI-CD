# FastLane-CI-CD
FastLane Environment Variable 정의


<h1>Fast Lane Setting</h1>



<h2>App Store Connect API Key</h2>

- TestFlight에 업로드 해야 하는 것처럼 App Store Connect에 접근해야 하는 작업에는 로그인이 필요하다.

<h2>Fast Lane 인증 방식</h2>

- App Store Connect API Key

  - API Key를 발급 받아 인증 하는 형식

    - <h3>사용 방법</h3>

      - Key 생성
        - App Store Connect API Key를 발급 받습니다.
        - Domain : https://appstoreconnect.apple.com/access/users
        - 생성 된 키를 다운로드 받습니다 (.p8)
          - 새로고침을 하면 다운로드를 받을 수 없으니 주의!!
        - FastLane 디렉토리에 [Key ID].json 파일 형식을 생성하고 아래와 같이 채워 넣습니다.

- 이중 인증(2EA)

  - 키체인에 저장해두고 로그인 하는 방식으로 세션이 만료되면 이중 인증을 해야한다. 보통 아무 설정(Setting)을 하지 않았다면 등록된 App developer 계정으로 이중 인증을 해야한다.

- Application-Spectfic Password (앱 암호)

  - 



#### Key ID json 형식

```swift
{
  "key_id": "D383SF739",
  "issuer_id": "6053b7fe-68a8-4acb-89be-165aa6465141",
  "key": "-----BEGIN PRIVATE KEY-----\nMIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHknlhdlYdLu\n-----END PRIVATE KEY-----",
  "duration": 1200,
  "in_house": false
}
```







<h1>FastLane Enviroment Variables Setting</h1>



- FastFile에 (FastLane 명령어 실행 시 참조하는 Ruby 언어로 쓰인 명세)에 다음과 같이 release라는  lane을 만든다.

```ruby
stages:
  - unit_tests
  - test_flight

variables:
  LC_ALL: "en_US.UTF-8"
  LANG: "en_US.UTF-8"

before_script:
  - gem install bundler
  - bundle install

unit_tests:
  dependencies: []
  stage: unit_tests
  artifacts:
    paths:
      - fastlane/screenshots
      - fastlane/logs
  script:
    - fastlane tests
  tags:
    - ios

test_flight_build:
  dependencies: []
  stage: test_flight
  artifacts:
    paths:
      - fastlane/screenshots
      - fastlane/logs
  script:
    - fastlane beta
  tags:
    - ios
  only:
     - /^release-.*$/
     - master
```



<h1>AppFile</h1>

- App File을 통해 App Store Connect id 와 bundle identifier를 정의, Apple Email 또한 Emviroment Variables = Apple ID 을 통하여 사용 하고 있는 Appple_id를 정의

```swift
app_identifier("app_identifier") # The bundle identifier of your app
apple_id("jenny@gmail.com") # Your Apple email address

itc_team_id("your-team-id") # App Store Connect Team ID
team_id("your-portal-team-id") # Developer Portal Team ID
```



<h1>FastLane Method</h1>



<h2>1️⃣ get_certificates<br><br>
  	2️⃣ get_provisioning_profile
</h2>



- get_certificates
  - 인증서,프로비저닝 프로파일을 가져오는 메서드 이다.
    - 혹여나 각 개발자가 본인 맥에 배포 관련 인증서나, 프로비저닝 프로파일을 가질 수 있기 떄문에 이 메서드를 사용하면 각자 Fastlane을 사용하면 각자의 인증서, 프로비저닝 프로파일을 가져오기 때문에 배포 할때마다 인증서를 주지 않아도 된다.
- get_provisioning_profile
  - 프로비저닝 프로파일을 가져오는 메서드 이다.
    - 프로비저닝 프로파일을 각자 다른 맥에 관리하거나 다를수가 있기 때문에  이 메서드를 통해 프로파일을 가져오면 프로비저닝 프로파일을 주지 않아도 된다.





<h2>3️⃣ cocoapods(use_bundle_exec: Bool)</h2>

- cocoapods(use_bundle_exec: Bool) : cocoapods를 사용하고 있기 때문에 배포를 하기 전 Cocoapods를 빌드 하고  배포할수 있도록 메서드 사용

```ruby
cocoapods(use_bundle_exec: false)
```



<h2>4️⃣ build_app(workspace: String, schme: String)</h2>

- 앱을 빌드하는 메서드 입니다.

```ruby
build_app(workspace: String, schme: "AC PRD")
```





<h2>5️⃣ upload_to_app_store(skip_metadata: Bool, skip_screenshots: Bool)</h2>

- upload_to_app_store(skip_metadata: Bool, skip_screenshots: Bool) : 빌드가 성공 되면 앱스토어애 업로드 하는 메서드 이다.

```ruby
upload_to_app_store(skip_metadata: )
```



<h2>6️⃣ clear_derived_data</h2>

- Xcode 파셍 데이터 삭제 FastLane 실행시 파생 데이터 삭제후 성공시 TestFilght에 빌드

```ruby
clear_derived_data
```
