---
title: "02 Run Trade"
permalink: /docs/trade-run/
excerpt: "CATS Lab 02 Running Trade"
last_modified_at: 2019-04-25T13:20:00
redirect_from:
  - /theme-setup/
toc: true
---

## 2. Trade 실행

BotContext로 지정한 BOT_NAME으로 context의 run 함수를 통해 Bot을 실행 시킬 수 있습니다. 
(예: BOT_NAME.run())

다음은 [Trade-01 BotContext](/catslab_docs/docs/trade-botcontext)에서 생성한 BotContext를 실행한 예시입니다.  

![image](https://user-images.githubusercontent.com/47657715/57592495-e679b180-7571-11e9-844c-9fc0327e5373.png)  

<br>  

Bot을 실행하게 되면 context를 생성할 때 입력한 정보들과 함수들을 이용하여, 1분 주기로 다음과 같은 과정을 반복합니다.


### 2.1. Update Current Balance  
현금과 가상화폐 보유량을 업데이트합니다.  
 
make_orders에서 생성하여 거래소로 전송한 거래 주문의 거래 체결 여부와 취소 여부를 확인하여 자산의 보유 현황을 업데이트하는 과정을 진행합니다.


### 2.2. Calculate Estimated Asset Price  
현재 자산의 현금가 추정치를 계산합니다.  

업데이트된 보유 자산을 이용하여 현재 자산들의 현금가격 추정치를 계산합니다.  


### 2.3. Check Safety Settings  
initialize에서 입력한 safety setting의 조건을 만족하는지의 여부를 검사합니다.  

이전 단계에서 계산한 현금가의 추정치를 이용하여 사용자가 입력한 safety setting 조건의 만족 여부를 검사합니다. 

이 단계에서 safety setting의 조건을 만족할 때, 사용자가 지정한 가동 중지의 여부에 따라 Bot의 가동을 중단 시키거나 계속해서 유지하게 됩니다.


아래의 과정은 Bot의 가동을 유지하는 경우에 진행됩니다.


### 2.4. Update Dataframes
전략에서 사용하는 가상화폐와 분봉들의 dataframe을 업데이트합니다.  

사용자가 전략에 사용하기 위해 입력한 가상화폐와 분봉의 종류에 따라 dataframe 타입으로 캔들 데이터를 저장하는데, 1분 주기로 각 화폐와 각 분봉 별로 새롭게 캔들이 생성되었는지의 여부를 확인하여 dataframe을 업데이트 합니다.


### 2.5. run_strategy 실행  
캔들 데이터 업데이트의 여부에 따라 전략에서 사용하는 지표를 업데이트 합니다.  

새롭게 생성된 캔들 데이터가 존재하는 경우 업데이트 된 기점으로 새롭게 지표를 계산하여 저장합니다.


### 2.6. make_orders 실행  
사용자가 지정한 과정을 거쳐 매수주문/매도주문/주문취소를 진행하는 함수를 실행합니다.  

이 단계에서 생성한 매수주문/매도주문은 set_order 함수를 이용하여 주문내역에 저장하고, set_cancel 함수를 이용하여 기존에 전송한 주문에 대한 주문취소 요청을 진행합니다.


### 2.7. Run Trading
거래소 API를 이용하여 거래소로 생성한 주문들을 요청합니다.  

이전 단계인 make_orders에서 생성한 주문들을 거래소 API를 사용하여 거래소로 주문 요청을 진행합니다.  

<br>  
 






