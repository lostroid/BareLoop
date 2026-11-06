
# 기본 파일 구성  
소스파일 생성시 아래와 같이 기본 파일을 구성 합니다.  
- {name}_type.h  
- {name}.h  
- {name}.c  
  
# 기본 코드 내용  
  
### {name}_type.h  
이곳은 Define, enum, struct 같은 타입만 작성합니다.  
다른 코드에서 참조 할때 순수 타입만 참조하기 위한 목적이며  
순환 헤더 참조를 방지 하고자 사용 합니다. 
```c  
/*
 * File        : {name}_type.h
 * Description : {name} Module
 * Author      : Lostroid
 * Created     : 2025-12-04
 * Encoding    : UTF-8
 */
#ifndef H_{name}_TYPE_H
#define H_{name}_TYPE_H
#include "common.h"

#endif
```  
### {name}.h  
함수 유형을 등록 리스트처럼 활용  
```c
/*
 * File        : {name}.h
 * Description : {name} Module
 * Author      : Lostroid
 * Created     : 2025-12-04
 * Encoding    : UTF-8
 */
#ifndef H_{name}_H
#define H_{name}_H
#include "{name}_type.h"

#endif
```  
   
### {name}.c    
소스코드
```c
/*
 * File        : {name}.c
 * Description : {name} Module
 * Author      : Lostroid
 * Created     : 2025-12-04
 * Encoding    : UTF-8
 */
#include "DBG/dbg.h"
#include "Scheduler/scheduler.h"
#include "sysmon.h
#include "{name}.h"

static uint32_t gv_debug = d_DBG_BIT_WARNING | d_DBG_BIT_ERROR;
static char ga_ver[] = "v00.01 {name}";

enum { m_RUN_JOB_TABLE_SIZE = 2 };
static void (*gpa_run_job_table[m_JOB_TABLE_SIZE])(ts_scheduler_ctrl *) = {
    f_base_{name}_job_start,
    f_base_{name}_job_end,
};
///==================================================================
/*### {name} Initialize (Required)
-------------------------------------------------------------------*/
void f_{name}_init(void)
{
    f_{name}_version();
}
///==================================================================
/*### {name} Module (Required)
-------------------------------------------------------------------*/
void f_{name}_module(void)
{
    f_scheduler_run(&gs_job_ctrl);     // UART Schedule
}
//===================================================================
/*### Version + Debug bit (Required)
-------------------------------------------------------------------*/
void f_{name}_version(void)
{
    f_dbg_print_version(gv_debug, ga_ver);
}
//===================================================================
/*### schedule time infomation rest (Required)
-------------------------------------------------------------------*/
void f_{name}_monitor_schedule_reset(void)
{
    f_scheduler_time_info_reset(&gs_job_ctrl);
}
//===================================================================
/*### scheduler execution time monitoring (Required)
-------------------------------------------------------------------*/
void f_{name}_monitor_schedule(void)
{
    f_scheduler_run_time_info_print(&gs_job_ctrl);
}

///==================================================================
/*### Buffer reset (option)
-------------------------------------------------------------------*/
void f_{name}_monitor_buffer_reset(ts_buffer_context *ps_uart)
{
    ps_{name}->v_read_next_pos = 0;
    ps_{name}->v_read_pending_pos = 0;
    ps_{name}->v_write_next_pos = 0;
    ps_{name}->v_write_pending_pos = 0;
}
//===================================================================
/*### UART monitor (option)
-------------------------------------------------------------------*/
void f_base_uart_monitor_buffer(void)
{
    f_sysmon_top_buffer_view("\r\n {name}_tx", &gs_{name}_tx_pos, m_FLOW_TX);
    f_sysmon_top_buffer_view("\r\n {name}_rx", &gs_{name}_rx_pos, m_FLOW_RX);
}
//===================================================================
/*### Set the Start Wakeup Table. (Default)
-------------------------------------------------------------------*/
void f_{name}_start_wakeup_table_set(void)
{
    f_scheduler_control_init(&gs_job_ctrl, gpa_run_job_table, "{name}(run)", m_RUN_JOB_TABLE_SIZE);
    f_scheduler_config(&gs_job_ctrl, 500, 3, 500000, d_NULL);  
}

#endif
```
