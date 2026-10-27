# 코딩 스타일

## 네이밍 규칙

### 형식

```
[범위] + [종류...] + _ + PascalCase
```

```c
g + ap + _ + PcmBuf  ->  gap_PcmBuf
```

- 범위 접두어가 가장 앞에 오며, 최대 1개만 씁니다.
- 종류 접두어는 조합할 수 있습니다. **마지막 글자가 실제 정체입니다.**
- `_` 뒤의 이름 본체는 PascalCase로 씁니다.

---

### 1. 범위 (Scope)

| 접두어 | 의미           | 예시                        |
|--------|----------------|-----------------------------|
| `g`    | 파일 static    | `static uint8_t g_Count;`   |
| `x`    | extern 전역    | `extern uint8_t x_Count;`   |
| `l`    | 함수 내 static | `static uint8_t l_Count;`   |
| (없음) | 지역 변수, 함수 인자, 구조체 멤버 | `uint8_t v_Idx;` |

---

### 2. 종류 (Kind)

| 접두어 | 의미                                  |
|--------|---------------------------------------|
| `a`    | 배열                                  |
| `p`    | 포인터                                |
| `c`    | 상수 (const)                          |
| `f`    | 함수                                  |
| `s`    | 구조체 변수                           |
| `u`    | 공용체 변수                           |
| `e`    | 열거형 변수                           |
| `b`    | 비트필드 멤버                         |
| `v`    | 일반 변수 (다른 접두어가 없을 때만)   |

#### 조합

한국어로 읽는 순서대로 쓰며, 마지막 글자가 실제 종류입니다.

| 접두어 | 의미                                   | 예시                              |
|--------|----------------------------------------|-----------------------------------|
| `ap_`  | 배열 포인터 (배열을 가리키는 포인터)   | `uint16_t (*ap_Buf)[256];`        |
| `pa_`  | 포인터 배열 (포인터들의 배열)          | `uint16_t *pa_Buf[4];`            |
| `pp_`  | 포인터의 포인터                        | `uint8_t **pp_Data;`              |
| `fp_`  | 함수 포인터                            | `void (*fp_Cb)(void);`            |
| `fpa_` | 함수 포인터 배열                       | `void (*fpa_Isr[8])(void);`       |
| `sp_`  | 구조체 포인터                          | `ts_Audio *sp_Audio;`             |
| `sa_`  | 구조체 배열                            | `ts_Audio sa_Ch[2];`              |
| `ca_`  | 상수 배열                              | `const uint8_t ca_Tbl[16];`       |
| `cp_`  | 상수 포인터 (상수를 가리키는 포인터)   | `const uint8_t *cp_Data;`         |
| `pc_`  | 포인터 상수 (주소가 고정된 포인터)     | `uint8_t *const pc_Reg;`          |

---

### 3. 타입 (Typedef)

타입 이름은 `t` + 종류로 씁니다.

| 접두어 | 의미                 | 예시                                 |
|--------|----------------------|--------------------------------------|
| `ts_`  | 구조체 타입          | `typedef struct { ... } ts_Audio;`   |
| `te_`  | 열거형 타입          | `typedef enum { ... } te_State;`     |
| `tu_`  | 공용체 타입          | `typedef union { ... } tu_Word;`     |
| `ta_`  | 배열 타입            | `typedef uint8_t ta_Mac[6];`         |
| `tfp_` | 함수 포인터 타입     | `typedef void (*tfp_Cb)(void);`      |
| `t_`   | 기본 타입 별칭       | `typedef uint16_t t_Sample;`         |

타입과 변수의 짝:

| 타입       | 변수                                          |
|------------|-----------------------------------------------|
| `ts_Audio` | `s_Audio`, `gs_Audio`, `sp_Audio`, `sa_Audio` |
| `te_State` | `e_State`, `ge_State`                         |
| `tu_Word`  | `u_Word`, `gu_Word`                           |
| `tfp_Cb`   | `fp_Cb`, `gfpa_Cb[4]`                         |

> 일반 포인터는 typedef로 만들지 않습니다 (예: `typedef uint8_t *tp_Buf;`).
> 변수 이름에서 포인터라는 게 드러나지 않고 `const` 위치가 헷갈리기 때문입니다.
> 함수 포인터 타입(`tfp_`)만 예외로 허용합니다.

---

### 4. 단독 접두어

다른 접두어와 조합하지 않습니다.

| 접두어 | 의미         | 예시                                     |
|--------|--------------|------------------------------------------|
| `d_`   | 매크로       | `#define d_BufSize 512`                  |
| `f_`   | 함수         | `void f_AudioPlay(void);`                |
| `i_`   | inline 함수  | `static inline uint8_t i_IsBusy(void);`  |
| `m_`   | 열거형 멤버  | `m_Idle`, `m_Play`                       |

---

### 5. 우선순위

1. **범위** (`g` / `x` / `l`) — 항상 가장 앞
2. **종류** (`a`, `p`, `c`, `f`, `s`, `u`, `e`) — 조합 가능, 마지막 글자가 실제 종류
3. **`v`** — 다른 접두어가 하나도 없을 때만

```c
static uint16_t ga_Pcm[512];   /* OK  */
static uint16_t gva_Pcm[512];  /* NG : v 불필요 */
```

---

### 6. 종합 예시

```c
/* 타입 */
typedef enum { m_Idle, m_Play, m_Stop } te_State;
typedef void (*tfp_Cb)(void);

typedef struct {
    te_State   e_State;
    uint8_t    b_Mute : 1;
    uint8_t    b_Loop : 1;
    uint16_t  *p_Pcm;
    tfp_Cb     fp_Done;
} ts_Audio;

/* 파일 static */
static ts_Audio        gs_Audio;
static ts_Audio       *gsp_Cur;
static uint16_t        ga_Pcm[512];
static uint16_t      (*gap_Half)[256];
static const uint8_t   gca_VolTbl[16];
static tfp_Cb          gfpa_Cb[4];

/* extern */
extern const uint16_t  xca_PcmData[];

/* 함수 */
void f_AudioPlay(uint16_t *p_Src, uint32_t v_Len)
{
    static uint32_t l_PlayCnt;
    uint32_t        v_Idx;
    te_State        e_Prev = gs_Audio.e_State;
}
```

---
