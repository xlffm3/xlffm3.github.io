---
title: "[Java] Thread 정리"
categories:
  - Java
tags:
  - Java
toc: true
toc_sticky: true
last_modified_at: 2020-12-28T20:05:00-05:00
---

## 1. Process vs Thread

프로세스란 **실행 중인 프로그램**이다. 프로그램을 실행하면 OS로부터 실행에 필요한 자원인 메모리를 할당받아 프로세스가 된다.

프로세스는 프로그램을 수행하는 데 필요한 데이터와 메모리 등의 자원 및 쓰레드로 구성된다. 프로세스의 자원을 이용해서 실제로 작업을 수행하는 것이 쓰레드이다.

모든 프로세스에는 최소한 하나 이상의 쓰레드가 존재하며, 둘 이상의 쓰레드를 가진 프로세스를 멀티쓰레드 프로세스라고 한다.

프로세스가 가질 수 있는 쓰레드의 개수는 제한되어 있지 않으나, 쓰레드가 작업을 수행하는데 개별적인 메모리 공간(호출 스택)을 필요로 하기 때문에 프로세스의 메모리 한계에 따라 생성할 수 있는 쓰레드의 수가 결정된다.

### Multi-Tasking vs Multi-Threading

OS는 멀티 태스킹을 지원하기 때문에, 여러 개의 프로세스가 동시에 실행될 수 있다.

멀티 쓰레딩은 하나의 프로세스 내에서 여러 쓰레드가 동시에 작업을 수행하는 것이다. CPU의 코어는 한 번에 단 하나의 작업만 수행할 수 있으므로, 실제로 동시에 처리되는 작업의 개수는 코어의 개수와 일치한다. 그러나 쓰레드를 통해 코어가 아주 짧은 시간 동안 여러 작업을 번갈아 가며 수행함으로써, 여러 작업들이 모두 동시에 수행되는 것처럼 보이게 된다.

프로세스의 성능은 단순히 쓰레드의 개수에 비례하지 않는다.

### Multi-Threading 장단점

* 장점
  * CPU의 사용률을 향상시킨다.
  * 자원을 보다 효율적으로 사용할 수 있다.
  * 사용자에 대한 응답성이 향상된다.
  * 작업이 분리되어 코드가 간결해진다.

여러 사용자에게 서비스를 해주는 서버 프로그램의 경우, 싱글 쓰레드라면 사용자의 요청 마다 새로운 프로세스를 생성해야 한다. 프로세스를 생성하는 것은 쓰레드를 생성하는 것에 비해 더 많은 시간 및 메모리 공간이 필요하기 때문에, 많은 수의 사용자 요청을 서비스하기 어렵다.

그러나 멀티 쓰레딩은 여러 쓰레드가 같은 프로세스 내에서 자원을 공유하기 때문에, 동기화 및 교착상태 등의 사이드 이펙트가 발생할 수 있다.

<br>

## 2. Thread 구현 및 실행

Thread 클래스를 상속받거나, Runnable 인터페이스를 구현한다. Runnable 인터페이스를 구현하는 방법이 재사용성이 높다. 두 방식은 Thread 인스턴스 생성 방법에 있어서 차이가 있다.

> Main.java

```java
public class Main {

    public static void main(String[] args) {
        MyThread myThread = new MyThread(); //Thread 클래스 상속
        myThread.start();

        Runnable runnable = new MyThread2();
        Thread myThread2 = new Thread(runnable);
        myThread2.start();
    }
}
```

Thread 클래스는 내부적으로 Runnable 인터페이스 참조변수를 가지고 있다. ``start()`` 메서드를 통해 쓰레드를 실행할 수 있다.

쓰레드를 통해 수행할 작업 내용은 다음과 같이 정의할 수 있다.
  * Thread 클래스를 상속받을 경우, ``run()``을 오버라이드하여 작업 내용을 정의한다.
  * Runnable 인터페이스를 구현하는 경우, ``run()`` 추상 메서드를 구현하여 작업 내용을 정의한다.

상속보다는 Runnable 인터페이스 구현을 통해 Thread에 ``run()``을 주입하는 방식이 보다 객체지향적인 방법이다.

생성자 및 setter 등을 통해 쓰레드의 이름을 지정할 수 있다.

한 번 실행된 쓰레드는 다시 실행될 수 없기 때문에, 작업을 한 번 더 수행해야 한다면 새로운 쓰레드 객체를 생성해야 한다. 또한 ``start()``가 호출되더라도 바로 실행되지 않고 실행 대기 상태에 머물다가, OS 스케쥴링에 의거하여 자신의 차례가 되었을 때 비로소 실행된다.

<br>

## 3. start() 및 run()

``start()`` 메서드는 새로운 쓰레드가 작업을 실행하는데 필요한 Call Stack을 생성한 다음, 생성된 호출 스택에 ``run()`` 메서드가 첫 번째로 올라가게 한다. 쓰레드가 종료되면 작업에 사용된 호출 스택은 소멸된다.

쓰레드는 작성된 OS 스케쥴에 따라 순서를 부여받고 작업을 수행하기 때문에, 쓰레드의 호출 스택에서 가장 위에 위치한 메서드 ``run()``은 실행 중이 아닌 대기 상태에 놓일 수 있다.

main 메서드 역시 main 쓰레드 위에서 작동되는 메서드이다. 실행 중인 사용자 쓰레드(User Thread)가 하나도 없을 때, 프로그램은 종료된다.

<br>

## 4. Single Thread vs Multi-Thread

쓰레드가 1개이든 2개이든, 작업 소요 시간은 거의 비슷하다. 오히려 멀티 쓰레드는 쓰레드 간의 작업 전환(context switching) 및 대기 시간으로 인해 시간이 더 소요된다.

싱글 코어인 경우 멀티 쓰레드라도 하나의 코어가 번갈아가면서 작업을 수행하기 때문에, 두 작업이 절대 겹치지 않는다. 그러나 멀티 코어의 경우 동시에 두 쓰레드가 수행될 수 있기 때문에, 자원을 놓고 경쟁하게 된다.

두 쓰레드가 서로 다른 자원을 사용하는 작업의 경우에는 싱글 쓰레드 프로세스보다 멀티 쓰레드 프로세스가 효율적이다.

Java가 OS 독립적이라고 하지만, 실제로 쓰레드는 OS에 종속적인 부분이다. JVM의 쓰레드 스케줄러에 의해서 어떤 쓰레드가 얼마동안 실행될 것인지 결정되는 것 처럼, Java 프로그램(프로세스) 또한 OS 프로세스 스케줄러에 영향을 받는다.

<br>

## 5. Thread 우선 순위

쓰레드는 우선 순위에 해당되는 속성을 가지고 있으며, 해당 값에 따라 쓰레드가 얻는 실행 시간이 달라진다. 쓰레드의 우선 순위는 쓰레드를 생성한 쓰레드로부터 자동 상속받는다. setter를 통해 인위적으로 우선 순위를 지정할 수 있으며, 숫자가 높을 수록 우선 순위가 높아진다.

그러나 이는 싱글 코어의 경우에만 보장된다. 멀티 코어의 환경에서는 OS마다 다른 방식으로 스케쥴링하기 때문에, 쓰레드 실행 결과가 OS마다 상이할 수 있다. 오히려 쓰레드의 우선 순위에 따른 차이가 없을 수 있다.

쓰레드에 우선 순위를 부여하는 대신 작업에 우선 순위를 두어 PriorityQueue에 저장해 놓고, 우선 순위가 높은 작업이 먼저 처리되도록 하는 것이 나을 수 있다.

<br>

## 6. ThreadGroup

서로 관련된 쓰레드를 그룹으로 다루기 위한 것이다. 쓰레드 그룹에 다른 쓰레드 그룹을 포함할 수 있다.

보안상의 이유로 도입된 개념이다. 자신이 속한 쓰레드 그룹이나 하위 쓰레드 그룹은 변경할 수 있지만, 다른 쓰레드 그룹의 쓰레드를 변경할 수는 없다.

쓰레드를 쓰레드 그룹에 포함시키려면 Thread 생성자를 이용한다.

```java
Thread(ThreadGroup threadGroup, String name)
//중략
```

모든 쓰레드는 반드시 쓰레드 그룹에 포함되어 있어야 하며, 쓰레드 그룹을 지정하지 않은 쓰레드는 기본적으로 자신을 생성한 쓰레드와 같은 그룹에 속하게 된다.

Java 어플리케이션이 실행되면 JVM은 main과 system이라는 쓰레드 그룹을 생성하고, JVM 운영에 필요한 쓰레드들을 생성해서 이 그룹에 포함시킨다. (예 : GC가 동작하는 Finalizer 쓰레드 등)

우리가 생성하는 쓰레드의 그룹을 지정하지 않으면 자동으로 main 쓰레드 그룹에 속하게 된다.

> Main.java

```java
public class Main {

    public static void main(String[] args) {
        ThreadGroup main = Thread.currentThread().getThreadGroup();
        ThreadGroup group1 = new ThreadGroup("group1");
        ThreadGroup group2 = new ThreadGroup("group2");
        ThreadGroup subGroup = new ThreadGroup(group1, "subgroup");

        group1.setMaxPriority(3);

        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                try {
                    Thread.sleep(1000L);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };

        new Thread(group1, runnable, "th1").start();
        new Thread(subGroup, runnable, "th2").start();
        new Thread(group2, runnable, "th3").start();

        System.out.println("Thread Name " + main.getName());
        System.out.println("Active Thread Group Counts" + main.activeGroupCount());
        System.out.println("Active Thread Counts" + main.activeCount());
        main.list();
    }
}
```

``list()`` 메서드를 통해 해당 쓰레드 그룹의 세부 정보를 출력한다.

<br>

## 7. Daemon Thread

데몬 쓰레드는 일반 쓰레드의 작업을 돕는 보조적인 역할을 수행하는 쓰레드이다. 일반 쓰레드가 모두 종료되면 데몬 쓰레드는 자동 종료된다.

데몬 쓰레드의 예로는 가비지 컬렉터, 워드 프로세서의 자동 저장, 화면 자동 갱신 등이 있다.

데몬 쓰레드는 무한 루프와 조건문을 이용해서 실행 후 대기하고 있다가, 특정 조건이 만족되면 작업을 수행하고 다시 대기되도록 작성한다.

데몬 쓰레드의 작성 및 생성 방법은 일반 쓰레드와 다르지 않으며, ``void setDaemon(boolean on)``을 호출하면 된다. 또한 데몬 쓰레드가 생성한 쓰레드는 자동적으로 데몬 쓰레드가 된다.

<br>

## 8. Thread 실행 제어

![image](https://user-images.githubusercontent.com/56240505/72275798-fbaf5e80-3671-11ea-9b9d-faae4e9bc5f9.png)<br><br> ![image](https://user-images.githubusercontent.com/56240505/72275824-0d910180-3672-11ea-9687-6d3141486a65.png)<br><br> ![image](https://user-images.githubusercontent.com/56240505/72275858-21d4fe80-3672-11ea-9b1a-fd44a822c9cf.png)<br><br>

쓰레드는 주어진 실행 시간이 다 되거나 ``yield()``를 만나면 다시 실행 대기 상태가 되고, 다음 차례의 쓰레드가 실행상태가 된다.

실행을 모두 마치거나 ``stop()``이 호출되면 쓰레드는 소멸된다.

``stop()``은 쓰레드를 종료시키고, ``suspend()``는 실행 상태의 쓰레드를 중지시키고, ``resume()``은 정지 상태의 쓰레드를 다시 실행 대기 상태로 만든다. 그러나 세 메서드들은 교착 상태 이슈로 deprecated 되었다.

### sleep(long millis)

나노 세컨드까지 세밀하게 값을 지정할 수 있다. 일시정지 상태가 된 쓰레드는 지정된 시간이 다 되거나 ``interrupt()``가 호출되면 InterruptedException이 발생하며 잠에서 깨어나 실행대기 상태가 된다.

```java
void delay(long millis) {
    try {
      Thread.sleep(millis);
    } catch (InterruptedException e) {
    }
}
```

항상 try-catch를 사용하는 것 보다 별도의 메서드를 사용하기도 한다.

``sleep()``은 항상 현재 실행 중인 쓰레드에 작동하기 때문에, 참조변수 호출이 아닌 static 호출을 사용하자.

### interrupt() 및 interrupted()

``interrupt()``는 쓰레드의 ``boolean interrupted``의 상태를 false에서 true로 변경한다. 쓰레드를 강제로 종료시키지는 못한다.

``interrupted()``는 쓰레드의 ``boolean interrupted``의 상태를 반환한 후, false로 변경한다.

쓰레드가 일시정지 상태(WAITING)일 때, 해당 쓰레드에 대해 ``interrupt()``를 호출하면, InterruptedException이 발생하고 쓰레드는 실행대기 상태(RUNNABLE)로 바뀐다.

``sleep()``가 종료되고 InterruptedException이 발생할 때, 쓰레드의 interrupted의 상태는 자동으로 false로 초기화된다. 따라서, ``interrupt()``를 호출하더라도 쓰레드가 ``sleep()``을 사용하고 있다면 ``isInterrupted()``가 true가 아닌 false가 나올 수 있다. catch block에서 ``interrupt()``를 다시 호출해주어야 원하는 결과가 나온다.



<br>


쓰레드의 동기화
---------------

-	멀티 쓰레드의 경우 자원을 공유하면서 원하지 않는 결과를 얻을 수 있다.<br><br>
-	공유 데이터를 사용하는 코드 영역을 임계 영역으로 지정하고, 공유 데이터가 가지고 있는 lock을 획득한 단 하나의 쓰레드만 코드를 수행할 수 있게 한다.<br><br>
-	한 쓰레드가 진행 중인 작업을 다른 쓰레드가 간섭하지 못하도록 막는 것을 쓰레드의 동기화라고 한다.<br><br>

synchronized
------------

```Java
public synchronized void calcSum() //메서드 전체를 임계 영역 지정
synchronized(객체 참조변수){
  ...
} //특정 영역을 임계 영역 지정
```

-	참조변수는 락을 걸고자 하는 객체를 참조하는 것이어야 한다.<br><br>

wait() & notify() & notifyAll()
-------------------------------

-	특정 쓰레드가 객체의 락을 가진 상태로 오랜 시간을 보내지 않도록 하는 것도 중요하다.<br><br>
-	임계 영역의 코드를 수행하다가 작업을 더 이상 진행할 상황이 아니면 `wait()` 을 호출하여 쓰레드가 락을 반납하고 기다리게 한다.<br><br>
-	나중에 작업을 진행할 수 있는 상황이 되면 `notify()` 를 호출하여 다시 락을 얻어 작업을 진행시킨다.<br><br>
-	Object 클래스에 정의되어 있으며, 동기화 블록 내에서만 사용이 가능하다.<br><br>
-	waiting pool은 객체마다 존재하기 때문에, 해당 메서드들은 호출된 객체의 waiting pool에만 관여한다.<br><br>

기아 현상과 경쟁 상태
---------------------

-	운이 나쁜 일부 쓰레드는 계속 통지를 받지 못하고 오랫동안 기다리게 된다.<br><br>
-	여러 쓰레드가 lock을 받기 위해 경쟁하는 것을 경쟁 상태라고 한다.<br><br>

Lock과 Condition을 이용한 동기화
--------------------------------

-	java.util.concurrent.locks 패키지가 제공하는 lock 클래스들을 이용하여 동기화할 수 있다.<br><br>
-	이를 통해 기아 현상 및 경쟁 상태를 해소할 수 있다.<br><br>

volatile
--------

```java
volatile boolean stopped = false;
volatile long tmp;
volatile double tmp2;
```

-	CPU 코어는 메모리에서 읽어온 값을 캐시에 저장하고, 캐시에서 값을 읽어 작업한다.<br><br>
-	같은 값을 읽어올 때는 먼저 캐시에 있는지 확인하고, 없을 때만 메모리에서 읽는다.<br><br>
-	메모리에 저장된 변수의 값이 변경되었는데도, 캐시에 저장된 값이 갱신되지 않아서 메모리에 저장된 값r과 다른 경우가 발생한다.<br><br>
-	volatile 키워드를 사용하면 코어가 변수의 값을 읽어올 때 캐시가 아닌 메모리에서 읽어오기 때문에 불일치가 해소된다.<br><br>
-	이는 synchronized 블럭의 사용과 효과가 동일하다.<br><br>
-	JVM은 데이터를 4 byte 단위로 처리하며, 4 byte 이하의 데이터는 단 하나의 명령어로 읽거나 쓰기나 가능하다.<br><br>
-	이 때문에 하나의 명령어는 최소의 작업 단위이며, 작업 중간에 다른 쓰레드가 끼어들 틈이 없다.<br><br>
-	8 byte의 자료형은 하나의 명령어로 값을 읽거나 쓸 수 없어, 다른 쓰레드가 끼어들 여지가 있다.<br><br>
-	따라서 volatile을 선언하여 해당 변수에 대한 읽기나 쓰기를 원자화시킨다.<br><br>
-	상수는 변하지 않는 값이므로 멀티 쓰레드에 안전하기 때문에 volatile을 붙이지 않는다.<br><br>
-	volatile은 원자화만 할 뿐, 동기화하는 것이 아니기 때문에 동기화에는 synchronized를 사용한다.<br><br>

fork & join 프레임웍
--------------------

-	프레임웍을 통해 하나의 작업을 작은 단위로 나눠서, 여러 쓰레드가 동시에 처리하는 것을 쉽게 만들어준다.<br><br>

---

Reference
---------

-	Java의 정석 (남궁성 저) Chapter 13
