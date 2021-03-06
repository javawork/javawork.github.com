Datagram Congestion Control Protocol(RFC 4340, section 11.4)에 기반한 UDP 신뢰전송 구현방식 중 하나인 ack vector에 대한 글을 번역해 봤습니다. Google QUIC과 Valve의 [GameNetworkingSockets](https://github.com/ValveSoftware/GameNetworkingSockets), Blizzard의 [Overwatch](https://www.youtube.com/watch?v=W3aieHjyNvw)에도 이 방식이 적용되어 있습니다.

[https://gafferongames.com/post/reliable_ordered_messages/](https://gafferongames.com/post/reliable_ordered_messages/)

많은 사람들이 UDP로 reliable message 시스템을 구현하는 것은 바보 같은 짓이라고 합니다. 왜 TCP를 또 구현합니까? 하지만 모두가 TCP가 동작하는 방식에만 만족할 수는 없습니다. reliable message 구현하는 데는 많은 방법이 있고, 대부분은 TCP와는 다릅니다. 창의적인 방식으로 TCP보다 더 나은, 실시간 게임을 위한 reliable message 시스템을 구현해 봅시다.

일반적으로 reliability를 게임에서 접근하는 방법은 reliable-order와 unreliable 두 개의 메세지 종류를 정의합니다. 많은 network library에서 이런 방식을 사용합니다. 기본 아이디어는 reliable 패킷을 송신측에서 받을 때까지 수신측에서 재전송하는 방식입니다. 나쁘지 않은 방법이지만, 더 나은 방법이 있습니다.

제가 선호하는 방식은 일단 메세지들이 작게 bitpacked되어 있고, 어떻게 시리얼라이즈 되는지 알고 있습니다. 전송될 메세지가 큐잉된 후 전송 될 때 마다 queue안에 있는 다른 메세지들을 포함합니다. 이런 방식으로 메세지 재전송을 없앨 수 있습니다. reliable 메세지는 수신측에서 진짜 수신할 때까지 전송되는 패킷에 계속 포함됩니다.

가장 쉬운 방법은 모든 unacked 메세지를 전송되는 매 패킷에 포함하는 것 입니다. 예를 들면, 모든 전송 메세지는 id가 있고, 전송될 때 마다 증가합니다. 모든 전송 메세지에는 시작 id와 포함된 모든 메세지의 id가 있습니다. 수신측은 지속적으로 최근 받은 id를 송신측에 보내주고(ack), 송신측은 ack된 메세지보다 새로운 메세지를 매 패킷에 포함합니다. 

이 방식은 간단하고 구현하기 쉽지만, 대량의 메세지가 소실되면 패킷의 사이즈가 크게 증가할 수 있습니다. 전송되는 메세지에 포함되는 메세지의 개수를 제한하면 방지할 수 있습니다. 하지만 1초에 60개의 패킷 전송처럼 자주 보내는 상황이라면 같은 메세지를 자주 전송할 수 있습니다. 

Round trip 타임이 100ms라면 ack를 받기 전에 불필요하게 6번이나 메세지를 전송할 수 있습니다.  만일 정말 실시간성이 중요한 경우라면 감수할 수도 있겠지만, 대부분의 경우는 그런 bandwidth는 다른 곳에 사용되는 것이 좋습니다.

저는 우선 순위를 매겨서 가장 중요한 n개의 메세지만을 매 패킷에 포함하는 방식을 선호합니다. 이 방식은 빠른 전송과 매 패킷에 최대 n개의 메세지만을 포함한다는 원칙을 모두 만족시킬수 있습니다.
