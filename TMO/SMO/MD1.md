# Система массового обслуживания M/D/1

## Описание
Система с:
- **M** — экспоненциальный поток ([[Пусоновское прибытие |Пуасоновский поток]]) поступления заявок
- **D** — детерминированное время($const$) обслуживания
- **1** — один канал обслуживания

**Параметры:**
- $\lambda$ — интенсивность поступления
- $t_s = 1/\mu$ — фиксированное время обслуживания
- $p = \lambda / \mu$ — коэффициент загрузки

## Основные формулы

- Среднее число заявок в очереди: $\large N_q = \frac{p^2}{2(1-p)}$
- Среднее число заявок в системе: $\large N = N_q + p$
- Среднее время ожидания: $\large W = \frac{p \cdot t_s}{2(1-p)}$
- Среднее время в системе: $\large T = t_s + W$

## MATLAB код

```matlab
MD1_vn = ones(1, N) * (1/u);
MD1_tn = exprnd(1/lambda, 1, N);

MD1_params = MD1_param(mean(MD1_vn), p);

fprintf("M/D/1:\nAvg queue len(N_q): %f\nAvg tasks in system(N): %f\nAvg waiting time(W): %f\nAvg time in system(T): %f\n", ...
        MD1_params.N_q, MD1_params.N, MD1_params.W, MD1_params.T);

MD1_vn = sort(cumsum(MD1_vn));
MD1_tn = sort(cumsum(MD1_tn));

figure;
plot(MD1_tn, 0:1:length(MD1_tn)-1);
hold on;
plot(MD1_vn, 0:1:length(MD1_vn)-1);
title("M/D/1: Зависимость числа пришедших/обслуженных заявок от времени");
xlabel("Время,с");
ylabel("Кол-во заявок,шт");
legend("Пришедшие заявки", "Обслуженные заявки");
grid on;
hold off;
