---
title: "08 order_list"
permalink: /docs/trade-order_list/
excerpt: "CATS Lab Trade 08 order_list"
last_modified_at: 2019-04-25T14:20:00
redirect_from:
  - /theme-setup/
toc: true
---

## 8. order_list 내부 구조와 사용 예시

Order 객체를 이용하여 생성된 주문은 내부 함수를 거쳐 거래소로 전송된 뒤에 BotContext의 exchanges 내부 order_list에 저장됩니다. 

order_list는 가상화폐 종류를 key값으로 한 dictionary 내부에 다시 order_id를 key값으로하고 해당 order_id의 주문 정보를 포함하는 dictionary를 value값으로 하여 저장됩니다. 

주문한 수량의 전체가 체결이 되거나, 전체가 취소될 경우 order_list에서 제거가 되며, 자산의 업데이트가 진행됩니다. 부분 체결 및 부분 취소가 발생한 경우에도 자산의 업데이트가 진행되며, 미체결된 수량이 존재하는 경우에는 remain_qty에 그 수량을 저장합니다. 

order_list에 대한 예시는 다음과 같습니다. coinone_bot이라는 BotContext에 저장되어 있는 order_list로, ‘iota’와 ‘xrp’에 관련된 미체결 주문 정보가 저장되어 있는 것을 볼 수 있습니다. 

![08 order_list Example1](https://user-images.githubusercontent.com/47657715/56647672-ce261d80-66bc-11e9-913a-a99fe66470b1.png)


order_list에 저장된 각 주문들의 정보는 다음과 같습니다.

### 8.1. currency  
주문한 가상화폐의 명칭을 저장합니다.


### 8.2. fee  
주문한 수량을 기준으로 계산한 수수료에 대한 정보입니다. 

__upbit 거래소__  
upbit 거래소의 경우 매수/매도 두 경우 모두 거래한 기축화폐로 수수료가 계산됩니다.  
따라서, 'fee'에 저장된 값은 Bot에 지정한 기축화폐에 대한 수량입니다.  
  

__coinone 거래소__  
‘order_type’이 ‘BUY’인 경우 수수료는 주문한 가상 화폐로 지불되므로, ‘fee’에 저장된 값은 해당 가상 화폐의 수량이며,  
‘order_type’이 ‘SELL’인 경우 ‘fee’에 저장된 값은 Bot에서 사용하는 거래소의 기축화폐(fiat)에 해당합니다. 



### 8.3. fiat  
Bot에서 사용하는 거래소의 기축화폐의 정보입니다.


### 8.4. order_type  
매수 주문인지 매도 주문인지 구분하는 역할을 합니다.


### 8.5. price  
주문 가격에 대한 정보입니다.


### 8.6. quantity  
주문한 수량에 대한 정보입니다.


### 8.7. remain_qty  
주문한 수량에서 부분 체결이나 부분 취소가 발생하고 난 뒤의 미체결된 주문 수량에 대한 정보입니다.


### 8.8. datetime
주문한 시간의 datetime에 대한 정보입니다.


__8.1. ~ 8.8. 내용은 두 거래소 모두 공통된 정보이며, 다음 정보는 UPBIT 거래소일 경우 추가로 저장되는 정보입니다.__  


### 8.9. locked  
보유 자산 중 해당 주문으로 인해 거래 중지 및 동결된 자산의 수량입니다. 
매수 주문인 경우 기축화폐가, 매도 주문인 경우 주문한 가상 화폐가 'locked' 정보에 저장됩니다.



