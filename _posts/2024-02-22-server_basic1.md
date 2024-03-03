---
title: "[서버] 서버란?"

categories:
    - Server
tags:
    - Computer
    - Server
    - IT
last_modified_at: 2024-02-22T21:10:00

toc: true
toc_sticky: true
toc_label: "On This Page"
---

> 데이터 분석가는 데이터 분석만 알고 잘하면 끝인 줄 알았는데 그게 아니더군요. 기초적인 컴퓨터 공학, IT 지식은 갖춰야 한다는 것을 뼈저리게 느꼈습니다. 다양한 분야에 대한 지식이 필요하겠지만 우선 서버에 대해서 공부해보고자 합니다.<br>

&#160; 기업, 조직마다 데이터를 분석하는 환경은 다양할 것입니다. 하지만 대부분의 규모가 있는 조직에서 서버 환경에서 데이터를 분석할 것이라고 생각합니다. 제가 지금 있는 팀도 온프레미스 환경에서 고객들에게 BI 솔루션 서비스를 제공하고 데이터 분석을 진행합니다(처음에는 온프레미스가 뭔지 클라우드가 뭔지도 잘 몰랐습니다). 따라서 개인적으로도 업무적으로도 성장하기 위해서는 서버에 대한 기초지식이 필요하다고 느꼈습니다. 그래서 이번 기회에 서버에 대한 기초를 공부하고 개념을 명확히 잡고자 합니다.

# 1. 서버란?
&#160; 우선 서버에 대한 정의부터 살펴보겠습니다. 서버란 무엇일까요? 저는 막연하게 서버는 단순하게 서버용 PC라고 생각했습니다. 실제로 실무에서는 서버용 PC라는 용어를 많이 접하다 보니 익숙해져서 그런가봅니다. 내가 사용하는 데스크탑/랩탑 PC가 아니라 엄청나게 고성능(?)의 서버용 PC가 데이터 센터 서버렉에 고이 설치되어 있는 모습을 상상하곤 했습니다ㅎㅎ. 하지만 정확한 서버의 정의는 다음과 같습니다.
> **서버**: 네트워크 상에서 "클라이언트"에게 정보를 제공하는 작업을 수행하는 컴퓨터 시스템

&#160; 조금 더 이해하기 쉽게 예를 들자면, 스마트 폰을 사용해 Youtube의 영상을 시청한다고 생각해봅시다. 우리는 스마트 폰의 Youtube App을 키면 영상들의 목록이 나열되고 시청하고자 하는 영상을 클릭해 영상을 시청합니다. 즉, 우리는 어떤 영상을 시청하기 위한 요청을 Youtube 서버에 보내고 Youtube 서버는 필요한 데이터와 서비스를 우리의 스마트폰으로 제공하는 과정을 거쳐 영상을 시청하게 됩니다. 즉, 서버(Youtube 서버)는 앞의 예시처럼 클라이언트(스마트폰)에 필요한 작업을 수행하게 하는 컴퓨터 시스템인 것입니다.<br>

# 2. 클라이언트?
&#160; 서버의 정의에 대해서 얘기할 때 "클라이언트"에 대해서 얘기했습니다. 서버와 클라이언트는 항상 같이 얘기되는 개념이며 하나의 시스템으로 묶입니다. 여기서 클라이언트는 요청하는 사람이라고 이해하면 쉬울 것 같습니다.<br><br>
&#160; 예를 들어, 우리가 식당에 간 손님(클라이언트)라고 해봅시다. 우리는 메뉴를 고르고 식당에 요리를 주문(요청)합니다. 이제 식당은 주문(요청)받은 요리를 조리하고 손님(클라이언트)에게 서빙합니다. 이제 우리(클라이언트)는 서빙된 음식을 맛있게 먹습니다. 컴퓨터 관점에서 얘기하면 식당=서버이고 음식을 조리하고 서빙하는 것은 마치 서버가 클라이언트의 요청을 처리하여 결과를 반환한 것과 같은 것입니다.
> 즉, **클라이언트/서버 시스템**은 클라이언트로부터 요청(request)을 받아 서버에서 처리하고 처리된 결과를 다시 클라이언트에게 제공(response)하는 시스템을 말합니다.

# 3. 서버의 종류!?
&#160; 서버는 필요로 하는 기능에 따라 정말 다양한 종류가 있습니다. 예를 들어, 웹서비스를 제공하는 'HTTPS 서버', 메일을 받고 보내기 위한 'SMTP 서버', 혹은 BI 솔루션용 서버, 개발서버, Test서버 등 정말 다양한 서버가 있습니다. 어떤 서비스를 위해 우리가 필요로 하는 목적과 기능을 정의하고 그것에 맞도록 서버를 구축하고 사용하면 됩니다.

# 4. 서버 구축하기
&#160; 그렇다면 이런 서버는 어떻게 구축하면 될까요? 대단한 것은 아닙니다. 소위 말하는 서버용 소프트웨어를 설치하면 서버가 됩니다. 특별한 장비가 필요한 것도 아니고, 개인 PC에 서버용 소프트웨어를 설치하면 서버로 사용할 수 있습니다. 심지어 한 대의 PC에서 여러 소프트웨어를 설치해 여러 서비스를 제공하는 함수로 활용할 수 도 있습니다. <br><br>
&#160; 앞서 말한 서버용 PC라는 것도 결국은 서버용 서비스를 제공하기 위한 물리적인 하드웨어 PC를 뜻합니다. 보통 서버용 PC라 함은 다수의 클라이언트에게 서비스를 제공할 수 있는 PC를 말하곤 합니다. 저는 서버용 PC는 뭔가 특별한 것일 줄 알았는데 그게 아니었습니다. 제대로 알고나니 제대로 보이는 군요

# 5. 서버 운영하기?
&#160; 흔히 IT 조직에는 개발 부서와 운영 부서가 나뉘어져 있습니다. 서버도 마찬가지로 구축이 완료되면 운영 관리 단계에 들어가게 됩니다. 그렇다면 서버 운영은 무엇을 하는 걸까요? 바로 **설정 변경, 장애 대응**과 같은 작업을 말합니다.<br><br>
&#160; 서비스를 제공하다보면 정말 말도 안되는 장애가 발생하곤 합니다. 시스템이 안정화 됐는데도 불구하고 말이죠. 장애가 발생했을 때, 서버 운영자는 왜 장애가 발생했는지 로그를 뒤져보고 원인을 찾습니다. 원인이 파악되거나 혹은 파악되지 않더라도 운영자는 장애 대응을 통해 원활한 서비스가 가능하도록 조지합니다. 이를 보통 **사후 대응**이라 합니다. 반면, **사전 대응**을 통해 장애가 발생하기 전에 미리 조치할 수도 있습니다. 예를 들어, 서버의 상태 - CPU, 메모리 사용량을 모니터링 하고 장애 발생 전에 조치할 수 있습니다. 혹은 장애 발생 방지를 위한 여러가지 최적화 작업을 수행할 수 도 있습니다. <br><br>
&#160; 설정 변경은 예를 들어 사용자 추가/삭제, 운영/보안 정책 변경 등을 수행하는 작업이라 생각하면 됩니다.

# 6. 네트워크! - 서버/클라이언트 통신
&#160; 서버/클라이언트 시스템이 제대로 작동하기 위해서는 서버와 클라이언트가 서로 데이터를 주고받을 수 있어야 합니다. 어떻게 주고받을 수 있을까요? 서버/클라이언트는 **네트워크**를 통해 서로 데이터를 주고받습니다. 보통 우리가 사용하는 인터넷도 네트워크의 한 종류인데 정확한 정의는 다음과 같습니다.
> **인터넷(Internetwork)**: 전세계에 산재한 네트워크를 연결한 거대한 컴퓨터 네트워크를 의미

&#160; 또 인터넷 외에도 LAN도 네트워크의 한 종류이며 정의는 다음과 같습니다.
> **LAN(Local Area Network)**: 일정 범위(local)안에 존재하는 컴퓨터를 연결한 컴퓨터 네트위크를 의미

# 6. 마치며...
&#160; 서버에 대한 기초 개념에 대해 살펴봤습니다. 사실 이전에는 정확하게 모르고 서버에 대해서 아는 척만 해왔습니다. 하지만 이렇게 개념을 한 번 잡고나니 서버/클라이언트 시스템, 네트워크에 대한 이해를 명확히 할 수 있었습니다. 이제 회사에서 더 이상 아는 척은 그만해도 되니 마음이 한결 편안해졌습니다 ㅎㅎ.