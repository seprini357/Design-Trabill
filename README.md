# 1. Introduction
 최근 여행 인구 증가로 인해 교통비, 숙박비, 식비 등 다양한 여행 경비를 여러 사람이 함께 부담하는 경우가 많아지고 있다. 그러나 지출 항목마다 참여 인원이 다르고 결제 방식이 달라 비용 정산 과정이 복잡해지며 수기로 관리할 경우 정산 누락이나 계산 오류가 발생할 수 있다. 이를 효율적으로 해결하기 위해 여행 중 발생하는 지출 내역을 기록하고 자동으로 정산 결과를 제공하는 여행 경비 관리 어플리케이션 Trabill을 개발한다.
 본 문서는 Analysis 단계에서 정의된 요구사항을 기반으로 Design 단계의 문서이다. Class Diagram, Sequence Diagram, State Mach
 ine Diagram을 통해 각 Diagram과 시스템의 구조와 동작과정에 대해 설명한다. Implementation requirements를 기술하여 본 시스템을 구현에 관여하는 모든 요소를 구체적으로 디자인하는 내용을 다룬다. 

# Class diagram
## 1) User
사용자의 기본 정보를 관리하는 클래스이다.

| 구분 | 내용 |
|---|---|
| Attributes | +userId : String : 사용자의 고유 아이디 |
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
| Methods | |
|  | +login(email : String, password : String) : 이메일과 비밀번호를 입력하여 로그인을 수행한다. |
|  | +logout(userId : String) : 사용자가 로그아웃을 한다. |
|  | +validateUser(userId : String) : 사용자 인증 정보가 유효한지 확인한다. |

## 3) TravelGroup

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
| Others | 여행 정산 기능의 중심이 되는 클래스이다. 하나의 여행 그룹은 여러 명의 그룹 멤버, 지출 내역, 정산 내역, 초대 정보를 포함한다. |

## 4) GroupMember

| 구분 | 내용 |
|---|---|
| Attributes | `memberId : String` : 그룹 멤버 고유 ID |
|  | `userId : String` : 해당 멤버와 연결된 사용자 ID |
|  | `groupId : String` : 멤버가 속한 여행 그룹 ID |
|  | `nickname : String` : 그룹 내에서 사용하는 닉네임 |
|  | `accountNumber : String` : 정산에 사용할 계좌번호 |
| Methods | `joinGroup()` : 여행 그룹에 참여한다. |
|  | `leaveGroup()` : 여행 그룹에서 탈퇴한다. |
| Others | 특정 여행 그룹에 참여한 사용자를 나타내는 클래스이다. 하나의 사용자는 여러 여행 그룹에 참여할 수 있으므로 `User`와 분리하여 관리한다. |

## 5) Expense

| 구분 | 내용 |
|---|---|
| Attributes | `expenseId : String` : 지출 내역 고유 ID |
|  | `groupId : String` : 지출이 발생한 여행 그룹 ID |
|  | `payerId : String` : 결제를 수행한 그룹 멤버 ID |
|  | `amount : Long` : 지출 금액 |
|  | `memo : String` : 지출 내역에 대한 메모 |
|  | `createdAt : Long` : 지출 내역 생성 시간 |
| Methods | `addExpense()` : 지출 내역을 추가한다. |
|  | `editExpense()` : 지출 내역을 수정한다. |
|  | `deleteExpense()` : 지출 내역을 삭제한다. |
|  | `viewExpense()` : 지출 내역을 조회한다. |
| Others | 여행 중 발생한 지출 정보를 관리하는 클래스이다. 지출 내역은 특정 여행 그룹에 속하며, 결제자는 `GroupMember`와 연결된다. |

## 6) ReceiptImage

| 구분 | 내용 |
|---|---|
| Attributes | `imageId : String` : 영수증 이미지 고유 ID |
|  | `expenseId : String` : 연결된 지출 내역 ID |
|  | `imageUrl : String` : 영수증 이미지 저장 경로 |
| Methods | `uploadImage()` : 영수증 이미지를 업로드한다. |
|  | `deleteImage()` : 영수증 이미지를 삭제한다. |
| Others | 특정 지출 내역에 첨부되는 영수증 이미지를 관리하는 클래스이다. `Expense`에 종속되는 객체이다. |

## 7) Settlement

| 구분 | 내용 |
|---|---|
| Attributes | `settlementId : String` : 정산 고유 ID |
|  | `groupId : String` : 정산이 진행되는 여행 그룹 ID |
|  | `totalAmount : Long` : 전체 정산 금액 |
|  | `createdAt : Long` : 정산 생성 시간 |
| Methods | `requestSettlement()` : 정산을 요청한다. |
|  | `calculateSettlement()` : 지출 내역을 바탕으로 정산 금액을 계산한다. |
|  | `viewSettlementResult()` : 정산 결과를 조회한다. |
| Others | 여행 그룹의 전체 지출을 기준으로 정산 결과를 생성하는 클래스이다. 하나의 정산은 여러 개의 정산 상세 내역을 가진다. |

## 8) SettlementDetail

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
| Others | 정산 결과의 세부 내역을 관리하는 클래스이다. 누가 누구에게 얼마를 보내야 하는지와 송금 및 수금 상태를 저장한다. |

## 9) Invitation

| 구분 | 내용 |
|---|---|
| Attributes | `invitationId : String` : 초대 고유 ID |
|  | `groupId : String` : 초대가 발생한 여행 그룹 ID |
|  | `receiverEmail : String` : 초대받는 사용자의 이메일 |
|  | `inviteCode : String` : 그룹 초대 코드 |
|  | `status : InvitationStatus` : 초대 상태 |
| Methods | `sendInvitation()` : 초대를 전송한다. |
|  | `acceptInvitation()` : 초대를 수락한다. |
|  | `rejectInvitation()` : 초대를 거절한다. |
| Others | 여행 그룹에 사용자를 초대하기 위한 정보를 관리하는 클래스이다. 초대 상태는 `InvitationStatus` 열거형으로 관리한다. |

## 10) Notification

| 구분 | 내용 |
|---|---|
| Attributes | `notificationId : String` : 알림 고유 ID |
|  | `userId : String` : 알림을 받는 사용자 ID |
|  | `type : NotificationType` : 알림 종류 |
|  | `message : String` : 알림 내용 |
|  | `createdAt : Long` : 알림 생성 시간 |
| Methods | `sendNotification()` : 사용자에게 알림을 전송한다. |
|  | `viewNotification()` : 알림 내용을 조회한다. |
| Others | 사용자에게 초대, 정산 요청, 정산 완료 등의 이벤트를 전달하는 클래스이다. 알림 종류는 `NotificationType` 열거형으로 관리한다. |

## 11) InvitationStatus

| 구분 | 내용 |
|---|---|
| Attributes | `PENDING` : 초대 대기 상태 |
|  | `ACCEPTED` : 초대 수락 상태 |
|  | `REJECTED` : 초대 거절 상태 |
| Methods | 없음 |
| Others | 초대 상태를 제한된 값으로 관리하기 위한 열거형 클래스이다. |

## 12) NotificationType

| 구분 | 내용 |
|---|---|
| Attributes | `INVITATION` : 그룹 초대 알림 |
|  | `SETTLEMENT_REQUEST` : 정산 요청 알림 |
|  | `SETTLEMENT_COMPLETE` : 정산 완료 알림 |
| Methods | 없음 |
| Others | 알림의 종류를 제한된 값으로 관리하기 위한 열거형 클래스이다. |
