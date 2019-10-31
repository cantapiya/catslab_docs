---
title: "03 initialize"
permalink: /docs/trade-initialize/
excerpt: "CATS Lab Trade 03 initialize"
last_modified_at: 2019-04-25T13:30:00
redirect_from:
  - /theme-setup/
toc: true
---

## 3. initialize 함수의 작성 및 사용 예시  


Initialize 함수의 작성 형식은 다음과 같은 형식을 가지며, 

```python
def initialize(self):
    self.context['trade_info'] = {
                                  "EXCHANGE_NAME_1": {
				      "currency": [], 
				      "interval": [],
				      "fiat": FIAT
				  },
				"EXCHANGE_NAME_2": {
				    "currency": [],
				    "interval": [],
				    "fiat": FIAT
				},
			… , 
			
				"safety_setting": {
				    "profit_target": {
				    	"checked": bool,
				    	"ratio": RATIO
				    },
				    "stop_loss":{
				    "checked": bool,
				    "ratio": RATIO
				    },
				    "stop_on_sell": bool,
				    "trailing_stop": {
				        "checked": bool,
				        "dec_ratio": RATIO,
				        "inc_ratio": RATIO
				    }
				}
			    }

```

실제로 initialize 함수를 작성한 예제는 다음과 같습니다.

```python
def initialize(self):
    self.context['trade_info'] = {
        "coinone": {
	    "currency": ["xrp"],
	    "interval": [1],
	    "fiat": "krw"
	},
	"upbit": {
	    "currency": ["xrp"],
	    "interval": [1],
	    "fiat": "krw"
	}, 
	"safety_setting": {
            "profit_target": {
		"checked": True, 
		"ratio": 0.10,
	    }, 
	    "stop_loss": {
	        "checked": False, 
		"ratio": 0.10
	    }, 
            "stop_on_sell": True, 
	    "trailing_stop": {
	        "checked": True, 
	        "dec_ratio": 0.05,
		"inc_ratio": 0.07
	    }
	}
    }

```

각각의 파라미터들은 다음과 같은 속성을 갖습니다.

## 3.1. EXCHANGE_NAME  

```python
"EXCHANGE_NAME": {"currency": [], "interval": [], "fiat": FIAT}  
```

Bot에서 사용할 거래소의 명칭 EXCHANGE_NAME을 key로 하여, 해당 거래소에서 사용할 가상화폐(currency)의 종류, 분봉(interval)의 종류, 기축화폐(fiat)의 정보를 dictionary 타입으로 입력합니다. 


### 3.1.1. currency  
```python
"currency": []  
```

EXCHANGE_NAME을 key값으로 하는 dictionary에서 가상화폐의 종류에 대한 정보는 “currency”를 key, 사용할 가상화폐의 종류가 저장된 list를 value값으로 합니다. 
(예: "currency": ["btc", "xrp", "eth"])


### 3.1.2. interval  
```python
"interval": []  
```

currency와 마찬가지로 각각의 가상화폐마다 사용할 분봉에 대한 정보는 “interval”을 key, 사용할 분봉의 종류가 저장된 list를 value값으로 합니다.  
(예: "interval": [1, 5, 60])


### 3.1.3. fiat  
```python
"fiat": FIAT  
```

거래소에서 사용하는 기축화폐의 정보의 경우 “fiat”를 key값으로 하고, string 타입의 FIAT를 value값으로 가집니다.  
(예: "fiat": "krw" 또는 "fiat": "usd")



## 3.2. safety_setting
```python
"safety_setting": {"profit_target":{}, "stop_loss":{}, "stop_on_sell":bool, trailing_stop”:{}}  
```

Bot에서 사용할 safety setting에 대한 정보 역시 “safety_setting”을 key값으로 하는 dictionary 타입으로 입력합니다. 


### 3.2.1. profit_target  
```python
"profit_target": {"checked": BOOL, "ratio": RATIO}  
```

Bot에서 사용할 익절 조건(“profit_target”)으로, 초기 투입 금액의 입력한 RATIO 이상으로 수익이 발생했을 시에 자동으로 Bot을 중단시키는 조건입니다. 

이 기능을 사용할 것인지에 대한 여부를 “checked”를 key값, bool 타입의 value값으로 입력하고, 익절 조건의 비율을 float 타입의 RATIO (0<=RATIO<=1)로 입력합니다.


### 3.2.2. stop_loss  
```python
"stop_loss": {"checked": BOOL, "ratio": RATIO}  
```

Bot에서 사용할 손절 조건(“stop_loss”)으로, 초기 투입 금액의 입력한 RATIO 이하으로 손해가 발생했을 시에 자동으로 Bot을 중단시키는 조건입니다. 

이 기능을 사용할 것인지에 대한 여부를 “checked”를 key값, bool 타입의 value값으로 입력하고, 손절 조건의 비율을 float 타입의 RATIO (0<=RATIO<=1)로 입력합니다.


### 3.2.3. stop_on_sell  
```python
"stop_on_sell": BOOL  
```

safety setting의 조건을 만족하여 거래를 중단할 경우 보유하고 있던 가상화폐들을 청산할 것인지에 대한 여부를 “stop_on_sell”를 key값으로하여 bool 타입의 value를 입력합니다. 


### 3.2.4. trailing_stop  
```python
"trailing_stop": {"checked": BOOL, "dec_ratio": RATIO, "inc_ratio": RATIO}  
```

Bot을 가동중에 입력한 RATIO(“inc_ratio”) 이상 수익이 발생한 경우, 이후 거래에서 “dec_ratio” 이하로 하락했을 시 Bot 가동을 중단시키는 기능입니다. 

기능의 사용 여부를 “checked”를 key값, bool 타입의 value값으로 입력하고, 기능이 발동되는 기준인 상승 조건을 “inc_ratio”의 key값과 float 타입의 RATIO (0<=RATIO<=1)로 입력합니다. 상승 이후 하락 조건을 “dec_ratio”의 key값과 float 타입의 RATIO (0<=RATIO<=1)로 입력합니다. 

