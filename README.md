![JSON](https://img.shields.io/badge/-JSON-666666?logo=json&logoColor=ffffff)
![JavaScript](https://img.shields.io/badge/-JavaScript-666666?logo=javascript&logoColor=f7df1e)
![TypeScript](https://img.shields.io/badge/-TypeScript-666666?logo=typescript&logoColor=3178C6)

# XPLA Vesting
This contract is to provide vesting account features for the both cw20 and native tokens.


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
interface Pagination {
  offset?: PaginationOffset,  //<- Optional
  stage?:  PaginationStage,   //<- Optional
                              //   (반드시 offset과 stage 둘 중 하나만 설정)
}
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
interface AccountAmount {
  address:       address,  // 수익자 주소
  each_amount?:  amount,   //<- Optional, 해당 수익자에게 베스팅할 1회 발생 물량
  total_amount?: amount,   //<- Optional, 해당 수익자에게 베스팅할 총 물량
                           //   (반드시 each_amount와 total_amount 둘 중 하나만 설정)
}
```
```jsonc
[
  {
    "address": "xpla1...ylya",  // 수익자 주소
    "each_amount": "100"        // 1회 발생 물량
  },
  {
    "address": "xpla1...80ae",  // 수익자 주소
    "total_amount": "15000"     // 총 물량
  }
]
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
[
  {
    "address": "xpla1...ylya",  // 수익자 주소
    "stage": 1                  // 1번 스테이지
  },
  {
    "address": "xpla1...80ae",  // 수익자 주소
    "stage": 2                  // 2번 스테이지
  }
]
```

## VestingToken
Token type of vesting.

There are 2 types of the pagination
- "native"
- "cw20"

```typescript
interface VestingToken {
  native?: string,   // 네이티브 토큰의 denom
  cw20?:   address,  // CW20 토큰의 컨트랙트 주소
                     // (반드시 native와 cw20 둘 중 하나만 설정)
}
```
```jsonc
[
  { "native": "axpla" },      // 네이티브 토큰
  { "cw20": "xpla1...wpmw" }  // CW20 토큰
]
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
  schedule:       string,        // 베스팅 스케쥴 종류
                                 //   (linear, periodic, daily_condition, weekly_condition,
                                 //    monthly_condition, yearly_condition)
  start_time:     time,          // 시작 시간(초)
  end_time:       time,          // 끝 시간(초)
  time_interval?: time,          //<- Optional, 시간 간격(초) (periodic의 경우에만)
  month?:         number[],      //<- Optional, 발생 월 (yearly_condition 경우에만)
  day?:           number[],      //<- Optional, 발생 일자 (yearly_condition과 monthly_condition의 경우에만)
  weekday?:       number[],      //<- Optional, 발생 요일 (weekly_condition의 경우에만)
  hour?:          number[],      //<- Optional, 발생 시간 (conditional의 경우에만)
  token:          VestingToken,  // 토큰 종류
  sum_amount:     amount,        // 스테이지 전체 베스팅 물량
  sum_claimed:    amount,        // 이 스테이지에서 현재까지 클레임된 물량
}
```
ref: [VestingToken](#vestingtoken)
```jsonc
{
  "schedule": "periodic",                // 일정 시간 간격의 스케쥴
  "start_time": "1649998800",            // 2022-04-15T05:00:00Z UTC에 시작
  "end_time": "1649999100",              // 2022-04-15T05:05:00Z UTC에 끝
  "time_interval": "60",                 // 1분마다
  "token": { "native": "axpla" },        // axpla 네이티브 토큰
  "sum_amount": "21000000000000000000",  // 스테이지 전체 21xpla 베스팅 예정
  "sum_claimed": "2000000000000000000"   // 본 스테이지에서 현재까지 클레임된 총 물량은 2xpla
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
ref: [VestingToken](#vestingtoken)
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
  hour: number[],  // 발생 시간 목록 (0~23)
}
interface WeeklyCondition {
  weekday: number[],  // 발생 요일 (0:Sun ~ 6:Sat)
  hour:    number[],  // 발생 시간 목록 (0~23)
}
interface MonthlyCondition {
  day:  number[],  // 발생 일자 (1 ~ 31)
  hour: number[],  // 발생 시간 목록 (0~23)
}
interface YearlyCondition {
  month: number[],  // 발생 월 (1 ~ 12)
  day:   number[],  // 발생 일자 (1 ~ 31)
  hour:  number[],  // 발생 시간 목록 (0~23)
}
interface VestingCondition {
  daily?:   DailyCondition,    // 매일 발생하는 조건
  weekly?:  WeeklyCondition,   // 매주 발생하는 조건
  monthly?: MonthlyCondition,  // 매달 발생하는 조건
  yearly?:  YearlyCondition,   // 매년 발생하는 조건
                               // (반드시 한가지 컨디션만 설정)
}
```

### DailyCondition
Vesting occurs at a certain time every day.

```jsonc
// 매일 1시와 13시에 베스팅 발생 (UTC 기준)
{
  "daily": { "hour": [ 1, 13 ] }
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
  name?:    string, //<- Optional, 이 컨트랙트를 나타내는 이름
  webpage?: uri,    //<- Optional, 이 컨트랙트에 대한 정보 및 설명 URI
}
interface exec {
  update_config: UpdateConfig,
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
v┃　　　　　　
e┃　　　　　　
s┃　　　　　　
t┃■■■■■■
 ┗┯━━━━━
 　start
```

```typescript
interface RegisterOnetimeVesting {
  start_time: time,             // 시작 시간(초)
  master?:    address,          //<- Optional, 관리자 주소 (베스팅 스케쥴 취소 가능)
                                //   (따로 지정하지 않으면 register를 실행하는 계정 주소로 default)
  accounts:   AccountAmount[],  // 수익자 목록 (total_amount만 등록 가능)
  info:       VestingInfo,      // 베스팅 상세 정보
}
interface exec {
  register_onetime_vesting: RegisterOnetimeVesting,
}
```
ref: [AccountAmount](#accountamount), [VestingInfo](#vestinginfo)
```jsonc
{
  "register_onetime_vesting": {
    "start_time": "1650016200",
    "end_time": "1650016200",
    "accounts": [
      { "address": "xpla1...ylya", "total_amount": "1000" },
      { "address": "xpla1...80ae", "total_amount": "1500" }
    ],
    "info": {
      "name": "Just Vesting",
      "description": "test"
    }
  }
}
```

## Execute register_linear_vesting
Register a linear vesting schedule for accounts.
Linearly increasing vesting.
```
v┃　　　　 ╱
e┃　　　╱　　
s┃　 ╱ 　　　
t┃╱　　　　　
 ┗┯━━━━┯
 　start　　end
```

```typescript
interface RegisterLinearVesting extends RegisterOnetimeVesting {
  end_time: time,  // 끝 시간(초)
}
interface exec {
  register_linear_vesting: RegisterLinearVesting,
}
```
ref: [RegisterOnetimeVesting](#execute-register_onetime_vesting)
```jsonc
{
  "register_linear_vesting": {
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
interval = □□□
v┃　　　　　　　　　■
e┃　　　　　　■■■■
s┃　　　■■■■■■■
t┃■■■■■■■■■■
 ┗┯━━━━━━━━┯
 　start　　　　　　end
```

```typescript
interface RegisterPeriodicVesting {
  start_time:    time,             // 시작 시간(초)
  end_time:      time,             // 끝 시간(초)
  time_interval: time,             // 시간 간격(초)
  master?:       address,          //<- Optional, 관리자 주소 (베스팅 스케쥴 취소 가능)
                                   //   (따로 지정하지 않으면 register를 실행하는 계정 주소로 default)
  accounts:      AccountAmount[],  // 수익자 목록 (total_amount만 등록 가능)
  info:          VestingInfo,      // 베스팅 상세 정보
}
interface exec {
  register_periodic_vesting: RegisterPeriodicVesting,
}
```
ref: [AccountAmount](#accountamount), [VestingInfo](#vestinginfo)
```jsonc
{
  "register_periodic_vesting": {
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

## Execute register_conditional_vesting
Register a conditional vesting schedule for accounts.
Vesting that occurs when the condition is satisfied.
```
condition = 📅⏰
```

```typescript
interface RegisterConditionalVesting {
  start_time:    time,              // 시작 시간(초)
  end_time:      time,              // 끝 시간(초)
  condition:     VestingCondition,  // 조건 내용
  master?:       address,           //<- Optional, 관리자 주소 (베스팅 스케쥴 취소 가능)
                                    //   (따로 지정하지 않으면 register를 실행하는 계정 주소로 default)
  accounts:      AccountAmount[],   // 수익자 목록 (total_amount만 등록 가능)
  info:          VestingInfo,       // 베스팅 상세 정보
}
interface exec {
  register_conditional_vesting: RegisterConditionalVesting,
}
```
ref: [AccountAmount](#accountamount), [VestingInfo](#vestinginfo)
```jsonc
{
  "register_conditional_vesting": {
    "start_time": time,  // 시작 시간(초)
    "end_time":   time,  // 끝 시간(초)
    "condition": {       // 조건 내용
      ...
    },
    "master":        address,  //<- Optional, 관리자 주소 (베스팅 스케쥴 취소 가능)
                               //   (따로 지정하지 않으면 register를 실행하는 계정 주소로 default)
    "accounts": [              // 수익자 목록
      {
        "address":      address,  // 수익자 주소
        "each_amount":  amount,   //<- Optional, 해당 수익자에게 베스팅할 1회 발생 물량
        "total_amount": amount,   //<- Optional, 해당 수익자에게 베스팅할 총 물량
                                  //   (반드시 each_amount와 total_amount 둘 중 하나만 설정)
      },
      ...
    ],
    "info": {  //<- Optional, 베스팅 설명 정보
      "name":        string,  //<- Optional, 베스팅 이름
      "description": string,  //<- Optional, 베스팅 내용 설명
      "icon":        uri,     //<- Optional, 베스팅 아이콘
      "image":       uri,     //<- Optional, 베스팅 이미지
      "webpage":     uri,     //<- Optional, 베스팅 설명 페이지
    },
  }
}
```

There are 4 types of the condition
- "daily": Occurs at a certain time every day.
- "weekly": Occurs every week.
- "monthly": Occurs every month.
- "yearly": Occurs every month.

### daily condition
```jsonc
{
  "register_conditional_vesting": {
    ...
    "condition": {
      "daily": {                  // 매일 발생
        "hour": [ number, ... ],  // 발생 시간 (0~23)
      }
    },
    ...
  }
}
```

```jsonc
{
  "register_conditional_vesting": {
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
    ...
    "condition": {
      "weekly": {                    // 매주 발생
        "weekday": [ number, ... ],  // 발생 요일 (0:Sun ~ 6:Sat)
        "hour":    [ number, ... ],  // 발생 시간 (0~23)
      }
    },
    ...
  }
}
```

```jsonc
{
  "register_conditional_vesting": {
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
    ...
    "condition": {
      "monthly": {                // 매월 발생
        "day":  [ number, ... ],  // 발생 일자 (1 ~ 31)
        "hour": [ number, ... ],  // 발생 시간 (0~23)
      }
    },
    ...
  }
}
```

```jsonc
{
  "register_conditional_vesting": {
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
    ...
    "condition": {
      "yearly": {                  // 매년 발생
        "month": [ number, ... ],  // 발생 월 (1 ~ 12)
        "day":   [ number, ... ],  // 발생 일자 (1 ~ 31)
        "hour":  [ number, ... ],  // 발생 시간 (0~23)
      }
    },
    ...
  }
}
```

```jsonc
{
  "register_conditional_vesting": {
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

## Vesting for CW20 tokens
When registering the schedule, you must send all the tokens to be vast.
Vesting is performed with the Denom of the token transmitted.

In order to register as a CW20 token, an account with a token
You must send the Vesting Registration Message by sending the quantity to the vesting contract.
```javascript
  const vesting_msg = {  // 베스팅 등록 메시지
    register_linear_vesting: {
      start_time: '' + ((new Date('2022-04-15T05:00:00.000Z')).getTime() / 1000),
      end_time: '' + ((new Date('2022-04-15T05:10:00.000Z')).getTime() / 1000),
      accounts: [
        { address: 'xpla1...ylya', total_amount: '1000' },
        { address: 'xpla1...80ae', total_amount: '1500' }
      ],
      info: {
        name: 'Gamer\'s Vesting',
        description: 'vesting test 1'
      }
    }
  };

  const execute = new MsgExecuteContract(
    'xpla1...c08z',  // 물량을 보내는 계정
    'xpla1...f49y',  // cw20 토큰 컨트랙트
    {
      send: {
        amount: '2500',            // 총 베스팅 물량을 담아서
                                   // (vesting_amount 쿼리의 결과와 일치해야함)
        contract: 'xpla1...qgaw',  // 베스팅 컨트랙트 주소
        msg: Buffer.from(JSON.stringify(vesting_msg), 'utf8').toString('base64'),
                                   // 베스팅 메시지를 base64로 인코딩
      }
    }
  );
  
  const send_tx = await wallet.createAndSignTx({
    msgs: [execute],
    gasPrices: { axpla: 850000000000 },
  });
  const txres = await lcd.tx.broadcastSync(send_tx);
```


## Execute deregister_vesting_accounts
Cancel the vesting schedule for accounts.

You should run using the master address that is registered.

```jsonc
{
  "deregister_vesting_accounts": {
    "accounts": [  // 등록을 해제할 수익자 목록
      {
        "address": address,  // 수익자 주소
        "stage":   number    // 스테이지 번호
      },
      ...
    ]
  }
}
```

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

```jsonc
{
  "deregister_vesting_stage": {
    "stage": number  // 전체 등록을 해제할 스테이지 번호
  }
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

```jsonc
{
  "update_vesting_info": {
    "stage": number,  // 정보를 갱신할 스테이지 번호
    "info": {  // 베스팅 설명 정보
      "name":        string,  //<- Optional, 베스팅 이름
      "description": string,  //<- Optional, 베스팅 내용 설명
      "icon":        uri,     //<- Optional, 베스팅 아이콘
      "image":       uri,     //<- Optional, 베스팅 이미지
      "webpage":     uri,     //<- Optional, 베스팅 설명 페이지
    },
  }
}
```

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

```jsonc
{
  "claim": {
    "stages": [  // 클레임할 베스팅 스테이지 목록
      number,    // 스테이지 번호
      ...
    ]
  }
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
```jsonc
{
  "vesting_account": {
    "address": address,     // 수익자 주소
    "pagination": { ... },  //<- Optional, 페이지 나누는 방식
  }
}
```
Output:
```jsonc
{
  "address": address,  // 수익자 주소
  "vestings": [        // 베스팅 목록
    {
      "stage":         number,     // 베스팅 스테이지 번호
      "schedule":      string,     // 베스팅 스케쥴 종류 (linear, periodic, daily_condition, weekly_condition, monthly_condition, yearly_condition)
      "start_time":    time,       // 시작 시간(초)
      "end_time":      time,       // 끝 시간(초)
      "time_interval": time,       //<- Optional, 시간 간격(초) (periodic의 경우에만)
      "month":   [ number, ... ],  // 발생 월 (yearly_condition 경우에만)
      "day":     [ number, ... ],  // 발생 일자 (yearly_condition과 monthly_condition의 경우에만)
      "weekday": [ number, ... ],  // 발생 요일 (weekly_condition의 경우에만)
      "hour":    [ number, ... ],  // 발생 시간 (conditional의 경우에만)
      "master":        address,    // 관리자 주소
      "name":          string,     //<- Optional, 베스팅 이름
      "description":   string,     //<- Optional, 내용 설명
      "icon":          uri,        //<- Optional, 아이콘
      "image":         uri,        //<- Optional, 이미지
      "webpage":       uri,        //<- Optional, 설명 페이지
      "token": { ... },            // 토큰 종류
      "amounts": {                 // 물량 확인
        "total":       amount,     // 총 베스팅 물량
        "vested":      amount,     // 현재까지 발생된 물량
        "claimed":     amount,     // 이미 받아간 물량
        "claimable":   amount,     // 클레임 가능한 물량
      }
    },
    ...
  ]
}
```

There are 2 types of the pagination
- "offset"
- "stage"

```jsonc
{
  "vesting_account": {
    ...
    "pagination": {
      "offset": {
        "start": 5,   //<- Optional, 5번째부터
        "limit": 10,  // 10개만 선택
      }
    },
  }
}
```
```jsonc
{
  "vesting_account": {
    ...
    "pagination": {
      "stage": {
        "after": 2,  //<- Optional, 2번 스테이지 뒤부터
        "limit": 5,  // 5개만 선택
      }
    },
  }
}
```

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
      "schedule": "linear",
      "start_time": "1650379800",
      "end_time": "1650379800",
      "time_interval": null,
      "month": [],
      "day": [],
      "weekday": [],
      "hour": [],
      "master": "xpla1...0r7f",
      "name": "Gamer's Vesting",
      "description": "vesting test 1",
      "icon": null,
      "image": null,
      "webpage": null,
      "token": { "native": "axpla" },
      "amounts": {
        "total": "1000000",
        "vested": "200000",
        "claimed": "150000",
        "claimable": "50000"
      }
    },
    {
      "stage": 2,
      "schedule": "periodic",
      "start_time": "1650015000",
      "end_time": "1650015300",
      "time_interval": "60",
      "month": [],
      "day": [],
      "weekday": [],
      "hour": [],
      "master": "xpla1...0r7f",
      "name": "Investor's Vesting",
      "description": null,
      "icon": null,
      "image": null,
      "webpage": null,
      "token": { "cw20": "xpla1...92f4" },
      "amounts": {
        "total": "1500000",
        "vested": "1000",
        "claimed": "0",
        "claimable": "1000"
      }
    },
    {
      "stage": 3,
      "schedule": "daily_condition",
      "start_time": "1650343500",
      "end_time": "1650553200",
      "time_interval": null,
      "month": [],
      "day": [],
      "weekday": [],
      "hour": [5],
      "master": "xpla1...0r7f",
      "name": "test3",
      "description": null,
      "icon": null,
      "image": null,
      "webpage": null,
      "token": { "native": "axpla" },
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
```jsonc
{
  "periodic_vesting_amount": {
    "start_time":    time,   // 시작 시간(초)
    "end_time":      time,   // 끝 시간(초)
    "time_interval": time,   // 시간 간격(초)
    "accounts": [            // 수익자 목록
      {
        "address":      address,  // 수익자 주소
        "each_amount":  amount,   //<- Optional, 해당 수익자에게 베스팅할 1회 발생 물량
        "total_amount": amount,   //<- Optional, 해당 수익자에게 베스팅할 총 물량
                                  //   (반드시 each_amount와 total_amount 둘 중 하나만 설정)
      },
      ...
    ],
  }
}
```
Output:
```jsonc
{
  "account_count": number,  // 수익자 수
  "vesting_count": number,  // 베스팅 발생 횟수
  "total_amount":  amount,  // 전체 물량
}
```

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
    ],
  }
}
```
Output:
```jsonc
{
  "account_count": 2,      // 계정 2개
  "vesting_count": 5,      // 5회의 발생
  "total_amount": "21000"  // each(1000*5) + total(15000)
}
```

## Query conditional_vesting_amount
Predicted of conditional vesting amounts.

Input:
```jsonc
{
  "conditional_vesting_amount": {
    "start_time":    time,   // 시작 시간(초)
    "end_time":      time,   // 끝 시간(초)
    "condition": { ... },    // 조건 내용
    "accounts": [            // 수익자 목록
      {
        "address":      address,  // 수익자 주소
        "each_amount":  amount,   //<- Optional, 해당 수익자에게 베스팅할 1회 발생 물량
        "total_amount": amount,   //<- Optional, 해당 수익자에게 베스팅할 총 물량
                                  //   (반드시 each_amount와 total_amount 둘 중 하나만 설정)
      },
      ...
    ],
  }
}
```
Output:
```jsonc
{
  "account_count": number,  // 수익자 수
  "vesting_count": number,  // 베스팅 조건 발생 횟수
  "total_amount":  amount,  // 전체 물량
}
```

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
    ],
  }
}
```
Output:
```jsonc
{
  "account_count": 2,      // 계정 2개
  "vesting_count": 16,     // 16회의 발생
  "total_amount": "31000"  // each(1000*16) + total(15000)
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
```jsonc
{
  "vesting_stages": {
    "pagination": { ... },  //<- Optional, 페이지 나누는 방식
  }
}
```
Output:
```jsonc
{
  "vestings": [
    {
      "stage":         number,     // 베스팅 스테이지 번호
      "schedule":      string,     // 베스팅 스케쥴 종류 (linear, periodic, daily_condition, weekly_condition, monthly_condition, yearly_condition)
      "start_time":    time,       // 시작 시간(초)
      "end_time":      time,       // 끝 시간(초)
      "time_interval": time,       //<- Optional, 시간 간격(초) (periodic의 경우에만)
      "month":   [ number, ... ],  // 발생 월 (yearly_condition 경우에만)
      "day":     [ number, ... ],  // 발생 일자 (yearly_condition과 monthly_condition의 경우에만)
      "weekday": [ number, ... ],  // 발생 요일 (weekly_condition의 경우에만)
      "hour":    [ number, ... ],  // 발생 시간 (conditional의 경우에만)
      "master":        address,    // 관리자 주소
      "name":          string,     //<- Optional, 베스팅 이름
      "description":   string,     //<- Optional, 내용 설명
      "icon":          uri,        //<- Optional, 아이콘
      "image":         uri,        //<- Optional, 이미지
      "webpage":       uri,        //<- Optional, 설명 페이지
      "account_count": number,     // 이 스테이지에 속한 계정 개수
      "token": { ... },            // 토큰 종류
      "total_amount":  amount,     // 스테이지 전체 베스팅 물량
      "total_claimed": amount,     // 현재까지 클레임된 전체 물량
    },
    ...
  ],
}
```

Input:
```jsonc
{
  "vesting_stages": {
    "pagination": {
      "offset": { "start": 1, "limit": 1, }
    }
  }
}
```
Output:
```jsonc
{
  "vestings": [
    {
      "stage": 2,
      "schedule": "periodic",
      "start_time": "1649911800",
      "end_time": "1649912100",
      "time_interval": "60",
      "month": [],
      "day": [],
      "weekday": [],
      "hour": [],
      "master": "xpla1...0r7f",
      "name": "Investor's Vesting",
      "description": "vesting test 2",
      "icon": null,
      "image": null,
      "webpage": null,
      "account_count": 2,
      "token": { "native": "axpla" },
      "total_amount": "31000",
      "total_claimed": "1000"
    }
  ]
}
```
