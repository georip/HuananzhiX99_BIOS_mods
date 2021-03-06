#### Рекомендуется прошивать биос с помощью S3TurboTool
*(за данную утилиту и новый драйвер анлока особая благодарность ser8989)*

#### Инструкция по разблокировке макс.частоты на все ядра, а не на два (в народе более известно как "анлок")
Не стоит бояться того, что тут много текста. На самом деле всё несложно, я лишь попытался ничего не упустить и быть с вами шаг за шагом.

За основу берём биос с открытым меню "CPU C State Control", например любой из "...phants_vxxx".
1. Запускаем S3TurboTool
2. Нажимаем MMTool5
3. В появившейся утилите "MMTool Aptio" нажимаем "Load Image" и выбираем необходимый биос
4. Переходим на вкладку "CPU Patch", выделяем микрокод "6F 06F2", отмечаем "Delete a Patch Data", нажимаем Apply и соглашаемся
5. Нажимаем "Save Image" и закрываем "MMTool Aptio"
6. В S3TurboTool нажимаем AMIBCP5
7. В появившейся утилите AMIBCP открываем биос
8. Раскрываем список и идём по пути "Common RefCode Configuration > IntelRCSetup > Advanced Power Management Configuration > CPU C State Control"
9. Справа, в столбце Optimal, двойным кликом меняем значение параметров:
    * "Package C State limit" на "C2 state"
    * "CPU C3 report" на "Enable"
    * "CPU C6 report" на "Disable"
10. Закрываем окно AMIBCP и соглашаемся на сохранение внесённых изменений
11. В S3TurboTool нажимаем "Собрать драйвер"
12. Настраиваем необходимые значения отрицательных смещений напряжения. Также выбираем, нужен ли дополнительный сигнал при включении и выводе системы из сна. Нажимаем "Собрать драйвер".
13. В S3TurboTool нажимаем UEFITool
14. В появившейся утилите UEFITool открываем биос
15. Раскрываем список и идём по пути "Intel image > BIOS region > 8C8CE578-...(самый нижний) >"
16. Примерно среди первых 20 значений находим 271DD6F2-...
17. Правый клик по нему, выбираем "Replace as is...", выбираем собранный ранее драйвер (находится в папке S3TurboHack)
18. Выбираем "File > Save image file...", сохраняем
#### На этом биос готов к прошивке

При возникновении трудностей, а также если у Вас есть замечания и пожелания, обращайтесь в Telegram-группу https://t.me/chinese_lga2011_3_x99

#### Также можете посмотреть видео-инструкцию *(спасибо Zerg_fb)*:

[![](https://i.ytimg.com/vi/2hfhdrIrXR4/mqdefault.jpg)](https://youtu.be/2hfhdrIrXR4)

#### Часто задаваемые вопросы:
(Вопрос:) Я привык использовать v3_payne_xx_xx.ffs или V3_MOF_xxxxxx.ffs, вшил по инструкции и получил кирпич. Почему?

(Ответ:) Потому что эти типы драйверов устанавливаются в другую секцию биоса. Рекомендуется собирать свой драйвер через S3TurboTool.

(Вопрос:) В чём преимущество нового драйвера от S3TurboTool?

(Ответ:) Была решена проблема, при которой анлок сбрасывался при выводе системы из режима сна. Также было уменьшено энергопотребление процессора без нагрузки.

(Вопрос:) Кот, расскажи, какие ещё есть возможности у нового драйвера от S3TurboTool?

(Ответ:) Можно снизить напряжение на процессоре без использования анлока, для этого не выполняем шаги 2-10 инструкции и на 12 шаге снимаем галку "Разблокировка турбо".

#### Подбор оптимальных значений смещения напряжений на вашем процессоре (в народе более известно как "андервольтинг")
Для чего и кому это нужно? У каждой модели процессора есть значение TDP, это максимальная его мощность в ваттах. В некоторых задачах происходит достижение этого предела, и процессор начинает уменьшать частоту на ядрах шагами по 100МГц, чтобы не перейти за эту грань. Мощность, это напряжение, умноженное на ток. Понижая напряжение, достигается улучшение энергоэффективности процессора, т.е. при той же нагрузке снижается потребление энергии и как следствие тепловыделение. Соответственно, при той же граничной нагрузке, процессор сможет более уверенно удерживать частоту.

Возникает вопрос, почему Intel сама не улучшила характеристику процессора понижением напряжения, если это возможно. Дело в том, что при серийном производстве есть некоторый разброс параметров, и чтобы минимизировать брак напряжения определяются с некоторым запасом. У процессоров, которые подлежат разгону, этот излишек напряжения уходил на тот самый разгонный потенциал. Если разгон недоступен или не нужен, рекомендуется снижать напряжение.

Затронем два блока в наших процессорах, влияющих на его потребление, это Core (ядра) и Cache (кэш). Напряжение понижается методом его смещения (offset) на определённую величину. Т.е. во всех режимах работы процессора, напряжение будет меньше на заданное нами значение. Крайне желательно произвести поиск значений на системе без анлока макс.частоты.
Для изменения смещения, используем программу Intel XTU, QuickCPU или ThrottleStop. Далее будет рассмотрено на примере последней.
Перед дальнейшими действиями, сохраните важные данные в вашей системе, будьте готовы к возможным зависаниям или синему экрану.
1. После запуска ThrottleStop нажимаем FIVR
2. В блоке "FIVR Control" отмечено "CPU Core", значит меняем смещение на ядрах
3. В блоке "CPU Core Voltage" выбираем "Unlock Adjustable Voltage" и понижаем "Offset Voltage", например до -100mV, и нажимаем Apply
4. Если система зависла или мы увидели синий экран, значит такое смещение нам точно не подходит, перезагружаемся и пробуем -95mV и т.д.
5. Если всё вроде бы стабильно, значит запускаем программу OCCT, выставляем режим теста "OCCT/большой набор данных/число потоков авто/набор инструкций SSE"
6. Перед запуском теста, заботимся о должном охлаждении процессора, области врм и оперативной памяти (она может троттлить без доп.охлаждения)
7. Запускаем тест. Если система зависла или мы увидели синий экран (обычно ошибка CLOCK_WATCHDOG_TIMEOUT), то уменьшаем наше смещение и продолжаем дальше тестировать, до появления стабильности (30 минут теста достаточно). Это и будет нашим значением для ядер.
Далее делаем всё тоже самое, только для кэша. Только тестируем в режиме Linpack (при нестабильности синий экран, обычно ошибка WHEA_UNCORRECTABLE_ERROR).
