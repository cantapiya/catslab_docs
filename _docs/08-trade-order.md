---
title: "06 Order"
permalink: /docs/trade-order/
excerpt: "CATS Lab Trade 06 Order"
last_modified_at: 2019-04-25T14:00:00
redirect_from:
  - /theme-setup/
toc: true
---

## 6. Order 객체 생성 및 사용 예시

거래소에 주문을 전송하기 전에 Order 객체를 생성하여 list에 저장을 한 뒤, 거래소 API를 이용하는 함수를 사용하여 list에 저장되어 있는 주문들을 한 번에 처리합니다.

```python
class Order(exchange, currency, order_type, quantity, price, fiat):
```

### 6.1. exchange  
주문할 거래소의 명칭을 string 타입으로 입력합니다. 
(예: 'coinone', 'upbit')


### 6.2. currency  
거래소에 주문을 넣을 가상 화폐의 명칭을 string 타입으로 입력합니다.  
(예: “btc”, “eth”, “xrp”, … )


### 6.3. order_type  
매수와 매도를 구분하기 위한 string 타입의 parameter입니다.   
매수 주문의 경우 “BUY”, 매도 주문의 경우 “SELL” 타입으로 입력합니다.


### 6.4. quantity  
주문할 가상화폐의 수량을 float 타입으로 입력합니다.


### 6.5. price  
주문할 가상화폐의 가격을 float 타입으로 입력합니다.


### 6.6. fiat  
거래소에서 사용하는 기축화폐를 string 타입으로 입력합니다.  
(예: "krw", "usd", … )


Order 객체의 사용 예시는 다음과 같습니다.

__매수주문 (현재 가격으로 주문):__  

```python
from coza.objects import Order

price = data[f'{currency]_{int(interval)}']['close'].loc[data[f'{currency]_{int(interval)}'].index[-1]]
buy_order = Order(exchange='coinone', currency='xrp', order_type='BUY', fiat='krw', price=price, quantity=2.5)
```

__매도주문 (지정가 주문):__  

```python
from coza.objects import Order

sell_order = Order(exchange='upbit', currency='eth', order_type='SELL', fiat='krw', price=150000, quantity=0.1)
```


  


