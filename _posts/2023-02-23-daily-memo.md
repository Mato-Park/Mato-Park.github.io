---
title: "[하루일기] '23/02/23 - 엑셀도 DB일 수 있을까?!(ft. 데이터 전처리)"

categories:
    - Data
tags:
    - Data
    - DB
last_modified_at: 2023-02-23T23:39:00

toc: true
toc_sticky: true
toc_label: "On This Page"
---

안녕하세요.<br><br>
데이터 분석 실무를 하다보니 어느새부턴가 개인적으로 엑셀로 저장된 데이터를 기피하게 되더군요. 이번 분석 프로젝트에서 클라이언트사의 중요한 데이터가 엑셀로 보관되어 있었고,
최근 5년치의 데이터를 엑셀로부터 가져와야 했습니다. 데이터 수집 프로그램을 짜다가 발생하는 수많은 오류를 마주하면서 얼마 전 팀장님이 고객에게 지나가듯이 했던 말이 떠올랐습니다.
> "엑셀도 DB의 일종이죠. "<br>

정말로 엑셀도 DB일 수 있을까? 곰곰이 곱씹어보다가 엑셀이 DB의 일종일 수 있으나 DB로서의 기능을 완벽하게 구현하기 어렵다 생각했습니다. 사실 엄격하게 보자면 DB라고 보기 어렵다고 생각합니다.
제 나름대로 그 이유를 생각해봤습니다. <br>
1. **수기 입력**
    - Human Error로 인하여 오타 혹은 데이터가 누락될 확률이 높음
    - 데이터 입력 제한/제약을 가할 수 있는 기능 부재
    - 분석가 입장에서 데이터를 전적으로 신뢰하기 어려움
2. **데이터 수집 속도**
    - DB와 비교했을 때, 데이터 처리 속도가 매우 떨어짐
3. **안정적인 운영 담보 어려움**
    - 엑셀 데이터를 지속적으로 활용한다 했을 때, 위 1번의 문제가 없으리라고 장담할 수 없음

제가 요 몇일동안 했던 작업도 데이터가 엑셀이 아닌 DB에 있었다면 1시간도 안되서 끝났을 일이라 생각합니다. 엑셀로부터 가져오다보니 수많은 오류를 일일이 프로그래밍을 통해 대응해야 했고,
속도는 더디고 하다보니 길어지네요. 제가 모든 발생 가능한 에러에 대해 미리 대응할 수 있었다면 좋았겠지만, 아직 그 정도 수준은 무리인 것 같습니다. <br><br>
그럼에도 왜 엑셀을 사용할까요? 이것도 지극히 개인적인 견해이지만 풀어보겠습니다.

1. 관성
    - 예전부터 써왔으니 편하니 별 생각없이 씀
    - 안그래도 본업에 치이는데 초기 시스템 구축을 위하여 짬을 내기 힘듬
2. 비용
    - 기업 입장에서는 시스템 구축을 위해서 비용이 투자되어야 하지만 투자 대비 정량적인 성과가 뚜렷하지 않음
    - 실무자, 관리자 모두 필요성에는 공감하나 비용 문제로 결정권자에 승인이 떨어지지 않는 경우
3. 역량
    - 노후화된 조직일수록 새로운 시스템을 받아들이기 힘들어 함
    - 리더가 의지를 갖고 조직원을 설득하고 적극적으로 밀어붙여야함

쓰다보니 엑셀에 대해서 너무 안좋게만 쓴 것 같네요. 무조건 나쁘다는 것은 아닙니다. 엑셀은 다양한 기능을 제공하며 사용하기 편한 장점이 있습니다. 적절하게 활용한다면 더할나위 없는 툴이겠죠.
하지만, 이제는 엑셀 데이터는 그만... 노노... 물론 분석가로서 데이터 수집과 전처리도 척척 해내야하지만, 좋은 것을 놔두고 굳이 돌아가야할 필요가 있을까요?<br>
앞으로도 당분간은 시스템화 하지 않고 임시방편으로 특정 서버에 올려놓은 엑셀 데이터를 처리해서 가져오기로 했는데, 앞날이 뻔히 보이는 건 저만의 착각일까요? 잘 되겠죠ㅎㅎ
