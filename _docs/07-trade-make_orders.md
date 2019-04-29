---
title: "05 make_orders"
permalink: /docs/trade-make_orders/
excerpt: "CATS Lab Trade 05 make_orders"
last_modified_at: 2019-04-25T13:50:00
redirect_from:
  - /theme-setup/
toc: true
---

## 5. make_orders 함수의 작성 및 예시

전략에서 사용하는 매수/매도 방식에 따라 매수주문/매도주문/주문취소를 진행합니다. 

주문을 생성할 때는 Order 객체를 이용하여 생성하고, 생성한 주문들은 set_order 함수를 이용하여 주문 내역에 저장합니다. 

주문 내역에 있는 주문들은 거래소 API를 이용하는 내부 함수를 사용하여 거래소로 전송됩니다. 주문 취소의 경우 set_cancel을 통해 거래소로 전송이 가능합니다.


주문을 생성하는 Order 객체 생성은 [Trade-06 Order](/catslab_docs/docs/trade-order) 을 참고하시고, 생성한 주문을 거래소에 전송하기 위해 list에 추가하는 set_order 함수는 [Trade-07 set_order](/catslab_docs/docs/trade-set_order)을 참고하시기 바랍니다. 또한, 체결 이전의 주문들의 정보를 저장하고 있는 order_list에 대한 내용은 [Trade-08 order_list](/catslab_docs/docs/trade-order_list)에서, 주문 취소를 요청하는 set_cancel 함수에 대한 설명과 사용 방법은 [Trade-09 set_cancel](/catslab_docs/docs/trade-set_cancel)을 참고하시기 바랍니다.

```python
def make_orders(self, is_update, trade_info, update_len, data): 
```
### 매수/매도 주문 시 주의사항  
현재 지원하는 coinone 거래소와 upbit 거래소의 경우 각 가상화폐마다 거래 최소금액(500 KRW)이 지정되어 있으며, 이 금액이 넘지 않는 매수/매도주문의 경우 거래소로의 주문 전송이 불가능합니다.

### 5.1. is_update  
[Trade-04 run_strategy](/catslab_docs/docs/trade-run_strategy)의 run_strategy 함수의 parameter와 동일합니다.


### 5.2. trade_info  
[Trade-04 run_strategy](/catslab_docs/docs/trade-run_strategy)의 run_strategy 함수의 parameter와 동일합니다.


### 5.3. update_len  
[Trade-04 run_strategy](/catslab_docs/docs/trade-run_strategy)의 run_strategy 함수의 parameter와 동일합니다.


### 5.4. data  
[Trade-04 run_strategy](/catslab_docs/docs/trade-run_strategy)의 run_strategy 함수의 parameter와 동일합니다.


make_orders 함수의 사용 예시는 다음과 같습니다.

다음 예시는 위의 run_strategy를 기반으로 5일 이동평균선이 20일 이동평균선을 상향 돌파하는 순간 매수, 하향 돌파하는 순간 매도하는 전략입니다.

```python
def make_orders(self, is_update, trade_info, update_len, data):
    exchange = self.exchange
    interval = 1
    
    total_fiat = self.exchanges[exchange].balance['fiat']
    
    for currency in trade_info[exchange]['currency']:
        current_index = data[f'{currency}_{int(interval)}'].index[-1]
        t = current_index
        
        if data[f'{currency}_{int(interval)}']['ema_20'].loc[current_index-timedelta(seconds=60)] \
        < data[f'{currency}_{int(interval)}']['ema_60'].loc[current_index-timedelta(seconds=60)] \
        and data[f'{currency}_{int(interval)}']['ema_20'].loc[current_index] \
        > data[f'{currency}_{int(interval)}']['ema_60'].loc[current_index]:
            order_type = 'BUY'
            price = data[f'{currency}_{int(interval)}']['close'].loc[current_index]
            quantity = math.floor( round (total_fiat*0.5 / price * 10**4, 4) )/(10**4)
            if quantity >= 1.0 and price * quantity <= self.exchanges[exchange].balance['fiat']:
                o = Order(exchange=exchange, currency=currency, order_type=order_type, fiat='krw', price=price, quantity=quantity)
                self.set_order(exchange=exchange, o=o, t=t)
                
        elif data[f'{currency}_{int(interval)}']['ema_20'].loc[current_index-timedelta(seconds=60)] \
        > data[f'{currency}_{int(interval)}']['ema_60'].loc[current_index-timedelta(seconds=60)] \
        and data[f'{currency}_{int(interval)}']['ema_20'].loc[current_index] \
        < data[f'{currency}_{int(interval)}']['ema_60'].loc[current_index]:
            order_type = 'SELL'
            price = data[f'{currency}_{int(interval)}']['close'].loc[current_index]
            quantity = math.floor(round(self.exchanges[exchange].balance[currency]['avail'] * 10**4, 4))/(10**4)
            if quantity >= 1.0:
                o = Order(exchange=exchange, currency=currency, order_type=order_type, fiat='krw', price=price, quantity=quantity)
                self.set_order(exchange=exchange, o=o, t=t)
```

__위의 예시로 사용한 전략은 단순히 run_strategy와 make_orders 함수의 사용 예시를 위한 예시 코드로, 전략의 수익성을 보장하지 않습니다.__  


