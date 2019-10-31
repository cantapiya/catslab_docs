---
title: "04 run_strategy"
permalink: /docs/trade-run_strategy/
excerpt: "CATS Lab Trade 04 run_strategy"
last_modified_at: 2019-04-25T13:40:00
redirect_from:
  - /theme-setup/
toc: true
---

## 4. run_strategy 함수의 작성 및 사용 예시

1분 주기로 업데이트 되는 dataframe을 이용하여 사용자가 전략에서 사용하는 기술적 지표 및 매수/매도의 근거가 되는 전략을 계산하고 업데이트하는 함수입니다.

```python
def run_strategy(self, is_update, trade_info, update_len, data): 
```

### 4.1. is_update  
is_update parameter는 dictionary type으로 가상화폐와 분봉의 종류에 따라 “{currency}_{interval}” 형태의 key값과, 해당 분봉의 update 여부에 따라 bool 타입의 value값을 갖습니다. 

예를 들어, 1분을 주기로 Trade 함수([Trade-02 Run Trade](/catslab_docs/docs/trade-run) 참고)가 진행될 때, 1분마다 생성되는 1분봉의 경우 매분 새롭게 캔들 데이터가 생성되어 dataframe 업데이트가 진행됩니다. 

이외의 분봉들은 해당 분봉이 생성되고 나서 dataframe 업데이트의 여부에 따라 bool 타입의 value값이 is_update에 저장됩니다.


### 4.2. trade_info  
전략에서 사용하는 거래소, 가상화폐, 분봉의 종류 및 safety setting에 대한 정보를 저장하고 있는 parameter입니다. 초기에 BotContext를 생성할 때 initialize([Trade-03 initialize](/catslab_docs/docs/trade-initialize/) 참고)에서 입력한 정보들과 같습니다.


### 4.3. update_len  
각각의 가상화폐와 분봉에 따라 1분 주기로 dataframe의 업데이트 여부를 is_update에 저장하는 것과 동시에 업데이트 된 캔들 데이터의 개수가 update_len에 저장됩니다. 

is_update와 마찬가지로 “{currency}_{interval}” 형태의 key값을 가지고, 업데이트 된 캔들 데이터의 개수를 value값으로 가지는 dictionary 타입입니다. 

이 parameter는 실제 거래소에서 캔들 데이터가 생성되더라도 크롤링이나 거래소 API를 이용하여 데이터를 호출하는 과정에서 업데이트 되어야할 캔들 데이터가 누락 또는 지연이 되거나, 거래소 점검으로 인해 업데이트 되는 캔들 데이터가 없는 경우를 구분하기 위해 사용합니다.


### 4.4. data  
전략에서 사용하기 위해 입력한 가상화폐의 종류와 분봉의 종류에 따라 “{currency}_{interval}” 형태의 key값과 dataframe을 value값으로 갖는 dictionary입니다. 

각 dataframe에는 해당 가상화폐와 분봉의 캔들 데이터 및 전략에서 사용되는 기술적 지표들이 저장되어 있습니다. 

또한, 각 분봉마다 가지고 있는 default 개수만큼을 가장 최근에 업데이트 된 캔들 데이터까지 저장하고 있습니다. 

각 분봉의 default 개수는 다음과 같습니다.
1분봉 		: 10080개 (1주일)  
3분봉 		: 3360개 (1주일)  
5분봉 		: 8640개 (1개월)  
15분봉		: 2880개 (1개월)  
30분봉		: 1440개 (1개월)  
60분봉		: 2160개 (3개월)  
120분봉	: 1080개 (3개월)  
240분봉	: 540개 (3개월)  
1440분봉	: 365개 (1년)  


run_strategy 함수의 사용 예시는 5일 이동평균선과 20일 이동평균선을 사용하는 전략으로 다음과 같습니다.

```python
from coza.ta import candle_slicing
from coza.ta import ema

def run_strategy(self, is_update, trade_info, update_len, data):
    exchange = self.exchange

    for currency in trade_info[exchange]['currency']:
        for interval in trade_info[exchange]['interval']:
            if int(interval) == 1:
                if len(is_update.keys()) != 0:
                    update_number = update_len[f'{currency}_{int(interval)}']

                    update_candle_20 = \
                    candle_slicing(df=data['{}_{}'.format(currency, interval)], n=20, update_number=update_number)
                    update_candle_60 = \
                    candle_slicing(df=data['{}_{}'.format(currency, interval)], n=60, update_number=update_number)

                    update_ema_20 = \
                    ema(update_candle_20['close'], 20).tail(update_number)
                    update_ema_60 = \
                    ema(update_candle_60['close'], 60).tail(update_number)
                    
                    if update_number >= 1:
                        start_index_20 = update_ema_20.index[0]
                        start_index_60 = update_ema_60.index[0]
                        
                        update_index_list = list(update_ema_20.index)                        
                        for _index in update_index_list:
                            data['{}_{}'.format(currency, interval)].set_value(_index, 'ema_20', update_ema_20.loc[_index])
                        
                        update_index_list = list(update_ema_60.index)
                        for _index in update_index_list:
                            data['{}_{}'.format(currency, interval)].set_value(_index, 'ema_60', update_ema_60.loc[_index])
                        
                        
                else:
                    data[f'{currency}_{int(interval)}']['ema_20'] = \
                    ema(data[f'{currency}_{int(interval)}']['close'], 20)
                    data[f'{currency}_{int(interval)}']['ema_60'] = \
                    ema(data[f'{currency}_{int(interval)}']['close'], 60)
```



