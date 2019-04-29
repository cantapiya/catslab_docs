---
title: "02 Run Backtest"
permalink: /docs/backtest-run/
excerpt: "CATS Lab Backtest 02 Running Backtest"
last_modified_at: 2019-04-25T15:20:00
redirect_from:
  - /theme-setup/
toc: true
---

## 2. Backtest 실행

생성한 BacktestContext를 run 함수를 이용하여 백테스트를 실행시킵니다. 
결과를 저장할 RESULT_NAME을 지정합니다.

```python
RESULT_NAME = BACKTEST_NAME.run(start_date, 
			end_date, 
			exchange, 
			init_budget, 
			slippage_rate)
```

### 2.1. start_date  
백테스트를 실행할 기간 중 시작 시점의 datetime(datetime.datetime)을 입력합니다.


### 2.2. end_date  
백테스트를 실행할 기간 중 종료 시점의 datetime(datetime.datetime)을 입력합니다.


### 2.3. exchange  
백테스트를 실행할 때에 사용할 거래소의 명칭을 입력합니다.


### 2.4. init_budget  
백테스트의 초기 투입 자산을 입력합니다.


### 2.5. slippage_rate  
백테스트를 실행할 때 고려할 슬리피지 비율을 입력합니다. 

실제 거래와 달리 백테스트에서는 주문한 매수주문/매도주문은 즉시 체결된 것으로 간주하여 결과를 반영하기 때문에, 실제 거래에서 발생할 수 있는 주문 미체결의 문제와 호가의 차이를 고려한 것이 슬리피지 비율입니다.

슬리피지 비율을 입력한 경우 매 거래가 발생 시 거래 수수료와 더불어 슬리피지 비용이 함께 차감되어 자산에 반영됩니다.






