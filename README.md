# XPLA Vesting
This contract is to provide vesting account features for the both cw20 and native tokens.


# Types

## Pagination
Pagination for a list output.

There are 2 types of the pagination
- "offset"
- "stage"

![](https://img.shields.io/badge/-JSON-666666?logo=json&logoColor=ffffff)```jsonc
{
  "offset": {
    "start": number,  //<- Optional, 시작 위치
    "limit": number,  // 출력 개수
  }
}
```
```jsonc
{
  "stage": {
    "after": number,  //<- Optional, 다음부터 시작할 스테이지 번호
    "limit": number,  // 출력 개수
  }
}
```
![](https://img.shields.io/badge/-JavaScript-666666?logo=javascript&logoColor=f7df1e)```javascript
const pagination = {
  offset: {
    start: 5,   //<- Optional, 5번째부터
    limit: 10,  // 10개만 선택
  },
};
```
```javascript
const pagination = {
  stage: {
    after: 2,  //<- Optional, 2번 스테이지 다음부터
    limit: 5,  // 5개만 선택
  },
};
```

## AccountAmount
Amounts for an account.

```jsonc
{
  "address":      address,  // 수익자 주소
  "each_amount":  amount,   //<- Optional, 해당 수익자에게 베스팅할 1회 발생 물량
  "total_amount": amount,   //<- Optional, 해당 수익자에게 베스팅할 총 물량
                            //   (반드시 each_amount와 total_amount 둘 중 하나만 설정)
}
```
```javascript
const accounts = [
  {
    address: 'xpla1...ylya',  // 수익자 주소
    each_amount: '100',       // 1회 발생 물량
  },
  {
    address: 'xpla1...80ae',  // 수익자 주소
    total_amount: '15000',    // 총 물량
  },
];
```

## AccountStage
Stage of an account.

```jsonc
{
  "address": address,  // 수익자 주소
  "stage":   number,   // 해당 수익자가 등록된 스테이지 번호
}
```
```javascript
const accounts = [
  {
    address: 'xpla1...ylya',  // 수익자 주소
    stage: 1,                 // 1번 스테이지
  },
  {
    address: 'xpla1...80ae',  // 수익자 주소
    stage: 2,                 // 2번 스테이지
  },
];
```

## VestingToken
Token type of vesting.

There are 2 types of the pagination
- "native"
- "cw20"

```jsonc
{
  "native": string,  // 네이티브 토큰의 denom
}
```
```jsonc
{
  "cw20": address,  // CW20 토큰의 컨트랙트 주소
}
```

```javascript
// 네이티브 토큰
const token = {
  native: 'axpla',
};
```
```javascript
// CW20 토큰
const token = {
  cw20: 'xpla1...wpmw',
};
```

## VestingInfo
Infomations of a vesting.

```jsonc
{
  "name":        string,  //<- Optional, 베스팅 이름
  "description": string,  //<- Optional, 베스팅 내용 설명
  "icon":        uri,     //<- Optional, 베스팅 아이콘
  "image":       uri,     //<- Optional, 베스팅 이미지
  "webpage":     uri,     //<- Optional, 베스팅 설명 페이지
}
```
```javascript
const info = {
  name: 'Vesting',
  description: 'vesting test #1',
  image: 'https://test.page/vesting.png',
};
```

## VestingStatus
Status of a vesting.

```jsonc
{
  "schedule":      string,        // 베스팅 스케쥴 종류
                                  //   (linear, periodic, daily_condition, weekly_condition,
                                  //    monthly_condition, yearly_condition)
  "start_time":    time,          // 시작 시간(초)
  "end_time":      time,          // 끝 시간(초)
  "time_interval": time,          //<- Optional, 시간 간격(초) (periodic의 경우에만)
  "month":   [ number, ... ],     // 발생 월 (yearly_condition 경우에만)
  "day":     [ number, ... ],     // 발생 일자 (yearly_condition과 monthly_condition의 경우에만)
  "weekday": [ number, ... ],     // 발생 요일 (weekly_condition의 경우에만)
  "hour":    [ number, ... ],     // 발생 시간 (conditional의 경우에만)
  "token":         VestingToken,  // 토큰 종류
  "sum_amount":    amount,        // 스테이지 전체 베스팅 물량
  "sum_claimed":   amount,        // 이 스테이지에서 현재까지 클레임된 물량
}
```
ref: [VestingToken](#vestingtoken)
```javascript
const status = {
  schedule: 'periodic',
  start_time: '' + ((new Date('2022-04-15T05:00:00Z')).getTime() / 1000),
  end_time: '' + ((new Date('2022-04-15T05:05:00Z')).getTime() / 1000),
  time_interval: '60',
  month: [],
  day: [],
  weekday: [],
  hour: [],
  token: { native: 'axpla' },
  sum_amount: '21000',
  sum_claimed: '2000',
};
```

## VestingAmounts
Amounts of a vesting.

```jsonc
{
  "token":     VestingToken,  // 토큰 종류
  "total":     amount,        // 총 베스팅 물량
  "vested":    amount,        // 현재까지 발생된 물량
  "claimed":   amount,        // 이미 받아간 물량
  "claimable": amount,        // 클레임 가능한 물량
}
```
ref: [VestingToken](#vestingtoken)
```javascript
const status = {
  schedule: 'periodic',
  start_time: '' + ((new Date('2022-04-15T05:00:00Z')).getTime() / 1000),
  end_time: '' + ((new Date('2022-04-15T05:05:00Z')).getTime() / 1000),
  time_interval: '60',
  month: [],
  day: [],
  weekday: [],
  hour: [],
  token: { native: 'axpla' },
  sum_amount: '21000',
  sum_claimed: '2000',
};
```

## VestingCondition
Condition for a conditional vesting.

There are 4 types of the condition
- "daily": Occurs at a certain time every day.
- "weekly": Occurs every week.
- "monthly": Occurs every month.
- "yearly": Occurs every year.

### daily condition
Vseting occurs at a certain time every day.

```json
{
  "daily": {  // 매일 발생
    "hour": [ number, ... ],  // 발생 시간 목록 (0~23)
  }
}
```
```javascript
// 매일 1시와 13시에 베스팅 발생 (UTC 기준)
const condition = {
  daily: {
    hour: [ 1, 13 ],
  },
};
```

### weekly condition
Vesting occurs every week.

```json
{
  "weekly": {  // 매주 발생
    "weekday": [ number, ... ],  // 발생 요일 (0:Sun ~ 6:Sat)
    "hour":    [ number, ... ],  // 발생 시간 (0~23)
  }
}
```
```javascript
// 매주 월,수,금요일마다 12시에 베스팅 발생 (UTC 기준)
const condition = {
  weekly: {
    weekday: [ 1, 3, 5 ],
    hour: [ 12 ],
  },
};
```

### monthly condition
Vesting occurs every month.

```json
{
  "monthly": {  // 매월 발생
    "day":  [ number, ... ],  // 발생 일자 (1 ~ 31)
    "hour": [ number, ... ],  // 발생 시간 (0~23)
  }
}
```
```javascript
// 매달 1일과 15일의 6,12,18시에 베스팅 발생 (UTC 기준)
const condition = {
  monthly: {
    day: [ 1, 15 ],
    hour: [ 6, 12, 18 ],
  },
};
```

### yearly condition
Vesting occurs every year.

```json
{
  "yearly": {  // 매년 발생
    "month": [ number, ... ],  // 발생 월 (1 ~ 12)
    "day":   [ number, ... ],  // 발생 일자 (1 ~ 31)
    "hour":  [ number, ... ],  // 발생 시간 (0~23)
  }
}
```
```javascript
// 매년 1,4,7,10월의 20일 0시에 베스팅 발생 (UTC 기준)
const condition = {
  "yearly": {
    "month": [ 1, 4, 7, 10 ],
    "day": [ 20 ],
    "hour": [ 0 ],
  },
};
```


# Operations

## Init
Instantiate contract.

```json
{
  "owner":           address, // 이 컨트랙트를 관리할 소유자 주소
  "name":            string,  // 이 컨트랙트를 나타내는 이름
  "description_uri": string,  //<- Optional, 이 컨트랙트에 대한 정보 및 설명 URI
}
```

```json
{
  "owner": "xpla1...c9v5",
  "name": "vesting test"
}
```

## Execute update_config
Change settings.
Run as an owner address.

```json
{
  "update_config": {
    "name":            string, //<- Optional, 이 컨트랙트를 나타내는 이름
    "description_uri": string, //<- Optional, 이 컨트랙트에 대한 정보 및 설명 URI
  }
}
```

```json
{
  "update_config": {
    "description_uri": "https://test.page/vesting"
  }
}
```

## Execute register_linear_vesting
Register a linear vesting schedule for accounts.
Linearly increasing vesting.

```json
{
  "register_linear_vesting": {
    "start_time":  time,     // 시작 시간(초)
    "end_time":    time,     // 끝 시간(초)
    "master":      address,  //<- Optional, 관리자 주소 (베스팅 스케쥴 취소 가능)
                             //   (따로 지정하지 않으면 register를 실행하는 계정 주소로 default)
    "accounts": [  // 수익자 목록
      {
        "address":      address,  // 수익자 주소
        "total_amount": amount,   // 해당 수익자에게 베스팅할 총 물량
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

```json
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

```json
{
  "register_periodic_vesting": {
    "start_time":    time,     // 시작 시간(초)
    "end_time":      time,     // 끝 시간(초)
    "time_interval": time,     // 시간 간격(초)
    "master":        address,  //<- Optional, 관리자 주소 (베스팅 스케쥴 취소 가능)
                               //   (따로 지정하지 않으면 register를 실행하는 계정 주소로 default)
    "accounts": [  // 수익자 목록
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

```json
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

```json
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
```json
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

```json
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
```json
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

```json
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
```json
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

```json
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
```json
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

```json
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

```json
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

```json
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

```json
{
  "deregister_vesting_stage": {
    "stage": number  // 전체 등록을 해제할 스테이지 번호
  }
}
```

```json
{
  "deregister_vesting_stage": {
    "stage": 1
  }
}
```


## Execute update_vesting_info
Update the vesting info.

You should run using the owner address or the master address that is registered.

```json
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

```json
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

```json
{
  "claim": {
    "stages": [  // 클레임할 베스팅 스테이지 목록
      number,    // 스테이지 번호
      ...
    ]
  }
}
```

```json
{
  "claim": {
    "stages": [ 1, 2 ]
  }
}
```


## Query config

Input:
```json
{
  "config": {}
}
```
Output:
```json
{
  "owner": "xpla1...c9v5",
  "name": "vesting test",
  "description_uri": null
}
```


## Query vesting_account
Check the registered vesting schedule.

Input:
```json
{
  "vesting_account": {
    "address": address,     // 수익자 주소
    "pagination": { ... },  //<- Optional, 페이지 나누는 방식
  }
}
```
Output:
```json
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

```json
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
```json
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
```json
{
  "vesting_account": {
    "address": "xpla1...ylya"
  }
}
```
Output:
```json
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
```json
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
```json
{
  "account_count": number,  // 수익자 수
  "vesting_count": number,  // 베스팅 발생 횟수
  "total_amount":  amount,  // 전체 물량
}
```

Input:
```json
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
```json
{
  "account_count": 2,      // 계정 2개
  "vesting_count": 5,      // 5회의 발생
  "total_amount": "21000"  // each(1000*5) + total(15000)
}
```

## Query conditional_vesting_amount
Predicted of conditional vesting amounts.

Input:
```json
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
```json
{
  "account_count": number,  // 수익자 수
  "vesting_count": number,  // 베스팅 조건 발생 횟수
  "total_amount":  amount,  // 전체 물량
}
```

Input:
```json
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
```json
{
  "account_count": 2,      // 계정 2개
  "vesting_count": 16,     // 16회의 발생
  "total_amount": "31000"  // each(1000*16) + total(15000)
}
```

## Query latest_stage

Input:
```json
{
  "latest_stage": {}
}
```
Output:
```json
{
  "latest_stage": 5
}
```

## Query vesting_stages
Status for vesting stages.

Input:
```json
{
  "vesting_stages": {
    "pagination": { ... },  //<- Optional, 페이지 나누는 방식
  }
}
```
Output:
```json
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
```json
{
  "vesting_stages": {
    "pagination": {
      "offset": { "start": 1, "limit": 1, }
    }
  }
}
```
Output:
```json
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
