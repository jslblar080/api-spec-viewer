# 오늘의 끼니 — 응답(서버 → 프론트) JSON 초안

> 피그마 프레임 6, 8, 9, 9-1, 10, 10-1, 10-2, 10-3
>
> **이 문서는 응답(서버 → 프론트) JSON의 초안입니다.** 요청 JSON은 백엔드 측에서 초안 주시면 제가 맞추겠습니다.
>
> 서로 다른 엔드포인트 JSON에서 meal_id와 menu_name이 불일치하는 경우가 있으니 감안해주시면 감사하겠습니다.
---

## 1. POST `/meal-plans/generate`

> **화면 6** — 프로필 기반 식단 생성 (로딩 화면 트리거)

```json
{
  "job_id": "job_abc123",
  "estimated_seconds": 10,
  "stages": ["프로필 분석", "식단 후보 생성", "가격 비교", "최적 조합 선정"]
}
```

---

## 2. POST `/meal-plans/confirm`

> **화면 9** — 식단 확정 후 30일 캘린더 생성

```json
{
  "plan_id": "plan_abc123",
  "start_date": "2026-04-06",
  "end_date": "2026-05-05",
  "duration_days": 30,
  "total_price_per_plan": 324000,
  "average_calories_per_plan": 1850,
  "generated_at": "2026-04-06T09:30:00Z"
}
```

- 확정 후 프론트는 캘린더로 이동하면서 `/meal-plans/calendar?month=2026-04`를 호출.
- 30일치 데이터를 응답에 포함하지 않음 (캘린더 엔드포인트와 중복 회피).

---

## 3. GET `/meal-plans/calendar?month=2026-04`

> **화면 9** — 월간 캘린더. 식단은 4/6 ~ 5/5 시나리오

**4월 응답:**

```json
{
  "month": "2026-04",
  "duration_days": 25,
  "total_price_per_month": 270000,
  "average_calories_per_month": 1850,
  "days": [
    { "date": "2026-04-01", "calories_per_day": null, "price_per_day": null, "meals": [] },
    { "date": "2026-04-02", "calories_per_day": null, "price_per_day": null, "meals": [] },
    { "date": "2026-04-03", "calories_per_day": null, "price_per_day": null, "meals": [] },
    { "date": "2026-04-04", "calories_per_day": null, "price_per_day": null, "meals": [] },
    { "date": "2026-04-05", "calories_per_day": null, "price_per_day": null, "meals": [] },
    {
      "date": "2026-04-06",
      "calories_per_day": 1850,
      "price_per_day": 10800,
      "meals": [
        { "slot": 1, "meal_id": "M_001", "menu_name": "볶음밥" },
        { "slot": 2, "meal_id": "M_002", "menu_name": "콩나물국" },
        { "slot": 3, "meal_id": "M_003", "menu_name": "제철 나물 비빔밥" }
      ]
    }
    // ... 4/7 ~ 4/30까지 동일 구조 (모두 식단 있음)
  ]
}
```

**5월 응답:**

```json
{
  "month": "2026-05",
  "duration_days": 5,
  "total_price_per_month": 54000,
  "average_calories_per_month": 1850,
  "days": [
    {
      "date": "2026-05-01",
      "calories_per_day": 1850,
      "price_per_day": 10800,
      "meals": [
        { "slot": 1, "meal_id": "M_001", "menu_name": "볶음밥" },
        { "slot": 2, "meal_id": "M_002", "menu_name": "콩나물국" },
        { "slot": 3, "meal_id": "M_003", "menu_name": "제철 나물 비빔밥" }
      ]
    },
    // ... 5/2 ~ 5/5까지 식단 데이터
    { "date": "2026-05-06", "calories_per_day": null, "price_per_day": null, "meals": [] },
    { "date": "2026-05-07", "calories_per_day": null, "price_per_day": null, "meals": [] },
    // ... 5/8 ~ 5/31까지 모두 빈 데이터
    { "date": "2026-05-31", "calories_per_day": null, "price_per_day": null, "meals": [] }
  ]
}
```

- `days` 배열은 항상 그 달의 모든 날짜 포함 (4월 30개, 5월 31개).
- 식단 없는 날: `calories_per_day: null`, `price_per_day: null`, `meals: []`.
- `total_price_per_month` / `average_calories_per_month`은 식단 있는 날만 합산/평균.
- `duration_days`: 식단 있는 일수.

---

## 4. PATCH `/meal-plans/{date}/swap`

> **화면 9 NOTES** — 두 날짜의 식단 스왑

```json
{
  "swapped": [
    {
      "date": "2026-04-06",
      "calories_per_day": 1700,
      "price_per_day": 9500,
      "meals": [
        { "slot": 1, "meal_id": "M_015", "menu_name": "훈제오리 월남쌈" },
        { "slot": 2, "meal_id": "M_016", "menu_name": "닭가슴살 현미죽" },
        { "slot": 3, "meal_id": "M_014", "menu_name": "소고기 야채 볶음" }
      ]
    },
    {
      "date": "2026-04-10",
      "calories_per_day": 1850,
      "price_per_day": 10800,
      "meals": [
        { "slot": 1, "meal_id": "M_011", "menu_name": "소고기 미역국과 잡곡밥" },
        { "slot": 2, "meal_id": "M_012", "menu_name": "닭가슴살 샐러드" },
        { "slot": 3, "meal_id": "M_013", "menu_name": "계란 볶음밥" }
      ]
    }
  ]
}
```

- 응답 구조를 `/meal-plans/calendar`의 `days` 항목과 동일하게 유지
- 요청 body에 `with_date` 같은 필드로 스왑 대상 날짜 지정 (백엔드 결정).

---

## 5. GET `/meal-plans/{date}`

> **화면 10** — 일일 식단 상세

```json
{
  "date": "2026-04-06",
  "calories_per_day": 1850,
  "price_per_day": 10800,
  "meals": [
    {
      "slot": 1,
      "meal_id": "M_001",
      "menu_name": "볶음밥",
      "calories": 650,
      "price": 3600,
      "image_url": null
    },
    {
      "slot": 2,
      "meal_id": "M_002",
      "menu_name": "콩나물국",
      "calories": 550,
      "price": 3600,
      "image_url": null
    },
    {
      "slot": 3,
      "meal_id": "M_003",
      "menu_name": "제철 나물 비빔밥",
      "calories": 650,
      "price": 3600,
      "image_url": null
    }
  ]
}
```

---

## 6. GET `/menus/{meal_id}`

> **화면 8, 10-1** — 레시피 상세 (재료 + 마켓별 가격)

```json
{
  "meal_id": "M_001",
  "menu_name": "제철 나물 비빔밥",
  "calories": 650,
  "price": 3600,
  "image_url": null,
  "video_url": null,
  "required_ingredient_ids": ["I_001", "I_002", "I_003"],
  "ingredients": [
    {
      "ingredient_id": "I_001",
      "ingredient_name": "제철 나물",
      "standard_unit": "100g",
      "image_url": null,
      "lowest_price_between_market": {
        "market": "coupang",
        "price": 1200
      },
      "e_commerce_prices": {
        "coupang": {
          "lowest_price": 1200
        },
        "market_kurly": {
          "lowest_price": 1400
        },
        "naver_shopping": {
          "lowest_price": 1300
        }
      }
    },
    {
      "ingredient_id": "I_002",
      "ingredient_name": "계란",
      "standard_unit": "2구",
      "image_url": null,
      "lowest_price_between_market": {
        "market": "coupang",
        "price": 1200
      },
      "e_commerce_prices": {
        "coupang": {
          "lowest_price": 1200
        },
        "market_kurly": null,
        "naver_shopping": {
          "lowest_price": 1300
        }
      }
    },
    {
      "ingredient_id": "I_003",
      "ingredient_name": "현미밥",
      "standard_unit": "200g",
      "image_url": null,
      "lowest_price_between_market": {
        "market": "coupang",
        "price": 1200
      },
      "e_commerce_prices": {
        "coupang": { "lowest_price": 1200 },
        "market_kurly": { "lowest_price": 1400 },
        "naver_shopping": { "lowest_price": 1300 }
      }
    }
  ]
}
```

---

## 7. GET `/shopping-list`

> **화면 9-1** — 장보기 목록 (마켓별 그룹)

```json
{
  "total_items": 6,
  "checked_items_count": 4,
  "total_price_per_shopping": 17400,
  "market_counts": [
    { "market": "coupang",        "count": 3 },
    { "market": "market_kurly",   "count": 0 },
    { "market": "naver_shopping", "count": 1 }
  ],
  "market_groups": [
    {
      "market": "coupang",
      "subtotal": 15600,
      "items": [
        {
          "item_id": "item_001",
          "ingredient_id": "I_xxx",
          "ingredient_name": "제철 나물",
          "standard_unit": "100g",
          "delivery_type": "로켓프레시",
          "lowest_price": 1200,
          "product_title": "제철 나물 100g",
          "purchase_link": "https://www.coupang.com/vp/products/...",
          "is_checked": true
        },
        {
          "item_id": "item_002",
          "ingredient_id": "I_xxx",
          "ingredient_name": "계란 (10구)",
          "standard_unit": "10구",
          "delivery_type": "로켓프레시",
          "lowest_price": 4500,
          "product_title": "신선란 10구",
          "purchase_link": "https://www.coupang.com/vp/products/...",
          "is_checked": true
        },
        {
          "item_id": "item_004",
          "ingredient_id": "I_xxx",
          "ingredient_name": "닭가슴살",
          "standard_unit": "1000g",
          "delivery_type": "로켓프레시",
          "lowest_price": 9900,
          "product_title": "냉동 닭가슴살 200g x 5팩",
          "purchase_link": "https://www.coupang.com/vp/products/...",
          "is_checked": true
        }
      ]
    },
    {
      "market": "market_kurly",
      "subtotal": 0,
      "items": [
        {
          "item_id": "item_003",
          "ingredient_id": "I_xxx",
          "ingredient_name": "현미밥",
          "standard_unit": "1kg",
          "delivery_type": "샛별배송",
          "lowest_price": 6800,
          "product_title": "현미밥 1kg",
          "purchase_link": "https://www.kurly.com/goods/...",
          "is_checked": false
        },
        {
          "item_id": "item_005",
          "ingredient_id": "I_xxx",
          "ingredient_name": "된장",
          "standard_unit": "500g",
          "delivery_type": "샛별배송",
          "lowest_price": 4200,
          "product_title": "재래식 된장 500g",
          "purchase_link": "https://www.kurly.com/goods/...",
          "is_checked": false
        }
      ]
    },
    {
      "market": "naver_shopping",
      "subtotal": 1800,
      "items": [
        {
          "item_id": "item_006",
          "ingredient_id": "I_xxx",
          "ingredient_name": "두부",
          "standard_unit": "1모",
          "delivery_type": "일반배송",
          "lowest_price": 1800,
          "product_title": "국산콩 두부 1모",
          "purchase_link": "https://smartstore.naver.com/...",
          "is_checked": true
        }
      ]
    }
  ]
}
```

- `is_checked`는 사용자 상태로 서버에 저장 (재방문 시 복원).
- `purchase_link`는 외부 마켓으로 연결하는 URL.

---

## 8. PATCH `/shopping-list/items/{id}`

> **화면 9-1** — 장보기 항목 체크/해제

```json
{
  "item_id": "item_001",
  "is_checked": false,
  "summary": {
    "total_items": 6,
    "checked_items_count": 3,
    "total_price_per_shopping": 16200,
    "market_counts": [
      { "market": "coupang",        "count": 2 },
      { "market": "market_kurly",   "count": 0 },
      { "market": "naver_shopping", "count": 1 }
    ]
  }
}
```

- 화면 상단 합계 표시(₩17,400 · 마켓 2곳, 쿠팡 3·컬리 0·네이버 1) 갱신용으로 summary 같이 반환.
- summary 구조는 GET /shopping-list의 최상위 합계 필드와 동일 → 같은 모델로 처리.

---

## 9. DELETE `/shopping-list/items`

> **화면 9-1** — 선택 항목 일괄 삭제

```json
{
  "deleted_count": 4,
  "deleted_item_ids": ["item_001", "item_002", "item_004", "item_006"],
  "summary": {
    "total_items": 2,
    "checked_items_count": 0,
    "total_price_per_shopping": 0,
    "market_counts": [
      { "market": "coupang",        "count": 0 },
      { "market": "market_kurly",   "count": 0 },
      { "market": "naver_shopping", "count": 0 }
    ]
  }
}
```

- `summary` 구조는 8번 PATCH와 동일 → 프론트에서 같은 모델로 처리.

---

## 10. GET `/ingredients/{ingredient_id}/prices`

> **화면 10-2** — 재료 마켓별 가격 비교 상세

```json
{
  "ingredient_id": "I_001",
  "ingredient_name": "제철 나물",
  "standard_unit": "100g",
  "image_url": null,
  "e_commerce_prices": {
    "coupang": {
      "delivery_type": "로켓프레시",
      "lowest_price": 1200,
      "product_title": "곰곰 국내산 제철 나물 100g",
      "purchase_link": "https://www.coupang.com/vp/products/123456",
      "is_lowest": true
    },
    "market_kurly": {
      "delivery_type": "샛별배송",
      "lowest_price": 1400,
      "product_title": "[KF365] 제철 나물 100g",
      "purchase_link": "https://www.kurly.com/goods/567890",
      "is_lowest": false
    },
    "naver_shopping": {
      "delivery_type": "일반배송",
      "lowest_price": 1300,
      "product_title": "농협 제철 나물 100g",
      "purchase_link": "https://smartstore.naver.com/nonghyup/products/111222",
      "is_lowest": false
    }
  }
}
```

- `is_lowest`: 서버에서 계산해 표시 (상품 가격 기준 마켓 간 비교에서 최저가).

---

## 11. PUT `/meal-plans/{date}/menus/{slot}`

> **화면 10-3** — 메뉴 변경 (특정 끼니 슬롯 교체)

```json
{
  "date": "2026-04-01",
  "calories_per_day": 1820,
  "price_per_day": 13700,
  "meals": [
    {
      "slot": 1,
      "meal_id": "M_101",
      "menu_name": "두부 김치덮밥",
      "calories": 620,
      "price": 6500,
      "image_url": null
    },
    {
      "slot": 2,
      "meal_id": "M_002",
      "menu_name": "콩나물국",
      "calories": 550,
      "price": 3600,
      "image_url": null
    },
    {
      "slot": 3,
      "meal_id": "M_003",
      "menu_name": "제철 나물 비빔밥",
      "calories": 650,
      "price": 3600,
      "image_url": null
    }
  ]
}
```

- 응답 구조는 5번 GET `/meal-plans/{date}`와 동일 → 같은 모델로 처리.
- 요청 body에 새 `meal_id` 전송 (백엔드 결정).
- 변경 후 캘린더 반영.

---

## 12. GET `/menus/{meal_id}/alternatives`

> **화면 10-3** — 메뉴 변경용 추천 대안

```json
{
  "current_meal": {
    "meal_id": "M_003",
    "menu_name": "볶음밥",
    "calories": 650,
    "price": 3600,
    "image_url": null,
    "date": "2026-04-01",
    "slot": 1
  },
  "alternatives": [
    {
      "meal_id": "M_101",
      "menu_name": "두부 김치덮밥",
      "calories": 620,
      "price": 6500,
      "image_url": null
    },
    {
      "meal_id": "M_102",
      "menu_name": "야채 볶음밥",
      "calories": 580,
      "price": 5800,
      "image_url": null
    },
    {
      "meal_id": "M_103",
      "menu_name": "닭가슴살 샐러드",
      "calories": 450,
      "price": 9200,
      "image_url": null
    },
    {
      "meal_id": "M_104",
      "menu_name": "참치 김밥",
      "calories": 550,
      "price": 4500,
      "image_url": null
    }
  ]
}
```

- 추천 로직은 AI 모델링/RAG 팀 영역.

---
