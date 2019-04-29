---
title: "07 Backtest Result"
permalink: /docs/backtest-result/
excerpt: "CATS Lab Backtest 04 Result"
last_modified_at: 2019-04-25T15:40:00
redirect_from:
  - /theme-setup/
toc: true
---

## 7. Result 객체의 생성 및 사용 예시


생성한 BacktestContext를 run 함수를 이용하여 백테스트를 실행시킨 뒤 그 결과를 Result 객체로 생성하여 저장 및 반환합니다. Result 객체에 저장되어 있는 정보는 아래와 같습니다. 

백테스트 결과를 도식화 하는 plot 함수의 기능은 [Backtest-08 plot](/catslab_docs/docs/backtest-plot/)을 참고하시기 바랍니다.

백테스트 결과를 저장하기 위해 지정한 RESULT_NAME을 이용하여 각각의 정보로 접근할 수 있습니다.


```python
RESULT_NAME = BacktestContext_NAME.run(start_date, end_date, exchange, init_budget, slippage_rate)
```

![07 Result 객체 Example01](https://user-images.githubusercontent.com/47657715/56654634-83f86880-66cb-11e9-9f51-ddfedfeb305c.png)
![07 Result 객체 Example02](https://user-images.githubusercontent.com/47657715/56654635-8490ff00-66cb-11e9-92ea-58264b3b7f6c.png)


### 7.1. created_time  
백테스트를 실행하여 Result 객체를 생성한 시간에 대한 정보입니다.



### 7.2. elapsed_time  
백테스트를 수행하는 데에 소요된 전체 경과시간에 대한 정보입니다. 



### 7.3. earning_rate  
백테스트 초기 투입 자산 대비 종료 시점의 자산의 수익률을 나타내는 정보입니다. 

![07 Result 객체 Example03](https://user-images.githubusercontent.com/47657715/56654638-8490ff00-66cb-11e9-93d5-96c37e941859.png)


### 7.4. final_balance  
백테스트 종료 후 최종 시점의 자산 보유량에 대한 정보입니다.

![B.7 Result 객체 Example04](https://user-images.githubusercontent.com/47657715/56654639-85299580-66cb-11e9-9577-1fdb53e774fd.png)


### 7.5. estimated_list  
백테스트를 진행하는 과정에서 매수 또는 매도가 발생했을 때의 거래 정보 및 그 시점에 보유한 모든 자산의 현금 평가가격에 대한 정보를 저장하고 있는 list입니다.

estimated_list에 저장되는 정보의 타입은 ‘datetime’이 key값이고 거래가 발생하는 시점의 datetime을 value값으로 갖는 정보와 ‘estimated’가 key값이고 그 시점의 현금 평가가격을 value값으로 가지는 dictionary 타입입니다.

![07 Result 객체 Example05](https://user-images.githubusercontent.com/47657715/56654640-85299580-66cb-11e9-87cd-ec96fc849106.png)


### 7.6. total_fee  
백테스트를 진행하는 과정에서 일어난 거래들의 총 수수료를 나타내는 정보입니다.

![07 Result 객체 Example06](https://user-images.githubusercontent.com/47657715/56654642-85c22c00-66cb-11e9-8e7d-b00f8584b254.png)


### 7.7. total_slippage  
백테스트를 진행하는 과정에서 입력한 slippage_rate을 기반으로 계산한 총 슬리피지 비용을 나타내는 정보입니다.

![07 Result 객체 Example07](https://user-images.githubusercontent.com/47657715/56654643-85c22c00-66cb-11e9-92ff-aa45a09a1593.png)


### 7.8. max_profit  
백테스트를 진행하는 과정에서 최대로 얻은 이익의 수익률에 대한 정보입니다.

![07 Result 객체 Example08](https://user-images.githubusercontent.com/47657715/56654646-865ac280-66cb-11e9-80c2-e9bfa92eed3e.png)


### 7.9. max_loss  
백테스트를 진행하는 과정에서 최대로 손실을 보게 된 시점의 수익률에 대한 정보입니다.

![07 Result 객체 Example09](https://user-images.githubusercontent.com/47657715/56654648-86f35900-66cb-11e9-99be-7c1514705f57.png)


### 7.10. trade_history  
백테스트 진행 과정에서 일어난 모든 거래에 대한 정보를 저장하고 있는 dictionary입니다. 

거래가 일어난 시점의 datetime을 key값으로 하고, 거래가 일어난 시점의 자산 보유 현황('balance')과, 수익률('earning_rate'), 평가가격('estimated'), Order 객체로 생성되어 저장되어 있는 'order_list'에 대한 정보가 포함되어 있습니다. 

![07 Result 객체 Example10](https://user-images.githubusercontent.com/47657715/56654651-86f35900-66cb-11e9-9832-626821d66213.png)






