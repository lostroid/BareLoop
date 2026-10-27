# 코딩 스타일

## 네이밍 규칙

### 형식

```
[범위] + [종류...] + _ + snake_case
```

```c
g + ap + _ + pcm_buf  ->  gap_pcm_buf
```

- 범위 접두어가 가장 앞에 오며, 최대 1개만 씁니다.
- 종류 접두어는 조합할 수 있습니다. **마지막 글자가 실제 정체입니다.**
- `_` 뒤의 이름 본체는 snake_case로 씁니다. 단, 매크로(`d_`)는 대문자 SNAKE_CASE로 씁니다 (예: `d_BUF_SIZE`).

---

### 1. 범위 (Scope)

| 접두어 | 의미           | 예시                        |
|--------|----------------|-----------------------------|
| `g`    | 파일 static    | `static uint8_t g_count;`   |
| `x`    | extern 전역    | `extern uint8_t x_count;`   |
| `l`    | 함수 내 static | `static uint8_t l_count;`   |
| (없음) | 지역 변수, 함수 인자, 구조체 멤버 | `uint8_t v_idx;` |

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
| `ap_`  | 배열 포인터 (배열을 가리키는 포인터)   | `uint16_t (*ap_buf)[256];`        |
| `pa_`  | 포인터 배열 (포인터들의 배열)          | `uint16_t *pa_buf[4];`            |
| `pp_`  | 포인터의 포인터                        | `uint8_t **pp_data;`              |
| `fp_`  | 함수 포인터                            | `void (*fp_cb)(void);`            |
| `fpa_` | 함수 포인터 배열                       | `void (*fpa_isr[8])(void);`       |
| `sp_`  | 구조체 포인터                          | `ts_audio *sp_audio;`             |
| `sa_`  | 구조체 배열                            | `ts_audio sa_ch[2];`              |
| `ca_`  | 상수 배열                              | `const uint8_t ca_tbl[16];`       |
| `cp_`  | 상수 포인터 (상수를 가리키는 포인터)   | `const uint8_t *cp_data;`         |
| `pc_`  | 포인터 상수 (주소가 고정된 포인터)     | `uint8_t *const pc_reg;`          |

---

### 3. 타입 (Typedef)

타입 이름은 `t` + 종류로 씁니다.

| 접두어 | 의미                 | 예시                                 |
|--------|----------------------|--------------------------------------|
| `ts_`  | 구조체 타입          | `typedef struct { ... } ts_audio;`   |
| `te_`  | 열거형 타입          | `typedef enum { ... } te_state;`     |
| `tu_`  | 공용체 타입          | `typedef union { ... } tu_word;`     |
| `ta_`  | 배열 타입            | `typedef uint8_t ta_mac[6];`         |
| `tfp_` | 함수 포인터 타입     | `typedef void (*tfp_cb)(void);`      |
| `t_`   | 기본 타입 별칭       | `typedef uint16_t t_sample;`         |

타입과 변수의 짝:

| 타입       | 변수                                          |
|------------|-----------------------------------------------|
| `ts_audio` | `s_audio`, `gs_audio`, `sp_audio`, `sa_audio` |
| `te_state` | `e_state`, `ge_state`                         |
| `tu_word`  | `u_word`, `gu_word`                           |
| `tfp_cb`   | `fp_cb`, `gfpa_cb[4]`                         |

> 일반 포인터는 typedef로 만들지 않습니다 (예: `typedef uint8_t *tp_buf;`).
> 변수 이름에서 포인터라는 게 드러나지 않고 `const` 위치가 헷갈리기 때문입니다.
> 함수 포인터 타입(`tfp_`)만 예외로 허용합니다.

---

### 4. 단독 접두어

다른 접두어와 조합하지 않습니다.

| 접두어 | 의미         | 예시                                     |
|--------|--------------|------------------------------------------|
| `d_`   | 매크로       | `#define d_BUF_SIZE 512`                 |
| `f_`   | 함수         | `void f_audio_play(void);`               |
| `i_`   | inline 함수  | `static inline uint8_t i_is_busy(void);` |
| `m_`   | 열거형 멤버  | `m_idle`, `m_play`                       |

---

### 5. 우선순위

1. **범위** (`g` / `x` / `l`) — 항상 가장 앞
2. **종류** (`a`, `p`, `c`, `f`, `s`, `u`, `e`) — 조합 가능, 마지막 글자가 실제 종류
3. **`v`** — 다른 접두어가 하나도 없을 때만

```c
static uint16_t ga_pcm[512];   /* OK  */
static uint16_t gva_pcm[512];  /* NG : v 불필요 */
```

---

### 6. 종합 예시

```c
/* 타입 */
typedef enum { m_idle, m_play, m_stop } te_state;
typedef void (*tfp_cb)(void);

typedef struct {
    te_state   e_state;
    uint8_t    b_mute : 1;
    uint8_t    b_loop : 1;
    uint16_t  *p_pcm;
    tfp_cb     fp_done;
} ts_audio;

/* 파일 static */
static ts_audio        gs_audio;
static ts_audio       *gsp_cur;
static uint16_t        ga_pcm[512];
static uint16_t      (*gap_half)[256];
static const uint8_t   gca_vol_tbl[16];
static tfp_cb          gfpa_cb[4];

/* extern */
extern const uint16_t  xca_pcm_data[];

/* 함수 */
void f_audio_play(uint16_t *p_src, uint32_t v_len)
{
    static uint32_t l_play_cnt;
    uint32_t        v_idx;
    te_state        e_prev = gs_audio.e_state;
}
```

---
