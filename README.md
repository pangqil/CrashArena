# Crash Arena

**맵에 떨어진 무기와 아이템을 주워 싸우는 탑다운 멀티플레이 팀 격투 게임**

[![Play Video](https://img.shields.io/badge/YouTube-플레이_영상-red?logo=youtube)](https://youtu.be/DT0-oWnLAxY)
![UE](https://img.shields.io/badge/Unreal_Engine-5.7-black?logo=unrealengine)
![C++](https://img.shields.io/badge/C++-GAS-blue?logo=cplusplus)

| 항목 | 내용 |
| --- | --- |
| 장르 | 탑다운 멀티플레이 팀 격투 (라운드제) |
| 기간 | 2026.05.29 ~ 2026.07.20 |
| 인원 | 6명 |
| 엔진·기술 | Unreal Engine 5.7, C++, Gameplay Ability System, Enhanced Input, EOS Online Subsystem |

---

## 🎮 핵심 기능

| 기능 | 내용 |
| --- | --- |
| 라운드제 팀전 | 상대 팀 전멸 시 라운드 승리, 라운드 카운트다운·전환·결과 UI, 매치 타이머와 스코어, 사망 후 관전 |
| GAS 전투 | 검·도끼·창·맨손별 약·강공격 조합 콤보, 지상·대시·공중 콤보, 락온, 패링, 경직·넉백·다운, 달리기 |
| 무기·아이템 | 줍기·장착·교체·투척을 하나의 아이템 생명주기로 통합, 포션·변신 포션·폭탄, 캐릭터 종류별 장착 제한, 아이템 스폰 규칙 |
| 맵 기믹 | 원형 경기장·시티 맵, 시간이 지나면 무너지는 벽과 바닥, 가시 함정, 이동 발판, 맵을 가로지르는 열차, 낙사 구역 |
| 로비·세션 | EOS 기반 방 만들기·찾기·참가, 로비 레디 상태와 방장 게임 시작, 닉네임 설정 |
| 피드백 & UI | 플로팅 대미지, 카메라 셰이크, 킬 알림, 초상화·HP가 보이는 플레이어 박스, 채팅, 배경음·UI 사운드 |

## 👥 팀 소개

| 이름 | 담당 |
| --- | --- |
| **조재혁 (팀장)** | GAS 전투 기반, 콤보·피격·다운·패링·락온 어빌리티, 무기·아이템 장착 구조 통합, 폭탄·열차 기믹 |
| 문창욱 | 인게임 UI 전반(플레이어 박스·킬 알림·라운드 전환·결과창), 채팅, 라운드 흐름과 매치 타이머, 콜로세움 케이지 |
| 홍성돈 | 검·맨손 콤보 몽타주와 대시·점프 공격, 무기 트레일 VFX, 무너지는 벽 기믹, 아이템 스폰 규칙, 관전 시스템 |
| 이현준 | 캐릭터 ABP·애니메이션, 도끼·창·목검 콤보 데이터, 가시 함정·이동 발판, 플로팅 대미지 |
| 이재호 | 줍기·사용·투척 아이템 시스템, 장비·소모품 분리, 고릴라 변신 포션, 도끼·창 콤보 |
| 전지원 | EOS 세션과 로비(방 목록·레디·방장 시작), 메뉴·닉네임 UI, 시티 맵, 배경음·UI 사운드, 락온 UI |

## 🙋 내 담당 상세

### 1. GAS 기반 약·강공격 조합 콤보

- Enhanced Input 입력을 GameplayTag로 변환, 대기 중인 공격 어빌리티가 태그를 받아 실행
- 입력 순서(예: `B → S → S`)를 `ComboData` DataAsset의 콤보 목록과 비교해 해당 몽타주 섹션으로 전환
- C++ 수정 없이 DataAsset만으로 무기별 콤보 추가·조정
- 입력을 시점과 함께 버퍼에 저장, ComboWindow가 열리면 먼저 들어온 입력부터 처리. 수명(`InputBufferLifetime`)이 지난 입력은 폐기하여 공격 모션 중 선입력도 순서대로 반영

### 2. 피격·패링

- HitStun·Knockback·Knockdown을 GAS 태그와 HitReact 어빌리티로 처리, 경직과 연출을 데미지와 같은 파이프라인에 통합
- 패링 성공 시 공격자에게 역경직 적용
- 데미지 흐름: `DamageParams → GameplayEffect → ExecCalc → AttributeSet → UI`

### 3. 무기·아이템 통합 구조

- 줍기·장착·교체·드랍·투척·사용을 하나의 상위 아이템 구조와 생명주기로 통합
- Throw / Pickup / Holder 역할 분리, 새 아이템은 같은 생명주기만 따르면 추가 가능
- 아이템 상태는 서버만 결정하고 클라이언트는 복제 결과만 표시하도록 정리, 클라이언트별 무기 위치 불일치 문제 해결

## 📂 코드 둘러보기

내 담당 부분의 주요 파일 (`Source/ProjectG/` 기준, `.h`는 `Public/`, `.cpp`는 `Private/`)

| 주제 | 파일 |
| --- | --- |
| 콤보 어빌리티·입력 버퍼 | `AbilitySystem/Abilities/GProjectAttackComboAbility` |
| 콤보 데이터 | `AbilitySystem/Combo/GProjectComboData`, `GProjectComboTypes` |
| 콤보·패링 입력 구간 | `Animation/GProjectComboWindowNotifyState`, `GProjectParryWindowNotifyState` |
| 공격 판정 | `Animation/GProjectAttackTraceNotifyState` |
| 피격·다운·패링 | `AbilitySystem/Abilities/GProjectHitReactAbility`, `GProjectKnockdownAbility`, `GProjectParryAbility` |
| 데미지 계산 | `AbilitySystem/ExecCalc/GProjectExecCalc_Damage`, `AbilitySystem/GProjectAttributeSet` |
| 아이템 구조 | `Item/GProjectItemActorBase`, `Item/GProjectItemDefinition`, `Item/GProjectItemHolderComponent` |
| 무기·투척 | `Item/Weapon/GProjectWeaponItemActor`, `Item/Weapon/GProjectThrownWeapon`, `AbilitySystem/Abilities/GProjectThrowWeaponAbility` |
| 줍기·사용 | `AbilitySystem/Abilities/GProjectPickupAbility`, `GProjectUseItemAbility` |

### 전체 폴더 구조

```text
Source/ProjectG
├─ AbilitySystem   GAS: ASC, AttributeSet, 어빌리티, 콤보 데이터, 데미지 ExecCalc
├─ Animation       콤보·패링 구간, 공격 판정, 줍기 노티파이
├─ Character       플레이어 캐릭터와 외형 변형
├─ Game            라운드·팀 승패를 관리하는 GameMode/GameState, 로비
├─ Item            아이템 베이스, 무기, 소비 아이템, 스폰
├─ Actor           함정, 이동 발판, 파괴 벽, 열차 등 맵 기믹
├─ Targeting       락온
├─ Subsystem       세션(EOS), 플레이어 정보
├─ Input           Enhanced Input → GameplayTag 매핑
└─ UI              HUD, 위젯 컨트롤러, 채팅·킬피드·라운드 UI, 로비 UI
```

## 📄 기타 문서

- [팀 협업 가이드](COLLABORATION_GUIDE.md) · 브랜치·커밋·LFS Lock 규칙
