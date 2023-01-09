# TDD

TDD의 목적은 깨끗한 코드를 작성하는 것이다.  
테스트 코드를 짜기 힘들다면 해당 코드를 호출하는 것이 힘들다는 것을 의미한다. 이는 코드가 너무 많은 일을 하거나 너무 많은 클래스들과 연관 관계를 맺고 있을 가능성이 있다.

또 TDD는 리팩토링과 많이 연관해서 얘기한다.  
테스트가 있으므로, 변경시 사이드 이펙트를 발견할 수 있다.

## 켄트백이 말하는 TDD의 장점

개발자가 자유자재로 개발의 보폭을 조절할 수 있다.  
현재 프로젝트 내에서 자신이 위치한 곳을 정확히 인지할 수 있다.  
매 단계 마다 지금 해야할 일을 정확히 알고 그것에만 온전히 집중할 수 있다.  
지금까지 작성한 코드에 대한 확신을 가지게 된다.  
그렇기 때문에 오히려 전체적인 개발 속도를 향상시키는 결과를 가져온다.  
TDD의 라이프사이클 특성상 개발 도중 반복적으로 리팩토링을 수행하기 때문에 개발 도중 어떠한 순간에도 코드의 품질은 좋은 상태로 유지된다.  
내가 다음에 구현할 기능에 대한 테스트만 작성하며, 테스트를 통과하기 위한 만큼의 코드만 작성하기 때문에 오버 엔지니어링의 가능성이 줄어들게 된다.

## 최범균님이 말하는 TDD의 장점

테스트 코드를 작성하는 과정에서 기능 구현을 위한 설계 요소들을 먼저 고민하게 된다.   
코드가 추가 또는 변경될 때마다 테스트를 수행하여 코드의 문제점을 빠르게 파악할 수 있다.  
코드 수정에 대한 심리적 불안감을 줄여주므로 리팩토링을 보다 과감하게 진행할 수 있고, 지속적인 리팩토링이 가능하다.  
지속적인 리팩토링을 통해 코드 품질이 급격히 나빠지는 것을 방지하고, 유지보수 비용을 낮춰준다.  
변화하는 요구 사항을 적은 비용으로 반영할 수 있게되어 소프트웨어의 생존 시간을 늘려준다.  
테스트 코드가 추가될 때마다 검증하는 범위가 넓어져서 소프트웨어 품질이 상승한다.  