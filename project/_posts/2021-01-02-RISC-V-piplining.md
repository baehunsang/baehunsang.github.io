---
layout: post
title: RISC-V CPU
subtitle: Pipeline 구현
gh-repo: baehunsang/COSE222
gh-badge: [star, fork, follow]
# cover-img: /assets/img/path.jpg
tags: [RISC-V, Verilog, Computer architecture]
comments: true
---
Pipelining 을 구현한 RISC-V CPU입니다. 
`data hazard`, `control hazard`를 해결하였습니다.

* `branch`의 destination은 `decoding` stage에서 결정됩니다.
* [WebRiscv](https://github.com/Mariotti94/WebRISC-V)를 참조했습니다.
