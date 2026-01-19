# UGS Marketplace MVP Demo

Unity + Cloud Code + Economy + Cloud Save 기반 학생용 거래소 MVP 세팅 문서

학생 포트폴리오 목적의 **UGS 기반 거래소 MVP**입니다.
Unity 클라이언트가 Cloud Code를 호출하고, Cloud Code가 Economy/Cloud Save를 조작하여 **등록, 조회, 구매, 취소, 정산**까지 동작합니다.

---

## 1. 프로젝트 구조 요약

### 1.1 Unity 클라이언트

* UI 버튼 입력 처리 → Cloud Code 호출
* Economy 잔고·인벤토리 조회 및 화면 갱신

### 1.2 Cloud Code(JavaScript)

* 거래 규칙 처리(검증·권한·정합성)
* Economy 재화 차감/지급, 인벤 아이템 이동
* Cloud Save에 거래소 데이터 저장/갱신

### 1.3 데이터 저장소 역할

* **Economy**: 통화 잔고, 인벤토리 아이템(인스턴스)
* **Cloud Save**: 거래소 목록, 리스팅 상세, 정산 대기 금액(earnings)

---

## 2. 포함 스크립트 목록

### 2.1 Unity C# 스크립트

* `UnityServiceInit.cs` : UGS 초기화
* `UserNamePw.cs` : Username/Password 로그인·회원가입 UI 및 로그인 후 갱신 트리거
* `UgsDiagnostics.cs` : UGS 상태 진단 로그
* `PortfolioMarketDemo.cs` : 데모 메인 컨트롤러(화면 갱신/버튼 이벤트/Cloud Code 호출)
* `InventoryRowUI.cs` : 인벤토리 1줄 UI(판매 등록 버튼 포함)
* `MarketRowUI.cs` : 마켓 1줄 UI(구매/취소 버튼 포함)

### 2.2 Cloud Code JS 스크립트(5개)

* `Mkt_CreateListing` : 판매 등록
* `Mkt_GetActiveListings` : 활성 리스팅 조회
* `Mkt_BuyListing` : 구매 처리
* `Mkt_CancelListing` : 판매 취소
* `Mkt_ClaimEarnings` : 정산 받기

---

## 3. 세팅 순서

아래 순서를 그대로 따라가면 막힐 확률이 가장 낮습니다.

---

### 3.1 Unity 프로젝트 생성 및 UGS 연결

1. Unity 새 프로젝트 생성
2. `Project Settings > Services`에서 UGS 프로젝트에 **Link**
3. 사용할 **Environment** 선택(예: Development)

   * 이후 모든 Publish/테스트는 **동일 Environment**에서 진행

---

### 3.2 Unity 패키지 설치

`Package Manager`에서 설치

* **Unity Services Core**
* **Authentication**
* **Economy**
* **Cloud Code**
* **TextMeshPro**

---

### 3.3 Unity Dashboard에서 Authentication 설정

1. Unity Dashboard 접속
2. **Authentication** 제품 활성화
3. **ID Providers**에서 **Username and Password** 추가

> 이 설정이 없으면 `SignUpWithUsernamePasswordAsync`, `SignInWithUsernamePasswordAsync`가 실패합니다.

---

### 3.4 Unity Dashboard에서 Economy 리소스 생성 및 Publish

Economy에서 아래 리소스 ID를 **대문자 그대로** 생성합니다.

#### Currency

* `COIN`

#### Inventory Item Templates

* `SWORD`
* `REDPOTION`
* `BLUEPOTION`

생성 후 반드시 **Publish**까지 진행합니다.
Publish가 누락되면 클라이언트에서 설정 동기화가 되어 보여도 실제 리소스가 맞지 않아 오류가 납니다.

---

### 3.5 Unity Dashboard에서 Cloud Save 활성화

Cloud Save는 별도 키를 미리 만들 필요가 없습니다.
Cloud Code가 실행되며 `customId = "market"` 아래에 데이터를 생성/갱신합니다.

---

### 3.6 Unity Dashboard에서 Cloud Code 스크립트 5개 생성 및 Publish

Dashboard에서 Cloud Code Scripts를 만들고, 각 파일 내용을 붙여넣은 뒤 **Publish**합니다.
스크립트 이름은 아래와 **완전히 동일해야 합니다.**

```text
Mkt_CreateListing
Mkt_GetActiveListings
Mkt_BuyListing
Mkt_CancelListing
Mkt_ClaimEarnings
```

#### Cloud Code 호출 규칙(중요)

Unity 클라이언트 C#에서 다음 형태로 호출합니다.

* `CallEndpointAsync("스크립트명", args)`

따라서 **Cloud Code 스크립트 이름이 1글자라도 다르면 호출이 실패**합니다.

---

### 3.7 Unity 씬 구성

#### 3.7.1 기본 오브젝트

* `Canvas`
* `EventSystem`

#### 3.7.2 패널/UI 구성 예시

**LoginPanel**

* Username 입력 `TMP_InputField`
* Password 입력 `TMP_InputField`
* Login 버튼
* SignUp 버튼
* 상태 출력 `Text`

**MarketPanel**

* COIN 표시 `Text`
* Refresh 버튼
* Give Random 버튼
* Add Coin 버튼
* Claim 버튼
* Inventory 리스트 `Content Transform`
* Market 리스트 `Content Transform`

#### 3.7.3 Row 프리팹 2개 제작

**InventoryRowUI 프리팹**

* 아이템명 텍스트
* 가격 입력
* Sell 버튼

**MarketRowUI 프리팹**

* 리스팅 텍스트
* Buy 버튼
* Cancel 버튼

---

### 3.8 씬에 스크립트 부착 및 인스펙터 연결

#### 권장 배치

빈 GameObject `Managers` 생성 후 아래 스크립트 부착

* `UnityServiceInit`
* `PortfolioMarketDemo`
* `UserNamePw`
* `UgsDiagnostics` (선택)

#### 인스펙터 연결 체크

**UserNamePw**

* `PortfolioMarketDemo` 참조 연결

**PortfolioMarketDemo**

* 코인 텍스트
* 버튼들(Refresh/Give Random/Add Coin/Claim 등)
* Inventory Content / Market Content
* Row Prefab 2개

  * 전부 연결

---

## 4. 실행 및 테스트 순서

1. Play
2. SignUp 진행

   * 비밀번호 규칙은 스크립트 검증을 통과해야 함
3. Login
4. Give Random

   * 인벤에 `SWORD` 또는 `REDPOTION` 또는 `BLUEPOTION` 지급 확인
5. Add Coin

   * `COIN` 증가 확인
6. Inventory에서 Sell

   * 마켓에 리스팅 표시 확인
7. Market에서 Buy 또는 Cancel
8. Claim

   * 판매자에게 earnings로 쌓인 금액 지급 확인

---

## 5. Cloud Code 파라미터 계약

C#과 JS는 아래 키 이름을 기준으로 통신합니다.
키 이름이 다르면 바로 실패합니다.

### CreateListing

* `players_inventory_item_id`
* `price`
* `currency_id`

### GetActiveListings

* `limit`
* `sort`

### BuyListing

* `listing_id`

### CancelListing

* `listing_id`

### ClaimEarnings

* `currency_id`

---

## 6. 자주 터지는 문제 체크리스트

### 6.1 Economy 리소스 ID 오타

* `COIN`, `SWORD`, `REDPOTION`, `BLUEPOTION` 대문자 정확히 확인

### 6.2 Economy Publish 누락

* 리소스 생성 후 Publish 누락 시 호출이 꼬입니다

### 6.3 Cloud Code 스크립트 이름 불일치

* Dashboard Script 이름과 C# 호출 이름이 다르면 바로 실패

### 6.4 Environment 불일치

* Development에 만들고 Publish를 다른 Environment에서 하면 데이터가 안 맞음

### 6.5 deleteInventoryItem 호출 파라미터 누락

* Cloud Code에서 `deleteInventoryItem` 호출 시 `inventoryDeleteRequest: {}` 누락하면 실패
* 현재 제공된 JS에는 포함된 상태여야 합니다

---

## 7. 권장 학습 진행 방식

### 1주차

* 로그인 성공
* COIN 읽기/증가
* 인벤 랜덤 지급

### 2주차

* 판매 등록 `CreateListing`
* 마켓 조회 `GetActiveListings`

### 3주차

* 구매 `BuyListing`
* 취소 `CancelListing`
* 정산 `ClaimEarnings`

---

## 8. 목표

* UGS 기반 실무형 역할 분리 이해
* 클라이언트: 호출 + UI
* 서버 Cloud Code: 규칙 + 데이터 정합성 + 트랜잭션 처리

---

## 9. 라이선스

교육용 샘플로 사용 가능하며, 상용 적용 시 보안/검증/트랜잭션 처리 강화가 필요합니다.
