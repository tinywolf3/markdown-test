# markdown-test

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


## VestingAmounts
Amounts of a vesting.

```jsonc
{
  "token":     ```[VestingToken](#vestingtoken)```,  // 토큰 종류
  "total":     amount,        // 총 베스팅 물량
  "vested":    amount,        // 현재까지 발생된 물량
  "claimed":   amount,        // 이미 받아간 물량
  "claimable": amount,        // 클레임 가능한 물량
}
```
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
