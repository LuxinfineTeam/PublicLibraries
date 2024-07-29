# Очень важная информация:
Наши моды/фиксы могут быть НЕ совместимы с кастомными модами, особенно, содержащими хуки/ASM/перебор рефлексии...
Например, GTNH форки модов, retrofuturabootstrap (аналог LaunchWrapper от GTNH), TMI Integration,
кастомные разработки от других авторов и т.д.

Также, сюда относятся обычные моды, которые "в корень" переписаны/изменены сигнатуры открытых методов/полей/классов,
в т.ч. кастомные ядра майнкрафта (с HardEngine наблюдались проблемы вроде), кастомные билды клиента.

Также зафиксирована ошибка `Caused by: java.lang.SecurityException: class "net.minecraftforge.common.ForgeChunkManager"'s signer information does not match signer information of other classes in the same package`,
возникает, если в сборке есть мод hodgepodge либо archaicfix

В случае возникновения проблем совместимости с этими модами - помощи с нашей стороны *НЕ БУДЕТ!!!*

Для повышения совместимости рекомендуется использовать AEHooksAPI и LuxinfineHelper.

Описания нет, но оно обязательно будет, с примерами реализации апишек и прочем. Ожидайте.