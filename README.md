![JSON](https://img.shields.io/badge/-JSON-666666?logo=json&logoColor=ffffff)
![JavaScript](https://img.shields.io/badge/-JavaScript-666666?logo=javascript&logoColor=f7df1e)
![TypeScript](https://img.shields.io/badge/-TypeScript-666666?logo=typescript&logoColor=3178C6)

# XPLA Vesting
This contract is to provide vesting account features for the both cw20 and native tokens.



# Operations

## Init
Instantiate contract.

```typescript
interface Init {
  owner:    address,  // 이 컨트랙트를 관리할 소유자 주소
  name:     string,   // 이 컨트랙트를 나타내는 이름
  webpage?: uri,      //<- Optional, 이 컨트랙트에 대한 정보 및 설명 URI
}
```
```jsonc
{
  "owner": "xpla1...c9v5",
  "name": "vesting test"
}
```

## Execute update_config
Change settings.
Run as an owner address.

```typescript
interface UpdateConfig {
  update_config: {
    name?:    string, //<- Optional, 이 컨트랙트를 나타내는 이름
    webpage?: uri,    //<- Optional, 이 컨트랙트에 대한 정보 및 설명 URI
  },
}
```
```jsonc
{
  "update_config": {
    "webpage": "https://test.page/vesting"
  }
}
```

## Execute register_onetime_vesting
Register a one-time vesting schedule for accounts.
Vesting that occurs just one time.
```
v|
e|
s|
t|
e|  ________________
d|  :
  --+---------------
  start
```
- if $T_n \geqq T_s$
  - $V = A_t$
  > $V$: vested amount, $A_t$: total amount,  
  > $T_s$: start time, $T_n$: current time

```typescript
interface RegisterOnetimeVesting {
  register_onetime_vesting: {  // 1회성 베스팅 스케쥴
    token:       VestingToken,          // 베스팅 토큰 종류
    start_time:  time,                  // 시작 시간(초)
    accounts:    AccountTotalAmount[],  // 수익자 목록 (total_amount만 등록 가능)
    master?:     address,               //<- Optional, 관리자 주소 (베스팅 스케쥴 취소 가능)
                                        //   (따로 지정하지 않으면 register를 실행하는 계정 주소로 default)
    withdrawer?: address,               //<- Optional, 남은 물량을 회수할 계정
                                        //   (따로 지정하지 않으면 register를 실행하는 계정 주소로 default)
    info?:       VestingInfo,           //<- Optional, 베스팅 상세 정보
  },
}
```
> [AccountTotalAmount](#accountamount), [VestingInfo](#vestinginfo), [VestingToken](#vestingtoken)
```jsonc
{
  "register_onetime_vesting": {
    "token": { "native": { "denom": "axpla" } },
    "start_time": "1650016200",
    "accounts": [
      { "address": "xpla1...ylya", "total_amount": "1000" },
      { "address": "xpla1...80ae", "total_amount": "1500" }
    ]
  }
}
```

## Execute register_linear_vesting
Register a linear vesting schedule for accounts.
Linearly increasing vesting.
```
v|            ___
e|           /:
s|         /  :
t|       /    :
e|     /      :
d|   /        :
  --+---------+--
  start      end
```
- if $T_n \geqq T_s$ and $T_n < T_e$
  - $V = A_t \times {(T_n - T_s) \div (T_e - T_s)}$
- if $T_n \geqq T_e$
  - $V = A_t$
  > $V$: vested amount, $A_t$: total amount,  
  > $T_s$: start time, $T_e$: end time, $T_n$: current time

```typescript
interface RegisterLinearVesting {
  register_linear_vesting: {  // 선형 베스팅 스케쥴
    token:       VestingToken,
    start_time:  time,                  // 시작 시간(초)
    end_time:    time,                  // 끝 시간(초)
    accounts:    AccountTotalAmount[],  // 수익자 목록 (total_amount만 등록 가능)
    master?:     address,
    withdrawer?: address,
    info?:       VestingInfo,
  },
}
```
> [AccountTotalAmount](#accountamount), [VestingInfo](#vestinginfo), [VestingToken](#vestingtoken)
```jsonc
{
  "register_linear_vesting": {
    "token": { "native": { "denom": "axpla" } },
    "start_time": "1650016200",
    "end_time": "1650016500",
    "accounts": [
      { "address": "xpla1...ylya", "total_amount": "1000" },
      { "address": "xpla1...80ae", "total_amount": "1500" }
    ],
    "info": {
      "name": "Gamer's Vesting",
      "description": "vesting test 1"
    }
  }
}
```

## Execute register_periodic_vesting
Register a periodic vesting schedule for accounts.
Vesting that occurs every time interval.
```
interval = *****
v|
e|                 ___
s|            _____:
t|       _____     :
e|  _____          :
d|  :              :
  --+--------------+--
  start          end
```
- if $T_n \geqq T_s$ and $T_n < T_e$
  - $C = 1 + {\lfloor {(T_n - T_s) \div I} \rfloor}$
  - if has each_amount
    - $V = A_e \times C$
  - if has total_amount
    - $V = A_t \div (T_e - T_s) \times I \times C$
- if $T_n \geqq T_e$
  - if has each_amount
    - $V = A_e \times (1 + {\lfloor {(T_e - T_s) \div I} \rfloor})$
  - if has total_amount
    - $V = A_t$
  > $V$: vested amount, $C$: vesting occurrence count,  
  > $A_e$: each amount, $A_t$: total amount,  
  > $T_s$: start time, $T_e$: end time, $I$: time interval, $T_n$: current time

```typescript
interface RegisterPeriodicVesting {
  register_periodic_vesting: {  // 일정 간격 베스팅 스케쥴
    token:         VestingToken,
    start_time:    time,
    end_time:      time,
    time_interval: time,             // 시간 간격(초)
    accounts:      AccountAmount[],  // 수익자 목록 (each_amount나 total_amount 중 하나로 등록 가능)
    master?:       address,
    withdrawer?:   address,
    info?:         VestingInfo,
  },
}
```
> [AccountAmount](#accountamount), [VestingInfo](#vestinginfo), [VestingToken](#vestingtoken)
```jsonc
{
  "register_periodic_vesting": {
    "token": { "native": { "denom": "axpla" } },
    "start_time": "1649911800",  // 2022-04-14 04:50:00 UTC 부터
    "end_time": "1649912100",    // 2022-04-14 04:55:00 UTC 까지
    "time_interval": "60",       // 1분마다
    "accounts": [
      { "address": "xpla1...ylya", "each_amount": "100" },
      { "address": "xpla1...80ae", "total_amount": "15000" }
    ],
    "info": {
      "name": "Investor's Vesting",
      "description": "vesting test 2"
    }
  }
}
```

each_amount로 등록한 계정은 베스팅이 발생할 때마다 정해진 물량이 추가됩니다.

total_amount로 등록한 계정은 베스팅 총 기간 중 조건이 발생한 횟수만큼을 나눠서 물량이 결정됩니다.

## Execute register_conditional_vesting
Register a conditional vesting schedule for accounts.
Vesting that occurs when the condition is matched.
```
condition = 📅⏰
v|
e|                 ___
s|            _____:
t|         ___:    :
e|  _______:  :    :
d|  :      :  :    :
  --+------+--+----+--
  start    c  c   end
```
- if $T_n \geqq T_s$ and $T_n < T_e$
  - $C_t = ($ number of condition matched at the block time $)$
  - if has each_amount
    - $V = A_e \times C_t$ 
  - if has total_amount
    - $V = A_t \times C_t \div C_a$
- if $T_n \geqq T_e$
  - if has each_amount
    - $V = A_e \times C_a$
  - if has total_amount
    - $V = A_t$
  > $V$: vested amount, $C_t$: vesting occurrence count, $C_a$: number of all matched that occurred during the period,  
  > $A_e$: each amount, $A_t$: total amount,  
  > $T_s$: start time, $T_e$: end time, $T_n$: current time

```typescript
interface RegisterConditionalVesting {
  register_conditional_vesting: {  // 특정 조건 베스팅 스케쥴
    token:       VestingToken,
    start_time:  time,
    end_time:    time,
    condition:   VestingCondition,  // 조건 내용
    accounts:    AccountAmount[],   // 수익자 목록 (each_amount나 total_amount 중 하나로 등록 가능)
    master?:     address,
    withdrawer?: address,
    info?:       VestingInfo,
  },
}
```
> [VestingCondition](#vestingcondition), [AccountAmount](#accountamount), [VestingInfo](#vestinginfo), [VestingToken](#vestingtoken)

There are 4 types of the condition
- "daily": Occurs at a certain time every day.
- "weekly": Occurs every week.
- "monthly": Occurs every month.
- "yearly": Occurs every month.

### daily condition
```jsonc
{
  "register_conditional_vesting": {
    "token": { "native": { "denom": "axpla" } },
    "start_time": "1640995200",  // 2022-01-01 00:00:00 UTC 부터
    "end_time": "1767225599",    // 2025-12-31 23:59:59 UTC 까지
    "condition": {
      "daily": {           // 매일 (UTC 기준)
        "hour": [ 1, 13 ]  // 1시와 13시에 베스팅 발생
      }
    },
    "accounts": [
      { "address": "xpla1...ylya", "each_amount": "1000" },
      { "address": "xpla1...80ae", "total_amount": "1500000" }
    ]
  }
}
```

### weekly condition
```jsonc
{
  "register_conditional_vesting": {
    "token": { "native": { "denom": "axpla" } },
    "start_time": "1640995200",  // 2022-01-01 00:00:00 UTC 부터
    "end_time": "1767225599",    // 2025-12-31 23:59:59 UTC 까지
    "condition": {
      "weekly": {                // 매주 (UTC 기준)
        "weekday": [ 1, 3, 5 ],  // 월, 수, 금 요일마다
        "hour": [ 12 ]           // 12시에 베스팅 발생
      }
    },
    "accounts": [
      { "address": "xpla1...ylya", "each_amount": "10" },
      { "address": "xpla1...80ae", "total_amount": "15000" }
    ]
  }
}
```

### monthly condition
```jsonc
{
  "register_conditional_vesting": {
    "token": { "native": { "denom": "axpla" } },
    "start_time": "1640995200",  // 2022-01-01 00:00:00 UTC 부터
    "end_time": "1767225599",    // 2025-12-31 23:59:59 UTC 까지
    "condition": {
      "monthly": {             // 매달 (UTC 기준)
        "day": [ 1, 15 ],      // 1일과 15일의
        "hour": [ 6, 12, 18 ]  // 6시, 12시, 18시에 베스팅 발생
      }
    },
    "accounts": [
      { "address": "xpla1...ylya", "each_amount": "100" },
      { "address": "xpla1...80ae", "total_amount": "15000" }
    ]
  }
}
```

### yearly condition
```jsonc
{
  "register_conditional_vesting": {
    "token": { "native": { "denom": "axpla" } },
    "start_time": "1640995200",  // 2022-01-01 00:00:00 UTC 부터
    "end_time": "1767225599",    // 2025-12-31 23:59:59 UTC 까지
    "condition": {
      "yearly": {                  // 매년 (UTC 기준)
        "month": [ 1, 4, 7, 10 ],  // 1월, 4월, 7월, 10월의
        "day": [ 20 ],             // 20일
        "hour": [ 0 ]              // 0시에 베스팅 발생
      }
    },
    "accounts": [
      { "address": "xpla1...ylya", "each_amount": "1000" },
      { "address": "xpla1...80ae", "total_amount": "15000" }
    ]
  }
}
```


## Execute deregister_vesting_accounts
Cancel the vesting schedule for accounts.

You should run using the master address that is registered.

```typescript
interface DeregisterVestingAccounts {
  deregister_vesting_accounts: {
    accounts:    AccountStage[],  // 등록을 해제할 수익자 목록
  },
}
```
> [AccountStage](#accountstage)
```jsonc
{
  "deregister_vesting_accounts": {
    "accounts": [
      { "address": "xpla1...ylya", "stage": 1 },
      { "address": "xpla1...80ae", "stage": 2 }
    ]
  }
}
```


## Execute deregister_vesting_stage
Cancel all accounts in a vesting stage.

You should run using the master address that is registered.

```typescript
interface DeregisterVestingStage {
  deregister_vesting_stage: {
    stage:       number,   // 전체 등록을 해제할 스테이지 번호
  },
}
```
```jsonc
{
  "deregister_vesting_stage": {
    "stage": 1
  }
}
```


## Execute update_vesting_info
Update the vesting info.

You should run using the owner address or the master address that is registered.

```typescript
interface UpdateVestingInfo {
  update_vesting_info: {
    stage: number,       // 정보를 갱신할 스테이지 번호
    info:  VestingInfo,  // 베스팅 설명 정보
  },
}
```
> [VestingInfo](#vestinginfo)
```jsonc
{
  "update_vesting_info": {
    "stage": 1,
    "info": {
      "name": "Operator's Vesting",
      "image": "https://test.page/vesting.png"
    }
  }
}
```


## Execute claim
Do claim.

You should run using the recipient account(address) that is registered.

```typescript
interface Claim {
  claim: {
    stages:     number[],  // 클레임할 베스팅 스테이지 목록
    recipient?: address,   //<- Optional, 클레임할 물량을 받을 계정
                           //   (지정하지 않으면 등록된 수익자에게 전송)
  },
}
```
```jsonc
{
  "claim": {
    "stages": [ 1, 2 ]
  }
}
```


## Query config
Input:
```jsonc
{
  "config": {}
}
```
Output:
```jsonc
{
  "owner": "xpla1...c9v5",
  "name": "vesting test",
  "webpage": null
}
```


## Query vesting_account
Check the registered vesting schedule.

Input:
```typescript
interface QueryVestingAccount {
  vesting_account: {
    address:     address,     // 수익자 주소
    pagination?: Pagination,  //<- Optional, 페이지 나누는 방식
  }
}
```
Output:
```typescript
interface VestingResponse {
  stage:    number,           // 스테이지 번호
  master:   address,          // 관리자 주소
  schedule: VestingSchedule,  // 베스팅 일정
  info:     VestingInfo,      // 베스팅 내용 정보
  amounts:  VestingAmounts,   // 베스팅 물량 정보
}
interface VestingAccountResponse {
  address:  address,            // 수익자 주소
  vestings: VestingResponse[],  // 베스팅 정보 목록
}
```
> [Pagination](#pagination), [VestingSchedule](#vestingschedule),
> [VestingInfo](#vestinginfo), [VestingAmounts](#vestingamounts)

Input:
```jsonc
{
  "vesting_account": {
    "address": "xpla1...ylya"
  }
}
```
Output:
```jsonc
{
  "address": "xpla1...ylya",
  "vestings": [
    {
      "stage": 1,
      "master": "xpla1...0r7f",
      "schedule": {
        "style": "linear",
        "token": { "native": { "denom": "axpla" } },
        "start_time": "1650379800",
        "end_time": "1650379800",
        "time_interval": null,
        "month": [],
        "day": [],
        "weekday": [],
        "hour": []
      },
      "info": {
        "name": "Gamer's Vesting",
        "description": "vesting test 1",
        "icon": null,
        "image": null,
        "webpage": null
      },
      "amounts": {
        "total": "1000000",
        "vested": "200000",
        "claimed": "150000",
        "claimable": "50000"
      }
    },
    {
      "stage": 2,
      "master": "xpla1...0r7f",
      "schedule": {
        "style": "periodic",
        "token": { "native": { "denom": "axpla" } },
        "start_time": "1650015000",
        "end_time": "1650015300",
        "time_interval": "60",
        "month": [],
        "day": [],
        "weekday": [],
        "hour": []
      },
      "info": {
        "name": "Investor's Vesting",
        "description": null,
        "icon": null,
        "image": null,
        "webpage": null,
      },
      "amounts": {
        "total": "1500000",
        "vested": "1000",
        "claimed": "0",
        "claimable": "1000"
      }
    },
    {
      "stage": 3,
      "master": "xpla1...0r7f",
      "schedule": {
        "style": "daily_condition",
        "token": { "native": { "denom": "axpla" } },
        "start_time": "1650343500",
        "end_time": "1650553200",
        "time_interval": null,
        "month": [],
        "day": [],
        "weekday": [],
        "hour": [5],
      },
      "info": {
        "name": "test3",
        "description": null,
        "icon": null,
        "image": null,
        "webpage": null,
      },
      "amounts": {
        "total": "3000000",
        "vested": "800000",
        "claimed": "120000",
        "claimable": "680000"
      }
    }
  ]
}
```

## Query periodic_vesting_amount
Predicted of periodic vesting amounts.

Input:
```typescript
interface QueryPeriodicVestingAmount {
  periodic_vesting_amount: {
    start_time:    time,             // 시작 시간(초)
    end_time:      time,             // 끝 시간(초)
    time_interval: time,             // 시간 간격(초)
    accounts:      AccountAmount[],  // 수익자 목록
  },
}
```
Output:
```typescript
interface VestingAmountResponse {
  account_count: number,  // 수익자 수
  vested_count:  number,  // 베스팅 발생 횟수
  sum_amounts:   amount,  // 모든 베스팅 물량의 총합
}
```
> [AccountAmount](#accountamount)

Input:
```jsonc
{
  "periodic_vesting_amount": {
    "start_time": "1649911800",  // 2022-04-14 04:50:00 UTC 부터
    "end_time": "1649912100",    // 2022-04-14 04:55:00 UTC 까지
    "time_interval": "60",       // 1분마다
    "accounts": [
      { "address": "xpla1...ylya", "each_amount": "1000" },
      { "address": "xpla1...80ae", "total_amount": "15000" }
    ]
  }
}
```
Output:
```jsonc
{
  "account_count": 2,    // 계정 2개
  "vested_count": 6,     // 6회의 발생 [ 0분(시작), 1분, 2분, 3분, 4분, 5분(끝) ]
  "sum_amounts": "21000"  // each(1000*5) + total(15000)
}
```

## Query conditional_vesting_amount
Predicted of conditional vesting amounts.

Input:
```typescript
interface QueryConditionalVestingAmount {
  conditional_vesting_amount: {
    start_time: time,              // 시작 시간(초)
    end_time:   time,              // 끝 시간(초)
    condition:  VestingCondition,  // 조건 내용
    accounts:   AccountAmount[],   // 수익자 목록
  }
}
```
Output:
```typescript
interface VestingAmountResponse {
  account_count: number,  // 수익자 수
  vested_count:  number,  // 베스팅 발생 횟수
  sum_amounts:   amount,  // 모든 베스팅 물량의 총합
}
```
> [VestingCondition](#vestingcondition), [AccountAmount](#accountamount)

Input:
```jsonc
{
  "conditional_vesting_amount": {
    "start_time": "1640995200",  // 2022-01-01 00:00:00 UTC 부터
    "end_time": "1767225599",    // 2025-12-31 23:59:59 UTC 까지
    "condition": {
      "yearly": {                  // 매년 (UTC 기준)
        "month": [ 1, 4, 7, 10 ],  // 1월, 4월, 7월, 10월의
        "day": [ 20 ],             // 20일
        "hour": [ 0 ]              // 0시에 베스팅 발생
      }
    },
    "accounts": [
      { "address": "xpla1...ylya", "each_amount": "1000" },
      { "address": "xpla1...80ae", "total_amount": "15000" }
    ]
  }
}
```
Output:
```jsonc
{
  "account_count": 2,    // 계정 2개
  "vested_count": 16,    // 16회의 발생 [ 22년1월20일0시, 22년4월20일0시, 22년7월20일0시, ... 25년10월20일0시 ]
  "sum_amounts": "31000"  // each(1000*16) + total(15000)
}
```

## Query latest_stage

Input:
```jsonc
{
  "latest_stage": {}
}
```
Output:
```jsonc
{
  "latest_stage": 5
}
```

## Query vesting_stages
Status for vesting stages.

Input:
```typescript
interface QueryVestingStages {
  vesting_stages: {
    pagination?: Pagination,  //<- Optional, 페이지 나누는 방식
  }
}
```
Output:
```typescript
interface StageResponse {
  stage:      number,           // 스테이지 번호
  master:     address,          // 관리자 주소
  withdrawer: address,          // 회수자 주소
  schedule:   VestingSchedule,  // 베스팅 일정
  info:       VestingInfo,      // 베스팅 내용 정보
  status:     VestingStatus,    // 베스팅 상태 정보
}
interface VestingStagesResponse {
  stages: StageResponse[],  // 스테이지 정보 목록
}
```

Input:
```jsonc
{
  "vesting_stages": {
    "pagination": {
      "offset": { "start": 2, "limit": 1, }
    }
  }
}
```
Output:
```jsonc
{
  "stages": [
    {
      "stage": 2,
      "master": "xpla1...0r7f",
      "withdrawer": "xpla1...0r7f",
      "schedule": {
        "style": "periodic",
        "token": { "native": { "denom": "axpla" } },
        "start_time": "1649911800",
        "end_time": "1649912100",
        "time_interval": "60",
        "month": [],
        "day": [],
        "weekday": [],
        "hour": []
      },
      "info": {
        "name": "Investor's Vesting",
        "description": "vesting test 2",
        "icon": null,
        "image": null,
        "webpage": null
      },
      "status": {
        "opened": true,
        "token": { "native": "axpla" },
        "sum_amounts": "21000000000000",
        "sum_claimed": "200000000000"
      }
    }
  ]
}
```



# Types

## Pagination
Pagination for a list output.

There are 2 types of the pagination
- "offset"
- "stage"

```typescript
interface PaginationOffset {
  start?: number,  //<- Optional, 시작 위치
  limit:  number,  // 출력 개수
}
interface PaginationStage {
  after?: number,  //<- Optional, 다음부터 시작할 스테이지 번호
  limit:  number,  // 출력 개수
}
type Pagination = (
  {
    offset:  PaginationOffset,
    stage?:  never,
  } | {
    stage:   PaginationStage,
    offset?: never,
  }
)  // offset과 stage 중 하나만 사용
```
```jsonc
{
  "offset": {
    "start": 5,   //<- Optional, 5번째부터
    "limit": 10,  // 10개만 선택
  }
}
// ["addr01","addr02",..."addr30"] => ["addr05","addr06",...,"addr14"]
```
```jsonc
{
  "stage": {
    "after": 2,  //<- Optional, 2번 스테이지 다음부터
    "limit": 5,  // 5개만 선택
  }
}
// [1,2,3,...,20] => [3,4,5,6,7]
```

## AccountAmount
Amounts for an account.

```typescript
interface AccountEachAmount {
  address:       address,  // 수익자 주소
  each_amount:   amount,   // 해당 수익자에게 베스팅할 1회 발생 물량
  total_amount?: never,
}
interface AccountTotalAmount {
  address:      address,  // 수익자 주소
  total_amount: amount,   // 해당 수익자에게 베스팅할 총 물량
  each_amount?: never,
}
type AccountAmount = (
  | AccountEachAmount
  | AccountTotalAmount
)  // each_amount와 total_amount 중 하나만 사용
```
```jsonc
{
  "address": "xpla1...ylya",  // 수익자 주소
  "each_amount": "100"        // 1회 발생 물량
}
```
```jsonc
{
  "address": "xpla1...80ae",  // 수익자 주소
  "total_amount": "15000"     // 총 물량
}
```

## AccountStage
Stage of an account.

```typescript
interface AccountStage {
  address: address,  // 수익자 주소
  stage:   number,   // 해당 수익자가 등록된 스테이지 번호
}
```
```jsonc
{
  "address": "xpla1...80ae",  // 수익자 주소
  "stage": 2                  // 2번 스테이지
}
```

## VestingToken
Token type of vesting.

There are 2 types of the token.
- "native"
- "cw20"

```typescript
interface NativeToken {
  native: {
    denom: string,  // 네이티브 토큰의 denom
  },
  cw20?:   never,
}
interface CW20Token {
  cw20: {
    contract: address,  // CW20 토큰의 컨트랙트 주소
  },
  native?: never,
}
type VestingToken = (
  | NativeToken
  | CW20Token
)  // native와 cw20 중 하나만 사용
```
```jsonc
{
  "native": { "denom": "axpla" }  // 네이티브 토큰
}
```
```jsonc
{
  "cw20": { "contract": "xpla1...wpmw" }  // CW20 토큰
}
```

## VestingSchedule
Schedule of a vesting.

```typescript
interface VestingSchedule {
  style:          string,        // 베스팅 스케쥴 형태
                                 //   (onetime, linear, periodic, daily_condition, weekly_condition,
                                 //    monthly_condition, yearly_condition)
  token:          VestingToken,  // 베스팅 토큰 종류
  start_time:     time,          // 시작 시간(초)
  end_time:       time,          // 끝 시간(초)
  time_interval?: time,          //<- Optional, 시간 간격(초) (periodic의 경우에만)
  month?:         number[],      //<- Optional, 발생 월 (conditional 중 yearly_condition 경우에만)
  day?:           number[],      //<- Optional, 발생 일자 (conditional 중 yearly_condition과 monthly_condition의 경우에만)
  weekday?:       number[],      //<- Optional, 발생 요일 (conditional 중 weekly_condition의 경우에만)
  hour?:          number[],      //<- Optional, 발생 시간 (conditional의 경우에만)
}
```
> [VestingToken](#vestingtoken)
```jsonc
{
  "style": "periodic",         // 일정 시간 간격의 스케쥴
  "token": { "native": { "denom": "axpla" } },
  "start_time": "1649998800",  // 2022-04-15T05:00:00Z UTC에 시작
  "end_time": "1649999100",    // 2022-04-15T05:05:00Z UTC에 끝
  "time_interval": "60",       // 1분마다
}
```

## VestingInfo
Infomations of a vesting.

```typescript
interface VestingInfo {
  name?:        string,  //<- Optional, 베스팅 이름
  description?: string,  //<- Optional, 베스팅 내용 설명
  icon?:        uri,     //<- Optional, 베스팅 아이콘
  image?:       uri,     //<- Optional, 베스팅 이미지
  webpage?:     uri,     //<- Optional, 베스팅 설명 페이지
}
```
```jsonc
{
  "name": "Test Vesting",
  "description": "test #1",
  "image": "https://test.page/vesting.png"
}
```

## VestingStatus
Status of a vesting.

```typescript
interface VestingStatus {
  started:     bool,          // 베스팅이 시작되었는지 여부
  token:       VestingToken,  // 토큰 종류
  sum_amounts: amount,        // 스테이지 전체 베스팅 물량
  sum_claimed: amount,        // 이 스테이지에서 현재까지 클레임된 물량 합계
}
```
> [VestingToken](#vestingtoken)
```jsonc
{
  "started": false,                       // 아직 시작 안 됨
  "token": { "native": "axpla" },         // axpla 네이티브 토큰
  "sum_amounts": "21000000000000000000",  // 스테이지 전체 21xpla 베스팅 예정
  "sum_claimed": "2000000000000000000"    // 본 스테이지에서 현재까지 클레임된 총 물량은 2xpla
}
```

## VestingAmounts
Amounts of a vesting.

```typescript
interface VestingAmounts {
  token:     VestingToken,  // 토큰 종류
  total:     amount,        // 총 베스팅 물량
  vested:    amount,        // 현재까지 발생된 물량
  claimed:   amount,        // 이미 받아간 물량
  claimable: amount,        // 클레임 가능한 물량
}
```
> [VestingToken](#vestingtoken)
```jsonc
{
  "token": { "native": "axpla" },     // axpla 네이티브 토큰
  "total": "21000000000000000000",    // 총 21xpla 베스팅 예정
  "vested": "5300000000000000000",    // 현재까지 5.3xpla 베스팅 발생
  "claimed": "3000000000000000000",   // 3xpla 클레임 완료
  "claimable": "2300000000000000000"  // 2.3xpla 클레임 가능
}
```

## VestingCondition
Condition for a conditional vesting.

There are 4 types of the condition
- "daily": Occurs at a certain time every day.
- "weekly": Occurs every week.
- "monthly": Occurs every month.
- "yearly": Occurs every year.

```typescript
interface DailyCondition {
  hour:    number[],  // 발생 시간 목록 (0~23)
}
interface WeeklyCondition extends DailyCondition {
  weekday: number[],  // 발생 요일 (0:Sun ~ 6:Sat)
}
interface MonthlyCondition extends DailyCondition {
  day:     number[],  // 발생 일자 (1 ~ 31)
}
interface YearlyCondition extends MonthlyCondition {
  month:   number[],  // 발생 월 (1 ~ 12)
}
type VestingCondition = (
  {
    daily:    DailyCondition,    // 매일 발생하는 조건
    weekly?:  never,
    monthly?: never,
    yearly?:  never,
  } | {
    weekly:   WeeklyCondition,   // 매주 발생하는 조건
    daily?:   never,
    monthly?: never,
    yearly?:  never,
  } | {
    monthly:  MonthlyCondition,  // 매달 발생하는 조건
    daily?:   never,
    weekly?:  never,
    yearly?:  never,
  } | {
    yearly:   YearlyCondition,   // 매년 발생하는 조건
    daily?:   never,
    weekly?:  never,
    monthly?: never,
  }
)  // 반드시 한가지 컨디션만 설정
```

### DailyCondition
Vesting occurs at a certain time every day.

```jsonc
// 매일 1시와 13시에 베스팅 발생 (UTC 기준)
{
  "daily": {
    "hour": [ 1, 13 ]
  }
}
```

### WeeklyCondition
Vesting occurs every week.

```jsonc
// 매주 월,수,금요일마다 12시에 베스팅 발생 (UTC 기준)
{
  "weekly": {
    "weekday": [ 1, 3, 5 ],
    "hour": [ 12 ]
  }
}
```

### MonthlyCondition
Vesting occurs every month.

```jsonc
// 매달 1일과 15일의 6,12,18시에 베스팅 발생 (UTC 기준)
{
  "monthly": {
    "day": [ 1, 15 ],
    "hour": [ 6, 12, 18 ]
  }
}
```

### YearlyCondition
Vesting occurs every year.

```jsonc
// 매년 1,4,7,10월의 20일 0시에 베스팅 발생 (UTC 기준)
{
  "yearly": {
    "month": [ 1, 4, 7, 10 ],
    "day": [ 20 ],
    "hour": [ 0 ]
  }
}
```
