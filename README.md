역사의 격전지 (2D 횡스크롤 자동 전투 디펜스)

브라우저에서 즐기는 2D 자동 전투 전략 디펜스 게임.
Canvas 기반 렌더링, 생산 큐/타워/스킬/시대(중세→근대→미래) 진화를 지원합니다.

Tech: HTML, CSS, JavaScript (Vanilla)
Run: 로컬 파일 직접 열기 또는 간단한 정적 서버 / GitHub Pages 배포

✨ 주요 기능

유닛 시스템: 근접/원거리/엘리트(+ 미래: 스페셜) 자동 이동·공격, 체력바 표시

생산 큐(예약): 생산 시간/쿨다운·뱃지 표시·남은 시간 HUD 툴팁

타워 시스템: 설치/업그레이드/해체(70% 환급), 플레이어·적 양측 운용

스킬(시대별 2개)

중세: Iron Wall(임시 장벽), Med Evac(힐)

근대: Rapid Reinforce(즉시 증원), Overwatch(타워 버프)

미래: Time Warp(슬로우), Era Blast(전장 광역 폭딜)

시대 진화: 경험치 달성 시 중세→근대→미래, 배경/기지 체력/스킬 세트 교체

난이도: EASY/NORMAL/HARD 적 웨이브 간격 조정

UI/HUD: 기지 체력 진행바, 골드·경험치·생산큐 표시, 버튼 쿨다운 오버레이/뱃지

오디오: 시대/타워/유닛 공격 효과음

🧩 파일 구조 (권장)
project-root/
├─ index.html                # (올려준 코드)
├─ img/                      # ⟵ 게임 에셋 폴더 (추가 작업)
│  ├─ medieval/
│  │  ├─ player/
│  │  │  └─ melee/ ranged/ elite/
│  │  └─ enemy/
│  │     └─ melee/ ranged/ elite/
│  ├─ modern/
│  │  ├─ player/...          # Attack.png, Dead.png, Idle.png, Walk.png
│  │  └─ enemy/...
│  └─ future/
│     ├─ player/...          # + special/
│     └─ enemy/...
└─ README.md


에셋 로더 규칙 (코드에 맞춤)

medieval(중세): 유닛 1종 = img/medieval/{team}/{type}/{type}_sheet.png

modern / future: 유닛 1종 = Attack.png, Dead.png, Idle.png, Walk.png

팀 폴더명: player, enemy

타입: melee, ranged, elite (+ future/special)

예) img/modern/player/ranged/Walk.png

🚀 실행 방법
1) 그냥 더블클릭

index.html 파일을 브라우저로 열기

일부 브라우저는 로컬 파일 보안 제약으로 오디오 자동재생이 제한될 수 있음(클릭 시 활성)

2) 간단 서버로 실행(권장)
# 파이썬
python -m http.server 5500
# 또는 Node
npx serve .


http://localhost:5500 접속

3) GitHub Pages 배포

이 저장소를 public으로 생성 → Settings → Pages → Branch: main (root) 선택

🎮 조작 방법

메뉴: 플레이 → 난이도 선택(EASY/NORMAL/HARD)

유닛 생산: 근접/원거리/엘리트(+미래 스페셜) 버튼

누르면 골드 차감 후 생산 큐에 예약

버튼 오른쪽 뱃지: 큐 내 해당 타입 개수

타워: 설치(시대별 비용/성능) → 업그레이드(2단계, 비용 가변) → 해체

해체: 버튼으로 “마지막 설치 타워” 해체 또는 캔버스에서 타워 클릭 → 확인

스킬: 시대별 2개, 골드 비용 + 쿨다운 표시/오버레이

승패: 적 기지 체력 0 → 승리 / 내 기지 체력 0 → 패배

🧠 시스템/아키텍처 개요
게임 루프

requestAnimationFrame(gameLoop) 로 프레임 진행

단계:

스킬 효과 만료/생산 큐 시간 감소 및 유닛 생성

캔버스 초기화 및 기지/타워/유닛 렌더링

타임워프 적용 이동 배율 → 이동/충돌/공격 처리

전투 결과 반영(골드/경험치/시대업)

HUD 업데이트(체력/자원/큐 툴팁)

핵심 객체

Unit: x, y, team, type, hp/maxHp, speed, damage, range, cooldown, baseAttackCooldown

Tower: x, range, damage, era, level(0~2), cooldown, team, costSpent, color

skillEffects[]: { type, ... , expireAt, ttl } 를 프레임마다 갱신

주요 상수/테이블

unitTypes : 시대/타입별 스탯(HP/Speed/Damage/Cost/Range)

towerStats : 시대별 타워 사거리/공격력/비용/색상

expTable, goldRewardTable

eraUpgradeExp, baseHpTable, eraBackgrounds

productionTime : 시대·타입별 생산 시간(초)

SLOW_FACTOR : Time Warp 이동·공격 배율

🖼️ 에셋 추가 가이드 (업데이트 계획)
1) 유닛 스프라이트 규칙

medieval: 단일 시트({type}_sheet.png)

예: img/medieval/player/melee/melee_sheet.png

시트 내 셀 규격을 정하고(예: 64×64) 코드에서 프레임 인덱싱 로직을 추가 예정

modern / future: 동작별 PNG

필수 파일: Attack.png, Dead.png, Idle.png, Walk.png

모든 PNG는 동일 프레임 크기(예: 64×64)와 동일 프레임 수를 권장

2) 타워 색상/레벨 컬러

코드에서 시대별 타워 레벨에 따라 색상 자동 변경

towerColors = { medieval: ['saddlebrown','peru','sienna'], ... }

타워를 스프라이트 이미지로 바꾸려면:

Tower.attack()의 직사각형 그리기 대신 ctx.drawImage(...) 로 교체

레벨/시대에 따른 이미지 테이블 추가

3) 경로/명명법 체크리스트

img/{era}/{team}/{type}/... 루트를 반드시 맞춰야 함

special 타입은 future 시대에만 존재

파일 대소문자 민감(서버 OS에 따라) → Attack.png처럼 대문자 정확히

🔧 설정/튜닝 포인트

난이도: setDifficulty(level) 내 spawn interval 조정

시대 업그레이드 경험치: eraUpgradeExp = { medieval: 500, modern: 1000 }

기지 체력: baseHpTable = { medieval:500, modern:700, future:900 }

타워 업그레이드 비용:
baseCost * 1.5(Lv0→1), baseCost * 2.5(Lv1→2)

스킬 지속/효과: 각 activate*() 내 duration, multiplier, extraRange 등

🧪 Known Issues / 개선 포인트

충돌/거리 판정이 단순(축 기준 거리 비교) → 원형/박스 충돌로 개선 여지

애니메이션: 현재는 색상 블록 중심 → 스프라이트 프레임 기반으로 확장 예정

오디오 자동재생: 브라우저 정책으로 최초 사용자 입력 후 재생됨

반응형 레이아웃: 현재 1900×800 기준 → 캔버스 리사이즈 & 스케일링 유틸 필요

에셋 미로딩 처리: 이미지 지연 로딩 시 플레이 중 빈 프레임 가능 → 프리로더 추가 권장

🗺️ 로드맵

 스프라이트 애니메이션 도입 (Idle/Walk/Attack/Dead)

 타워 스프라이트화, 발사 이펙트/피격 이펙트 추가

 웨이브/스테이지 시스템, 보스 유닛

 반응형/모바일 HUD 개편

 세이브 데이터(로컬스토리지) / 리플레이 로그

🤝 기여 가이드

브랜치 전략: feature/xxxx → PR

커밋 메시지: feat:, fix:, refactor:, style:, docs:, chore: 프리픽스

에셋 추가 시 폴더/파일명 규칙을 엄수하고, README의 에셋 체크리스트 업데이트

📜 라이선스

개인/학습 목적의 공개 사용은 자유.
제3자 에셋(스프라이트, 사운드 등)은 각 저작권/라이선스를 반드시 확인하세요.

📎 부록 – 코드 포인터(찾기 쉽게)

유닛 타입/스탯: unitTypes

타워 스탯/레벨 컬러: towerStats, towerColors, Tower.upgrade()

생산 큐: productionQueue, updateQueueBadges(), spawnUnit()

스킬: activateIronWall/MedEvac/RapidReinforce/Overwatch/TimeWarp/EraBlast

시대 업그레이드: tryEraUpgrade()

난이도: setDifficulty(level)

HUD: updateHUD(), 쿨다운 오버레이 CSS .hud-btn.cooling

에셋 로더: loadAllAssets() + 경로 규칙
