# 오늘의 끼니 — API 명세서 v2

> 피그마 프레임 6, 8, 9, 9-1, 10, 10-1, 10-2, 10-3
>
> **v2 업데이트 (2026-05):** 백엔드 실제 구현에 맞춰 경로/스키마 정렬. 일부 endpoint 미구현 상태는 `TODO` 마커로 표기.

---

## 공통 규약

### Base URL
모든 endpoint는 `/api/v1` 하위. 예: `https://<host>/api/v1/meal/calendar`

### 인증
모든 endpoint는 JWT Bearer 토큰 필요:
```
Authorization: Bearer <access_token>
```
- 토큰 payload는 `{ sub: "<user_id>", exp: <unix_ts> }` 형태.

### 에러 응답
주요 상태 코드:
- `401` — 토큰 없음/만료/검증 실패
- `403` — 인증은 되었으나 권한 없음
- `404` — 리소스 없음 (예: 해당 날짜 식단 없음)
- `422` — 요청 body 검증 실패 (Pydantic)
- `500` — 서버 내부 오류

---

# Meal Plans

## POST `/meal/generate`

> **화면 6** — 식단 생성 중

**Request body:** 없음 (유저 프로필은 토큰의 user_id로 조회)

**Response 202:**
```json
{
  "job_id": "job_abc123",
  "estimated_seconds": 10,
  "stages": ["프로필 분석", "식단 후보 생성", "가격 비교", "최적 조합 선정"]
}
```

- 현재 상태: job_id만 반환. 실제 DB write는 일어나지 않음.
- 실제 데이터 저장은 /meal/sample_data_three_days 또는 /meal/request_monthly_plan에서 일어남.
- TODO (백엔드): AI 모델링 팀의 식단 생성 서버 호출 → 결과를 crud_meal.save_recommendation_result로 DB 저장 (Celery/BackgroundTasks).

---

## GET `/meal/calendar?month=YYYY-MM`

> **화면 9** — 식단 캘린더

**Query parameters:**
- `month`: `"YYYY-MM"` 형식

**Response 200 (시연 시나리오 — 식단 4/6 ~ 5/5):**

2026-04
```json
{
    "month": "2026-04",
    "duration_days": 25,
    "total_price_per_month": 250000,
    "average_calories_per_month": 1850,
    "days": [
        {
            "date": "2026-04-01",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-04-02",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-04-03",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-04-04",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-04-05",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-04-06",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-07",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-08",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-09",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-10",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-11",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-12",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-13",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-14",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-15",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-16",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-17",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-18",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-19",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-20",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-21",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-22",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-23",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-24",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-25",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-26",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-27",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-28",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-29",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-04-30",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        }
    ]
}
```

2026-05
```json
{
    "month": "2026-05",
    "duration_days": 5,
    "total_price_per_month": 50000,
    "average_calories_per_month": 1850,
    "days": [
        {
            "date": "2026-05-01",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-05-02",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-05-03",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-05-04",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-05-05",
            "calories_per_day": 1850,
            "price_per_day": 10000,
            "meals": [
                {
                    "slot": 1,
                    "meal_id": "M_001",
                    "menu_name": "볶음밥"
                },
                {
                    "slot": 2,
                    "meal_id": "M_002",
                    "menu_name": "콩나물국"
                },
                {
                    "slot": 3,
                    "meal_id": "M_003",
                    "menu_name": "제철 나물 비빔밥"
                }
            ]
        },
        {
            "date": "2026-05-06",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-07",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-08",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-09",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-10",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-11",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-12",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-13",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-14",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-15",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-16",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-17",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-18",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-19",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-20",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-21",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-22",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-23",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-24",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-25",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-26",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-27",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-28",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-29",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-30",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        },
        {
            "date": "2026-05-31",
            "calories_per_day": null,
            "price_per_day": null,
            "meals": []
        }
    ]
}
```

d{4}-\d{2} (식단 없는 모든 달)
```json
{
    "month": "0000-00",
    "duration_days": 0,
    "total_price_per_month": 0,
    "average_calories_per_month": 0,
    "days": []
}
```

- `days` 배열은 항상 해당 월의 모든 날짜 포함.
- 식단 없는 날: `calories_per_day: null`, `price_per_day: null`, `meals: []`.
- `total_price_per_month` / `average_calories_per_month`: 식단 있는 날만 합산/평균.
- `duration_days`: 식단 있는 일수.
- 참고: 백엔드의 meal_id는 내부적으로 int지만 응답에서 str(menu_id)로 직렬화. mock JSON의 "M_001" 같은 prefix 형태가 아니라 "101" 같은 숫자 문자열로 옴.

---

## GET `/meal/{date}`

> **화면 8, 10** — 홈/오늘의 메뉴, 일일 식단 상세

**Path:** `date`: `YYYY-MM-DD`

**Response 200:**
```json
{
    "date": "2026-04-06",
    "calories_per_day": 1850,
    "price_per_day": 10000,
    "meals": [
        {
            "slot": 1,
            "meal_id": "001",
            "menu_name": "볶음밥",
            "calories": 650,
            "price": 2600,
            "image_url": null
        },
        {
            "slot": 2,
            "meal_id": "002",
            "menu_name": "콩나물국",
            "calories": 550,
            "price": 3900,
            "image_url": null
        },
        {
            "slot": 3,
            "meal_id": "003",
            "menu_name": "제철 나물 비빔밥",
            "calories": 650,
            "price": 3500,
            "image_url": null
        }
    ]
}
```

**Response 404:** 해당 날짜의 식단 없음.
- image_url은 백엔드가 Pixabay 검색 결과로 채움. 검색 실패 시 null.
---

## GET `/meal/menu/{meal_date}/{menu_id}`

> **화면 8, 10-1** — 홈/오늘의 메뉴, 재료 목록 화면

**Path:**
- meal_date: YYYY-MM-DD
- menu_id: 메뉴 ID (예: "101")

**Response 200:**

001
```json
{
    "meal_id": "001",
    "menu_name": "볶음밥",
    "calories": 650,
    "price": 2600,
    "image_url": null,
    "video_url": null,
    "required_ingredient_ids": [
        "I_004",
        "I_005",
        "I_003"
    ],
    "ingredients": [
        {
            "ingredient_id": "I_004",
            "ingredient_name": "양파",
            "standard_unit": "1개",
            "image_url": null,
            "lowest_price_between_market": {
                "market": "coupang",
                "price": 800
            },
            "e_commerce_prices": {
                "coupang": {
                    "lowest_price": 800
                },
                "market_kurly": {
                    "lowest_price": 1000
                },
                "naver_shopping": {
                    "lowest_price": 900
                }
            }
        },
        {
            "ingredient_id": "I_005",
            "ingredient_name": "당근",
            "standard_unit": "1개",
            "image_url": null,
            "lowest_price_between_market": {
                "market": "naver_shopping",
                "price": 700
            },
            "e_commerce_prices": {
                "coupang": {
                    "lowest_price": 800
                },
                "market_kurly": {
                    "lowest_price": null
                },
                "naver_shopping": {
                    "lowest_price": 700
                }
            }
        },
        {
            "ingredient_id": "I_003",
            "ingredient_name": "현미밥",
            "standard_unit": "200g",
            "image_url": null,
            "lowest_price_between_market": {
                "market": "naver_shopping",
                "price": 1100
            },
            "e_commerce_prices": {
                "coupang": {
                    "lowest_price": 1200
                },
                "market_kurly": {
                    "lowest_price": 1400
                },
                "naver_shopping": {
                    "lowest_price": 1100
                }
            }
        }
    ]
}
```

002
```json
{
    "meal_id": "002",
    "menu_name": "콩나물국",
    "calories": 550,
    "price": 3900,
    "image_url": null,
    "video_url": null,
    "required_ingredient_ids": [
        "I_006",
        "I_007",
        "I_002"
    ],
    "ingredients": [
        {
            "ingredient_id": "I_006",
            "ingredient_name": "콩나물",
            "standard_unit": "300g",
            "image_url": null,
            "lowest_price_between_market": {
                "market": "market_kurly",
                "price": 1500
            },
            "e_commerce_prices": {
                "coupang": {
                    "lowest_price": 1700
                },
                "market_kurly": {
                    "lowest_price": 1500
                },
                "naver_shopping": {
                    "lowest_price": 1600
                }
            }
        },
        {
            "ingredient_id": "I_007",
            "ingredient_name": "대파",
            "standard_unit": "1대",
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
                "market_kurly": {
                    "lowest_price": null
                },
                "naver_shopping": {
                    "lowest_price": 1300
                }
            }
        }
    ]
}
```

003
```json
{
    "meal_id": "003",
    "menu_name": "제철 나물 비빔밥",
    "calories": 650,
    "price": 3500,
    "image_url": null,
    "video_url": null,
    "required_ingredient_ids": [
        "I_001",
        "I_002",
        "I_003"
    ],
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
                "market_kurly": {
                    "lowest_price": null
                },
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
                "market": "naver_shopping",
                "price": 1100
            },
            "e_commerce_prices": {
                "coupang": {
                    "lowest_price": 1200
                },
                "market_kurly": {
                    "lowest_price": 1400
                },
                "naver_shopping": {
                    "lowest_price": 1100
                }
            }
        }
    ]
}
```

- 백엔드는 식단의 selected_menu.ingredient_costs 리스트에서 재료별 가격 정보 추출.
- 마켓에 재고 없으면 해당 마켓 객체 null.

---

## PUT `/meal/{date}/menus/{slot}`

> **화면 10-3** — 메뉴변경

**Path:** `date`: `YYYY-MM-DD`, `slot`: 1, 2, 3...

**Request body:**
```json
{
  "new_menu_id": "101"
}
```

**Response 200:** (그 날 전체 식단 갱신본)
```json
{
    "date": "2026-04-01",
    "calories_per_day": 1820,
    "price_per_day": 13700,
    "meals": [
        {
            "slot": 1,
            "meal_id": "101",
            "menu_name": "두부 김치덮밥",
            "calories": 620,
            "price": 6500,
            "image_url": null
        },
        {
            "slot": 2,
            "meal_id": "002",
            "menu_name": "콩나물국",
            "calories": 550,
            "price": 3600,
            "image_url": null
        },
        {
            "slot": 3,
            "meal_id": "003",
            "menu_name": "제철 나물 비빔밥",
            "calories": 650,
            "price": 3600,
            "image_url": null
        }
    ]
}
```

- 응답 구조는 GET `/meal/{date}`와 동일.
- 백엔드는 plan.content[slot].alternative_menus에서 new_menu_id 매칭으로 새 메뉴 찾음.
- 데이터 전제: 식단 데이터에 반드시 alternative_menus 배열이 채워져 있어야 동작. request_monthly_plan이 저장하는 mock 데이터에 이게 포함돼있는지 확인 필요.

---

## GET `/meal/menus/{meal_id}/alternatives`

> **화면 10-3** — 메뉴변경

**Path:** `meal_id` (현재 메뉴)

**Query parameters (optional):**
- target_date: YYYY-MM-DD — 정확한 식단 검색용. 미지정 시 오늘 이후 가장 가까운 식단에서 검색.

**Response 200:**
```json
{
  "current_meal": {
    "meal_id": "001",
    "menu_name": "볶음밥",
    "calories": 650,
    "price": 3600,
    "image_url": null,
    "date": "2026-04-01",
    "slot": 1
  },
  "alternatives": [
    {
      "meal_id": "101",
      "menu_name": "두부 김치덮밥",
      "calories": 620,
      "price": 6500,
      "image_url": null
    },
    {
      "meal_id": "102",
      "menu_name": "야채 볶음밥",
      "calories": 580,
      "price": 5800,
      "image_url": null
    },
    {
      "meal_id": "103",
      "menu_name": "닭가슴살 샐러드",
      "calories": 450,
      "price": 9200,
      "image_url": null
    },
    {
      "meal_id": "104",
      "menu_name": "참치 김밥",
      "calories": 550,
      "price": 4500,
      "image_url": null
    }
  ]
}
```

- 백엔드는 DB의 식단에서 alternative_menus를 추출하여 반환.

---

# Shopping

## GET `/shopping/shopping-list`

> **화면 9-1** — 장보기

**Response 200:**
```json
{
    "total_items": 6,
    "checked_items_count": 4,
    "total_price_per_shopping": 17400,
    "market_counts": [
        {
            "market": "coupang",
            "count": 3
        },
        {
            "market": "market_kurly",
            "count": 0
        },
        {
            "market": "naver_shopping",
            "count": 1
        }
    ],
    "market_groups": [
        {
            "market": "coupang",
            "subtotal": 15600,
            "items": [
                {
                    "item_id": "item_001",
                    "ingredient_id": "I_001",
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
                    "ingredient_id": "I_002",
                    "ingredient_name": "계란 (10구)",
                    "standard_unit": "1팩",
                    "delivery_type": "로켓프레시",
                    "lowest_price": 4500,
                    "product_title": "신선란 10구",
                    "purchase_link": "https://www.coupang.com/vp/products/...",
                    "is_checked": true
                },
                {
                    "item_id": "item_004",
                    "ingredient_id": "I_004",
                    "ingredient_name": "닭가슴살",
                    "standard_unit": "200g x 5",
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
                    "ingredient_id": "I_003",
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
                    "ingredient_id": "I_005",
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
                    "ingredient_id": "I_006",
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

- `is_checked`는 서버 저장 상태. 재방문 시 복원.
- `purchase_link`는 외부 마켓 URL.

---

## POST `/shopping/add-shopping-items`

> **화면 10** 일일 식단 상세

**Request body:** 추가할 재료들의 배열. 최소 필드만 전송.
[
  { "ingredient_id": "I_001", "market_name": "coupang", "is_checked": true, "is_essential": true }
]

**Response 200:**
{ "message": "장보기 항목이 추가되었습니다.", "added_count": 2 }

- **백엔드 책임**: `ingredient_id` + `market_name` 만 받아 ingredient/마켓 정보를 
  자체 DB 에서 lookup 후 ShoppingItem 레코드 생성.
- 프론트는 응답을 사용하지 않음. 호출 후 `/shopping/shopping-list` GET 으로 
  갱신된 목록 받음.
- **TODO (백엔드)**: 현재 `IngredientSelectRequest` 스키마가 9개 필드 모두 필수.
  위 명세에 맞춰 `ingredient_name`, `standard_unit`, `price`, `delivery_type`, 
  `product_title`, `purchase_link` 6개를 optional 로 변경 + DB lookup 로직 추가 필요.
  
- 이번에 '이 날 장보기 추가' 버튼 연동 작업하다가 한 가지 요청 드릴 게 있어요.
POST /api/v1/shopping/add-shopping-items 의 IngredientSelectRequest 스키마에서 
필수 필드를 줄여주실 수 있을까요?
필수로 받을 것: ingredient_id, market_name, is_checked, is_essential
Optional 로 변경할 것: ingredient_name, standard_unit, price, delivery_type, product_title, purchase_link
이유: ingredient_id + market_name 만 받아도 백엔드 DB 에서 나머지 정보 lookup 가능하고,
책임 분리 측면에서 백엔드가 자기 DB 정보를 직접 가져가는 게 자연스러워 보여요. 
프론트가 모든 정보 다 들고 보내는 형태는 menu_detail 응답에 그 정보가 다 있어야 하는데 현재 응답 스키마와 안 맞기도 하고요.
프론트는 다음과 같이 보낼 예정입니다:
[
  { "ingredient_id": "I_001", "market_name": "coupang", "is_checked": true, "is_essential": true },
  { "ingredient_id": "I_002", "market_name": "market_kurly", "is_checked": true, "is_essential": true }
]

---

## PATCH `/shopping/shopping-list/items/check`

> **화면 9-1** — 장보기

**Request body:** 갱신할 항목들의 배열
```json
[
  { "item_id": "item_001", "is_checked": false }
]
```
- 단건 토글도 배열로 감싸서 전송. 여러 항목 동시 갱신 가능.

**Response 200:**
```json
{
  "updated_items": [
    { "item_id": "item_001", "is_checked": false }
  ],
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

- `summary` 구조는 GET `/shopping/shopping-list`의 최상위 합계 필드와 동일.

---

## DELETE `/shopping/shopping-list/items/batch-delete`

> **화면 9-1** — 장보기

**Request body:**
```json
{
  "item_ids": ["item_001", "item_002", "item_004", "item_006"]
}
```

**Response 200:**
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

---

## GET `/shopping/ingredients/{ingredient_id}/prices`

> **화면 10-2** — 재료 상세 화면

**Path:** `ingredient_id`

**Response 200:**

I_001
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
            "purchase_link": "https://www.coupang.com/products/123",
            "is_lowest": true
        },
        "market_kurly": {
            "delivery_type": "샛별배송",
            "lowest_price": 1400,
            "product_title": "컬리 제철 나물 모음 100g",
            "purchase_link": "https://www.kurly.com/goods/456",
            "is_lowest": false
        },
        "naver_shopping": {
            "delivery_type": null,
            "lowest_price": 1300,
            "product_title": "네이버 쇼핑 제철 나물 100g",
            "purchase_link": "https://shopping.naver.com/789",
            "is_lowest": false
        }
    }
}
```

I_002
```json
{
    "ingredient_id": "I_002",
    "ingredient_name": "계란",
    "standard_unit": "2구",
    "image_url": null,
    "e_commerce_prices": {
        "coupang": {
            "delivery_type": "로켓프레시",
            "lowest_price": 1200,
            "product_title": "곰곰 동물복지 유정란 2구",
            "purchase_link": "https://www.coupang.com/products/234",
            "is_lowest": true
        },
        "market_kurly": {
            "delivery_type": null,
            "lowest_price": null,
            "product_title": null,
            "purchase_link": null,
            "is_lowest": false
        },
        "naver_shopping": {
            "delivery_type": null,
            "lowest_price": 1300,
            "product_title": "네이버 유정란 2구",
            "purchase_link": "https://shopping.naver.com/890",
            "is_lowest": false
        }
    }
}
```

I_003
```json
{
    "ingredient_id": "I_003",
    "ingredient_name": "현미밥",
    "standard_unit": "200g",
    "image_url": null,
    "e_commerce_prices": {
        "coupang": {
            "delivery_type": "로켓배송",
            "lowest_price": 1200,
            "product_title": "곰곰 즉석 현미밥 200g",
            "purchase_link": "https://www.coupang.com/products/345",
            "is_lowest": false
        },
        "market_kurly": {
            "delivery_type": "샛별배송",
            "lowest_price": 1400,
            "product_title": "컬리 무농약 현미밥 200g",
            "purchase_link": "https://www.kurly.com/goods/567",
            "is_lowest": false
        },
        "naver_shopping": {
            "delivery_type": null,
            "lowest_price": 1100,
            "product_title": "네이버 쇼핑 현미밥 200g",
            "purchase_link": "https://shopping.naver.com/901",
            "is_lowest": true
        }
    }
}
```

I_004
```json
{
    "ingredient_id": "I_004",
    "ingredient_name": "양파",
    "standard_unit": "1개",
    "image_url": null,
    "e_commerce_prices": {
        "coupang": {
            "delivery_type": "로켓프레시",
            "lowest_price": 800,
            "product_title": "곰곰 햇양파 1개",
            "purchase_link": "https://www.coupang.com/products/456",
            "is_lowest": true
        },
        "market_kurly": {
            "delivery_type": "샛별배송",
            "lowest_price": 1000,
            "product_title": "컬리 친환경 양파 1개",
            "purchase_link": "https://www.kurly.com/goods/678",
            "is_lowest": false
        },
        "naver_shopping": {
            "delivery_type": null,
            "lowest_price": 900,
            "product_title": "네이버 쇼핑 양파 1개",
            "purchase_link": "https://shopping.naver.com/012",
            "is_lowest": false
        }
    }
}
```

I_005
```json
{
    "ingredient_id": "I_005",
    "ingredient_name": "당근",
    "standard_unit": "1개",
    "image_url": null,
    "e_commerce_prices": {
        "coupang": {
            "delivery_type": "로켓프레시",
            "lowest_price": 800,
            "product_title": "곰곰 당근 1개",
            "purchase_link": "https://www.coupang.com/products/567",
            "is_lowest": false
        },
        "market_kurly": {
            "delivery_type": null,
            "lowest_price": null,
            "product_title": null,
            "purchase_link": null,
            "is_lowest": false
        },
        "naver_shopping": {
            "delivery_type": null,
            "lowest_price": 700,
            "product_title": "네이버 쇼핑 당근 1개",
            "purchase_link": "https://shopping.naver.com/123",
            "is_lowest": true
        }
    }
}
```

I_006
```json
{
    "ingredient_id": "I_006",
    "ingredient_name": "콩나물",
    "standard_unit": "300g",
    "image_url": null,
    "e_commerce_prices": {
        "coupang": {
            "delivery_type": "로켓프레시",
            "lowest_price": 1700,
            "product_title": "곰곰 무농약 콩나물 300g",
            "purchase_link": "https://www.coupang.com/products/678",
            "is_lowest": false
        },
        "market_kurly": {
            "delivery_type": "샛별배송",
            "lowest_price": 1500,
            "product_title": "컬리 콩나물 300g",
            "purchase_link": "https://www.kurly.com/goods/789",
            "is_lowest": true
        },
        "naver_shopping": {
            "delivery_type": null,
            "lowest_price": 1600,
            "product_title": "네이버 쇼핑 콩나물 300g",
            "purchase_link": "https://shopping.naver.com/234",
            "is_lowest": false
        }
    }
}
```

I_007
```json
{
    "ingredient_id": "I_007",
    "ingredient_name": "대파",
    "standard_unit": "1대",
    "image_url": null,
    "e_commerce_prices": {
        "coupang": {
            "delivery_type": "로켓프레시",
            "lowest_price": 1200,
            "product_title": "곰곰 국내산 대파 1대",
            "purchase_link": "https://www.coupang.com/products/789",
            "is_lowest": true
        },
        "market_kurly": {
            "delivery_type": "샛별배송",
            "lowest_price": 1400,
            "product_title": "컬리 친환경 대파 1대",
            "purchase_link": "https://www.kurly.com/goods/890",
            "is_lowest": false
        },
        "naver_shopping": {
            "delivery_type": null,
            "lowest_price": 1300,
            "product_title": "네이버 쇼핑 대파 1대",
            "purchase_link": "https://shopping.naver.com/345",
            "is_lowest": false
        }
    }
}
```

- `is_lowest`: 마켓 간 비교에서 최저가 여부. 서버 계산.

---
