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

단, 부분 취소의 경우 coinone 거래소에서만 제공하며, upbit 거래소에서는 부분취소 기능을 제공하지 않습니다.
upbit 거래소의 경우 qty를 입력하더라도(currency와 order_id도 함께) 미체결 주문의 모든 수량을 주문취소합니다. 

다음은 주문을 취소하는 4가지 방법에 대한 예시입니다. 


![09 set_cancel Example01](https://user-images.githubusercontent.com/47657715/56647750-eeee7300-66bc-11e9-8cdf-f593418a2fb1.png)
![09 set_cancel Example02](https://user-images.githubusercontent.com/47657715/56647752-ef870980-66bc-11e9-993d-d2357aae3946.png)
<img width="414" alt="09 set_cancel Example03" src="https://user-images.githubusercontent.com/47657715/56647753-ef870980-66bc-11e9-9224-7b6cca40816e.png">

![09 set_cancel Example04](https://user-images.githubusercontent.com/47657715/56647860-252bf280-66bd-11e9-8cff-b681430bca8e.png)
![09 set_cancel Example05](https://user-images.githubusercontent.com/47657715/56647862-25c48900-66bd-11e9-809a-fa74ba477871.png)



![09 set_cancel Example06](https://user-images.githubusercontent.com/47657715/56647920-412f9400-66bd-11e9-8bf7-49802521eb18.png)
![09 set_cancel Example07](https://user-images.githubusercontent.com/47657715/56647922-41c82a80-66bd-11e9-85b7-34e711432d39.png)


4개의 ‘XRP’ 주문이 들어간 상태에서 거래소의 미체결 주문 현황과 Bot의 order_list에 저장되어 있는 주문들에 대한 정보입니다.


__cancel #4 (특정 미체결 주문의 부분 취소)__  
```python
set_cancel(exchange=EXCHANGE_NAME, currency=CURRENCY, order_id=ORDER_ID, qty=QUANTITY)
```

입력 받은 거래소의 해당 가상 화폐와 order_id를 갖는 주문을 입력받은 수량 만큼 부분 취소합니다.  
입력한 qty가 미체결 수량보다 많은 경우 전체 수량을 취소하고, 적은 경우에는 부분 취소합니다. 

![09 set_cancel Example08](https://user-images.githubusercontent.com/47657715/56647980-61f7e980-66bd-11e9-9e47-d90bb8cc157a.png)
![09 set_cancel Example09](https://user-images.githubusercontent.com/47657715/56647982-61f7e980-66bd-11e9-9a3d-dcb5a9b9f294.png)
![09 set_cancel Example10](https://user-images.githubusercontent.com/47657715/56647984-62908000-66bd-11e9-83d5-8a0b709b1bdb.png)


주문 수량이 1이었던 주문을 0.5 수량만큼을 부분 취소하는 경우 order_list에서 해당 order_id의 remain_qty가 0.5로 남아 있는 것과, 거래소 미체결 주문 현황에 주문잔량이 0.5000으로 저장되어 있는 것을 확인할 수 있습니다.


__cancel #3 (특정 미체결 주문 전체 취소)__  
```python
set_cancel(exchange=EXCHANGE_NAME, currency=CURRENCY, order_id=ORDER_ID)
```

qty를 입력하지 않는 경우 해당 가상화폐의 order_id를 갖는 주문의 미체결 수량 전체를 취소합니다.


![09 set_cancel Example11](https://user-images.githubusercontent.com/47657715/56648074-94a1e200-66bd-11e9-9af4-4f5f87b6eb47.png)
![09 set_cancel Example12](https://user-images.githubusercontent.com/47657715/56648075-94a1e200-66bd-11e9-8c0b-c68f9852764e.png)
![09 set_cancel Example13](https://user-images.githubusercontent.com/47657715/56648076-94a1e200-66bd-11e9-86b8-2a67625db8af.png)


__cancel #2 (특정 거래소, 특정 가상 화폐의 미체결 주문 전체 취소)__  
```python
set_cancel(exchange=EXCHANGE_NAME, currency=CURRENCY)
```

order_id를 입력하지 않고 가상화폐에 대한 정보만 입력한 경우 입력한 거래소의 currency에 해당하는 주문을 전부 취소합니다.

![09 set_cancel Example14](https://user-images.githubusercontent.com/47657715/56648127-b26f4700-66bd-11e9-9896-776a78eb79e0.png)
![09 set_cancel Example15](https://user-images.githubusercontent.com/47657715/56648128-b307dd80-66bd-11e9-9f4a-ec12424a7a6a.png)

거래소 명칭과 전체 취소할 가상화폐만을 입력한 경우, order_list와 거래소 미체결 주문 현황에 저장되어 있던 이전의 ‘XRP’ 주문들이 순차적으로 취소되어 모두 취소된 것을 확인할 수 있습니다.


__cancel #1 (특정 거래소의 미체결 주문 전체 취소)__  
```python
set_cancel(exchange=EXCHANGE_NAME)
```

거래소의 명칭만 입력한 경우, 입력 받은 거래소에 전송된 주문에 대하여 가상화폐의 종류, 남은 수량과 관계 없이 모든 미체결 주문을 전체를 취소합니다.

![09 set_cancel Example16](https://user-images.githubusercontent.com/47657715/56648187-cf0b7f00-66bd-11e9-94cb-f338c684f3fa.png)
![09 set_cancel Example17](https://user-images.githubusercontent.com/47657715/56648190-cfa41580-66bd-11e9-86d5-15072e5024a6.png)

![09 set_cancel Example18](https://user-images.githubusercontent.com/47657715/56648191-cfa41580-66bd-11e9-8e57-d01b4ce7eaa0.png)

<img width="413" alt="09 set_cancel Example19" src="https://user-images.githubusercontent.com/47657715/56648192-d03cac00-66bd-11e9-90af-2699a05a8078.png">
<img width="413" alt="09 set_cancel Example20" src="https://user-images.githubusercontent.com/47657715/56648193-d03cac00-66bd-11e9-93c0-bc5f1e4d4f9d.png">


![09 set_cancel Example21](https://user-images.githubusercontent.com/47657715/56648194-d0d54280-66bd-11e9-9061-8e888ac71712.png)
![09 set_cancel Example22](https://user-images.githubusercontent.com/47657715/56648196-d0d54280-66bd-11e9-8143-4f6c027eeb02.png)

<img width="410" alt="09 set_cancel Example23" src="https://user-images.githubusercontent.com/47657715/56648198-d16dd900-66bd-11e9-9c31-06a650dcd0aa.png">
<img width="411" alt="09 set_cancel Example24" src="https://user-images.githubusercontent.com/47657715/56648199-d16dd900-66bd-11e9-8ad0-9d26e1ec81cd.png">

‘XRP’와 ‘MIOTA’가 각각 수량 1 씩 주문된 상태에서, 거래소 명칭(예시에서는 'coinone')만을 입력하여 set_cancel 함수를 호출한 경우 두 가상화폐의 미체결 주문들이 모두 취소 된 것을 확인할 수 있습니다.


