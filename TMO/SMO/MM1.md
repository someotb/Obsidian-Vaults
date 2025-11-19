# Система массового обслуживания M/M/1

## Описание
Система с:
- **M** — экспоненциальный поток [[Пусоновское прибытие |Пуасоновский поток]] поступления заявок
- **M** — [[Экспоненциальное распределение|Экспоненциальное время]] обслуживания
- **1** — один канал обслуживания

**Параметры:**
- $\lambda$ — интенсивность поступления заявок
- $\mu$ — интенсивность обслуживания
- $p = \lambda / \mu$ — коэффициент загрузки системы

Система стационарна, если $\lambda < \mu$.

## Основные формулы

- Среднее число заявок в очереди: $\large N_q = \frac{p^2}{1 - p}$
- Среднее число заявок в системе: $\large N = \frac{p}{1 - p}$
- Среднее время ожидания в очереди: $\large W = N \cdot \text{avg\_service\_time}$
- Среднее время нахождения заявки в системе: $\large T = \frac{\text{avg\_service\_time}}{1 - p}$

## MATLAB код

```matlab
lambda = 8; 
u = 15; 
p = lambda / u; 
N = 1000; 

MM1_vn = exprnd(1/u, 1, N);
MM1_tn = exprnd(1/lambda, 1, N);

MM1_params = MM1_param(MM1_vn, p);

fprintf("Avg queue len(N_q): %f\nAvg tasks in system(N): %f\nAvg waiting time(W): %f\nAvg time in system(T): %f\n", ...
        MM1_params.N_q, MM1_params.N, MM1_params.W, MM1_params.T);

MM1_vn = sort(cumsum(exprnd(1/u, 1, N)));
MM1_tn = sort(cumsum(exprnd(1/lambda, 1, N)));

figure;
plot(MM1_tn, 0:1:length(MM1_tn)-1);
hold on;
plot(MM1_vn, 0:1:length(MM1_vn)-1);
title("Зависимость числа пришедших/обслуженных заявок от времени");
xlabel("Время,с");
ylabel("Кол-во заявок,шт");
legend("Пришедшие заявки", "Обслуженные заявки");
grid on;
hold off;
