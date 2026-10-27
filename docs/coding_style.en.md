# Coding Style

## Naming Convention

### Format

```
[Scope] + [Kind...] + _ + PascalCase
```

```c
g + ap + _ + PcmBuf  ->  gap_PcmBuf
```

- Scope comes first (at most one).
- Kind letters can be combined. **The last letter is what it actually is.**
- The name body after `_` is PascalCase.

---

### 1. Scope

| Prefix | Meaning      | Example                     |
|--------|--------------|-----------------------------|
| `g`    | file static  | `static uint8_t g_Count;`   |
| `x`    | extern       | `extern uint8_t x_Count;`   |
| `l`    | local static | `static uint8_t l_Count;`   |
| (none) | local variable, parameter, struct member | `uint8_t v_Idx;` |

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
| `ap_`  | pointer to array   | `uint16_t (*ap_Buf)[256];`        |
| `pa_`  | array of pointers  | `uint16_t *pa_Buf[4];`            |
| `pp_`  | pointer to pointer | `uint8_t **pp_Data;`              |
| `fp_`  | function pointer   | `void (*fp_Cb)(void);`            |
| `fpa_` | array of function pointers | `void (*fpa_Isr[8])(void);` |
| `sp_`  | struct pointer     | `ts_Audio *sp_Audio;`             |
| `sa_`  | struct array       | `ts_Audio sa_Ch[2];`              |
| `ca_`  | const array        | `const uint8_t ca_Tbl[16];`       |
| `cp_`  | pointer to const   | `const uint8_t *cp_Data;`         |
| `pc_`  | const pointer      | `uint8_t *const pc_Reg;`          |

---

### 3. Typedef

Type names use `t` + kind.

| Prefix | Meaning               | Example                              |
|--------|-----------------------|--------------------------------------|
| `ts_`  | struct type           | `typedef struct { ... } ts_Audio;`   |
| `te_`  | enum type             | `typedef enum { ... } te_State;`     |
| `tu_`  | union type            | `typedef union { ... } tu_Word;`     |
| `ta_`  | array type            | `typedef uint8_t ta_Mac[6];`         |
| `tfp_` | function pointer type | `typedef void (*tfp_Cb)(void);`      |
| `t_`   | basic type alias      | `typedef uint16_t t_Sample;`         |

Type and variable pairs:

| Type       | Variables                                  |
|------------|--------------------------------------------|
| `ts_Audio` | `s_Audio`, `gs_Audio`, `sp_Audio`, `sa_Audio` |
| `te_State` | `e_State`, `ge_State`                      |
| `tu_Word`  | `u_Word`, `gu_Word`                        |
| `tfp_Cb`   | `fp_Cb`, `gfpa_Cb[4]`                      |

> Do not typedef plain pointers (e.g. `typedef uint8_t *tp_Buf;`).
> It hides the pointer and makes `const` placement confusing.
> Only function pointer types (`tfp_`) are allowed.

---

### 4. Standalone

These are not combined with other prefixes.

| Prefix | Meaning         | Example                                  |
|--------|-----------------|------------------------------------------|
| `d_`   | macro           | `#define d_BufSize 512`                  |
| `f_`   | function        | `void f_AudioPlay(void);`                |
| `i_`   | inline function | `static inline uint8_t i_IsBusy(void);`  |
| `m_`   | enum member     | `m_Idle`, `m_Play`                       |

---

### 5. Priority

1. **Scope** (`g` / `x` / `l`) — always first
2. **Kind** (`a`, `p`, `c`, `f`, `s`, `u`, `e`) — combined, last letter is the actual kind
3. **`v`** — only when no other prefix applies

```c
static uint16_t ga_Pcm[512];   /* OK  */
static uint16_t gva_Pcm[512];  /* NG : v is not needed */
```

---

### 6. Example

```c
/* Types */
typedef enum { m_Idle, m_Play, m_Stop } te_State;
typedef void (*tfp_Cb)(void);

typedef struct {
    te_State   e_State;
    uint8_t    b_Mute : 1;
    uint8_t    b_Loop : 1;
    uint16_t  *p_Pcm;
    tfp_Cb     fp_Done;
} ts_Audio;

/* File static */
static ts_Audio        gs_Audio;
static ts_Audio       *gsp_Cur;
static uint16_t        ga_Pcm[512];
static uint16_t      (*gap_Half)[256];
static const uint8_t   gca_VolTbl[16];
static tfp_Cb          gfpa_Cb[4];

/* Extern */
extern const uint16_t  xca_PcmData[];

/* Function */
void f_AudioPlay(uint16_t *p_Src, uint32_t v_Len)
{
    static uint32_t l_PlayCnt;
    uint32_t        v_Idx;
    te_State        e_Prev = gs_Audio.e_State;
}
```

---
