---
title: "00 Backtest Basic"
permalink: /docs/backtest-basic/
excerpt: "CATS Lab Backtest 00 Basic"
last_modified_at: 2019-04-25T15:00:00
redirect_from:
  - /theme-setup/
toc: true
---


### 0. CATS Lab package 및 필요한 모듈과 함수 import  

### 0.1. 필수적인 library 및 class import  

BacktestContext 생성을 위해 import 합니다.

```python
from coza.backtest import BacktestContext
```

Backtest에서 가상으로 주문을 넣기 위한 Order 객체를 import 합니다.

```python
from coza.objects import Order
```

### 0.2. 부가적인 library 및 class import  

부가적으로 사용할 CATS Lab 패키지 내의 객체나 함수 및 전략에서 이용할 TA들을 import 합니다.

```python
from coza.ta import (TA_NAME)
```

이외에도 전략 구현에 필요한 python library를 import 합니다.

