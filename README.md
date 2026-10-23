# RUN & GUN | 박건후 게임 프로그래밍 포트폴리오

**고속 이동과 성장 선택을 결합한 Unreal Engine 5 C++ 아레나 FPS**

이 저장소는 **박건후의 개인 기여를 소개하는 포트폴리오**입니다. 게임은 5인 팀 공동 결과물이며, 원본 소스는 [팀 저장소](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project)에서 확인할 수 있습니다.

대시·슬라이딩·이단 점프·벽달리기·그래플을 이어 가며 적과 싸우고, 전투 중 얻은 강화로 다음 구역의 전투를 바꾸는 5인 팀 프로젝트입니다.

| 항목 | 내용 |
| --- | --- |
| 개발 엔진 | Unreal Engine 5.6 (`UnrealCH3.uproject` 기준) |
| 구현 | C++, Blueprint, UMG, Enhanced Input, Behavior Tree / Blackboard, DataTable |
| 팀 규모 | 5명 |
| 개인 기여 | 박건후 — 기획·기능명세, 순환형 레벨 디자인, UI 구조 및 전투 HUD, 목표 추적 UI, 적 스폰 시스템·데이터 구성 |

**담당:** 기획·기능명세 / 순환형 레벨 디자인 / UIManager·HUDController / Delegate 기반 UI / Blueprint 목표 추적 / 스폰 시스템·DataTable

<!-- 대표 플레이 영상: 실제 업로드 URL을 확인한 뒤 이 위치에 추가. 이동 연계 → 전투 → 강화 선택 → 다음 구역 순서의 60~90초 영상을 권장. -->

## 핵심 게임 루프

**구역 진입 → 이동·전투 → 목표 달성 → 포털 진입 → 핵심 강화 선택 → 다음 구역**

- 전투 중에는 적 처치로 경험치를 얻고, 레벨업 시 일반 강화를 선택합니다.
- 일반 구역에서는 설정된 목표 처치 수와 데이터 코어 파괴 수를 충족하면 구역을 완료합니다. 처치·코어 목표는 스테이지 설정에 따라 적용됩니다.
- 구역 사이에서 핵심 강화를 선택하고 성장 상태를 이어 갑니다. 보스 구역은 보스 처치를 완료 조건으로 사용합니다.

완료 판정은 [RGGameModeBase.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Gamemode/RGGameModeBase.cpp)의 `CheckStageClearCondition()`, 포털 연동은 [RGStagePortal.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Gamemode/RGStagePortal.cpp), 성장 상태 관리는 [RGProgressionSubsystem.h](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Gamemode/RGProgressionSubsystem.h)에서 확인할 수 있습니다.

## 개인 기여 — 박건후

### 1. 기획·기능명세와 순환형 레벨 디자인

이동·전투·성장이 이어지는 플레이 흐름을 기획하고 기능명세를 정리했습니다. 고속 이동을 활용해 전투 공간을 반복 순회할 수 있는 순환형 동선을 설계하고 레벨을 제작했습니다.

관련 맵: [블록아웃](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Content/Maps/Map01/L_Map01_Blockout.umap), [게임플레이 맵](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Content/Maps/Map01/L_Map01_GamePlay.umap), [두 번째 게임플레이 맵](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Content/Maps/Map01/L_Map02_GamePlay02.umap), [보스 맵](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Content/Maps/Map01/L_Map01_BoosMap.umap).

<!-- 이 위치에 기능명세서의 공개 가능한 실제 링크, 동선에 화살표를 표시한 탑뷰, 이동 연계 GIF 추가. 기획 문서 링크와 동선 이미지는 아직 미등록. -->

### 2. UIManager / HUDController / Widget의 역할 분리

- **UIManager:** HUD 생성·제거, 화면 열기·닫기, 레이어와 입력 모드를 관리합니다. 같은 클래스의 활성 View를 재사용하고, 메뉴와 강화 선택 화면의 동시 열기를 제한합니다.
- **HUDController:** 장착 무기의 상태·이벤트를 받아 UI에 전달합니다. 무기 교체 시 이전 구독과 타이머를 정리한 뒤 새 무기에 연결합니다.
- **Widget:** 전달받은 값으로 텍스트·크로스헤어·차지 등의 화면 표현을 갱신합니다.

관련 소스: [UIManager.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/UI/UIManager.cpp), [HUDController.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/UI/Controller/HUDController.cpp), [HUDWidget.h](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/UI/HUDWidget.h), [UITypes.h](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/UI/UITypes.h).

### 3. Delegate 기반 전투 UI

탄약 변경은 다음 경로로 전달됩니다.

```text
RGBaseWeapon::OnAmmoChanged
  → HUDController::HandleWeaponAmmoChanged
    → WeaponInfoWidget::ApplyWeaponInfo
      → 탄약 텍스트 갱신
```

`BindWeapon()`에서 `AddUniqueDynamic()`으로 탄약·재장전·발사·명중 피드백을 구독하고, `UnbindWeapon()`에서 `RemoveDynamic()`으로 해제합니다. 위젯이 무기 상태를 직접 계속 조회하는 대신 이벤트 수신을 통해 표시를 갱신하도록 역할을 나눴습니다.

연속적으로 변하는 재장전·레일건 차지·퀵슬롯 표시는 타이머를 함께 사용합니다. 따라서 모든 UI를 이벤트만으로 갱신하는 구조는 아닙니다.

관련 소스: [무기 이벤트 발행](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Private/RGBaseWeapon.cpp), [이벤트 연결·해제와 타이머](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/UI/Controller/HUDController.cpp), [탄약 표시](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/UI/View/Combat/WeaponInfoWidget.cpp).

### 4. Blueprint 벡터 기반 목표 추적 월드 UI

플레이어와 목표의 상대 위치·방향 벡터를 활용해 목표 방향을 안내하는 UI를 Blueprint로 구현했습니다.

관련 에셋: [WBP_CoreObjectiveMarker](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Content/UI/View/Combat/WBP_CoreObjectiveMarker.uasset), [BP_CombatUIManager](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Content/UI/System/BP_CombatUIManager.uasset).

> 이 항목의 구현 방식은 개인 기여 설명 기준입니다. 저장소에서 관련 에셋의 존재는 확인했으며, 바이너리 Blueprint 내부의 벡터 계산·화면 투영·화면 밖 처리 방식은 에디터 그래프 확인이 필요합니다.

<!-- 이 위치에 실제 Blueprint 그래프 캡처와 목표 방향이 바뀌는 플레이 GIF 추가. 확인 전에는 특정 벡터 수식이나 화면 밖 표시 알고리즘을 단정하지 않습니다. -->

### 5. SpawnManager / SpawnPoint와 DataTable

적 생성 규칙과 레벨 배치 지점의 책임을 분리했습니다.

- **EnemySpawnManager:** `StageId`로 스폰 예산 행을 조회하고, 초기 구성과 타이머 기반 보충 생성을 처리합니다. 동시 생존 수, 누적 생성 예산, 누적 엘리트 생성 상한을 구분하며 보충 후보는 가중치로 선택합니다.
- **EnemySpawnPoint:** 배치 위치·방향, 사용 가능 여부, 플레이어와의 최소 거리를 관리합니다. 적 클래스 기본 객체의 캡슐 반높이와 지면 여유 높이를 생성 위치에 반영합니다.
- **생성 실패 대응:** 거리 조건을 통과한 포인트들을 섞어 생성하며, `DontSpawnIfColliding`으로 실패한 경우 다음 후보에서 시도합니다. 생성 성공 후 사망·파괴 이벤트를 연결해 활성 적 목록을 갱신합니다.
- **데이터 구성:** `FEnemySpawnBudgetRow`에 초기 구성·보충 후보·간격·예산을 정의해 스테이지별 규칙을 코드와 분리했습니다. 엘리트 플래그는 이 시스템에서 생성 예산 분류에 사용하며, 그 자체가 능력치 강화를 적용한다는 뜻은 아닙니다.

관련 소스: [EnemySpawnManager.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Enemy/EnemySpawnManager.cpp), [EnemySpawnPoint.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Enemy/EnemySpawnPoint.cpp), [RGEnemySpawnData.h](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Enemy/DataTableStruct/RGEnemySpawnData.h), [DT_SpawnBudget](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Content/Enemy/DataTable/DT_SpawnBudget.uasset).

팀 전체에서도 무기·성장·진행 설정에 DataTable을 사용합니다. [무기 스탯](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Public/RGWeaponStats.h), [진행 설정 행](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Gamemode/DataTableStruct/RGRunConfigRows.h), [성장 테이블 조회](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Gamemode/RGProgressionSubsystem.cpp)는 관련 팀 구현이며, 모두 개인 단독 기여로 주장하지 않습니다.

## 팀 전체 구현 기능

아래는 팀 공동 결과물입니다. 개인 담당 범위는 위 개인 기여 절과 구분해 읽어 주세요.

| 영역 | 주요 기능 | 관련 소스 |
| --- | --- | --- |
| 플레이어·이동 | 대시, 슬라이딩, 이단 점프, 벽달리기, 그래플 | [RGCharacter.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Player/RGCharacter.cpp), [RGWallRunMovement.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Component/RGWallRunMovement.cpp), [RGGrappleComponent.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Component/RGGrappleComponent.cpp) |
| 무기·전투 | 돌격소총 연사, 산탄총 분산 사격, 레일건 충전·관통, 수류탄 | [RGBaseWeapon.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Private/RGBaseWeapon.cpp), [RGAssaultRifle.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Private/RGAssaultRifle.cpp), [RGShotgun.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Private/RGShotgun.cpp), [RGRailgun.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Private/RGRailgun.cpp), [RGGrenade.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Private/RGGrenade.cpp) |
| 적 AI·보스 | 근접·원거리 적, 감지와 행동 트리, 보스 패턴·페이즈 | [AIEnemyController.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Enemy/AIEnemyController.cpp), [PursuerEnemy.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Enemy/PursuerEnemy.cpp), [ShooterEnemy.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Enemy/ShooterEnemy.cpp), [BossEnemy.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Enemy/BossEnemy.cpp) |
| 진행·성장 | 목표 판정, 포털, 일반·핵심 강화, 맵 전환 간 성장 상태 유지 | [RGGameModeBase.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Gamemode/RGGameModeBase.cpp), [RGProgressionSubsystem.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Gamemode/RGProgressionSubsystem.cpp) |
| UI·레벨·스폰 | 전투 HUD, 메뉴·선택·결과 화면, 목표 안내, 전투 공간, 적 생성 규칙 | [UIManager.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/UI/UIManager.cpp), [EnemySpawnManager.cpp](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/blob/f41a5e97cee47919746435c5e07a359403d65a8f/Source/UnrealCH3/Enemy/EnemySpawnManager.cpp), [맵](https://github.com/NBcampUnrealTrack/10th-Team4-CH3-Project/tree/f41a5e97cee47919746435c5e07a359403d65a8f/Content/Maps/Map01) |

## 5인 팀 역할

발표 자료의 담당 구분을 기준으로 작성했습니다. 아래 구분은 주 담당 영역이며 파일별 단독 저작권이나 전체 코드의 단독 구현을 의미하지 않습니다.

| 팀원 | 주 담당 |
| --- | --- |
| 김규동 | 게임모드 |
| 최용준 | 무기 3종, 핵심 강화, 레벨업 강화 |
| 조수현 | 적 AI 2종, 비헤이비어 트리 |
| 백동준 | 플레이어 캐릭터, 이동 기능, 보스 |
| 박건후 | 기획, UI, 레벨 디자인 — 세부 기여는 위 개인 기여 절 참조 |

## 실행 준비

1. Unreal Engine **5.6**과 Unreal C++ 개발 환경을 준비합니다.
2. Git LFS가 설치된 환경에서 위 원본 팀 저장소를 Clone하고 LFS 에셋을 내려받습니다. `.uasset`, `.umap` 등은 LFS 관리 대상입니다.
3. `UnrealCH3.uproject`의 프로젝트 파일을 생성하고 `UnrealCH3Editor`를 빌드한 뒤 에디터에서 엽니다.
4. 기본 시작 맵은 `Config/DefaultEngine.ini` 기준 `Content/Maps/Map01/L_Map01_Blockout.umap`입니다.

이 포트폴리오 저장소에는 게임 소스·실행 파일이 포함되어 있지 않습니다. 실행 준비는 원본 팀 저장소 기준이며, 이 문서 작성 과정에서 별도의 빌드·플레이 실행 검증은 수행하지 않았습니다.

## 시연·문서 보강 위치

| 자료 | 권장 위치·내용 | 현재 상태 |
| --- | --- | --- |
| 대표 플레이 영상 | 소개 바로 아래, 이동·전투·강화·보스 흐름 | 링크 미등록 |
| 개인 UI 시연 | 개인 기여 2~4절, 탄약·무기 교체·명중·목표 추적 | 미등록 |
| 레벨 디자인 | 개인 기여 1절, 동선 탑뷰와 이동 GIF | 미등록 |
| 기획·기능명세서 | 개인 기여 1절, 공개 가능한 문서 | 링크 미등록 |
| 발표 자료 | 이 절, 공개용 PDF 업로드 후 실제 경로 연결 | 저장소 링크 미등록 |

### 문서 검증 범위

소스·경로 검토 기준 커밋: `f41a5e97cee47919746435c5e07a359403d65a8f`.
팀 역할은 프로젝트 발표 자료, 개인 기획·레벨·Blueprint 기여 범위는 담당자 설명을 기준으로 정리했습니다. C++ 구조와 경로는 저장소에서 확인했으며 Blueprint 그래프·맵 내부 배치·DataTable 실제 행 값은 에디터 검증 범위로 남겨 두었습니다.
