# Tailwind CSS

## flex
부모를 **정렬 가능한 컨테이너**로 만들고
자식 요소들을 **한 줄로 배치**할 수 있게 해준다.
```
display: flex;
```
### 1️⃣ flex 안 썼을 때 (기본 block)
```
<div>
  <div>■</div>
  <div>■</div>
  <div>■</div>
</div>
```
```
■
■
■
```
- 요소는 **위에서 아래로**
- 정렬 제어 거의 불가능

### 2️⃣ flex 썼을 때 (기본 row)
```
<div class="flex">
  <div>■</div>
  <div>■</div>
  <div>■</div>
</div>
```
```
■ ■ ■
```
- 요소가 **가로로 나열**
- 이 상태부터 justify / items 사용 가능
👉 flex = 배치 환경 ON

### 3️⃣ flex + items-center (세로 정렬)
```
<div class="flex items-center">
```
```
|   ■   |
|   ■   |
|   ■   |
```
- 가로로 나열된 요소들을
- **세로 가운데로 맞춤***
👉 아이콘 + 텍스트 정렬할 때 필수

### 4️⃣ flex + justify-center (가로 정렬)
```
<div class="flex justify-center">
```
```
|    ■ ■ ■    |
```
- 요소 묶음 전체를
- **가로 가운데로 이동**

### 5️⃣ flex + items-center + justify-center (정중앙)
```
<div class="flex items-center justify-center">
```
```
+-------------+
|      ■      |
+-------------+
```
- 가로 + 세로 완전 중앙
- 로딩, 모달, 빈 화면

### 6️⃣ flex 방향 바꾸기 (flex-col)
```
<div class="flex flex-col">
```
```
■
■
■
```
- 이번엔 세로가 주축
- 기준이 반대로 바뀜

#### flex-col + justify-center
```
<div class="flex flex-col justify-center">
```
```
|
■
■
■
|
```
👉 세로 중앙 정렬

#### flex-col + items-center
```
<div class="flex flex-col items-center">
```
```
   ■
   ■
   ■
```
👉 가로 중앙 정렬

### 7️⃣ 한줄 요약 🔥
flex는 정렬이 아니라 “배치 방향을 만드는 것”
정렬은 그 다음에 justify / items가 한다
```
flex        → ■ ■ ■   (배치 방향)
justify-*   → 가로 위치 이동
items-*     → 세로 위치 맞춤
flex-col    → 축 반전
```

## justify-* vs items-* 한눈 비교
| 구분 | 기준 축 | 대표 용도    |
|-----|--------|----------|
| justify-* | 주축 | 가로 간격 분배 |
| items-* | 교차축 | 세로 정렬    |

## justify-* 

### 1️⃣ justify-start
- 아이템을 왼쪽부터 붙임
```
|■ ■ ■       |
```

### 2️⃣ justify-center
- 아이템들을 가운데로 모음
```
|    ■ ■ ■    |
```

### 3️⃣ justify-end
- 아이템을 오른쪽으로 붙임
```
|       ■ ■ ■|
```

### 4️⃣ justify-between ⭐ 많이 씀
- 양 끝은 딱 붙고
- 사이 간격만 균등
```
|■     ■     ■|
```

### 5️⃣ justify-around
- **각 아이템 주변에 동일한 여백**
- 양 끝도 여백이 있음 (중간보다 작음)
```
|■     ■     ■|
```
👉 보기엔 자연스럽지만 정렬이 애매할 때 있음

### 6️⃣ justify-evenly
- 아이템 사이 + 양 끝 여백까지 전부 동일
```
|   ■   ■   ■   |
```
👉 균형감 제일 좋음 (요즘 많이 씀)

| 속성 | 특징                 | 언제 쓰냐      |
|------|--------------------|------------|
| justify-start | 왼쪽 정렬              | 기본 레이아웃    |
| justify-center | 가운데 정렬             | 버튼 그룹      |
| justify-end | 오른쪽 정렬             | 액션 버튼      |
| justify-between | 양 끝 고정, 사이 간격만 벌어짐 | 헤더 / 네비게이션 |
| justify-around | 아이템 주변에 여백         | 카드 나열      |
| justify-evenly | 양 끝 포함 모든 간격 동********일    | 균등 레이아웃    |

## items-* 
### 1️⃣ items-start
```
align-items: flex-start;
```
- 아이템을 위쪽으로 정렬
```
■
■
■
```

### 2️⃣ items-center ⭐ 제일 많이 씀
```
align-items: center;
```
- 아이템을 **세로 가운데로 정렬**
- 아이콘 + 텍스트를 나란히 둘 때 자주 사용
```
   ■
   ■
   ■
```

### 3️⃣ items-end
```
align-items: flex-end;
```
- 아이템을 아래쪽으로 정렬
```
       ■
       ■
       ■
```

### 4️⃣ items-stretch (기본값)
```
align-items: stretch;
```
- 아이템이 컨테이너 높이를 꽉 채움
- 높이가 지정되지 않은 경우 자동으로 늘어남
```
███
███
███
```

### 5️⃣ items-baseline
```
align-items: baseline;
```
아이템을 텍스트 기준선(baseline)에 맞춰 정렬
폰트 크기가 다를 때 줄이 어긋나지 않음
```
■──
  ■──
    ■──
```

| 속성 | 특징 | 언제 쓰냐 |
|------|------|-----------|
| items-start | 위쪽 정렬 | 기본 세로 정렬 |
| items-center | 세로 가운데 정렬 | 아이콘 + 텍스트 정렬 |
| items-end | 아래쪽 정렬 | 하단 정렬 |
| items-stretch | 높이 꽉 채움 (기본값) | 카드 높이 맞출 때 |
| items-baseline | 텍스트 기준선 정렬 | 서로 다른 폰트 크기 정렬 |