# VR 아바타 네트워킹 설정 가이드 (Host 전용 아바타)

## 1. Host 아바타 Prefab 생성

### Host 아바타 Prefab 만들기
1. **Shinano_kisekae Variant를 Prefab으로 생성**
2. **다음 컴포넌트 추가**:
   - `NetworkObject` (Fusion 네트워킹용)
   - `VRIKNetworkPlayer` (Host 전용)
   - `VRIK` (Final IK 컴포넌트)

### VRIKNetworkPlayer 설정

#### VRIK Configuration
- `VRIK`: Shinano_kisekae Variant의 VRIK 컴포넌트 연결

#### VR Target Objects - Full Body Tracking (자동 연결)
씬에 다음 **정확한 이름**으로 GameObject들을 생성하세요:
- `Head Target`: HMD 추적 GameObject
- `Left Hand Target`: 왼손 컨트롤러 추적 GameObject
- `Right Hand Target`: 오른손 컨트롤러 추적 GameObject  
- `Waist Target`: 허리 트래커 추적 GameObject
- `Left Foot Target`: 왼발 트래커 추적 GameObject
- `Right Foot Target`: 오른발 트래커 추적 GameObject

**중요**: 이름이 정확해야 자동으로 찾을 수 있습니다! Host 시작 시 자동으로 연결됩니다.

## 2. BasicSpawner 설정

BasicSpawner 컴포넌트:
- `Host Avatar Prefab`: 위에서 만든 Host 아바타 Prefab 연결

**작동 방식**: Host가 세션 시작하면 Host 아바타가 자동 스폰되고, 모든 플레이어가 볼 수 있습니다.

## 3. VRIK 설정 (아바타별 자동 감지)

### 🎯 새로운 동적 본 시스템!
이제 **아바타마다 다른 본 구조를 자동으로 감지**합니다:

#### 지원되는 본들 (있는 것만 사용)
✅ **기본 본들**: Root, Pelvis, Spine, Chest, Neck, Head
✅ **팔 본들**: LeftShoulder, LeftUpperArm, LeftForearm, LeftHand
✅ **팔 본들**: RightShoulder, RightUpperArm, RightForearm, RightHand  
✅ **다리 본들**: LeftThigh, LeftCalf, LeftFoot
✅ **다리 본들**: RightThigh, RightCalf, RightFoot

#### VRIK References 설정만 해주세요!
- Final IK VRIK 컴포넌트에서 **있는 본들만** 설정
- 없는 본들은 null로 두어도 괜찮음
- 시스템이 자동으로 감지하고 콘솔에 본 개수를 출력

### Solver 설정
- `Plant Feet`: false (Pelvis Position Weight > 0일 때)
- `Locomotion Weight`: 1.0 (걷기 애니메이션 사용 시)

## 4. 간단한 사용법

### Host 설정
1️⃣ **Host 아바타 Prefab 생성** (NetworkObject + VRIKNetworkPlayer + VRIK)
2️⃣ **씬에 정확한 이름으로 6개 VR Target GameObject 생성**
   - `Head Target`, `Left Hand Target`, `Right Hand Target`
   - `Waist Target`, `Left Foot Target`, `Right Foot Target`
3️⃣ **BasicSpawner에 Host Avatar Prefab 연결**
4️⃣ **Host 버튼 클릭** 🎉

### Client 설정 (아바타 없음)
1️⃣ **별도 설정 불필요**
2️⃣ **Join 버튼 클릭** 🎉

**핵심**: Host가 VR로 조작하는 아바타를 모든 플레이어(Host + Clients)가 실시간으로 관전합니다.

**시스템이 자동으로:**
- ✅ Host 아바타 VRIK 설정
- ✅ 모든 플레이어에게 아바타 표시
- ✅ Client 연결 시 관전자 모드

## 5. VR 트래커 연결

### OpenXR (권장)
```csharp
// XR Origin 설정 필요
// XR Interaction Toolkit 사용
```

### 타겟 오브젝트 설정
각 타겟 GameObject는 VR 디바이스를 따라가도록 설정:
- Head Target → HMD 추적
- Hand Targets → 컨트롤러 추적  
- Waist/Foot Targets → VIVE 트래커 추적

## 6. 테스트 방법

### Host 테스트 (Full Body Tracking)
1. VIVE Pro 2 헤드셋 + 컨트롤러 2개 + 트래커 3개 연결
2. 6개 타겟 오브젝트가 VR 디바이스를 따라가는지 확인
3. Host 버튼 클릭
4. **콘솔 확인**: `"Host avatar spawned for player X"`
5. **Host 아바타가 스폰되고** VRIK가 올바르게 추적하는지 확인

### Client 테스트
1. 다른 기기에서 Join 버튼 클릭
2. **Host의 아바타가 동일하게 보이는지** 확인
3. **Host VR 움직임이 실시간으로** Client에서도 동기화되는지 확인
4. **모든 본의 회전이 정확히** 복사되는지 체크

## 7. 다양한 아바타 지원

### Shinano 아바타
- 표준 휴머노이드 구조
- 보통 17-20개 본 감지

### 다른 VRChat 아바타  
- 각자 다른 본 구조 자동 지원
- Final IK VRIK References만 올바르게 설정하면 됨

### 커스텀 아바타
- 표준 휴머노이드 본 구조 권장
- 특수 본들은 무시되고 표준 본들만 동기화

## 8. 문제 해결

### "cached 0 bones" 오류
- VRIK References가 올바르게 설정되지 않음
- VRIK 컴포넌트가 연결되지 않음

### 아바타가 움직이지 않음 (Client)
- VRIK가 Client에서 비활성화되었는지 확인
- NetworkPlayerPrefab이 올바르게 스폰되었는지 확인

### 일부 관절만 움직임
- 해당 본들이 VRIK References에 설정되지 않음
- 콘솔 로그로 감지된 본 개수 확인

## 9. 최적화 및 확장

### 네트워크 최적화 ✅
- **극한 단순화**: 네트워크 동기화 완전 제거
- **VRIK 직접 제어**: Host가 로컬에서 아바타 조작
- **관전자 모드**: Client들은 네트워크 오버헤드 없이 관전

### 향후 확장 가능성
- ✅ **Host 전용 아바타 시스템 완료**
- 🔄 표정 동기화 (ARKit/SRanipal)
- 🔄 손가락 트래킹 추가
- 🔄 다중 Host 지원 (여러 아바타) 