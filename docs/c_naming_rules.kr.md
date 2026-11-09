Embedded C Naming Convention Rules

모든 Embedded C 코드의 변수, 함수, 매크로 이름은 다음 명명 규칙을 따른다.

1. 기본 명명 순서

Module → Object → Purpose → Property → Action

각 항목의 의미는 다음과 같다.
Module: 소속 모듈 또는 대분류 (gap, uart, sys)
Object: 관리 대상 (job_table, buffer, timer)
Purpose: 세부 기능 또는 용도 (start, wakeup, sleep, tx)
Property: 객체의 속성 (size, count, status, index)
Action: 수행 동작 (get, set, init, reset, update, run)
불필요한 항목은 생략할 수 있으나, 사용되는 항목의 상대적 순서는 유지한다.

2. 변수 명명 규칙
snake_case를 사용한다.
기본 순서는 module_object_purpose_property이다.
모듈 내부 전역 변수에는 기본적으로 static을 사용한다.
변수 이름에 get, set 등의 동작 표현을 사용하지 않는다.

예시:

static uint32_t gap_job_table_wakeup_size;
static uint32_t uart_buffer_tx_count;
static uint32_t sys_timer_tick_prev;

3. 함수 명명 규칙

snake_case를 사용한다.

기본 순서는 module_object_purpose_property_action이다.

동작(Action)은 함수 이름의 마지막에 배치한다.

모듈 내부 전용 함수는 기본적으로 static으로 선언한다.

외부 공개 API만 External Linkage를 허용한다.

예시:

gap_job_table_wakeup_init();
gap_job_table_wakeup_size_get();
uart_buffer_tx_try_send();
sys_timer_tick_update();

4. 매크로 명명 규칙

UPPER_SNAKE_CASE를 사용한다.

기존 프로젝트에서 사용하는 m_ 접두어를 유지한다.

기본 순서는 m_MODULE_OBJECT_PURPOSE_PROPERTY이다.

예시:

#define m_GAP_JOB_TABLE_WAKEUP_SIZE  16U
#define m_UART_BUFFER_TX_SIZE        1024U
#define m_SYS_TIMER_TICK_MAX         0xFFFFFFFFU

5. 추가 규칙

현재 값과 이전 값은 cur / prev로 통일한다.
Boolean 속성에는 enabled, ready, valid 등을 사용한다.
배열 인덱스는 idx, 위치 정보는 pos를 사용한다.
get, set, init, reset, update, run, try_send 등은 Action으로 분류한다.
동일 기능군은 동일한 접두어를 사용하여 검색과 자동완성이 쉽도록 한다.
기존 코드 수정 시에는 외부 API, 프로토콜, 링커 심볼과의 호환성을 유지한다.
기존 이름을 변경해야 한다면 변경 전후 이름을 명시한다.

6. 코드 작성 및 수정 시 적용
앞으로 모든 코드 생성과 리팩터링에 위 규칙을 기본 적용한다.
기존 코드에 규칙과 일치하지 않는 이름이 있으면 개선안을 제시하되, 단순 기능 수정 요청에서는 불필요한 이름 변경을 하지 않는다.
가장 중요한 원칙은 Module → Object → Purpose → Property → Action 순서를 일관되게 유지하는 것이다.
