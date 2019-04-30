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

거래소별 API를 사용하는 함수에 접근하고, 거래소별 자산, 주문 현황, 체결 현황과 같은 정보에 접근하기 위해서는 Bot을 생성할 때 입력한 거래소의 명칭을 key값으로 가지는 exchanges 클래스에 접근하여 사용이 가능합니다. 
 
전략을 구현하고, Bot을 가동하는 과정에서 실시간으로 사용자의 Bot에 저장되어 있는 정보를 불러오는 함수들과 사용 예시들은 다음과 같습니다.

다음 예시는 “XRP”와 “MIOTA”를 각각 2개씩 매수주문을 넣은 상태에서 각각의 함수를 조회한 예시입니다.

![10 부가적인 기능의 함수 Example01](https://user-images.githubusercontent.com/47657715/56653584-e7cd6200-66c8-11e9-9720-51ab28b73441.png)


### 10.1. get_balance 함수  
거래소의 자산 보유량을 실시간으로 조회하는 함수입니다. 

다음 예시는 현재의 ‘coinone’ 거래소의 자산 현황을 조회한 결과입니다. 

초기 투입금액에서 “XRP”와 “MIOTA”를 각각 2개씩 매수주문을 넣은 상태에서의 자산 변화를 볼 수 있습니다.

![10 부가적인 기능의 함수 Example02](https://user-images.githubusercontent.com/47657715/56653585-e7cd6200-66c8-11e9-8724-83e783b0e16f.png)


### 10.2. get_time 함수  
현재 시간을 datetime 타입으로 불러오는 get_time 함수입니다.

![10 부가적인 기능의 함수 Example03](https://user-images.githubusercontent.com/47657715/56653586-e865f880-66c8-11e9-9564-d33627c08d5e.png)


### 10.3. get_order_list 함수  
현재 거래소의 미체결 주문 정보를 실시간으로 불러오는 함수입니다.

![10 부가적인 기능의 함수 Example04](https://user-images.githubusercontent.com/47657715/56653587-e865f880-66c8-11e9-806f-d057a32f51ed.png)


### 10.4. get_orders 함수  
get_orders 함수는 함수를 호출한 시점에 orders라는 list에 저장되어 있는 주문들을 불러오는 함수입니다. 

Order 객체를 사용하여 set_order 함수([Trade-07 set_order](/catslab_docs/docs/trade-set_order/) 참고)를 사용하게 되면, 생성한 주문들을 내부 함수를 이용하여 거래소로 전송하기 이전에 orders라는 list에 임시적으로 저장을 해 둡니다. 

1분마다 스케쥴링을 반복하면서 orders에 저장된 주문을 Order 객체에서 주문이 지정된 시간별로 거래소로 전송하여 처리하게 됩니다. 

전송된 주문들은 orders list에서 빠지게 되고, 주문을 전송한 후 미체결 주문은 order_list에서 관리하게 됩니다.


orders에 저장된 주문들은 거래소로 주문을 전송하는 시간을 기점(timestamp)으로하여 이전 시간의 timestamp 값을 갖는 주문들은 모두 전송하는 방식으로 처리됩니다. 

즉, 주문을 생성할 때 현재 시간, 혹은 이전의 시간으로 지정한 주문들의 경우 다음 스케쥴링에서 거래소로 주문이 전송되며, 주문 시간을 지정하여 나중에 주문이 전송되도록 설정할 수도 있습니다. 


예를 들어, 현재 시간의 한 시간 뒤의 시간으로 주문을 지정하는 경우 1시간 뒤 스케쥴링에서 거래소로 주문이 전송됩니다. 


![A.10 부가적인 기능의 함수 Example05](https://user-images.githubusercontent.com/47657715/56653588-e8fe8f00-66c8-11e9-8d1b-760c9d4b932a.png)


### 10.5. calc_estimated 함수  
calc_estimated 함수는 현재 보유하고 있는 모든 자산의 현금 평가가격을 계산하여 반환해주는 함수입니다.

다음 예시는 “XRP”와 “MIOTA”를 각각 2개씩의 매수주문을 넣고, “XRP”의 주문이 모두 체결 된 상태에서 get_balance, get_order_list, calc_esimated 함수를 조회한 예시입니다. 

주문이 체결 됬기 때문에 보유한 자산 목록에 avail이 증가한 것을 확인할 수 있고, 미체결 주문 리스트인 order_list에는 체결되지 않은 “MIOTA”의 주문이 남아 있는 상태입니다. 

이때, calc_estimated 함수를 조회하여 조회 시점의 가상화폐의 가격을 기준으로 하여 계산한 현금 평가가격을 확인할 수 있습니다. 

![A.10 부가적인 기능의 함수 Example06](https://user-images.githubusercontent.com/47657715/56653590-e8fe8f00-66c8-11e9-9ba9-98c299b9b51f.png)
![A.10 부가적인 기능의 함수 Example07](https://user-images.githubusercontent.com/47657715/56653592-e9972580-66c8-11e9-9574-9211f521603f.png)
![A.10 부가적인 기능의 함수 Example08](https://user-images.githubusercontent.com/47657715/56653593-e9972580-66c8-11e9-981f-0843b4f02ec2.png)
![A.10 부가적인 기능의 함수 Example09](https://user-images.githubusercontent.com/47657715/56653595-ea2fbc00-66c8-11e9-8542-fd64ba97ee43.png)


### 10.6. clear_balance 함수  
clear_balance 함수는 현재 보유하고 있는 가상화폐들을 호가창의 정보를 이용하여 바로 청산을 하는 기능을 수행합니다. 보유하고 있는 가상화폐의 호가창의 가장 높은 금액으로 매도 주문을 넣습니다.

위의 예시에 이어지는 상황으로, “XRP” 주문이 체결된 후 clear_balance 함수를 호출하면 보유하고 있던 1.998개의 “XRP”를 바로 청산 주문을 하여 체결이 된 것을 확인할 수 있습니다.  

![A.10 부가적인 기능의 함수 Example10](https://user-images.githubusercontent.com/47657715/56653597-ea2fbc00-66c8-11e9-9a0c-be46aaf322a4.png)

