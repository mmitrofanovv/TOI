# TOI
Лабораторная работа №1
[Лабораторная работа 1 - Моделирование случайных величин1.pdf](https://github.com/user-attachments/files/17598079/1.-.1.pdf)

Лабораторная работа №2

[Лаб 2 - Распознавание образов ГСВ1.pdf](https://github.com/user-attachments/files/17598083/2.-.1.pdf)

![Снимок экрана 2024-11-01 120709](https://github.com/user-attachments/assets/5386811d-818d-4787-bffa-385f8bbc339c)
![Снимок экрана 2024-11-01 120722](https://github.com/user-attachments/assets/15ad2abd-66f7-4768-aaa7-5033e9e933df)
![Снимок экрана 2024-11-01 120730](https://github.com/user-attachments/assets/8760d061-e412-4cd6-bbf6-40dc62b8dec8)
![Снимок экрана 2024-11-01 120742](https://github.com/user-attachments/assets/84e81108-14c6-4c7b-b6b8-f4e13abbc6b4)

Лабораторная работа №3
[Лаб 3 - Распознавание образов ГСВ с разными ков. мат..pdf](https://github.com/user-attachments/files/17598099/3.-.pdf)

Чтобы построить график зависимости экспериментальной ошибки второго рода для первого класса от величины дисперсии классов и сравнить с теоретическим значением, нужно внести изменения в код. Мы будем изменять дисперсию классов (ковариационные матрицы), фиксируя другие параметры, и вычислять экспериментальные и теоретические ошибки второго рода. Далее мы построим график, отображающий эту зависимость.

Шаги для решения задачи
Изменить дисперсию классов — будем изменять элементы ковариационной матрицы 
𝐶
C по диагонали, поскольку дисперсия связана с диагональными элементами матрицы.
Вычислять экспериментальную ошибку второго рода — для каждого значения дисперсии выполним цикл тестирования (как в основном коде).
Вычислять теоретическую ошибку второго рода — с помощью функции normcdf по матрице 
𝑃
𝐼
𝐽
PIJ.
Построить график зависимости — отображаем экспериментальные и теоретические значения ошибки второго рода в зависимости от величины дисперсии.
Пример кода с добавлением цикла для дисперсий и построения графика
Добавьте следующий код в конец вашего скрипта:

matlab
Копировать код
% Параметры для графика
disp_range = 1:1:10;  % Диапазон значений дисперсии для исследования
exp_error_second_kind = zeros(size(disp_range));
theor_error_second_kind = zeros(size(disp_range));

% Цикл для изменения дисперсии и вычисления ошибок
for d = disp_range
    % Установка новой дисперсии в ковариационных матрицах классов
    C(:,:,1) = d * eye(n);   % Увеличиваем дисперсию класса 1
    C(:,:,2) = d * eye(n);   % Увеличиваем дисперсию класса 2

    % Пересчёт обратных матриц ковариаций
    for k = 1:M
        C_(:,:,k) = inv(C(:,:,k));
    end

    % Расчёт теоретической матрицы вероятностей ошибок
    for i = 1:M
        for j = i + 1:M
            dmij = m(:, i) - m(:, j);
            l0_(i, j) = log(pw(j) / pw(i));
            mg1 = 0.5 * (trace(C_(:, :, j) * C(:, :, i) - eye(n)) + dmij' * C_(:, :, i) * dmij - log(det(C(:,:,i)) / det(C(:,:,j))));
            Dg1 = 0.5 * trace((C_(:,:,j) * C(:,:,i) - eye(n))^2) + dmij' * C_(:,:,j) * C(:,:,i) * C_(:,:,j) * dmij;
            sD1 = sqrt(Dg1);
            PIJ(i, j) = normcdf(l0_(i, j), mg1, sD1);  % Вероятность ошибки второго рода для класса 1
        end
    end

    % Сохраняем теоретическое значение ошибки второго рода для первого класса
    theor_error_second_kind(d) = PIJ(1, 2);

    % Тестирование для экспериментальной матрицы вероятностей ошибок
    Pc_ = zeros(M);
    for k = 1:K
        for i = 1:M
            [x, px] = randncor(n, 1, C(:, :, i)); x = x + m(:, i);
            for j = 1:M
                u(j) = -0.5 * (x - m(:, j))' * C_(:, :, j) * (x - m(:, j)) - 0.5 * log(det(C(:, :, j))) + log(pw(j));
            end
            [ui, iai] = max(u);
            Pc_(i, iai) = Pc_(i, iai) + 1;
        end
    end
    Pc_ = Pc_ / K;

    % Сохраняем экспериментальное значение ошибки второго рода для первого класса
    exp_error_second_kind(d) = Pc_(1, 2);
end

% Построение графика
figure;
plot(disp_range, exp_error_second_kind, 'ro-', 'LineWidth', 2); hold on;
plot(disp_range, theor_error_second_kind, 'bo--', 'LineWidth', 2);
xlabel('Величина дисперсии классов');
ylabel('Ошибка второго рода для первого класса');
legend('Экспериментальная ошибка второго рода', 'Теоретическая ошибка второго рода');
title('Зависимость ошибки второго рода от величины дисперсии классов');
grid on;
Объяснение кода:
Цикл for d = disp_range — проходит по различным значениям дисперсии, увеличивая её на каждой итерации.
Обновление ковариационных матриц C(:,:,1) и C(:,:,2) — изменяем диагональные элементы для увеличения дисперсии.
Пересчёт теоретической ошибки второго рода с обновлёнными параметрами и сохранение её значения.
Экспериментальное тестирование ошибок: для каждой дисперсии вычисляется и сохраняется экспериментальная ошибка второго рода для первого класса.
Построение графика: по оси X — значение дисперсии, по оси Y — ошибки второго рода, экспериментальная и теоретическая.
Ожидаемый результат
График покажет, как изменяется вероятность ошибочного распознавания (ошибка второго рода) для первого класса в зависимости от дисперсии классов.


[Lab_4_-_Raspoznavanie_obrazov_opisyvaemykh_binarnymi_priznakami.pdf](https://github.com/user-attachments/files/17598152/Lab_4_-_Raspoznavanie_obrazov_opisyvaemykh_binarnymi_priznakami.pdf)









