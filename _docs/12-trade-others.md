---
title: "10 others"
permalink: /docs/trade-others/
excerpt: "CATS Lab Trade 10 others"
last_modified_at: 2019-04-25T14:40:00
redirect_from:
  - /theme-setup/
toc: true
---

## 10. 부가적인 기능의 함수들과 사용 예시

전략에서 사용하는 지표를 관리하고, 주문 및 주문취소 기능을 하는 함수 외에도 전략을 운용하면서 사용할 수 있는 부가적인 기능의 함수들이 있습니다. 각각의 함수들은 run_strategy, make_orders 함수 안에서 사용할 수 있으며, 생성한 객체를 통하여 함수들을 호출함하여 필요한 기능별로 유용하게 사용할 수 있습니다.  


전략을 구현하고, Bot을 가동하는 과정에서 실시간으로 사용자의 Bot에 저장되어 있는 정보를 불러오는 함수들과 사용 예시들은 다음과 같습니다.

<br>  


### 10.1. get_balance 함수  
Bot에서 운용하는 자산의 보유량을 실시간으로 조회하는 함수입니다.  

```python
# run_strategy, make_orders 함수 안에서
self.get_balance(exchange=EXCHANGE)
```  

Bot을 가동함과 동시에 기축화폐로 투입된 초기 자산에서 매수/매도가 발생함에 따라 보유한 자산은 실시간으로 변화하게 됩니다.  

__get_balance__ 함수는 Bot을 운용하는 과정에서 함수를 호출하는 시점의 BotContext에서 저장하고 있는 __각 자산별 보유량 정보__ 를 제공합니다. 

다음 예시는 7000 KRW를 투입하여 “BTC”와 “ETH”, "XRP"를 대상으로 Bot을 운용하는 도중 get_balance 함수를 통해 현재 Bot의 보유 자산을 조회한 예시입니다.

![get_balance example](https://user-images.githubusercontent.com/47657715/57609435-d8478780-75a9-11e9-8ad8-7741b7bf4fd1.png)

투입한 기축화폐는 'fiat'를 key값으로 하여 보유량이 저장되며, 사용자가 initialize에서 입력한 가상화폐 별로 다시 total balance를 의미하는 'balance'와 available balance를 의미하는 'avail'로 구분되어 저장됩니다.  

각 가상화폐 자산의 수량은 'balance'에 저장되며, 'avail'에 저장되어 있는 화폐의 수량은 현재 거래 가능한 자산의 수량을 의미합니다.  

매수 주문에 대한 미체결 주문의 수량은 'balance'에 저장되지만 실제 이용가능한 자산이 아니므로 'avail'에는 반영되지 않고, 주문이 체결된 것이 확인됨과 동시에 'avail'에 반영이 됩니다.  

매도 주문의 경우 매도 주문을 넣은 수량은 거래가 불가능하기 때문에 'avail'에서 수량이 차감되며, 체결과 동시에 'balance'에 반영이 됩니다.

<br>  


### 10.2. get_time 함수  
함수를 호출하는 시점의 시간을 datetime 타입으로 조회하는 함수입니다.  

```python
# run_strategy, make_orders 함수 안에서
self.get_time(exchange=EXCHANGE)
```  

제공하는 거래소의 지역에 따라 timezone의 정보가 반영된 시간의 datetime이 제공됩니다.  

![get_time example](https://user-images.githubusercontent.com/47657715/57609442-dd0c3b80-75a9-11e9-828d-1476678a58b7.png)

<br>  


### 10.3. get_order_list 함수  
함수가 호출되는 시점의 BotContext에 저장되어 있는 order_list([Trade-08 order_list](/catslab_docs/docs/trade-order_list/) 참고)에 대한 정보를 조회하는 함수입니다.  

```python
# run_strategy, make_orders 함수 안에서
self.get_order_list(exchange=EXCHANGE)
```

다음 예시는 “BTC”와 “ETH”, "XRP"를 대상으로 운용하는 Bot의 order_list 정보를 특정 시점에 조회한 예시입니다.  


![get_order_list example](https://user-images.githubusercontent.com/47657715/57609575-265c8b00-75aa-11e9-90eb-95758f49d5e4.png)

<br>  


### 10.4. get_orders 함수  
함수가 호출되는 시점의 BotContext에 저장되어 있는 orders에 대한 정보를 조회하는 함수입니다.  

```python
# run_strategy, make_orders 함수 안에서
self.get_orders(exchange=EXCHANGE)
```

set_order 함수([Trade-07 set_order](/catslab_docs/docs/trade-set_order/) 참고)의 parameter인 t에 주문 시간을 특정하지 않으면 set_order 함수의 호출과 동시에 주문이 전송되지만, 주문 시간이 지정된 경우 스케쥴링이 진행되면서 지정한 시간 이전에는 거래소로 주문이 전송되지 않습니다.  

시간을 특정하여 생성한 주문들은 거래소로 전송하기 이전에 orders라는 list에 임시적으로 저장을 해 둡니다.  

orders에 저장된 주문들은 1분마다 스케쥴링을 반복하면서 Order 객체를 생성할 때 지정된 시간별로 거래소로 전송됩니다.  

전송된 주문들은 orders에서 빠지게 되고, 주문을 전송한 후 미체결 주문은 order_list에서 관리하게 됩니다.  

orders에 저장된 주문들은 거래소로 주문을 전송하는 시간을 기점(datetime)으로하여 이전 시간의 datetime 값을 갖는 주문들은 모두 전송하는 방식으로 처리됩니다. 

즉, 주문을 생성할 때 현재 시간, 혹은 이전의 시간으로 지정한 주문들의 경우 다음 스케쥴링에서 거래소로 주문이 전송되며, 주문 시간을 지정하여 나중에 주문이 전송되도록 설정할 수도 있습니다. 


예를 들어, 현재 시간보다 한 시간 뒤의 시간으로 주문시간을 지정한 주문의 경우 1시간 뒤 스케쥴링에서 거래소로 주문이 전송됩니다.

get_orders 함수를 이용하면 거래소로 전송되기 이전의 order들이 저장되어 있는 list인 orders의 정보를 조회할 수 있습니다.  

다음 예시는 get_time 함수를 이용하여 현재 시간을 불러오고, 주문시간을 불러온 시간을 기점으로 1시간 뒤로 설정한 예시입니다. Order 객체를 생성하여 set_order 함수를 사용 후 get_orders 함수를 호출하면 다음 예시와 같이 생성한 주문이 orders에 저장되어 있는 것을 확인할 수 있습니다. 

![get_orders example](https://user-images.githubusercontent.com/47657715/57609456-e5647680-75a9-11e9-8f5e-432e6cff7bc6.png)

<br>  



### 10.5. get_orderbook 함수  
함수를 호출하는 시점에서의 해당 거래소의 호가창 정보를 조회하는 함수입니다.  

BotContext를 생성할 때 입력한 거래소의 명칭과 조회할 가상화폐의 명칭을 입력합니다.

```python
# run_strategy, make_orders 함수 안에서
self.get_orderbook(exchange=EXCHANGE, currency=CURRENCY)
```  


반환되는 정보는 pandas.DataFrame 타입으로 제공되며, colume으로는 매수 호가와 호가수량인 'bid_price', 'bid_quantity'와 매도 호가와 호가수량인 'ask_price', 'ask_quantity'가 지정되어 있습니다. 

호가 정보는 매수/매도 호가 별로 20개씩 제공되며, 시장가격을 기준으로 하여 가장 가까운 가격으로 0번 index부터 지정됩니다.  

다음 예시는 'XRP' 호가 정보를 조회한 예시입니다.  


![get_orderbook example](https://user-images.githubusercontent.com/47657715/57609670-4be99480-75aa-11e9-813f-893694be7001.png)

<br>  



### 10.6. get_estimated 함수  
get_estimated 함수는 현재 보유하고 있는 모든 자산의 평가금액을 기축화폐로 환산하여 반환해주는 함수입니다.  

평가금액 대비 가상화폐 자산의 비율과 투입 금액 대비 현재 수익률도 함께 제공됩니다.

```python
# run_strategy, make_orders 함수 안에서
self.get_estimated(exchange=EXCHANGE)
```

Bot을 가동하여 전략을 운용하면 매수/매도 주문이 체결됨에 따라 자산의 보유량이 변동하고 가상화폐의 가격 또한 실시간으로 변동합니다.  

이에 따라 자산의 평가금액도 실시간으로 변화하게 되는데, 함수가 호출되는 시점의 보유한 모든 자산의 가치를 BotContext를 생성할 때 입력한 fiat를 기준으로 환산합니다.  

이때 각 자산별로 평가가격을 계산할 때는 get_orderbook 함수를 호출하여 호가수량이 존재하는 호가 중 시장가격과 가장 가까운 가격으로 계산합니다.  

또한, 함수의 호출 시점의 미체결 주문들은 주문가격을 이용하여 환산하고 미체결 주문을 제외한 실제 보유한 자산으로 평가금액을 계산합니다.  

get_estimated 함수를 호출하여 반환되는 정보는 dictionary 타입으로 제공됩니다. 'estimated' key 값으로 제공되는 평가금액 외에도 평가금액 대비 가상화폐 자산의 비율이 'currency_ratio'를 key 값으로, 투입 금액 대비 현재 수익률도 'earning_rate'를 key 값으로 하여 함께 제공됩니다.  

<br>  


다음 예시는 위의 예시 중 get_balance 함수에서 조회한 시점(위의 예시의 order_list에 포함된 미체결 주문도 해당)의 자산에 대하여 평가금액 정보를 환산한 예시입니다.  

![get_estimated example](https://user-images.githubusercontent.com/47657715/57609715-63288200-75aa-11e9-92b7-a12388abaca7.png)

<br>  


### 10.7. clear_balance 함수  
함수를 호출하는 시점의 미체결 주문들을 모두 취소하고 보유한 가상화폐 자산을 청산하는 함수입니다.  

```python
# run_strategy, make_orders 함수 안에서
self.clear_balance(exchange=EXCHANGE)
```  

clear_balance 함수는 함수가 호출되는 시점의 미체결 주문들을 모두 취소하고, 보유하고 있는 가상화폐들을 호가창의 정보를 이용하여 바로 청산을 하는 기능을 수행합니다. 보유하고 있는 가상화폐별로 매도 호가금액 중 가장 낮은 금액으로 매도 주문을 넣습니다.  

위의 예시에 이어지는 상황으로, 보유한 자산과 미체결된 주문이 있는 상태에서 clear_balance 함수를 호출한 예시입니다.  


![clear_balance example1](https://user-images.githubusercontent.com/47657715/57610001-f19d0380-75aa-11e9-8cb4-2b314b4b2e11.png)


![clear_balance example2](https://user-images.githubusercontent.com/47657715/57610004-f366c700-75aa-11e9-83df-175e52b9d801.png)

![clear_balance example3](https://user-images.githubusercontent.com/47657715/57610012-f6fa4e00-75aa-11e9-81bc-61c0c5768061.png)  

<br>  

보유 자산을 청산한 뒤 get_order_list, get_balance, get_estimated 함수를 호출한 예시입니다.  

__coinone 거래소의 경우 소수점 넷째자리 미만의 수량의 경우 입력 및 거래가 불가능하여 청산이 진행되지 않는 수량이 있을 수 있습니다.__  

![result after clear_balance](https://user-images.githubusercontent.com/47657715/57610163-39bc2600-75ab-11e9-9f3c-239ec45156c1.png)


