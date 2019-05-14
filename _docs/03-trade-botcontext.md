---
title: "05 BotContext"
permalink: /docs/trade-botcontext/
excerpt: "CATS Lab Trade 01 BotContext"
last_modified_at: 2019-04-25T13:10:00
redirect_from:
  - /theme-setup/
toc: true
---


## 1. BotContext의 생성

BOT_NAME을 지정하여 아래의 parameter를 입력하여 BotContext를 생성할 수 있습니다. 

```python
BOT_NAME = BotContext(api_key, 
		secret_key, 
		exchange,
		user_uuid,
		bot_id, 
		initialize, 
		run_strategy, 
		make_orders,
		init_budget,
		running_mode,
		using_api)
```

 
### 1.1. api_key  
거래소로부터 발급 받은 사용자의 거래소 API key(또는 Access Key)를 string 타입으로 입력합니다.


### 1.2. secret_key  
거래소로부터 발급 받은 사용자의 거래소 Secret key(또는 Access Secret Key)를 string 타입으로 입력합니다.  


### 1.3. exchange  
Bot에서 가상화폐 거래를 할 때 이용할 거래소의 명칭을 string 타입으로 입력합니다.  
(해당 거래소에서 발급 받은 api key, secret key를 위의 입력 값으로 사용합니다.) 


### 1.4. user_uuid  
CATS Lab에서 발급 받은 사용자의 user_uuid를 string 타입으로 입력합니다.  


### 1.5. bot_id  
CATS Lab에서 사용자가 생성한 bot_id를 int 타입으로 입력합니다.


### 1.6. initialize  
사용자가 Bot을 생성할 때 전략에서 사용할 가상화폐의 종류, 분봉, 거래소의 기축 화폐에 대한 정보와 safety setting에 대한 정보를 저장하는 함수입니다.

자세한 설명과 작성 예시는 [Trade-04 initialize](/catslab_docs/docs/trade-initialize)를 참고하시기 바랍니다.


### 1.7. run_strategy  
Bot에서 사용하는 전략에서 매수 및 매도를 위해 참고할 기술적 지표를 계산하여 저장하는 함수입니다.  

자세한 설명과 작성 예시는 [Trade-05 run_strategy](/catslab_docs/docs/trade-run_strategy)를 참고하시기 바랍니다.


### 1.8. make_orders  
사용자가 정한 방식에 따라 매수주문/매도주문/주문취소 등을 진행하는 함수입니다.  

자세한 설명과 작성 예시는 [Trade-06 make_orders](/catslab_docs/docs/trade-make_orders)를 참고하시기 바랍니다.


### 1.9. init_budget  
사용자의 Bot에서 사용할 자산의 초기 투입 금액을 int 타입으로 입력합니다.  


### 1.10. running_mode  
running_mode는 CATS Lab Server를 통해서 Bot을 가동시킬지 혹은 거래소의 API를 이용하여 바로 Bot을 가동시킬지에 대한 입력값으로, ‘LIVE’ 모드 또는 ‘LOCAL’ 모드로 입력이 가능합니다. 

‘LIVE’ 모드로 Bot을 가동시키기 위해서는 1.4. user_uuid와 1.5. bot_id가 필수적으로 입력되어야 하며, CATS Lab 서버에 Bot이 생성되어 있으며, 가동중 상태에 있어야 합니다. 또한, 1.11. using_api parameter의 입력값이 ‘CATSLAB’으로 입력되어야 합니다.  

'LOCAL' 모드에서는 직접 거래소 API를 사용합니다. 1.11. using_api의 입력값은 'EXCHANGE'로 입력되어야 합니다.


### 1.11. using_api  
using_api의 입력값으로는 ‘CATSLAB’ 또는 ‘EXCHANGE’로 입력이 가능합니다.

‘CATSLAB’ 모드는 CATS Lab server를 통해 거래소로 API 요청을 보내는 모드입니다. ‘CATSLAB
‘EXCHANGE’ 모드는 입력한 api_key, secret_key를 이용해 직접 거래소의 API를 사용할 수 있는 모드입니다.


실제 사용 예시는 다음과 같습니다.

![Trade Basic](https://user-images.githubusercontent.com/47657715/57591933-e9bf6e00-756e-11e9-915b-6eba8a9cf682.png)  

![BotContext 생성](https://user-images.githubusercontent.com/47657715/57591932-e9bf6e00-756e-11e9-9a78-0699cd238a1d.png) 

필요한 라이브러리와 객체를 import한 뒤, 직접적으로 전략과 관련된 함수인 __run_strategy__, __make_order__, __initialize__ 를 작성하여  BotContext를 생성합니다.  

BotContext를 생성하면, initialize에 입력한 거래소, 가상화폐, 분봉 별로 candle 데이터를 로드하여 Bot의 가동을 준비합니다.



<br> <br />

------------------------------

BOT_NAME을 지정하여 BotContext를 생성하는 것과 동시에 Bot의 전략에서 사용하는 가상화폐(currency)와 분봉(interval) 별로 거래소로 요청하여 캔들 데이터들을 불러옵니다. 로드한 캔들 데이터들은 각각의 화폐와 분봉별로 dataframe으로 저장하여 context 내부에 관리 및 업데이트가 이루어 집니다. 

dataframe에 접근하는 방식은 다음과 같습니다.  
우선 BotContext를 생성할 때의 지정한 BOT_NAME의 이름을 가지는 클래스에서 exchanges로 접근하여 Bot에서 사용하는 거래소 객체로 접근할 수 있습니다.  

각 거래소 객체에서는 data로 접근하여 ‘{currency}_{interval}’ 형태의  key값으로 가상화폐 별, 분봉 별 dataframe에 접근이 가능합니다.  

접근 방법과 사용에 대한 예시는 다음과 같습니다.


![prepared candle 01](https://user-images.githubusercontent.com/47657715/57592030-6d795a80-756f-11e9-8ffe-012c692dc928.png)

![prepared candle 02](https://user-images.githubusercontent.com/47657715/57592031-6eaa8780-756f-11e9-8dff-a05bad49f786.png)



