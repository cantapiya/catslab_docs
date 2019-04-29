---
title: "07 set_order"
permalink: /docs/trade-set_order/
excerpt: "CATS Lab Trade 07 set_order"
last_modified_at: 2019-04-25T14:10:00
redirect_from:
  - /theme-setup/
toc: true
---

## 7. set_order 함수의 사용과 사용과 예시

Order 객체를 이용하여 생성한 주문들을 거래소 API를 이용하여 거래소에 전송을 하기 위한 함수입니다. 

주문내역에 대한 정보를 저장하는 Order 객체와, 주문 시간을 지정하여 list에 추가한 뒤, 내부 함수를 이용하여 list 저장되어 있는 주문을 거래소로 전송을 합니다. 

거래소로 전송함과 동시에 자산의 보유량을 업데이트하여 변경된 정보를 저장합니다. 보유 자산을 업데이트할 때는 수수료를 고려하여 적용이 됩니다.

```python
def set_order(self, exchange, o, t=None):
```

### 7.1. exchange  
Bot에서 사용하는 거래소의 명칭을 입력합니다.


### 7.2. o  
[Trade-06 Order](/catslab_docs/docs/trade-order/)의 Order 객체입니다.

make_orders에서 Order 객체를 사용하여 생성할 수 있으며, 주문할 가상화폐의 종류, 주문 유형, 주문 수량, 주문 가격, 거래소에서 사용하는 기축화폐에 대한 정보를 포함합니다.


### 7.3. t  
주문을 요청하는 시간을 datetime.datetime 타입으로 입력합니다.  
별도로 지정하지 않는 경우 주문을 생성하는 시간으로 지정됩니다.

```python
from coza.objects import Order
from datetime import datetime

# 현재 시간으로 주문
price = data[f'{currency]_{int(interval)}']['close'].loc[data[f'{currency]_{int(interval)}'].index[-1]]
buy_order = Order(exchange='coinone', currency='xrp', order_type='BUY', fiat='krw', price=price, quantity=2.75)
order_time = datetime.now()
set_order(exchange='coinone', o=buy_order, t=order_time)


# 주문 신호가 들어온 캔들의 시간으로 주문
sell_order = Order(exchange='upbit', currency='eth', order_type='SELL', fiat='krw', price=175000, quantity=0.1)
t = data[f'{currency]_{int(interval)}'].index[-1]
set_order(exchange='upbit', o=sell_order, t=t)
```

![07 Order 객체 Example1](https://user-images.githubusercontent.com/47657715/56643701-14777e80-66b5-11e9-91ce-d43aafaf4172.png)