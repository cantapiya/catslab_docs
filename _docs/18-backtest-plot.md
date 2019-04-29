---
title: "08 Plot"
permalink: /docs/backtest-plot/
excerpt: "CATS Lab Backtest 05 plot"
last_modified_at: 2019-04-25T15:50:00
redirect_from:
  - /theme-setup/
toc: true
---

## 8. plot 함수의 사용 방법 및 사용 예시

Result 객체를 이용하여 사용할 수 있는 plot 함수는 백테스트에 이용한 가상화폐의 가격(종가 기준)의 변화와 백테스트 과정에서 거래가 발생한 시점마다 저장 되는 평가가격을 기반으로 자산의 변화를 도식화시켜주는 함수입니다. 이 함수의 사용 방법은 다음과 같습니다.

```python
def plot(add_marker, current_list, main_interval)
```


### 8.1. add_marker  
거래가 발생하는 시점을 매수와 매도를 구분하여 marker로 표시할지에 대한 여부를 bool 타입으로 입력합니다. 

plot 함수는 기본적으로 백테스트 기간 동안 거래에 이용한 가상화폐의 가격 변화 그래프를 보여주는데, add_marker=True로 입력하는 경우 거래가 발생한 매 시점의 가격이 매수주문과 매도주문을 구분하여 가격 변화 그래프 위에 표시됩니다.


### 8.2. currenct_list  
그래프를 그릴 때, 사용자가 도식화하고자 하는 가상화폐의 종류를 입력합니다. 

한 가지 가상화폐만 입력할 수도 있고, 여러 개를 입력하는 경우 여러 가상화폐의 가격 변화를 하나의 그래프에 동시에 나타낼 수도 있습니다.


### 8.3. main_interval  
입력한 가상화폐의 가격 변화를 종가를 기준으로 하여 그래프를 그리는데, 종가가격의 기준이 되는 분봉을 입력하는 parameter입니다.  

plot 함수의 사용 예시는 다음과 같습니다.


![Backtest Result Plot Example01](https://user-images.githubusercontent.com/47657715/56639515-d3c73780-66ab-11e9-81db-85f57e5a383d.png)

![Backtest Result Plot Example02](https://user-images.githubusercontent.com/47657715/56641034-57ceee80-66af-11e9-82a9-0fee9fa2a849.png)


