개발한 서비스에 사용자 유입이 적을 때는 괜찮겠지만 유입이 급격히 늘어난다면 가용 자원 서버에 부하가 걸려 병목현상을 일으킬수 있습니다. 예를들면 검색엔진 사이트의 경우에 손흥민이 골을 넣었다던지 핫한 연애인에 관련된 이슈가 터지게 되면 사용자 유입이 급격히 늘어날 것입니다.

만약 대규모 트랙픽으로 인해 웹서버에 병목현상이 발생한다면 웹서버는 정보를 요청만 하고 어떠한 것도 저장하지 않고 있기 때문에 단순히 장비만 추가해주면 해결이 될 것입니다. 서버에 데이터를 저장 하지 않는 것을 Stateless하다고 표현합니다. 반대로 웹서버가 Stateful하다면 모든 정보를 저장하고 있을 것이고 모든 데이터가 날라가게 된다면 이 데이터를 복구할 방법이 없을 것입니다. Stateless하다면 웹서버에 데이터가 저장되어 있지 않고 클라이언트나 DB 서버에 해당 데이터가 저장되어 있어서 복구할 웹 서버에 데이터를 복구할 수 있을 것입니다.

**정말로 문제가 되는것은 DB 서버에 병목현상이 생기면 어떻게 해결해야 할까**라는 것입니다.

서버의 성능을 향상시켜서 또는 서버의 대수를 늘려서 해결 해야합니다. 이제부터 트랙픽으로 인해 DB 병목현상에 대비할 수 있는 확장성(Scalability)에 대해서 **Scale up**과 **Scale out**을 알아보도록 하겠습니다.



# Scale up & Scale out



![image](https://user-images.githubusercontent.com/55625864/94815388-6f2e1180-0435-11eb-9ef3-d90b24ec5bc0.png)



#### Scale up

Scale up은 수직적 확장으로써 서버 성능을 증가시켜 관리하는 것을 말합니다. RAM, CPU, DISK를 교체하거나 추가하여 서버의 성능을 올리는 것입니다. 하지만 Scale up은 서버의 크기에 따라 서버에 추가할 수 있는 장치들의 수가 제한되어 있습니다. 더이상 추가 할 수 있는 여유 슬롯이 없으면 더 큰 서버로 변경하는데 모든 데이터를 Migration해야 하기 때문에 서버를 정지해서 작업할 때 다운타임이 발생하여 서비스를 정지 해야할 위험성을 가지고있습니다. 또한, 속도나 성능을 높이기 위해서 추가 장치들을 계속 추가하면 비용 대비 큰 성능 폭을 기대할 수 없습니다.

서버의 확장성을 고려할 때 Scale up만 사용한다면 한대의 서버에 리소스가 집중되고, SPOF(단일 고장점)으로 인해 서버의 데이터를 모두 잃어버릴 수 있는 위험성이 있습니다. 이렇기 때문에 Scale up은 정합성을 또는 데이터베이스 성능을 기대할 때 사용하는것이 적합합니다.

#### Scale out

Scale out은 수평적 확장으로써 여러대의 서버를 확장하여 관리하는 것을 말합니다. 지속적인 확장을 할 수 있지만 서버 대수가 확장됨에 따라 유지보수 및 관리 비용이 증가하게 되고, 많은 서버를 유지하기 위해 복잡한 설계를 할 수 있는 능력이 필요합니다. Scale out은 복잡하지 않고 단순한 데이터를 관리하는데 유용합니다. 예를 들어 대규모의 트래픽이 들어올 때 많은 작업이 이루어 지지 않고 단순히 Session을 관리하는 것에 적합합니다. 데이터베이스의 성능을 높이기 위해 사용하기 보다는 대용량 트래픽이 발생할 수 있는 SNS나 검색엔진등에서 사용을 필요로 할것입니다.

또한, 여러 서버를 확장해서 사용할 수 있으니 데이터를 잃어버리지 않기 위해서 사용하는 이중화에 대해서도 생각해 볼 수 있습니다. 한 대의 서버에 병목현상(bottleneck)이 생겨 서버가 터진다면 이중화로 서버를 Failover(복구) 할 수 있습니다. 이렇게 이중화를 사용하여 다른 서버에 데이터를 백업해 저장한다면 가용성 측면에서도 장점이 있습니다.



#### Scale up vs Scale out

|                     Scale up                      |                      Scale out                       |
| :-----------------------------------------------: | :--------------------------------------------------: |
|          성능 증가 대비 비용이 많이 나옴          |             용량 증가 대비 비용이 저렴함             |
| 비용 대비 서버 성능 측면에서 비교적 성능이 안나옴 |       비용처리 만큼 서버의 용량을 늘릴 수 있음       |
|                   관리가 용이함                   |            서버 확장에 따라 관리가 복잡함            |
|                 설계 비교적 쉬움                  |            서버 확장에 따라 설계가 복잡함            |
|       정합성을 유지가 어려운 경우에 사용됨        | 정합성 유지가 쉬울 때 또는 가용성을 요구할 때 사용됨 |
|                  DB 서버에 적합                   |                    웹 서버에 적합                    |
|   OLTP(Online Transaction Processing)에 사용됨    |     OLAP(Online Analytical Processing)에 사용됨      |



현재 프로젝트로 Youtube를 모티브로한 sns를 개발하고 있습니다. 위의 설명과 같이 대규모 트래픽을 다룰 때 장점이 되는 Scalue out을 선택해야 합니다. Scale out은 대규모 데이터를 읽고/쓰기에 있어서 적합한 솔루션이라고 할 수 있을것입니다. 좀 더 자세히 알아보도록하겠습니다.

먼저 서버 구성에 대해 알아 보도록 하겠습니다. 보통 서버는 Master와 Slave로 나누어 구축하게 되고 데이터를 Failover(복구) 하기위해 이중화를 하여 백업시킬 수 있습니다. 그렇다면 어떻게 서버를 살려놓고 터진 서버를 해결하고 다시 정상 서비스로 돌려 놓을지 생각해봤습니다. 

서버에 장애가 생겨 서버를 백업해야하는 상황이 있을 때 최소 서버는 몇개가 있어야 할까요? 정답부터 말씀드리면 최소 3대가 있어야합니다. 그 이유는 한대의 서버가 터져서 재부팅하면 일반적으로 다시 복구될 수도 있겠지만 디스크가 깨지는 현상이 일어난다면 sub-서버가 서비스를 대신하게 해야합니다. 이 후 서버를 한대 더 가지고 복구를 해서 서비스를 복귀 시켜야 하기 때문에 서버는 최소 3대가 필요합니다.



그렇다면 어떻게 효율적으로 읽고/쓰기를 할 수 있을지 한번 알아보도록하겠습니다. 일반적으로 서버가 쓰기 기능보다는 읽기를 많이 하기 때문에 읽기 서버에 대한 비중을 더 늘려 서버를 분리하면 성능이 증가할 수 있습니다. 보통은 7:3 또는 8:2 정도로 진행한다는데 서비스 마다 이 수치는 다를거라고 생각합니다. 만약 쓰기가 늘어단다면 파티셔닝을 통해 성능을 증가 시킬 수 있습니다. 하지만 파티셔닝의 단점은 request 수 / N대의 서버로 파티셔닝을 진행하면 장비의 수가 늘어나 비용이 증가한다는 것입니다. 파티셔닝은 규모나 서비스의 부하를 보고 적절히 선택해야하는 부분인것 같습니다.



파티셔닝을 할 때 비용이 증가한다면 이것을 늦출 수 있는 방법은 없을까도 생각해 봐야할 부분입니다. 이 부분도 Consistent Hashing을 이용해서 효율적으로 서버를 운영할 수 있습니다. 예를 들어 서버 한대가 터진다면 그 안에 있는 데이터들을 포함해서 전체의 데이터들을 현재 서비스 되고 있는 서버에 request 수 / 서버대 수로 재분배를 해야합니다. 이러한 방법 말고 Consistent Hashing을 이용하면 터진 서버의 데이터들만 남아 있는 서버에 재 분배되게 하기 때문에 효율성이 증가 하게 됩니다. 이로써 파티셔닝을 하게 되는 시점을 늦출 수 있습니다.



서버를 대략적으로 어떻게 운영해야하는지도 간략하게 설명을 한것 같습니다. 그렇다면 많은 서버에 세션 데이터를 어떤 방식으로 저장 해야할까요?

예를 들면 한 명의 유저가 첫 로그인 요청을 하여 SessionID를 A서버에 저장하고 유저는 쿠키에 SessionId를 저장하면서 통신을 하고 있다가, 새로운 요청을 할 때 B서버에 요청을 하게 되면 로그인이 안되었다는 안내를 받게 될것입니다. 사용자 입장에서는 너무나 어처구니가 없고 계속 로그인을 해야하는 것도 아닌 이상한 상황이 벌어지게 됩니다. 이처럼 서버에 데이터를 어떻게 저장하고 운영해야 할 것인지도 고민해 보아야할 문제가 될것 같습니다.



![image](https://user-images.githubusercontent.com/55625864/94943432-aaead900-0512-11eb-80a9-4ab7d6cbae82.png)



위 정합성 문제를 위해서는 Load balancing, Sticky session, Session clustering, Session Storage에 대해서도 알아야합니다.



#### 결론

1. legacy인 scale up을 무조건 지양하는 것이 아니라 scale out과 비교하여 서로의 장점을 잘 사용할 수 있어야 합니다.
2. scale up은 수직적 확장이고, scale out은 수평적 확장입니다.
3. scale up은 OLTP을 요구할 때 사용되고 scale out은 OLAP을 요구할 때 사용됩니다.
4. 대규모 트래픽을 예상하는 SNS 어플리케이션에는 scale out이 적합하다.



#### 참고

- Memcached와 Redis - 강대명 지음 [한빛미디어]
- [확장성 있는 웹 아키텍처와 분산 시스템](https://d2.naver.com/helloworld/206816)