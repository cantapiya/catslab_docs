---
title: "01 BacktestContext"
permalink: /docs/backtest-backtestcontext/
excerpt: "CATS Lab Backtest 01 BacktestContext"
last_modified_at: 2019-04-25T15:10:00
redirect_from:
  - /theme-setup/
toc: true
---

## 1. BacktestContext의 생성


BACKTEST_NAME을 지정하여 아래의 parameter를 입력하여 BacktestContext를 생성할 수 있습니다. 


```python
BACKTEST_NAME = BacktestContext(user_uuid, 
			initialize, 
			run_strategy, 
			make_orders, 
			use_data, 
			data_path, 
			save_result, 
			return_result)
```

### 1.1. user_uuid  
Cats Lab에서 발급 받은 사용자의 user_uuid를 입력합니다.


### 1.2. initialize  
[Trade-03 initialize](/catslab_docs/docs/trade-initialize)의 initialize 함수와 동일합니다.


### 1.3. run_strategy  
[Trade-04 run_strategy](/catslab_docs/docs/trade-run_strategy)의 run_strategy 함수와 동일합니다.


### 1.4. make_orders  
[Trade-05 make_orders](/catslab_docs/docs/trade-make_orders)의 make_orders 함수와 동일합니다.


### 1.5. use_data  
use_data parameter는 string 타입으로, ‘LIVE’ 또는 ‘LOCAL’의 입력이 가능합니다.  

use_data = “LIVE”로 입력할 경우 거래소 API를 이용하여 입력한 기간만큼의 캔들 데이터를 실시간으로 호출하여 사용합니다.

use_data = “LOCAL”로 입력할 경우 지정한 경로의 입력한 가상화폐와 분봉에 해당하는 CSV 파일을 로드하여 사용합니다.


<img width="555" alt="local candle data 파일의 format" src="https://user-images.githubusercontent.com/47657715/56639764-6536a980-66ac-11e9-9a3a-3d5c311beb0f.png">



### 1.6. data_path  
1.5. use_data에서 ‘LOCAL’ 모드로 입력하여 사용하는 경우, 캔들 데이터로 사용할 CSV 파일들의 경로를 입력합니다.


### 1.7. return_result  
백테스트의 결과를 반환할 것인지에 대한 여부를 bool 타입으로 입력합니다. 

로컬에서 백테스트를 실행할 경우 백테스트 결과를 확인하기 위해 True로 입력하여 backtest의 결과에 대한 정보가 저장된 result 객체를 반환 받을 수 있습니다.










