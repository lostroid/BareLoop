# Coding Style

## Naming Convention

### Format

```
[Scope] + [Kind...] + _ + snake_case
```

```c
g + ap + _ + pcm_buf  ->  gap_pcm_buf
```

- Scope comes first (at most one).
- Kind letters can be combined. **The last letter is what it actually is.**
- The name body after `_` is snake_case. Macros (`d_`) are the exception: UPPER SNAKE_CASE (e.g. `d_BUF_SIZE`).

---

### 1. Scope

| Prefix | Meaning      | Example                     |
|--------|--------------|-----------------------------|
| `g`    | file static  | `static uint8_t g_count;`   |
| `x`    | extern       | `extern uint8_t x_count;`   |
| `l`    | local static | `static uint8_t l_count;`   |
| (none) | local variable, parameter, struct member | `uint8_t v_idx;` |

---

### 2. Kind

| Prefix | Meaning                           |
|--------|-----------------------------------|
| `a`    | array                             |
| `p`    | pointer                           |
| `c`    | const                             |
| `f`    | function                          |
| `s`    | struct variable                   |
| `u`    | union variable                    |
| `e`    | enum variable                     |
| `b`    | bit field member                  |
| `v`    | plain variable (only when no other prefix applies) |

#### Combinations

Read in order; the last letter is the actual kind.

| Prefix | Meaning            | Example                           |
|--------|--------------------|-----------------------------------|
| `ap_`  | pointer to array   | `uint16_t (*ap_buf)[256];`        |
| `pa_`  | array of pointers  | `uint16_t *pa_buf[4];`            |
| `pp_`  | pointer to pointer | `uint8_t **pp_data;`              |
| `fp_`  | function pointer   | `void (*fp_cb)(void);`            |
| `fpa_` | array of function pointers | `void (*fpa_isr[8])(void);` |
| `sp_`  | struct pointer     | `ts_audio *sp_audio;`             |
| `sa_`  | struct array       | `ts_audio sa_ch[2];`              |
| `ca_`  | const array        | `const uint8_t ca_tbl[16];`       |
| `cp_`  | pointer to const   | `const uint8_t *cp_data;`         |
| `pc_`  | const pointer      | `uint8_t *const pc_reg;`          |

---

### 3. Typedef

Type names use `t` + kind.

| Prefix | Meaning               | Example                              |
|--------|-----------------------|--------------------------------------|
| `ts_`  | struct type           | `typedef struct { ... } ts_audio;`   |
| `te_`  | enum type             | `typedef enum { ... } te_state;`     |
| `tu_`  | union type            | `typedef union { ... } tu_word;`     |
| `ta_`  | array type            | `typedef uint8_t ta_mac[6];`         |
| `tfp_` | function pointer type | `typedef void (*tfp_cb)(void);`      |
| `t_`   | basic type alias      | `typedef uint16_t t_sample;`         |

Type and variable pairs:

| Type       | Variables                                  |
|------------|--------------------------------------------|
| `ts_audio` | `s_audio`, `gs_audio`, `sp_audio`, `sa_audio` |
| `te_state` | `e_state`, `ge_state`                      |
| `tu_word`  | `u_word`, `gu_word`                        |
| `tfp_cb`   | `fp_cb`, `gfpa_cb[4]`                      |

> Do not typedef plain pointers (e.g. `typedef uint8_t *tp_buf;`).
> It hides the pointer and makes `const` placement confusing.
> Only function pointer types (`tfp_`) are allowed.

---

### 4. Standalone

These are not combined with other prefixes.

| Prefix | Meaning         | Example                                  |
|--------|-----------------|------------------------------------------|
| `d_`   | macro           | `#define d_BUF_SIZE 512`                 |
| `f_`   | function        | `void f_audio_play(void);`               |
| `i_`   | inline function | `static inline uint8_t i_is_busy(void);` |
| `m_`   | enum member     | `m_idle`, `m_play`                       |

---

### 5. Priority

1. **Scope** (`g` / `x` / `l`) — always first
2. **Kind** (`a`, `p`, `c`, `f`, `s`, `u`, `e`) — combined, last letter is the actual kind
3. **`v`** — only when no other prefix applies

```c
static uint16_t ga_pcm[512];   /* OK  */
static uint16_t gva_pcm[512];  /* NG : v is not needed */
```

---

### 6. Example

```c
/* Types */
typedef enum { m_idle, m_play, m_stop } te_state;
typedef void (*tfp_cb)(void);

typedef struct {
    te_state   e_state;
    uint8_t    b_mute : 1;
    uint8_t    b_loop : 1;
    uint16_t  *p_pcm;
    tfp_cb     fp_done;
} ts_audio;

/* File static */
static ts_audio        gs_audio;
static ts_audio       *gsp_cur;
static uint16_t        ga_pcm[512];
static uint16_t      (*gap_half)[256];
static const uint8_t   gca_vol_tbl[16];
static tfp_cb          gfpa_cb[4];

/* Extern */
extern const uint16_t  xca_pcm_data[];

/* Function */
void f_audio_play(uint16_t *p_src, uint32_t v_len)
{
    static uint32_t l_play_cnt;
    uint32_t        v_idx;
    te_state        e_prev = gs_audio.e_state;
}
```

---
