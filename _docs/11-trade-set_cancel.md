---
title: "09 set_cancel"
permalink: /docs/trade-set_cancel/
excerpt: "CATS Lab Trade 09 set_cancel"
last_modified_at: 2019-04-25T14:30:00
redirect_from:
  - /theme-setup/
toc: true
---

## 9. set_cancel 함수의 사용과 사용 예시

거래소에 전송한 주문 중 미체결 주문들을 취소할 때 사용할 수 있는 함수입니다. 

```python
def set_cancel(self, exchange, currency=None, order_id=None, qty=None):
```

### 9.1. exchange  
Bot에서 사용하는 거래소의 명칭을 입력합니다.


### 9.2. currency  
취소할 주문의 가상화폐를 입력합니다.  
(예: “btc”, “eth”, “xrp”, … )


### 9.3. order_id
취소할 주문의 order_id를 입력합니다.  
order_id를 입력하여 주문을 취소하는 경우 currency를 함께 입력해야 합니다.


### 9.4. qty  
주문 취소할 수량을 입력합니다.  
수량을 입력하지 않는 경우 입력한 currency 또는 currency의 order_id에 남아 있는 주문 수량 전체를 취소합니다.

__단, 부분 취소의 경우 coinone 거래소에서만 제공하며, upbit 거래소에서는 부분취소 기능을 제공하지 않습니다.
upbit 거래소의 경우 qty를 입력하더라도(currency와 order_id도 함께 입력) 미체결 주문의 모든 수량을 주문취소합니다.__  

다음은 주문을 취소하는 4가지 방법에 대한 예시입니다. 


우선 "BTC", "ETH", "XRP" 세 화폐에 대해서 각각 3개 씩 주문을 넣은 예시입니다.

![image](https://user-images.githubusercontent.com/47657715/57599080-86dece80-7590-11e9-9f78-8164c42c7921.png)

![image](https://user-images.githubusercontent.com/47657715/57599083-880ffb80-7590-11e9-8f72-0ad477c3d264.png)

![image](https://user-images.githubusercontent.com/47657715/57599085-89d9bf00-7590-11e9-8c5f-e35e0f8fbc31.png)


<br>  


BotContext의 order_list와 거래소 미체결 주문 리스트를 확인해보면 다음과 같습니다.

__BotContext의 order_list__  

<img width="520" alt="" src="https://user-images.githubusercontent.com/47657715/57599110-a0801600-7590-11e9-97fc-ca72000f12b1.png">

<img width="520" alt="" src="https://user-images.githubusercontent.com/47657715/57599114-a2e27000-7590-11e9-8ffb-b99c54bda58b.png">



__거래소 미체결 주문 리스트__  

<img width="413" alt="" src="https://user-images.githubusercontent.com/47657715/57599193-dd4c0d00-7590-11e9-81d0-66329afe1542.png">

<img width="413" alt="" src="https://user-images.githubusercontent.com/47657715/57599272-1b493100-7591-11e9-98e5-168765b14f97.png">

<img width="413" alt="" src="https://user-images.githubusercontent.com/47657715/57599184-d8875900-7590-11e9-86ed-07be362f9d0e.png">





__cancel #4 (특정 미체결 주문의 부분 취소)__  
```python
set_cancel(exchange=EXCHANGE_NAME, currency=CURRENCY, order_id=ORDER_ID, qty=QUANTITY)
```

입력 받은 거래소의 해당 가상화폐와 order_id를 갖는 주문을 입력받은 수량 만큼 부분 취소합니다.  
입력한 qty가 미체결 수량보다 많은 경우 전체 수량을 취소하고, 적은 경우에는 부분 취소합니다. 

다음 예시는 주문 수량이 1.5873이었던 "XRP"의 주문 중 0.2 수량만큼을 부분 취소하는 예시입니다.  

order_list에서 해당 order_id의 remain_qty가 1.3873만큼 남아있는 것과, 거래소의 미체결 주문 현황에 주문잔량이 1.3873으로 변경되어 있는 것을 확인할 수 있습니다.


![image](https://user-images.githubusercontent.com/47657715/57599787-d4f4d180-7592-11e9-9d96-d14be379beec.png)

![image](https://user-images.githubusercontent.com/47657715/57599727-a840ba00-7592-11e9-8863-c2a7c89fd926.png)

<img width="413" alt="" src="https://user-images.githubusercontent.com/47657715/57599815-eb029200-7592-11e9-9920-a1cc546ffc6c.png">


<br>  

아래의 예시와 같이 존재하지 않는 order id 혹은 order id를 잘못 입력하는 경우 order_list에서 order id를 확인하여 해당하는 order id가 없는 경우 주문 취소를 진행하지 않습니다.


![image](https://user-images.githubusercontent.com/47657715/57599840-01105280-7593-11e9-92c3-fa27a2d74c77.png)



<br>  


__cancel #3 (특정 미체결 주문 전체 취소)__  
```python
set_cancel(exchange=EXCHANGE_NAME, currency=CURRENCY, order_id=ORDER_ID)
```

qty를 입력하지 않는 경우 해당 가상화폐의 order_id를 갖는 주문의 미체결 수량 전체를 취소합니다.

다음 예시는 "BTC" 화폐의 주문 중에서 미체결 주문을 주문취소하는 예시입니다. 

8,368,000 KRW으로 주문을 넣은 order가 주문이 체결이 되어 주문취소를 실패한 것과, 8,363,000 KRW로 주문을 넣은 order의 order id를 입력하여 주문을 취소하는 것을 확인할 수 있습니다. 

![image](https://user-images.githubusercontent.com/47657715/57600171-015d1d80-7594-11e9-9047-463d4538acea.png)


![image](https://user-images.githubusercontent.com/47657715/57600173-028e4a80-7594-11e9-85ba-35ef1647edb4.png)


BotContext의 order_list에 남아있는 주문도 함께 업데이트 되어있는 것을 확인할 수 있습니다.

<img width="520" alt="" src="https://user-images.githubusercontent.com/47657715/57600345-87796400-7594-11e9-935e-ef4842cf5ce4.png">


<br>  


__cancel #2 (특정 거래소, 특정 가상화폐의 미체결 주문 전체 취소)__  
```python
set_cancel(exchange=EXCHANGE_NAME, currency=CURRENCY)
```

order_id를 입력하지 않고 가상화폐에 대한 정보만 입력한 경우 입력한 거래소의 currency에 해당하는 주문을 전부 취소합니다.  

다음 예시는 주문 취소할 currency로 "ETH"를 입력하여 미체결로 남아있는 3개의 "ETH" 화폐의 주문을 모두 취소하는 예시입니다. 

![image](https://user-images.githubusercontent.com/47657715/57600469-d7f0c180-7594-11e9-8208-505bba012c38.png)



거래소 명칭과 전체 취소할 가상화폐만을 입력한 경우, order_list와 거래소 미체결 주문 현황에 저장되어 있던 이전의 ETH 화폐의 주문들이 순차적으로 취소되어 모두 취소된 것을 확인할 수 있습니다.

<img width="520" alt="" src="https://user-images.githubusercontent.com/47657715/57600513-ffe02500-7594-11e9-8eee-089c03c460e7.png">

<img width="413" alt="" src="https://user-images.githubusercontent.com/47657715/57600524-0a022380-7595-11e9-9138-79c1b3fc085d.png">


<br>  



__cancel #1 (특정 거래소의 미체결 주문 전체 취소)__  
```python
set_cancel(exchange=EXCHANGE_NAME)
```

거래소의 명칭만 입력한 경우, 입력 받은 거래소에 전송된 주문에 대하여 가상화폐의 종류, 남은 수량과 관계 없이 모든 미체결 주문을 전체를 취소합니다.


다음 예시는 "XRP" 화폐의 미체결 주문이 3개, "BTC" 화폐의 미체결 주문이 1개인 상태에서, 거래소 명칭 'coinone' 만을 입력하여 set_cancel 함수를 호출하여 두 가상화폐의 미체결 주문들의 주문취소를 진행합니다. "BTC"의 미체결 주문의 경우 주문이 체결되어 주문취소가 진행되지 않은 것을 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/47657715/57600966-3c605080-7596-11e9-8d20-b9a4bdc9a13e.png)


order_list에 저장되어 있는 미체결 주문의 정보가 모두 삭제된 것을 확인할 수 있고, 두 건의 체결된 "BTC" 화폐의 주문으로 인해 자산이 변화하게 된 것도 함께 확인할 수 있습니다.

<img width="413" alt="" src="https://user-images.githubusercontent.com/47657715/57601162-b85a9880-7596-11e9-9e37-2fd02238f8c2.png">

<img width="413" alt="" src="https://user-images.githubusercontent.com/47657715/57601163-b8f32f00-7596-11e9-97a2-06689c03ef50.png">




