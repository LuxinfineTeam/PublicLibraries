Описания нет, но оно обязательно будет, с примерами реализации апишек и прочем. Ожидайте.

**Изменения LuxinfineHelper:**
---
v1.1
- Фиксы табкомлитов аргументов, содержащих пробел и требующих экранирования
- Возможность задать принудительно дроп мобам в конфиге, который будет использоваться в API EntityUtils#getDrop
- Парочка дополнительных методов создания аргументов игрока в DefaultCommandArguments
- Удаление излишних подробностей при выводе ошибки обработки аргументов команды, когда подробная трассировка ошибок аргументов отключена конфигом
- Фикс проверок прав при обработке targetPlayer() из DefaultCommandArguments

v1.2
- Дополнительные методы в EnergyType классе
- Отказ от клампа энергии до int типа данных при выводе информации в тултипах

v1.3
- Фиксы опечаток в коде при обработке WorldUtils#harvestBlock

v1.4
- Возможность вызывать TileMachineBase#readFromNBT и TileEnergyHandler#readFromNBT на клиенте

v1.5
- Дополнительные доработки табкомплита команд
- Добавление команды /version <jar_name|Luxinfine|all> - дампит modid, версию и дату сборки указанного .jar / всех модов от LuxinfineTeam / всех модов

v1.6
- Отказ от использования Unsafe#ensureClassInitialized в целях совместимости с java под MacOS

**Изменения LuxinfineAEHooks:**
---
v5.7
- Фикс компоновки ингредиентов в кастомных шаблонах. Технически правка влияет лишь на более компактное древо автокрафта
