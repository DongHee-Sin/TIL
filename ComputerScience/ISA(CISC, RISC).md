# ISA
개발자들이 프로그래밍을 위해 작성하는 프로그래밍 언어들은(Swift, JAVA, C) 모두 사람이 이해하기 위운 형태의 고급 언어이다. <br/>
프로그램을 실행시키는 프로세서는 0과 1로 이루어진 기계어만 이해할 수 있기 때문에 고급 언어로 작성된 소스코드를 기계어와 근접한 형태로 변환해야 하는데 그 과정이 컴파일이며, 컴파일된 언어는 어셈블리 언어로 변환된다. <br/>

컴파일된 어셈블리 언어는 CPU가 따라야 하는 명령어들로 이루어지는데, 예를 들면 add, load과 같은 프로그램을 실행시키기 위한 모든 스텝을 명령어로 갖는다. <br/>
이렇게 변환된 어셈블리 언어의 소스코드를 어셈블러가 기계어로 변환하여 CPU가 인식하고 실행할 수 있게 되는 것이다.

<br/>

고급 언어 코드(Swift, JAVA ..) => 컴파일러 => 어셈블리 언어 <br/>
어셈블리 언어 => 어셈블러 => 기계어

<br/>

하지만, 서로 다른 구조를 갖는 프로세서에서 컴파일된 어셈블리 언어는 다른 프로세서에서는 사용할 수 없다. (ex. m1칩 기반 맥북에서 컴파일된 어셈블리 코드는 인텔 맥북에서 실행할 수 없다.)<br/>
이유는 프로세서는 각기 다른 ISA(Instruction Set Architecture)를 갖기 때문이며, ISA는 프로세서에서 실행할 수 있는 "명령", "커맨드" 집합을 의미한다. 

<br/>

인텔맥과 m1맥에서 서로 프로그램이 호환되지 않는 이유도 각기 다른 ISA를 갖기 때문이다. (로제타로 인텔맥 프로그램을 m1에서 돌릴 수 있지만.. 이 내용은 일단 생략)<br/>
인텔의 칩은 CISC를 채택하였고, ARM 기반으로 만들어진 m1칩은 RISC를 채택했다.

<br/>
<br/>

## CISC
복잡하고 다양한 명령어 집합으로 이루어진 CPU 아키텍쳐로 인텔과 AMD의 x86, x86-64가 CISC를 채택하고 있는 대표적인 아키텍쳐이다. <br/>
복잡한 명령어를 많이 처리해야 하기 때문에 배터리 효율성이 좋지 않고, 속도도 느리고, 트랜지스터도 많이 필요하다. <br/>
단점만 보이는 CISC를 사용했던 이유는 고급 언어가 없던 시절.. 어셈블리 언어를 손으로 짜던 시점을 생각해야 한다. <br/>
CISC는 다양한 명령어 집합을 가지기 때문에 직접 작성하는 코드가 적어져서 프로그래밍이 비교적(?) 수월했고, 작성되는 코드가 줄어든 만큼 프로그램의 용량도 줄어들었기 때문에 메모리 용량이 부족하던 해당 시점에는 CISC 방식의 많이 사용되었다.

<br/>
<br/>

## RISC
사용 빈도가 높은 단순한 명령어 집합으로 이루어져 있고, 대표적으로 ARM 프로세서가 RISC를 채택하여 만들어졌다. <br/>
명령어가 단순하기 때문에 배터리 효율성이 좋고, 단순 작업을 빠르게 반복하기 때문에 속도도 빠르다. <br/>
적은 명령어 개수를 사용해서 트랜지스터 개수도 CISC 방식에 비해 훨씬 적다.

<br/>
<br/>

이렇게만 보면 RISC가 무조건 좋은 것 같지만.. 꼭 그렇지는 않다. <br/>
파이프라이닝, 병렬 처리, 호환성 같은 다양한 부분에 있어서 서로 장단점이 있고 무엇보다 요즘은 CISC와 RISC가 서로의 장점을 가져와서 개선하면서 그 경계가 좀 희미해졌다고 한다.