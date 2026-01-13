# 카카오 i 오픈빌더 스킬서버 응답 타입 레퍼런스

> **Version**: SkillResponse v2.0
> **최종 업데이트**: 2026-01-10

## 목차

1. [기본 구조](#1-기본-구조)
2. [출력 타입 (Outputs)](#2-출력-타입-outputs)
   - [SimpleText](#21-simpletext)
   - [SimpleImage](#22-simpleimage)
   - [BasicCard](#23-basiccard)
   - [CommerceCard](#24-commercecard)
   - [ListCard](#25-listcard)
   - [ItemCard](#26-itemcard)
   - [Carousel](#27-carousel)
3. [QuickReply (바로연결)](#3-quickreply-바로연결)
4. [Context (컨텍스트 제어)](#4-context-컨텍스트-제어)
5. [Data (커스텀 데이터)](#5-data-커스텀-데이터)
6. [버튼 액션 타입](#6-버튼-액션-타입)
7. [제약사항 요약](#7-제약사항-요약)

---

## 1. 기본 구조

모든 스킬 응답은 다음과 같은 기본 구조를 따릅니다.

```json
{
  "version": "2.0",
  "template": {
    "outputs": [],
    "quickReplies": []
  },
  "context": {
    "values": []
  },
  "data": {}
}
```

### 필드 설명

| 필드 | 타입 | 필수 | 설명 |
|------|------|------|------|
| `version` | string | ✓ | 응답 포맷 버전 (항상 "2.0") |
| `template.outputs` | array | ✓ | 출력할 컴포넌트 배열 (최대 3개) |
| `template.quickReplies` | array |  | 바로연결 버튼 배열 (최대 10개) |
| `context.values` | array |  | 컨텍스트 데이터 배열 |
| `data` | object |  | 커스텀 데이터 (클라이언트로 전달) |

---

## 2. 출력 타입 (Outputs)

### 2.1. SimpleText

**용도**: 간단한 텍스트 메시지 전달

**사용 사례**:
- 챗봇의 기본 응답
- 안내 메시지
- 에러 메시지

**제약사항**:
- 최대 **1,000자**
- 줄바꿈은 `\n` 사용

**JSON 예시**:
```json
{
  "version": "2.0",
  "template": {
    "outputs": [
      {
        "simpleText": {
          "text": "안녕하세요! 무엇을 도와드릴까요?"
        }
      }
    ]
  }
}
```

---

### 2.2. SimpleImage

**용도**: 단순 이미지 출력

**사용 사례**:
- 이미지만 전달하는 경우
- 인포그래픽 공유
- QR 코드 제공

**제약사항**:
- 이미지 URL은 **HTTPS**만 지원
- 권장 비율: **800x600** (4:3) 또는 **800x800** (1:1)
- 최대 파일 크기: **5MB**

**JSON 예시**:
```json
{
  "version": "2.0",
  "template": {
    "outputs": [
      {
        "simpleImage": {
          "imageUrl": "https://example.com/image.jpg",
          "altText": "이미지 설명"
        }
      }
    ]
  }
}
```

**필드 설명**:

| 필드 | 타입 | 필수 | 설명 |
|------|------|------|------|
| `imageUrl` | string | ✓ | 이미지 URL (HTTPS) |
| `altText` | string | ✓ | 대체 텍스트 (접근성) |

---

### 2.3. BasicCard

**용도**: 제목, 설명, 썸네일, 버튼을 포함한 카드형 메시지

**사용 사례**:
- 상품/서비스 소개
- 블로그 포스트 미리보기
- 뉴스 기사 요약
- 이벤트 안내

**제약사항**:
- 제목(`title`): 최대 **50자**
- 설명(`description`): 최대 **230자**
- 버튼: 최대 **3개**
- 버튼 라벨: 최대 **14자**

**JSON 예시**:
```json
{
  "version": "2.0",
  "template": {
    "outputs": [
      {
        "basicCard": {
          "title": "카카오 i 오픈빌더",
          "description": "누구나 쉽게 챗봇을 만들 수 있는 카카오의 챗봇 빌더 플랫폼입니다.",
          "thumbnail": {
            "imageUrl": "https://example.com/thumbnail.jpg"
          },
          "buttons": [
            {
              "action": "webLink",
              "label": "자세히 보기",
              "webLinkUrl": "https://i.kakao.com"
            },
            {
              "action": "message",
              "label": "문의하기",
              "messageText": "문의하기"
            }
          ]
        }
      }
    ]
  }
}
```

**필드 설명**:

| 필드 | 타입 | 필수 | 설명 |
|------|------|------|------|
| `title` | string |  | 카드 제목 (최대 50자) |
| `description` | string |  | 카드 설명 (최대 230자) |
| `thumbnail` | object |  | 썸네일 이미지 |
| `thumbnail.imageUrl` | string | ✓ | 썸네일 URL (HTTPS) |
| `thumbnail.link` | object |  | 썸네일 클릭 링크 |
| `buttons` | array |  | 버튼 배열 (최대 3개) |

---

### 2.4. CommerceCard

**용도**: 가격 정보를 포함한 상품 카드

**사용 사례**:
- 쇼핑몰 상품 소개
- 가격 비교
- 할인/프로모션 안내

**제약사항**:
- 제목(`title`): 최대 **50자**
- 설명(`description`): 최대 **230자**
- 가격(`price`): 숫자 (원 단위)
- 버튼: 최대 **3개**

**JSON 예시**:
```json
{
  "version": "2.0",
  "template": {
    "outputs": [
      {
        "commerceCard": {
          "title": "카카오프렌즈 라이언 인형",
          "description": "폭신폭신한 라이언 인형입니다.",
          "price": 29900,
          "discount": 5000,
          "currency": "won",
          "thumbnails": [
            {
              "imageUrl": "https://example.com/product1.jpg",
              "link": {
                "web": "https://example.com/product/1"
              }
            }
          ],
          "buttons": [
            {
              "action": "webLink",
              "label": "구매하기",
              "webLinkUrl": "https://example.com/buy/1"
            }
          ]
        }
      }
    ]
  }
}
```

**필드 설명**:

| 필드 | 타입 | 필수 | 설명 |
|------|------|------|------|
| `title` | string | ✓ | 상품명 (최대 50자) |
| `description` | string | ✓ | 상품 설명 (최대 230자) |
| `price` | number | ✓ | 정가 (원 단위) |
| `discount` | number |  | 할인 금액 (원 단위) |
| `discountRate` | number |  | 할인율 (%) |
| `discountedPrice` | number |  | 할인된 가격 |
| `currency` | string | ✓ | 통화 단위 ("won") |
| `thumbnails` | array | ✓ | 썸네일 배열 (최대 1개) |
| `buttons` | array |  | 버튼 배열 (최대 3개) |

---

### 2.5. ListCard

**용도**: 여러 항목을 리스트 형태로 표시

**사용 사례**:
- 검색 결과 목록
- 메뉴 리스트
- FAQ 목록
- 순위 정보

**제약사항**:
- 헤더 제목(`header.title`): 최대 **50자**
- 아이템(`items`): 최대 **5개**
- 아이템 제목(`title`): 최대 **50자**
- 아이템 설명(`description`): 최대 **230자**
- 버튼: 최대 **2개**

**JSON 예시**:
```json
{
  "version": "2.0",
  "template": {
    "outputs": [
      {
        "listCard": {
          "header": {
            "title": "인기 메뉴 TOP 3",
            "imageUrl": "https://example.com/header.jpg"
          },
          "items": [
            {
              "title": "아메리카노",
              "description": "진한 에스프레소의 풍미",
              "imageUrl": "https://example.com/americano.jpg",
              "link": {
                "web": "https://example.com/menu/americano"
              }
            },
            {
              "title": "카페라떼",
              "description": "부드러운 우유와 에스프레소",
              "imageUrl": "https://example.com/latte.jpg",
              "link": {
                "web": "https://example.com/menu/latte"
              }
            },
            {
              "title": "카푸치노",
              "description": "풍부한 우유 거품",
              "imageUrl": "https://example.com/cappuccino.jpg",
              "link": {
                "web": "https://example.com/menu/cappuccino"
              }
            }
          ],
          "buttons": [
            {
              "action": "webLink",
              "label": "전체 메뉴",
              "webLinkUrl": "https://example.com/menu"
            }
          ]
        }
      }
    ]
  }
}
```

**필드 설명**:

| 필드 | 타입 | 필수 | 설명 |
|------|------|------|------|
| `header.title` | string | ✓ | 헤더 제목 (최대 50자) |
| `header.imageUrl` | string |  | 헤더 이미지 URL |
| `items` | array | ✓ | 아이템 배열 (최대 5개) |
| `items[].title` | string | ✓ | 아이템 제목 (최대 50자) |
| `items[].description` | string |  | 아이템 설명 (최대 230자) |
| `items[].imageUrl` | string |  | 아이템 이미지 URL |
| `items[].link` | object |  | 아이템 클릭 링크 |
| `buttons` | array |  | 버튼 배열 (최대 2개) |

---

### 2.6. ItemCard

**용도**: 프로필/아이템 정보를 키-밸류 형태로 표시

**사용 사례**:
- 주문 정보 확인
- 배송 현황
- 사용자 프로필
- 예약 정보

**제약사항**:
- 제목(`title`): 최대 **50자**
- 설명(`description`): 최대 **230자**
- 아이템 리스트(`itemList`): 최대 **5개**
- 버튼: 최대 **3개**

**JSON 예시**:
```json
{
  "version": "2.0",
  "template": {
    "outputs": [
      {
        "itemCard": {
          "thumbnail": {
            "imageUrl": "https://example.com/order.jpg"
          },
          "head": {
            "title": "주문 정보"
          },
          "itemList": [
            {
              "title": "주문번호",
              "description": "20240110-123456"
            },
            {
              "title": "상품명",
              "description": "카카오프렌즈 라이언 인형"
            },
            {
              "title": "수량",
              "description": "1개"
            },
            {
              "title": "금액",
              "description": "29,900원"
            },
            {
              "title": "배송상태",
              "description": "배송중"
            }
          ],
          "buttons": [
            {
              "action": "webLink",
              "label": "배송조회",
              "webLinkUrl": "https://example.com/delivery/123456"
            }
          ]
        }
      }
    ]
  }
}
```

**필드 설명**:

| 필드 | 타입 | 필수 | 설명 |
|------|------|------|------|
| `thumbnail` | object |  | 썸네일 이미지 |
| `head.title` | string | ✓ | 카드 제목 (최대 50자) |
| `itemList` | array | ✓ | 키-밸류 아이템 배열 (최대 5개) |
| `itemList[].title` | string | ✓ | 키 (최대 50자) |
| `itemList[].description` | string | ✓ | 값 (최대 230자) |
| `buttons` | array |  | 버튼 배열 (최대 3개) |

---

### 2.7. Carousel

**용도**: 여러 카드를 가로 슬라이드 형태로 표시

**사용 사례**:
- 여러 상품 비교
- 추천 상품 목록
- 다양한 옵션 제공
- 이벤트 모음

**제약사항**:
- 카드 개수: 최대 **10개**
- 각 카드는 `BasicCard`, `CommerceCard` 타입만 지원
- 모든 카드는 **동일한 타입**이어야 함

**JSON 예시 (BasicCard Carousel)**:
```json
{
  "version": "2.0",
  "template": {
    "outputs": [
      {
        "carousel": {
          "type": "basicCard",
          "items": [
            {
              "title": "상품 A",
              "description": "상품 A 설명입니다.",
              "thumbnail": {
                "imageUrl": "https://example.com/product-a.jpg"
              },
              "buttons": [
                {
                  "action": "webLink",
                  "label": "자세히 보기",
                  "webLinkUrl": "https://example.com/product/a"
                }
              ]
            },
            {
              "title": "상품 B",
              "description": "상품 B 설명입니다.",
              "thumbnail": {
                "imageUrl": "https://example.com/product-b.jpg"
              },
              "buttons": [
                {
                  "action": "webLink",
                  "label": "자세히 보기",
                  "webLinkUrl": "https://example.com/product/b"
                }
              ]
            },
            {
              "title": "상품 C",
              "description": "상품 C 설명입니다.",
              "thumbnail": {
                "imageUrl": "https://example.com/product-c.jpg"
              },
              "buttons": [
                {
                  "action": "webLink",
                  "label": "자세히 보기",
                  "webLinkUrl": "https://example.com/product/c"
                }
              ]
            }
          ]
        }
      }
    ]
  }
}
```

**JSON 예시 (CommerceCard Carousel)**:
```json
{
  "version": "2.0",
  "template": {
    "outputs": [
      {
        "carousel": {
          "type": "commerceCard",
          "items": [
            {
              "title": "상품 A",
              "description": "상품 A 설명",
              "price": 10000,
              "currency": "won",
              "thumbnails": [
                {
                  "imageUrl": "https://example.com/product-a.jpg"
                }
              ]
            },
            {
              "title": "상품 B",
              "description": "상품 B 설명",
              "price": 20000,
              "discount": 5000,
              "currency": "won",
              "thumbnails": [
                {
                  "imageUrl": "https://example.com/product-b.jpg"
                }
              ]
            }
          ]
        }
      }
    ]
  }
}
```

**필드 설명**:

| 필드 | 타입 | 필수 | 설명 |
|------|------|------|------|
| `type` | string | ✓ | 카드 타입 ("basicCard" 또는 "commerceCard") |
| `items` | array | ✓ | 카드 배열 (최대 10개) |

---

## 3. QuickReply (바로연결)

**용도**: 사용자에게 빠른 응답 옵션 제공

**사용 사례**:
- 자주 묻는 질문 선택지
- 다음 액션 가이드
- 메뉴 네비게이션
- 예/아니오 선택

**제약사항**:
- 최대 **10개**
- 라벨(`label`): 최대 **14자**
- 메시지(`messageText`): 최대 **1,000자**

**JSON 예시**:
```json
{
  "version": "2.0",
  "template": {
    "outputs": [
      {
        "simpleText": {
          "text": "무엇을 도와드릴까요?"
        }
      }
    ],
    "quickReplies": [
      {
        "action": "message",
        "label": "주문 조회",
        "messageText": "주문 조회"
      },
      {
        "action": "message",
        "label": "배송 조회",
        "messageText": "배송 조회"
      },
      {
        "action": "message",
        "label": "고객센터",
        "messageText": "고객센터 연결"
      },
      {
        "action": "block",
        "label": "처음으로",
        "blockId": "home_block_id"
      }
    ]
  }
}
```

**필드 설명**:

| 필드 | 타입 | 필수 | 설명 |
|------|------|------|------|
| `action` | string | ✓ | 액션 타입 ("message", "block") |
| `label` | string | ✓ | 버튼 라벨 (최대 14자) |
| `messageText` | string |  | 전송할 메시지 (action="message"일 때 필수) |
| `blockId` | string |  | 이동할 블록 ID (action="block"일 때 필수) |
| `extra` | object |  | 추가 파라미터 |

---

## 4. Context (컨텍스트 제어)

**용도**: 대화 흐름에서 데이터 유지 및 전달

**사용 사례**:
- 사용자 입력 값 저장
- 다단계 대화 상태 관리
- 블록 간 데이터 전달
- 세션 정보 유지

**제약사항**:
- 컨텍스트 이름(`name`): 최대 **50자**
- 유효 기간(`lifeSpan`): 0 (삭제), 1~100 (유지 턴 수)
- TTL(`ttl`): 0~86400 (초 단위, 최대 24시간)

**JSON 예시 (컨텍스트 저장)**:
```json
{
  "version": "2.0",
  "template": {
    "outputs": [
      {
        "simpleText": {
          "text": "주문이 접수되었습니다."
        }
      }
    ]
  },
  "context": {
    "values": [
      {
        "name": "order_info",
        "lifeSpan": 10,
        "params": {
          "order_id": "20240110-123456",
          "product_name": "카카오프렌즈 라이언 인형",
          "quantity": 1,
          "total_price": 29900
        }
      }
    ]
  }
}
```

**JSON 예시 (컨텍스트 삭제)**:
```json
{
  "version": "2.0",
  "template": {
    "outputs": [
      {
        "simpleText": {
          "text": "주문이 완료되었습니다."
        }
      }
    ]
  },
  "context": {
    "values": [
      {
        "name": "order_info",
        "lifeSpan": 0
      }
    ]
  }
}
```

**필드 설명**:

| 필드 | 타입 | 필수 | 설명 |
|------|------|------|------|
| `name` | string | ✓ | 컨텍스트 이름 (최대 50자) |
| `lifeSpan` | number | ✓ | 유효 턴 수 (0=삭제, 1~100=유지) |
| `ttl` | number |  | 유효 시간 (초, 0~86400) |
| `params` | object |  | 저장할 데이터 (키-밸류) |

---

## 5. Data (커스텀 데이터)

**용도**: 클라이언트(앱/웹)로 커스텀 데이터 전달

**사용 사례**:
- 앱에서 특정 화면으로 이동
- 클라이언트 사이드 로직 처리
- 분석/로깅 데이터 전달
- 외부 시스템 연동 정보

**제약사항**:
- JSON 직렬화 가능한 객체
- 크기 제한: 응답 전체 크기 제한 내에서 사용

**JSON 예시**:
```json
{
  "version": "2.0",
  "template": {
    "outputs": [
      {
        "simpleText": {
          "text": "상품 상세 페이지로 이동합니다."
        }
      }
    ]
  },
  "data": {
    "type": "navigation",
    "screen": "product_detail",
    "product_id": "12345",
    "analytics": {
      "event": "view_product",
      "category": "shopping"
    }
  }
}
```

**사용 방법**:
- 클라이언트에서 `data` 필드를 파싱하여 처리
- 카카오톡 채팅창에는 표시되지 않음
- 앱/웹에서만 접근 가능

---

## 6. 버튼 액션 타입

버튼은 `BasicCard`, `CommerceCard`, `ListCard`, `ItemCard` 등에서 사용됩니다.

### 6.1. webLink (웹 링크)

**용도**: 외부 웹 페이지로 이동

```json
{
  "action": "webLink",
  "label": "자세히 보기",
  "webLinkUrl": "https://example.com"
}
```

**필드**:
- `webLinkUrl`: HTTPS URL (필수)

---

### 6.2. message (메시지 전송)

**용도**: 특정 메시지를 자동으로 전송

```json
{
  "action": "message",
  "label": "문의하기",
  "messageText": "고객센터 연결 부탁드립니다."
}
```

**필드**:
- `messageText`: 전송할 메시지 (필수)

---

### 6.3. phone (전화 걸기)

**용도**: 전화번호로 전화 걸기

```json
{
  "action": "phone",
  "label": "전화 상담",
  "phoneNumber": "02-1234-5678"
}
```

**필드**:
- `phoneNumber`: 전화번호 (필수)

---

### 6.4. block (블록 이동)

**용도**: 특정 블록으로 이동 (시나리오 흐름 제어)

```json
{
  "action": "block",
  "label": "처음으로",
  "blockId": "home_block_id",
  "extra": {
    "key1": "value1"
  }
}
```

**필드**:
- `blockId`: 이동할 블록 ID (필수)
- `extra`: 블록에 전달할 추가 파라미터

---

### 6.5. share (공유하기)

**용도**: 카카오톡 공유

```json
{
  "action": "share",
  "label": "친구에게 공유"
}
```

---

### 6.6. operator (상담원 연결)

**용도**: 상담원 채팅으로 전환

```json
{
  "action": "operator",
  "label": "상담원 연결"
}
```

---

## 7. 제약사항 요약

### 7.1. 공통 제약사항

| 항목 | 제한 |
|------|------|
| 응답 타임아웃 | **5초** |
| `outputs` 개수 | 최대 **3개** |
| `quickReplies` 개수 | 최대 **10개** |
| 이미지 URL | **HTTPS**만 지원 |
| 이미지 파일 크기 | 최대 **5MB** |
| 버튼 라벨 | 최대 **14자** |
| QuickReply 라벨 | 최대 **14자** |

### 7.2. 출력 타입별 제약사항

#### SimpleText
| 항목 | 제한 |
|------|------|
| 텍스트 길이 | 최대 **1,000자** |

#### BasicCard
| 항목 | 제한 |
|------|------|
| 제목 | 최대 **50자** |
| 설명 | 최대 **230자** |
| 버튼 개수 | 최대 **3개** |

#### CommerceCard
| 항목 | 제한 |
|------|------|
| 제목 | 최대 **50자** |
| 설명 | 최대 **230자** |
| 썸네일 개수 | 최대 **1개** |
| 버튼 개수 | 최대 **3개** |

#### ListCard
| 항목 | 제한 |
|------|------|
| 헤더 제목 | 최대 **50자** |
| 아이템 개수 | 최대 **5개** |
| 아이템 제목 | 최대 **50자** |
| 아이템 설명 | 최대 **230자** |
| 버튼 개수 | 최대 **2개** |

#### ItemCard
| 항목 | 제한 |
|------|------|
| 제목 | 최대 **50자** |
| itemList 개수 | 최대 **5개** |
| itemList 키(title) | 최대 **50자** |
| itemList 값(description) | 최대 **230자** |
| 버튼 개수 | 최대 **3개** |

#### Carousel
| 항목 | 제한 |
|------|------|
| 카드 개수 | 최대 **10개** |
| 지원 카드 타입 | BasicCard, CommerceCard만 지원 |
| 카드 타입 | 모든 카드가 동일한 타입이어야 함 |

### 7.3. Context 제약사항

| 항목 | 제한 |
|------|------|
| 컨텍스트 이름 | 최대 **50자** |
| lifeSpan | 0 (삭제) 또는 1~100 (턴 수) |
| ttl | 0~86400 (초, 최대 24시간) |

---

## 참고 자료

- [카카오 i 오픈빌더 공식 문서](https://i.kakao.com/docs)
- [SkillResponse v2.0 API Reference](https://i.kakao.com/docs/skill-response-format)

---

**문서 버전**: 1.0.0
**작성일**: 2026-01-10
**라이선스**: MIT
