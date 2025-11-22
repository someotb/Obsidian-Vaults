## 1. Введение

В Linux существует несколько способов организовать ожидание событий с таймаутом и обработкой сигналов. Важно уметь выбирать подходящий метод для конкретной задачи:

- Ожидание на файловых дескрипторах ([[epoll, poll, ppoll|poll, ppoll, epoll]])
- Межпоточная синхронизация (`pthread_cond_timedwait`)
- Межпроцессное взаимодействие (`POSIX MQ`)
- Таймеры (`timerfd`, `clock_nanosleep`)

---

## 2. `ppoll` и безопасная обработка сигналов

### Сценарий

- Основной поток блокирует сигнал `SIGUSR1`.
- Создаётся поток-отправитель, который через некоторое время посылает `SIGUSR1`.
- Основной поток вызывает `ppoll()`, передавая маску сигналов, где `SIGUSR1` разаблокирован.
- `ppoll()` атомарно разблокирует сигнал и начинает ожидание на файловом дескрипторе.
- При приходе сигнала `ppoll` прерывается (`EINTR`) и вызывается обработчик.

### Почему `ppoll` критичен

Использовать обычный `poll` или `read` с сигналами опасно: может возникнуть race condition, когда сигнал приходит между проверкой состояния и блокирующим вызовом. `ppoll` решает это атомарно.

---

### Пример кода: `timeout_ppoll.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#include <pthread.h>
#include <poll.h>
#include <unistd.h>
#include <string.h>
#include <errno.h>
#include <time.h>

static volatile sig_atomic_t sigusr1_received = 0;

static void sigusr1_handler(int signo) {
    (void)signo;
    sigusr1_received = 1;
    const char msg[] = "Signal handler executed!\n";
    write(STDOUT_FILENO, msg, sizeof(msg)-1);
}
static void *sender_thread(void *arg) {
    pthread_t *target = (pthread_t *)arg;
    sleep(1);
    pthread_kill(*target, SIGUSR1);
    return NULL;
}

int main(void) {
    setvbuf(stdout, NULL, _IOLBF, 0);

    struct sigaction sa = {0};
    sa.sa_handler = sigusr1_handler;
    sigaction(SIGUSR1, &sa, NULL);

    sigset_t block_mask, original_mask;
    sigemptyset(&block_mask);
    sigaddset(&block_mask, SIGUSR1);
    pthread_sigmask(SIG_BLOCK, &block_mask, &original_mask);

    pthread_t main_thread = pthread_self();

    pthread_t tid;
    pthread_create(&tid, NULL, sender_thread, &main_thread);

    int fds[2];
    pipe(fds);
    struct pollfd pfd = {.fd = fds[0], .events = POLLIN};

    sigset_t unblock_mask = original_mask;
    sigdelset(&unblock_mask, SIGUSR1);

    struct timespec timeout = {.tv_sec = 5, .tv_nsec = 0};
    int rc = ppoll(&pfd, 1, &timeout, &unblock_mask);

    if (rc == -1 && errno == EINTR) {
        printf("ppoll interrupted by signal as expected.\n");
    }

    if (sigusr1_received) {
        printf("Signal handler executed successfully.\n");
    }

    pthread_join(tid, NULL);
    close(fds[0]); close(fds[1]);
    return 0;
}
```

## 3. Важные моменты

- `TIMER_ABSTIME` нужен для стабильного периодического сна, чтобы избежать дрейфа времени.
- Обработчик сигнала должен использовать **только async-signal-safe функции** (например, `write`).
- `ppoll` + атомарная маска = безопасная обработка сигналов без race condition.

---

## 4. Рекомендации

- Для **event-loop** и таймеров: `timerfd + epoll_pwait` или `ppoll`.
- Для **межпоточной синхронизации**: `pthread_cond_timedwait`.
- Для **межпроцессного обмена**: POSIX MQ.
- Для **низкоуровневых периодических задач**: `clock_nanosleep(TIMER_ABSTIME)`.

---

## 5. Схема работы `ppoll` с сигналом

```text
Блокировка SIGUSR1
       |
       v
  Основной поток
       |
       +--> вызов ppoll(fd, timeout, unblock_mask)
                 |
                 | атомарно разблокирует SIGUSR1
                 v
         Ожидание FD / сигналов
                 |
        -------------------------
        |                       |
       сигнал приходит           timeout истёк
        |                       |
   ppoll прерывается EINTR       ppoll возвращает 0
   вызывается обработчик         fd готов/timeout

