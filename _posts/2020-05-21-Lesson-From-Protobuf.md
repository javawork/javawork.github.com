Cap'n Proto 개발자가 쓴 protobuf의 문제점 입니다.

[https://capnproto.org/cxx.html](https://capnproto.org/cxx.html)

## PROTOBUF에서 배운 교훈
Cap’n Proto의 개발자는 protobuf 2의 개발자이기도 합니다. 거기서 얻은 많은 교훈으로 Cap’n Proto를 개발했습니다.
* Protobuf는 모든 메세지에 parse와 serialize 코드를 생성하기 때문에, 자동 생성된 코드의 양이 엄청 많습니다. 이는 많은 문제를 야기시킵니다. 서버 실행파일은 수백 메가바이트의 컴파일된 코드를 포함하게 됩니다. Cap’n Proto도 자동 생성된 코드가 있습니다만, 대부분 인라인화된 접근자입니다. .capnp.o files에 들어가는 것은 포인터 필드를 위한 디폴트 값(드물게 필요한 경우만)과 인코딩된 스키마입니다. 그나마 후자는 동적 리플렉션이 필요하지 않으면 제거됩니다.
* C++ protobuf 구현은 main 함수 전에 실행되는 많은 동적 초기화 코드(글로벌 테이블에 등록하는 작업과 같은)를 사용합니다. 이것은 많은 프로토콜과 연관이 있으면서도, 빨리 떠야하는 프로그램에서는 문제가 많습니다. Cap’n Proto는 어떠한 동적 초기화 코드도 사용하지 않습니다.
* C++ protobuf는 인터페이스와 구현부에 STL을 많이 사용합니다. 템플릿 인스턴스화의 증식은 protobuf 런타임 라이브러리에 큰 footprint를 안겨줍니다(메모리 사용량이 많음). 그리고 인터페이스에 STL을 사용하는 것은 ABI문제를 야기시키고 컴파일 타임도 증가시킵니다. Cap’n Proto는 어떠한 STL 컨테이너도 사용하지 않고 구현을 가볍게 만듭니다. 이 결과로 Cap’n Proto 런타임 라이브러리는 작고 컴파일이 빠릅니다.
* Protobuf 메시지는 많은 힙 객체와 관련이 있습니다. 각 메시지는 객체이고, primitive 타입이 아닌 repeated 필드는 포인터의 배열을 allocate하고, 문자열은 2개의 힙 객체를 추가하기도 합니다. Cap’n Proto는 자체적으로 arena allocation를 사용하기 때문에, 전체 메시지는 소수의 연속된 segment에서 할당됩니다. 따라서 메모리를 할당하는데 많은 시간이 필요치 않고, 메시지를 콤팩트하게 저장하고, 메모리 fragmentation을 방지합니다.
* 이전 토픽과 연속해서, protobuf는 성능향상을 위해 객체의 재사용을 많이 합니다. 새로 생성된 Protobuf 객체에 메시지를 building하거나 parsing하는 것은 기존 객체에 하는 것에 비해 훨씬 느립니다. 하지만, protobuf 객체의 메모리 사용량은 재사용할수록 늘어나는 경향이 있기 때문에, 여러 다른 형태의 메시지를 parsing 하는데 사용되면, 객체는 가끔씩 delete되고 재할당 되어야 합니다. 이러한 것들이 protobuf를 느리게 만듭니다. 대조적으로 Cap’n Proto는 메시지를 building 하거나 parsing 할 때 제공된 byte buffer를 사용하기 때문에 메모리 재사용이 간단합니다. 제공된 메모리가 전체 메시지를 사용하는데 충분하다면 Cap’n Proto는 메모리를 더 allocate 하지 않습니다.
