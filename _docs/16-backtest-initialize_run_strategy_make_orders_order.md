---
title: "03~06 initialize, run_strategy, make_orders, Order"
permalink: /docs/backtest-initialize_run_strategy_make_orders_order/
excerpt: "CATS Lab Backtest 03 initialize, run_strategy, make_orders, Order"
last_modified_at: 2019-04-25T15:30:00
redirect_from:
  - /theme-setup/
toc: true
---

# 3. ~ 6. initialize, run_strategy, make_orders, Order 작성

## 3. initialize의 작성

[Trade-03 initialize](/catslab_docs/docs/trade-initialize)의 initialize 함수와 동일합니다.


## 4. run_strategy 작성

기본적으로 [Trade-04 run_strategy](/catslab_docs/docs/trade-run_strategy)의 run_strategy 함수와 동일하나 1분 주기로 dataframe을 업데이트하고 그에 따른 기술적 지표를 업데이트하는 Trade에서와는 달리, Backtest에서는 dataframe의 업데이트가 발생하지 않고 처음 BacktestContext를 생성할 때 입력된 기간동안의 dataframe 및 기술적 지표를 한 번에 연산하여 저장합니다. 

백테스트를 실행하면 사용자가 입력한 가상화폐와 분봉들마다 저장해놓은 dataframe을 순차적으로 잘라서 가져오는 방식이기 때문에 1분 주기로 run_strategy를 실행하는 과정이 생략됩니다.


## 5. make_orders 작성

[Trade-05 make_orders](/catslab_docs/docs/trade-make_orders)의 make_orders 함수와 동일합니다.


## 6. Order 객체 생성

[Trade-06 Order](/catslab_docs/docs/trade-order)의 Order 객체와 동일합니다.








