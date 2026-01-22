## 🎨 Item Visual System Update

기존의 하드코딩된 아이템 ID 방식을 제거하고, `ScriptableObject`를 통해 아이템의 ID, 이름, 아이콘을 관리하도록 변경했습니다.

### 📁 주요 파일
* **Script:** `Assets/Scripts/MarketPlace/ItemVisualData.cs`
* **Data:** `Assets/Data/Market/GlobalItemVisuals.asset`

### 🛠 아이템 추가 방법 (How to add new items)
1. 프로젝트 창에서 `GlobalItemVisuals` 데이터 파일을 선택합니다.
2. Inspector 창에서 `Items` 리스트의 `+` 버튼을 누릅니다.
3. 아래 정보를 입력합니다:
   * **ID:** UGS Economy에 등록된 Resource ID (예: `PAINT_RED`)
   * **Item Name:** 게임 내 UI에 표시될 이름 (예: `빨간 물감`)
   * **Icon:** 사용할 Sprite 이미지를 드래그 앤 드롭
4. 게임을 실행하면 자동으로 인벤토리와 상점에 반영됩니다.

---