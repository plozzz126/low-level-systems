# 🖥️ Systems Programming Roadmap

> Полный путь от нуля до глубокого понимания: **C · Assembler · Linux · Сети · Архитектура ПК**

Этот репозиторий — наш общий учебный план. Каждый изучает материал в своём темпе, но идём по одной дороге. Задавайте вопросы в Issues, делитесь решениями в Pull Requests.

---

## 📋 Содержание

- [Как пользоваться репозиторием](#как-пользоваться-репозиторием)
- [Этап 1 — Фундамент](#этап-1--фундамент-месяцы-1–3)
- [Этап 2 — Углубление](#этап-2--углубление-месяцы-4–6)
- [Этап 3 — Ядро и ОС](#этап-3--ядро-и-ос-месяцы-7–9)
- [Этап 4 — Сети](#этап-4--сети-месяцы-10–12)
- [Этап 5 — Интеграция](#этап-5--интеграция-месяцы-13–18)
- [Главное правило](#-главное-правило)
- [Ресурсы](#-ресурсы)
- [Инструменты](#-инструменты)
- [Как контрибьютить](#-как-контрибьютить)

---

## Как пользоваться репозиторием

```
/etap-1/
  /c/          ← задания и примеры по C
  /asm/        ← задания по Assembler
  /arch/       ← материалы по архитектуре
/etap-2/
  ...
/projects/     ← итоговые проекты
/notes/        ← конспекты 
```

- **Issues** → вопросы, непонятные темы, баги в коде
- **Pull Requests** → добавляйте конспекты, решения задач, примеры
- **Discussions** → обсуждение книг и концепций

Золотое правило при написании кода: **сначала напиши на C, потом посмотри что получилось в asm.**

```bash
gcc -O0 -g hello.c -o hello   # компилируй
objdump -d hello               # смотри asm
gdb hello                      # отлаживай
```

---

## Этап 1 — Фундамент (месяцы 1–3)

Три потока идут параллельно с первого дня. Не жди, пока «дойдёшь» до Assembler — начинай всё сразу.

### 🔵 Язык C

**Цель:** уверенно писать программы, понимать модель памяти.

**Темы:**
- Базовые типы: `int`, `char`, `float`, `double`, размеры в байтах
- Условия, циклы, функции
- Массивы и строки (нуль-терминатор!)
- Указатели — базовый уровень: `*`, `&`, арифметика указателей
- Компиляция вручную: `gcc`, флаги `-Wall -Wextra -g`

**Книги:**
- [The C Programming Language](https://en.wikipedia.org/wiki/The_C_Programming_Language) — Kernighan & Ritchie (K&R). Читать от начала до конца.
- Первые 3 главы CS:APP (Bryant & O'Hallaron) — параллельно

**Задание этапа:**
```c
// Напишите функцию, которая меняет два числа местами без временной переменной.
// Потом посмотрите в objdump что сгенерировал компилятор.
void swap(int *a, int *b);
```

---

### 🔴 Assembler (x86-64, NASM)

**Цель:** читать дизассемблированный код, понимать как CPU выполняет инструкции.

**Темы:**
- Регистры общего назначения: `rax`, `rbx`, `rcx`, `rdx`, `rsi`, `rdi`, `rsp`, `rbp`
- Инструкции: `mov`, `add`, `sub`, `mul`, `div`, `cmp`, `jmp`, `je`, `jne`
- Стек: `push`, `pop`, `call`, `ret`
- Работа с памятью: `[rax]`, `[rax + 8]`, `lea`
- Соглашение вызовов System V AMD64 ABI (как C вызывает функции)

**Книги и ресурсы:**
- [Programming from the Ground Up](https://savannah.nongnu.org/projects/pgubook/) — бесплатно, PDF
- [godbolt.org](https://godbolt.org) — пишешь C слева, видишь asm справа в реальном времени

**Задание этапа:**
```nasm
; Напишите на NASM функцию суммирования массива целых чисел.
; Вызовите её из C через extern.
section .text
global array_sum
array_sum:
    ; ваш код здесь
```

---

### 🟢 Архитектура ПК

**Цель:** понять что происходит внутри компьютера на уровне железа.

**Темы:**
- Структура CPU: ALU, регистры, счётчик команд (PC/IP), декодер инструкций
- Тактовый генератор, конвейер инструкций
- Шины: адресная, данных, управления
- Иерархия памяти: регистры → L1/L2/L3 кэш → RAM → диск
- Как происходит обращение к памяти (цикл fetch-decode-execute)

**Книга:**
- Computer Organization and Design — Patterson & Hennessy (по 1 главе в неделю, параллельно с остальным)

**Практика:**
```bash
# Посмотри размеры кэшей своего CPU
lscpu | grep -i cache
cat /sys/devices/system/cpu/cpu0/cache/index*/size
```

---

## Этап 2 — Углубление (месяцы 4–6)

### 🔵 C углублённо

**Темы:**
- Динамическая память: `malloc`, `calloc`, `realloc`, `free`
- Утечки памяти и как их находить (`valgrind`)
- Указатели на функции: `int (*fn)(int, int)`
- Структуры `struct`, объединения `union`, выравнивание
- Работа с файлами: `fopen`, `fread`, `fwrite`, `fclose`
- Препроцессор: `#define`, `#include`, `#ifdef`
- Системные вызовы напрямую: `write(1, "hello\n", 6)`

**Книга:** Expert C Programming — Peter van der Linden

**Задание этапа:**
```c
// Реализуйте свой malloc на основе sbrk().
// Простая версия: список свободных блоков, first-fit алгоритм.
void *my_malloc(size_t size);
void  my_free(void *ptr);
```

---

### 🔴 Asm + компилятор изнутри

**Темы:**
- Inline assembler в C: `asm volatile("..." : output : input : clobbers)`
- Как компилятор оптимизирует код: флаги `-O0`, `-O1`, `-O2`, `-O3`
- Разбор объектных файлов: `objdump -d`, `readelf`, `nm`
- Отладка на уровне инструкций в GDB: `si`, `ni`, `info registers`, `x/16xb $rsp`
- Стековый фрейм: пролог и эпилог функции, `rbp`-relative адресация

**Инструменты:**
```bash
gcc -O0 -g program.c -o program   # без оптимизации, с отладочной инфо
gdb ./program
(gdb) disassemble main             # дизассемблировать функцию
(gdb) x/16xb $rsp                  # посмотреть стек побайтово
objdump -d -M intel program        # дизассемблировать в Intel-синтаксисе
```

---

### 🟢 Linux — основы

**Цель:** чувствовать себя уверенно в командной строке Linux.

**Темы:**
- Файловая система: `/proc`, `/sys`, `/dev`, `/etc` — что где лежит и зачем
- Права доступа: `chmod`, `chown`, владелец/группа/остальные, `setuid`
- Процессы: `ps aux`, `top`, `htop`, `kill`, `nice`, `renice`
- Bash-скрипты: переменные, циклы, условия, функции, `$?`, `$!`, `$$`
- Перенаправление и пайпы: `>`, `>>`, `<`, `|`, `2>&1`
- `strace` — системные вызовы процесса в реальном времени

**Практика:**
```bash
strace ls 2>&1 | head -30          # что ls делает под капотом
cat /proc/$$/maps                  # карта памяти текущего bash
ls -la /proc/1/fd                  # файловые дескрипторы init
```

**Установка:** используй Debian, Ubuntu или Arch Linux без GUI. Это важно — учишься работать с системой напрямую.

---

## Этап 3 — Ядро и ОС (месяцы 7–9)

### 🔵 Системное программирование на C

**Темы:**
- POSIX API: `fork()`, `exec()`, `wait()`, `waitpid()`
- Межпроцессное взаимодействие (IPC): пайпы `pipe()`, очереди сообщений, общая память `mmap()`
- Сигналы: `signal()`, `sigaction()`, `kill()`, `SIGCHLD`, `SIGSEGV`
- Потоки: `pthread_create()`, `pthread_join()`, мьютексы, семафоры
- Файловые дескрипторы: `open()`, `read()`, `write()`, `close()`, `dup2()`

**Книга:** Advanced Programming in the UNIX Environment — W. Richard Stevens (APUE)

**Задание этапа:**
```c
// Реализуйте простой shell: читает команды, fork()+exec() для запуска,
// поддерживает пайпы (cmd1 | cmd2) и перенаправление (> файл).
int main() {
    char line[1024];
    while (fgets(line, sizeof(line), stdin)) {
        // ваш код
    }
}
```

---

### 🔴 Ядро Linux

**Темы:**
- Архитектура ядра: монолитное ядро, пространство ядра vs пространство пользователя
- Таблица системных вызовов: как `write()` в C превращается в инструкцию `syscall`
- Планировщик CFS (Completely Fair Scheduler): очереди выполнения, `nice`, приоритеты
- Виртуальная файловая система VFS: как ядро абстрагирует ext4, tmpfs, procfs
- Написание kernel module

**Книга:** Linux Kernel Development — Robert Love

**Практика — первый kernel module:**
```c
#include <linux/module.h>
#include <linux/kernel.h>

static int __init hello_init(void) {
    printk(KERN_INFO "Hello from kernel!\n");
    return 0;
}
static void __exit hello_exit(void) {
    printk(KERN_INFO "Goodbye from kernel!\n");
}
module_init(hello_init);
module_exit(hello_exit);
MODULE_LICENSE("GPL");
```
```bash
make && sudo insmod hello.ko && dmesg | tail -3
sudo rmmod hello && dmesg | tail -3
```

---

### 🟢 Управление памятью и виртуализация

**Темы:**
- MMU (Memory Management Unit): как виртуальный адрес переводится в физический
- Страничная адресация: таблицы страниц, TLB, page fault
- Swap: когда и как ядро выгружает страницы на диск
- Huge Pages: зачем нужны страницы по 2MB
- `/proc/<pid>/maps` — карта памяти процесса

**Практика:**
```bash
cat /proc/self/maps                     # карта памяти текущего процесса
vmstat 1 5                              # статистика виртуальной памяти
sudo cat /proc/kpagecount | head        # счётчики физических страниц
```

---

## Этап 4 — Сети (месяцы 10–12)

### 🔵 Теория сетей

**Темы:**
- Модель OSI: 7 уровней, что происходит на каждом
- Стек TCP/IP: Ethernet → IP → TCP/UDP → HTTP
- IP-адресация: IPv4, IPv6, маски, CIDR, NAT
- TCP: трёхстороннее рукопожатие (SYN/SYN-ACK/ACK), управление потоком, надёжность
- UDP: когда и почему выбирают вместо TCP
- DNS: как `google.com` превращается в IP-адрес
- HTTP/1.1: методы, заголовки, статус-коды

**Книга:** TCP/IP Illustrated, Volume 1 — W. Richard Stevens (читать медленно, это библия)

**Практика:**
```bash
# Посмотри трёхстороннее рукопожатие TCP своими глазами
sudo tcpdump -n 'host google.com and tcp' &
curl https://google.com > /dev/null
```

---

### 🔴 Сети — программирование на C

**Темы:**
- BSD Socket API: `socket()`, `bind()`, `listen()`, `accept()`, `connect()`
- Работа с адресами: `struct sockaddr_in`, `htons()`, `inet_pton()`
- Блокирующий vs неблокирующий I/O: `select()`, `poll()`, `epoll()`
- Raw sockets: работа с пакетами напрямую

**Задание этапа:**
```c
// Напишите HTTP-сервер на чистом C.
// Минимум: принимает GET-запросы, отдаёт статические файлы.
// Продвинутый уровень: поддержка нескольких клиентов через fork() или epoll().
int main() {
    int server_fd = socket(AF_INET, SOCK_STREAM, 0);
    // ваш код
}
```

---

### 🟢 Linux + администрирование сети

**Темы:**
- Сетевые интерфейсы: `ip link`, `ip addr`, `ip route`
- Диагностика: `ss`, `netstat`, `ping`, `traceroute`, `nmap`
- Захват трафика: `tcpdump`, `wireshark`
- Брандмауэр: `iptables` (цепочки INPUT/OUTPUT/FORWARD, правила, NAT)
- Настройка `nftables` как современная альтернатива
- VLAN и bridging руками

**Практика:**
```bash
ss -tulpn                               # открытые порты и сокеты
sudo iptables -L -v -n                  # текущие правила firewall
sudo tcpdump -i any -n port 80          # захват HTTP трафика
ip route show                           # таблица маршрутизации
```

---

## Этап 5 — Интеграция (месяцы 13–18)

Финальный этап — выбери один (или все) из проектов. Это синтез всего изученного.

---

### 🏗️ Проект A: Мини-операционная система

Напиши минимальную ОС с нуля: загрузчик → ядро → прерывания → вывод текста.

**Ресурсы:**
- [OSDev Wiki](https://wiki.osdev.org/) — главный ресурс, там есть всё
- Writing a Simple Operating System from Scratch — Nick Blundell (PDF, бесплатно)
- [xv6](https://github.com/mit-pdos/xv6-riscv) — учебная ОС от MIT, читай и изучай код

**Минимальный чеклист:**
- [ ] Загрузчик GRUB загружает ядро
- [ ] Ядро выводит текст на VGA
- [ ] Настроены прерывания (IDT)
- [ ] Есть простой планировщик (round-robin)
- [ ] Ядро умеет делать `syscall`

---

### 🌐 Проект B: Свой TCP/IP стек

Реализуй минимальный сетевой стек поверх raw sockets в Linux.

**Что реализовать:**
- [ ] Парсинг Ethernet-фрейма
- [ ] IP-пакет: заголовок, checksum
- [ ] ICMP (ответ на ping)
- [ ] UDP-сокет
- [ ] Базовый TCP: handshake, передача данных, teardown

---

### 🔍 Проект C: Reverse Engineering

Научись читать чужой код без исходников.

**Инструменты:**
```bash
# Ghidra — декомпилятор от NSA (бесплатно)
# https://ghidra-sre.org/

# radare2 — консольный RE-инструмент
r2 -A ./binary
[0x00401000]> pdf @ main      # дизассемблировать main

# GDB + pwndbg плагин
pip install pwntools
```

**Практика:**
- Реши 10+ задач на [picoCTF](https://picoctf.org/) категории «reverse», «binary»
- Попробуй [pwn.college](https://pwn.college/) — структурированный курс по бинарной безопасности

**Темы безопасности:**
- Buffer overflow: как происходит, почему опасно
- Stack canary, ASLR, NX-бит — как защищает ОС
- Return-Oriented Programming (ROP)
- SELinux и AppArmor: мандатный контроль доступа

---

## ⭐ Главное правило

> **Никогда не изучай одну тему в изоляции.**

| Написал на C | → | Посмотри в `objdump` |
|---|---|---|
| Понял системный вызов | → | Найди его в исходниках ядра |
| Узнал про TCP | → | Напиши сокет |
| Сделал программу | → | Посмотри `strace` что она делает |

Связи между слоями — это и есть настоящее понимание.

---

## 📚 Ресурсы

### Книги (обязательные)

| Тема | Книга | Приоритет |
|------|-------|-----------|
| C | The C Programming Language — K&R | ⭐⭐⭐ обязательно |
| C | CS:APP — Bryant & O'Hallaron | ⭐⭐⭐ обязательно |
| Assembler | Programming from the Ground Up | ⭐⭐⭐ обязательно |
| Архитектура | Computer Organization and Design — Patterson | ⭐⭐ важно |
| C системное | Advanced Programming in the UNIX Environment — Stevens | ⭐⭐⭐ обязательно |
| Linux ядро | Linux Kernel Development — Robert Love | ⭐⭐⭐ обязательно |
| Сети | TCP/IP Illustrated Vol. 1 — Stevens | ⭐⭐⭐ обязательно |
| C углублённо | Expert C Programming — Van der Linden | ⭐⭐ важно |

### Онлайн-ресурсы

| Ресурс | Для чего |
|--------|----------|
| [godbolt.org](https://godbolt.org) | C → asm в браузере, незаменимо |
| [wiki.osdev.org](https://wiki.osdev.org) | Написание ОС с нуля |
| [man7.org](https://man7.org/linux/man-pages/) | Man-страницы Linux |
| [linux-insides](https://github.com/0xAX/linux-insides) | Ядро Linux изнутри, на русском |
| [picoCTF](https://picoctf.org) | CTF-задачи: reverse, pwn, сети |
| [pwn.college](https://pwn.college) | Курс по бинарной безопасности |
| [nasm.us](https://nasm.us) | Документация NASM |

---

## 🛠️ Инструменты

### Обязательная установка

```bash
# Debian / Ubuntu
sudo apt update && sudo apt install -y \
    build-essential gcc gdb nasm \
    valgrind strace ltrace \
    binutils-dev \
    linux-headers-$(uname -r) \
    tcpdump wireshark-common \
    net-tools iproute2 \
    git curl wget python3

# Проверка
gcc --version
nasm --version
gdb --version
```

### Полезные GDB команды

```
run                   — запустить программу
break main            — точка останова на main
next / ni             — следующая строка / инструкция
step / si             — зайти в функцию / инструкцию
continue              — продолжить до следующего breakpoint
info registers        — показать все регистры
x/16xb $rsp           — 16 байт стека в hex
disassemble main      — дизассемблировать функцию
print *ptr            — разыменовать указатель
backtrace             — стек вызовов
```

### Полезные системные команды

```bash
objdump -d -M intel ./prog         # дизассемблировать в Intel-синтаксисе
readelf -a ./prog                  # все секции ELF-файла
nm ./prog                          # таблица символов
strace ./prog                      # системные вызовы
ltrace ./prog                      # вызовы библиотечных функций
valgrind --leak-check=full ./prog  # утечки памяти
size ./prog                        # размеры секций
file ./prog                        # тип файла
ldd ./prog                         # зависимые библиотеки
```

---

## 🤝 Как контрибьютить

1. **Добавь конспект** → создай файл в `/notes/ваше-имя/тема.md`
2. **Добавь решение задачи** → в соответствующую папку этапа
3. **Нашёл ошибку или непонятное место** → открой Issue
4. **Хочешь обсудить тему** → открой Discussion

```bash
git clone https://github.com/ВАШ_НИК/systems-roadmap
git checkout -b feature/my-notes
# добавь файлы
git add .
git commit -m "add: конспект по указателям (этап 1)"
git push origin feature/my-notes
# → открой Pull Request
```

---

## 📈 Прогресс

Отмечай выполненные этапы в своём форке — создай файл `progress/ваше-имя.md`:

```markdown
# Прогресс — Имя

## Этап 1
- [x] K&R главы 1-4
- [x] Первая программа на NASM
- [ ] Задание: swap без временной переменной
- [ ] Patterson & Hennessy глава 1

## Этап 2
...
```

---

*Удачи. Это долгий путь, но каждый слой понимания делает следующий проще.*
