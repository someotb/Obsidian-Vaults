# Система массового обслуживания M/G/1

## Описание
Система с:
- **M** — экспоненциальный поток ([[Пусоновское прибытие |Пуасоновский поток]]) поступления заявок
- **G** — произвольное распределение времени обслуживания
- **1** — один канал обслуживания

**Параметры:**
- $\large \lambda$ — интенсивность поступления
- $\large t_s$ — произвольное распределение обслуживания
- $\large p = \lambda \cdot \text{avg\_service\_time}$ — коэффициент загрузки
- $\large c = \frac{\text{Var}(t_s)}{(\text{avg\_service\_time})^2}$ — коэффициент вариации

## Основные формулы

- Среднее число заявок в очереди: $\large N_q = \frac{p^2 (1 + c)}{2(1 - p)}$
- Среднее число заявок в системе: $\large N = N_q + p$
- Среднее время ожидания: $\large W = \frac{p \cdot \text{avg\_service\_time} (1 + c)}{2(1-p)}$
- Среднее время в системе: $\large T = \text{avg\_service\_time} + W$

## MATLAB код

```matlab
MG1_vn = exprnd(1/u, 1, N); 
c = var(MG1_vn) / mean(MG1_vn)^2;

MG1_params = MG1_param(MG1_vn, c, p);

fprintf("M/G/1:\nAvg queue len(N_q): %f\nAvg tasks in system(N): %f\nAvg waiting time(W): %f\nAvg time in system(T): %f\n", ...
        MG1_params.N_q, MG1_params.N, MG1_params.W, MG1_params.T);

MG1_vn = sort(cumsum(MG1_vn));
MG1_tn = sort(cumsum(exprnd(1/lambda, 1, N)));

figure;
plot(MG1_tn, 0:1:length(MG1_tn)-1);
hold on;
plot(MG1_vn, 0:1:length(MG1_vn)-1);
title("M/G/1: Зависимость числа пришедших/обслуженных заявок от времени");
xlabel("Время,с");
ylabel("Кол-во заявок,шт");
legend("Пришедшие заявки", "Обслуженные заявки");
grid on;
hold off;
