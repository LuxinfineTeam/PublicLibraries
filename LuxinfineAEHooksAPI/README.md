**Изменения LuxinfineAEHooks:**
---
v5.7
- Фикс компоновки ингредиентов в кастомных шаблонах. Технически правка влияет лишь на более компактное древо автокрафта

v5.8
- Повышение производительности при использовании с Luxinfine AE2 (реализация опциональных API)

v5.9
- Фикс совместимости с некоторыми устаревшими версиями AE2 от LuxinfineTeam

v5.10
- Фикс параллельной работы крафтеров на основе этого API (раньше работал только один из механизмов в сети)

v5.11
- Фикс совместимости с GTNH

v5.12
- Фикс краша Class not found appeng.api.util.IInterfaceViewable (API GTNH) при использовании на обычном AE2
- Подпись билдов

v5.13
- Дополнительные методы в IExtendedInventoryCrafting и AEConverters
- Фикс bad class "file: *** illegal start of class file" при попытке компиляции мода с обычным Ae2 в зависимостях (не GTNH)

v5.14
- Небольшие оптимизации redirectPushPattern

v5.15
- Небольшие оптимизации IO операций в SingleMEStorage

v5.16
- Учет EXTRACT/INJECT пермишенов терминала безопасности при работе SingeMEStorage / CustomMEStorage

v5.17
- Механизм защиты от зацикливания из сети в саму себя для SingeMEStorage / CustomMEStorage как в оригинальном AE2

v5.18
- Фикс совместимости с GTNH версией WirelessCrafingTerminal мода

v5.19
- Фикс неработоспособности порта ввода-вывода для кастомных ячеек на основе AEHooksAPI при отсутствии Botania в сборке