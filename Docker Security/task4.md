Description: In this scenario you will learn how to apply Seccomp to reduce the attack surface of containers.
Seccomp - один из механизмов безопасности ядра Linux, который даёт возможность огранить набор доступных системных вызовов для приложений.

Step 1 of 2


cat 1_chmod.json



docker run --rm -it \
  --security-opt seccomp:1_chmod.json \
  benhall/strace \
  chmod 400 /etc/hostname (контейнер пытался выполнить cat, но вызов не произойдёт, так как seccomp его заблокировал)


Step 2 -Identify Syscalls
Суть в том, что для каждой операционной системы, системные вызовы вызываются по разному. Поэтому если у тебя запрет на chmod в ubuntu, то есть вероятность что на FreeBsd например твой запрет тоже сработает
