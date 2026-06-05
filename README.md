# 1. Introduction
  최근 여행 인구 증가로 인해 교통비, 숙박비, 식비 등 다양한 여행 경비를 여러 사람이 함께 부담하는 경우가 많아지고 있다. 그러나 지출 항목마다 참여 인원이 다르고 결제 방식이 달라 비용 정산 과정이 복잡해지며 수기로 관리할 경우 정산 누락이나 계산 오류가 발생할 수 있다. 이를 효율적으로 해결하기 위해 여행 중 발생하는 지출 내역을 기록하고 자동으로 정산 결과를 제공하는 여행 경비 관리 어플리케이션 Trabill을 개발한다.
 
  본 문서는 Analysis 단계에서 정의된 요구사항을 기반으로 Design 단계의 문서이다. Class Diagram, Sequence Diagram, State Machine Diagram을 통해 각 Diagram과 시스템의 구조와 동작과정에 대해 설명한다. Implementation requirements를 기술하여 본 시스템을 구현에 관여하는 모든 요소를 구체적으로 디자인하는 내용을 다룬다. 

# 2. Class diagram
![image](https://github.com/user-attachments/assets/8c4e6606-a197-4134-88ee-5b42bc426ddd)

## 1) User
사용자의 기본 정보를 관리하는 클래스이다.

| 구분 | 내용 |
|---|---|
| Attributes | `userId : String` : 사용자의 고유 아이디 |
|  | `email : String` : 사용자가 입력한 이메일 |
|  | `nickname : String` : 사용자가 입력한 닉네임 |
|  | `password : String` : 사용자가 입력한 비밀번호 |
|  | `accountNumber : String` : 사용자가 입력한 계좌번호 |
| Methods | signUp() : 사용자의 정보로 회원가입을 한다. |
|  | updateProfile() : 사용자 정보를 수정한다. |
|  | deleteAccount() : 사용자 계정을 삭제한다. |

## 2) AuthService
로그인, 로그아웃, 사용자 인증을 담당하는 서비스 클래스이다. 

| 구분 | 내용 |
|---|---|
| Methods | `login(email : String, password : String)` : 이메일과 비밀번호를 입력하여 로그인을 수행한다. |
|  | `logout(userId : String)` : 사용자가 로그아웃을 한다. |
|  | `validateUser(userId : String)` : 사용자 인증 정보가 유효한지 확인한다. |

## 3) TravelGroup
 여행 정산 기능의 중심이 되는 클래스이다.
 
| 구분 | 내용 |
|---|---|
| Attributes | `groupId : String` : 여행 그룹 고유 ID |
|  | `creatorId : String` : 여행 그룹을 생성한 사용자 ID |
|  | `groupName : String` : 여행 그룹 이름 |
|  | `inviteCode : String` : 그룹 초대를 위한 초대 코드 |
|  | `createdAt : Long` : 그룹 생성 시간 |
| Methods | `createGroup()` : 여행 그룹을 생성한다. |
|  | `inviteMember()` : 여행 그룹에 멤버를 초대한다. |
|  | `deleteGroup()` : 여행 그룹을 삭제한다. |

## 4) GroupMember
특정 여행 그룹에 참여한 사용자를 나타내는 클래스이다. 하나의 사용자는 여러 여행 그룹에 참여할 수 있으므로 User와 분리하여 관리한다.

| 구분 | 내용 |
|---|---|
| Attributes | `memberId : String` : 그룹 멤버 고유 ID |
|  | `userId : String` : 해당 멤버와 연결된 사용자 ID |
|  | `groupId : String` : 멤버가 속한 여행 그룹 ID |
|  | `nickname : String` : 그룹 내에서 사용하는 닉네임 |
|  | `accountNumber : String` : 정산에 사용할 계좌번호 |
| Methods | `joinGroup()` : 여행 그룹에 참여한다. |
|  | `leaveGroup()` : 여행 그룹 빠진다. |

## 5) Expense
여행 중 발생한 지출 정보를 관리하는 클래스이다. 지출 내역은 특정 여행 그룹에 속하며, 결제자는 GroupMember와 연결된다.

| 구분 | 내용 |
|---|---|
| Attributes | `expenseId : String` : 지출 내역 고유 ID |
|  | `groupId : String` : 지출이 발생한 여행 그룹 ID |
|  | `payerId : String` : 결제를 수행한 그룹 멤버 ID |
|  | `amount : Long` : 지출 금액 |
|  | `memo : String` : 지출 내역에 대한 메모를 작성한다. |
|  | `createdAt : Long` : 지출 내역 생성 시간 |
| Methods | `addExpense()` : 지출 내역을 추가한다. |
|  | `editExpense()` : 지출 내역을 수정한다. |
|  | `deleteExpense()` : 지출 내역을 삭제한다. |
|  | `viewExpense()` : 지출 내역을 조회한다. |

## 6) ReceiptImage
특정 지출 내역에 첨부되는 영수증 이미지를 관리하는 클래스이다. Expense에 종속되는 객체이다.

| 구분 | 내용 |
|---|---|
| Attributes | `imageId : String` : 영수증 이미지 고유 ID |
|  | `expenseId : String` : 연결된 지출 내역 ID |
|  | `imageUrl : String` : 영수증 이미지 저장 경로 |
| Methods | `uploadImage()` : 영수증 이미지를 업로드한다. |
|  | `deleteImage()` : 영수증 이미지를 삭제한다. |

## 7) Settlement
여행 그룹의 전체 지출을 기준으로 정산 결과를 생성하는 클래스이다. 하나의 정산은 여러 개의 정산 상세 내역을 가진다.

| 구분 | 내용 |
|---|---|
| Attributes | `settlementId : String` : 정산 고유 ID |
|  | `groupId : String` : 정산이 진행되는 여행 그룹 ID |
|  | `totalAmount : Long` : 전체 정산 금액 |
|  | `createdAt : Long` : 정산 생성 시간 |
| Methods | `requestSettlement()` : 정산을 요청한다. |
|  | `calculateSettlement()` : 지출 내역을 바탕으로 정산 금액을 계산한다. |
|  | `viewSettlementResult()` : 정산 결과를 조회한다. |


## 8) SettlementDetail
정산 결과의 세부 내역을 관리하는 클래스이다. 누가 누구에게 얼마를 보내야 하는지와 송금 및 수금 상태를 저장한다.

| 구분 | 내용 |
|---|---|
| Attributes | `detailId : String` : 정산 상세 내역 고유 ID |
|  | `settlementId : String` : 연결된 정산 ID |
|  | `senderId : String` : 돈을 보내는 그룹 멤버 ID |
|  | `receiverId : String` : 돈을 받는 그룹 멤버 ID |
|  | `amount : Long` : 송금해야 하는 금액 |
|  | `sendStatus : Boolean` : 송금 완료 여부 |
|  | `receiveStatus : Boolean` : 수금 확인 여부 |
| Methods | `updateSendStatus()` : 송금 상태를 변경한다. |
|  | `updateReceiveStatus()` : 수금 상태를 변경한다. |
|  | `completeSettlement()` : 정산 상세 내역을 완료 처리한다. |

## 9) Invitation
여행 그룹에 사용자를 초대하기 위한 정보를 관리하는 클래스이다. 초대 상태는 InvitationStatus 열거형으로 관리한다.

| 구분 | 내용 |
|---|---|
| Attributes | `invitationId : String` : 초대 고유 ID |
|  | `groupId : String` : 초대가 발생한 여행 그룹 ID |
|  | `receiverEmail : String` : 초대받는 사용자의 이메일 |
|  | `inviteCode : String` : 그룹 초대 코드 |
|  | `status : InvitationStatus` : 그룹 초대 상태 |
| Methods | `sendInvitation()` : 그룹 초대를 전송한다. |
|  | `acceptInvitation()` : 그룹 초대를 수락한다. |
|  | `rejectInvitation()` : 그룹 초대를 거절한다. |


## 10) Notification
사용자에게 초대, 정산 요청, 정산 완료 등의 이벤트를 전달하는 클래스이다. 

| 구분 | 내용 |
|---|---|
| Attributes | `notificationId : String` : 알림 고유 ID |
|  | `userId : String` : 알림을 받는 사용자 ID |
|  | `type : NotificationType` : 알림 종류 |
|  | `message : String` : 알림 내용 |
|  | `createdAt : Long` : 알림 생성 시간 |
| Methods | `sendNotification()` : 사용자에게 알림을 전송한다. |
|  | `viewNotification()` : 알림 내용을 조회한다. |


## 11) InvitationStatus
초대 상태를 제한된 값으로 관리하기 위한 열거형 클래스이다.

| 구분 | 내용 |
|---|---|
| Attributes | `PENDING` : 초대 대기 상태 |
|  | `ACCEPTED` : 초대 수락 상태 |
|  | `REJECTED` : 초대 거절 상태 |
| Methods |  |


## 12) NotificationType
알림의 종류를 제한된 값으로 관리하기 위한 열거형 클래스이다.

| 구분 | 내용 |
|---|---|
| Attributes | `INVITATION` : 그룹 초대 알림 |
|  | `SETTLEMENT_REQUEST` : 정산 요청 알림 |
|  | `SETTLEMENT_COMPLETE` : 정산 완료 알림 |
| Methods |  |

# 3. Sequence diagram
## 1) Sign up 
<img width="706" height="587" alt="image" src="https://github.com/user-attachments/assets/7aaaddd5-4248-4648-90c8-f871ecd9d9fa" />

사용자가 회원가입 화면에서 이메일, 비밀번호, 닉네임, 계좌번호를 입력하는 Sequence Diagram이다. AuthService는 사용자가 입력한 정보를 바탕으로 User 객체 생성을 요청한다. 이미 사용 중인 이메일이 아니라면 회원가입이 완료되고 중복된 이메일이 존재할 경우 오류 메시지를 반환한다.

## 2) Login
<img width="701" height="603" alt="image" src="https://github.com/user-attachments/assets/fffddfc2-7c18-4687-9ece-71f6c9d08e4d" />

사용자가 로그인하는 Use Case에 대한 Sequence Diagram이다. 사용자가 이메일과 비밀번호를 입력하면 AuthService가 User 정보를 확인한다. 입력한 정보가 일치하면 로그인이 성공하고, 일치하지 않으면 오류 메시지를 반환한다.

## 3) Create Travel Group
<img width="684" height="602" alt="image" src="https://github.com/user-attachments/assets/6cc8fb24-ef35-4aac-96bb-5659c6094973" />

사용자가 새로운 여행 그룹을 생성하는 Use Case에 대한 Sequence Diagram이다. 사용자가 그룹 이름을 입력하면 TravelGroup은 그룹을 생성하고 초대 코드를 만든다.  

## 4) Invite Member
<img width="706" height="533" alt="image" src="https://github.com/user-attachments/assets/79b012d0-acf3-4fee-9226-03bc8db8bd3f" />

사용자가 여행 그룹에 다른 사용자를 초대하는 Use Case에 대한 Sequence Diagram이다. 사용자가 초대할 이메일을 입력하면 TravelGroup은 Invitation을 생성한다. 초대가 정상적으로 생성되면 Notification을 통해 초대 알림이 전송된다.

## 5) Join Group
<img width="723" height="560" alt="image" src="https://github.com/user-attachments/assets/5670ba3b-3edd-4930-9119-27bb11637c40" />

초대받은 사용자가 여행 그룹에 참여하는 Use Case에 대한 Sequence Diagram이다. 사용자가 초대 코드를 통해 초대를 수락하면 Invitation은 TravelGroup에 초대 코드의 유효성을 확인한다. 초대가 유효하면 GroupMember가 생성되고, 초대 상태가 ACCEPTED로 변경된다.

## 6) Add Expense
<img width="683" height="600" alt="image" src="https://github.com/user-attachments/assets/da7e9597-c6f8-40a1-a7e4-e5f2a8f4e6e1" />

사용자가 여행 중 발생한 지출 내역을 등록하는 Use Case에 대한 Sequence Diagram이다. 사용자는 결제자를 선택하고 금액, 메모 등의 지출 정보를 입력한다. 영수증 이미지가 있는 경우 ReceiptImage를 통해 이미지를 추가로 업로드한다.

## 7) Edit or Delete Expense
<img width="696" height="586" alt="image" src="https://github.com/user-attachments/assets/e8b6f670-658c-47ec-97a6-8a9f05b8c430" />

사용자가 등록된 지출 내역을 수정하거나 삭제하는 Use Case에 대한 Sequence Diagram이다. 사용자는 먼저 지출 내역을 조회한 뒤 금액이나 메모를 수정할 수 있다. 지출 내역을 삭제하는 경우 연결된 영수증 이미지도 함께 삭제된다.

## 8) Settlement Calculation
<img width="1448" height="1086" alt="settlementCalculation" src="https://github.com/user-attachments/assets/b1204bec-b779-4e9a-b386-8723a48970cd" />

사용자가 여행 그룹의 정산을 요청하는 Use Case에 대한 Sequence Diagram이다. Settlement는 TravelGroup에서 그룹 멤버 목록을 가져오고 Expense에서 지출 내역을 가져온다. 이후 정산 금액을 계산하고 누가 누구에게 얼마를 보내야 하는지 SettlementDetail을 생성한다.

## 9) Update Settlement status
<img width="1313" height="1198" alt="update" src="https://github.com/user-attachments/assets/af024872-39f5-4303-ba15-4f60d551e4e9" />

사용자가 정산 송금 및 수금 상태를 변경하는 Use Case에 대한 Sequence Diagram이다. 송금자가 송금을 완료하면 sendStatus가 변경되고 정산을 받는 자가 확인하면 receiveStatus가 변경된다.  
두 상태가 모두 완료되면 정산이 완료 처리되고 알림이 전송된다.

## 10) Delete Travel Group
<img width="713" height="592" alt="image" src="https://github.com/user-attachments/assets/db816862-0370-447b-9adc-5f44f9d2150a" />

사용자가 여행 그룹을 삭제하는 Use Case에 대한 Sequence Diagram이다. 여행 그룹 삭제는 그룹 생성자만 수행할 수 있다. 삭제가 가능할 경우 TravelGroup에 종속된 GroupMember, Expense, Settlement, Invitation 정보가 함께 삭제된다.

# 4. State machine diagram
<img width="309" height="577" alt="image" src="https://github.com/user-attachments/assets/017d3db5-715e-4703-a520-f81e940fcab1" />

State Machine Diagram은 Settlement 객체의 생명주기를 기준으로 작성한 것이다. 시스템에서 사용자가 정산을 요청하면 Requested 상태가 된다. 이후 정산 계산이 시작되면 Calculating 상태로 이동하고 그룹 멤버와 지출 내역을 바탕으로 정산 계산이 성공하면 ResultGenerated 상태가 된다. 정산 결과가 생성되면 각 멤버가 송금하거나 수금 확인을 해야 하므로 PendingTransfer 상태로 이동한다. 모든 송금 및 수금 확인이 완료되면 Completed 상태가 되고 최종 상태로 종료된다. 정산 요청, 계산, 송금 과정에서 오류가 발생하면 Failed 상태로 이동한다. 이 경우 사용자는 문제를 해결한 뒤 다시 정산을 요청할 수 있다. 정산 완료 전 사용자가 정산을 취소하면 Cancelled 상태로 이동하며 이후 시스템은 종료 상태로 이동한다.

# 5. Implementation requirements
안드로이드 환경에서만 작동이 가능하며, Android Studio 2021.2.1 이상, Android 6.0(SDK 23) 이상 시스템 구동이 가능하다.

# 6. Glossary
| TERMS | Description |
|---------|-------------|
| Class Diagram | 시스템의 논리 설계를 위한 클래스를 존재와 그들의 관계를 도식으로 정의한 것으로 단일 클래스 다이어그램은 시스템 클래스 구조를 보여줌. |
| State Machine Diagram | 시스템의 동작을 설명하는 상태 다이어그램의 유형으로 설명된 시스템이 한정된 수의 상태로 구성됨. |

# 7. References
