# Luxinfine Hooks API Docs

## Оглавление

1. [Классы-контейнеры и @HooksContainer](#1-классы-контейнеры-для-хуков-и-hookscontainer)  
2. [Параметры @HooksContainer](#2-параметры-hookscontainer)  
   - [targetClass / targetClassRef](#targetclass--targetclassref)  
   - [inherit](#inherit)  
   - [requiredInterfaces / requiredInterfacesRef](#requiredinterfacesrequiredinterfacesref)  
   - [requiredAnnotations / requiredAnnotationsRef](#requiredannotationsrequiredannotationsref)  
   - [interfacesToInject / interfacesToInjectRef](#interfacestoinjectinterfacestoinjectref)  
3. [Примеры классов хуков](#3-примеры-классов-хуков)  
4. [Методы-хуки и аннотация @Inject](#4-методы-хуки-и-аннотация-inject)  
5. [Параметры @Inject](#5-параметры-inject)  
   - [target](#target)  
   - [targetInfo](#targetinfo)  
   - [methodName](#methodname)  
   - [methodNameSRG](#methodnamesrg)  
   - [methodDesc](#methoddesc)  
   - [createMethodIfAbsent](#createmethodifabsent)  
   - [createLocalVarsCount](#createlocalvarscount)  
   - [fuzzy](#fuzzy)  
   - [mandatory](#mandatory)  
   - [requiredMods / blacklistedMods](#requiredmodsblacklistedmods)  
   - [conditionCallback](#conditioncallback)  
   - [priority](#priority)  
6. [Примеры методов с инжектами](#6-примеры-разных-метод-с-инжектами)  
   - [InjectTarget.HEAD](#injecttargethead)  
   - [InjectTarget.THROW](#injecttargetthrow)  
   - [InjectTarget.RETURN](#injecttargetreturn)  
   - [InjectTarget.REPLACE_INVOKE](#injecttargetreplace_invoke)  
   - [InjectTarget.FIELD_GET / FIELD_SET](#injecttargetfield_get--injecttargetfield_set)  
   - [InjectTarget.BEFORE_INVOKE / AFTER_INVOKE](#injecttargetbefore_invoke--injecttargetafter_invoke)  
   - [InjectTarget.CONSTANT_LOAD](#injecttargetconstant_load)

---

##  1. Классы-контейнеры для хуков и @HooksContainer
Для создания хука требуется создать класс с аннотацией @HooksContainer. Хук будет зарегистрирован автоматически.

## 2. Параметры @HooksContainer

#### targetClass / targetClassRef
Имя целевого класса в формате java/lang/Object или сам класс Object.class. Аргумент обязателен.
#### inherit
Применять ли хуки ко всем наследникам класса (true) или только к самому классу (false).
#### requiredInterfaces/requiredInterfacesRef
Интерфейсы, которые должен реализовывать класс для применения хука.
#### requiredAnnotations/requiredAnnotationsRef
Аннотации, которые должны присутствовать на классе для применения хука.
#### interfacesToInject/interfacesToInjectRef
Интерфейсы, которые будут добавлены в implements целевого класса. Методы интерфейса вы должны реализовать сами через хуки или они должны быть default, иначе будут ошибки.

## 3. Примеры классов хуков:

```java
@HooksContainer(targetClassRef = Minecraft.class)
public class MinecraftHook {
    // Пример класса хука с параметром targetClassRef
}

@HooksContainer(targetClass = "net/minecraft/client/Minecraft")
public class MinecraftHook {
    // Пример класса хука с параметром targetClass
}
```

## 4. Методы-хуки и аннотация @Inject

Помимо класса с аннотацией @HooksContainer в нем требуется задать методы с аннотацией @Inject. Все методы хуков, над которыми вешается аннотация @Inject, должны иметь модификаторы public static. В конец метода по желанию можно добавить аргумент IHookContext для различных манипуляций.

## 5. Параметры @Inject

#### target
Тип инжекта. Может принимать значения:

1. InjectTarget.HEAD <br>(Инжект в начало метода.  Если инжект в конструктор - то он будет выполнен только после вызова this() / super() конструктора, однако в нестандартных случаях байткода могут произойти ошибки! По этому по возможности не рекомендуется применять HEAD для конструктора. targetInfo должен быть пустым. Метод должен быть void. Выполнить досрочный выход из целевого метода можно с помощью IHookContext#exit)

2. InjectTarget.THROW <br>(Инжект перед выбросом исключения. targetInfo должен быть пустым. IHookContext#getRedirectedValue будет возвращать само исключение. Метод должен быть void. Отменить выкидывание ошибки, заменив на возврат можно с помощью IHookContext#exit. Подменяет только реальный throw внутри метода, куда нацелен хук)

3. InjectTarget.RETURN <br>(Инжект в конец метода перед return. targetInfo должен быть пустым или содержать тег InjectTarget#THROWABLE_SAFETY_TAG. IHookContext#getRedirectedValue будет возвращать ожидаемый RETURN. Метод должен быть void. Изменить возвращаемый тип можно с помощью IHookContext#exit. Если требуется гарантия, что хук будет вызван даже в случае возникновения исключения в пределах кода целевого метода - можно указать InjectTarget#THROWABLE_SAFETY_TAG. В этом случае, при возникновении исключения - IHookContext#getRedirectedValue вернёт перехваченное возникшее исключение, а IHookContext#caughtException будет возвращать true. Также, в этом случае, если в хук-методе не будет вызван IHookContext#exit - будет выброшено перехваченное исключение сразу после вызова хук-метода, иначе - метод будет завершён с указанным RETURN значением, игнорируя перехваченное исключение)

4. InjectTarget.REPLACE_INVOKE <br>(Инжект, заменяющий вызов указанного метода на другой метод.  Инжект, заменяющий вызов указанного метода/конструктора на указанный метод. Первые аргументы указанного метода должны совпадать с теми, что передаются в заменяемый метод, а также метод должен возвращать конечный результат, если заменяемый метод не был void. Если заменяемый метод не статичен - то в указанном методе должен быть 0 аргумент, способный принять ссылку на инстанс. targetInfo должен содержать имя класса, где находится метод, который требуется заменить, а также само имя метода и его дескриптор. Имя класса для замены не всегда совпадает с именем, где располагается сам метод!!! Чаще всего, это класс, от которого идёт доступ к методу. ASMPlugin в IDEA покажет точное имя класса-owner`а. Пример targetInfo: ["net/minecraft/item/Item", "func_77617_a", "(I)Lnet/minecraft/util/IIcon;"])

5. InjectTarget.FIELD_GET <br>(Инжект после получения значения поля класса. Первый аргумент метода должен принять значение, которое будет получено из поля. Если поле не статичное - то должен быть ещё 0 аргумент для принятия ссылки на инстанс поля. Инжект-метод должен обязательно возвращать конечное значение, которое получит дальнейшая часть кода после получения значения поля. Имя класса для замены не всегда совпадает с именем, где располагается сам метод!!! Чаще всего, это класс, от которого идёт доступ к полю. ASMPlugin в IDEA покажет точное имя класса-owner`а. Пример для targetInfo: ["net/minecraft/item/ItemStack", "stackSize", "I"])

6. InjectTarget.FIELD_SET <br>(Инжект перед установкой значения поля класса. Первый аргумент метода должен принять значение, которое должно было быть присвоено полю. Если поле не статичное - то должен быть ещё 0 аргумент для принятия ссылки на инстанс поля. Инжект-метод должен обязательно возвращать конечное значение поля. Имя класса для замены не всегда совпадает с именем, где располагается сам метод!!! Чаще всего, это класс, от которого идёт доступ к полю. ASMPlugin в IDEA покажет точное имя класса-owner`а. Пример для targetInfo: ["net/minecraft/item/ItemStack", "stackSize", "I"])

7. InjectTarget.BEFORE_INVOKE <br>(Инжект перед вызовом указанного метода. Первые аргументы указанного метода должны совпадать с теми, что передаются в заменяемый метод, а также метод быть void. Если метод, перед которым будет вставлен инжект, не статичен - то в методе-инжекторе должен быть 0 аргумент, способный принять ссылку на инстанс. targetInfo должен содержать имя класса, где находится метод, который требуется заменить, а также само имя метода и его дескриптор. Имя класса для замены не всегда совпадает с именем, где располагается сам метод!!! Чаще всего, это класс, от которого идёт доступ к методу. ASMPlugin в IDEA покажет точное имя класса-owner`а. Пример targetInfo: ["net/minecraft/item/Item", "func_77617_a", "(I)Lnet/minecraft/util/IIcon;"])

8. InjectTarget.AFTER_INVOKE <br>(Инжект после вызова указанного метода. Первые аргументы указанного метода должны совпадать с теми,что передаются в целевой метод, а также хук-метод должен возвращать конечное значение вызова метода. Если изменений не требуется - можно вернуть IHookContext#getRedirectedValue. Если метод, после которого будет вставлен инжект, не статичен - то в методе-инжекторе должен быть 0 аргумент, способный принять ссылку на инстанс. targetInfo должен содержать имя класса, где находится метод, который требуется заменить, а также само имя метода и его дескриптор. Имя класса для замены не всегда совпадает с именем, где располагается сам метод!!! Чаще всего, это класс, от которого идёт доступ к методу. ASMPlugin в IDEA покажет точное имя класса-owner`а. IHookContext#getRedirectedValue возвращает результат вызова целевого метода (если он не void). Пример targetInfo: ["net/minecraft/item/Item", "func_77617_a", "(I)Lnet/minecraft/util/IIcon;"]. Если требуется гарантия, что хук будет вызван даже в случае возникновения исключения в пределах кода целевого метода - можно указать InjectTarget#THROWABLE_SAFETY_TAG. В этом случае, при возникновении исключения - IHookContext#getRedirectedValue() вернёт перехваченное возникшее исключение, а IHookContext#caughtException() будет возвращать true. Также, в этом случае, если в хук-методе не будет вызван IHookContext#exit(Object} - будет выброшено перехваченное исключение сразу после вызова хук-метода, иначе - метод будет завершён с указанным RETURN значением, игнорируя перехваченное исключение.

9. InjectTarget.CONSTANT_LOAD <br>(Инжект после загрузки константы. Инжект-метод должен обязательно возвращать конечное значение, которое получит дальнейшая часть кода после загрузки константы. В аргументы хука значение целевой константы не передаётся. B targetInfo() нужно указать 2 парамера: Тип константы. Допустимые значения: InjectTarget#CLASS_TYPE, InjectTarget#STRING_TYPE, InjectTarget#NUMBER_TYPE, InjectTarget#DOUBLE_NUMBER_TYPE.Значение константы. InjectTarget#CLASS_TYPE - имя класса, для InjectTarget#NUMBER_TYPE - целое число, для InjectTarget#DOUBLE_NUMBER_TYPE - дробное число Тип данных char приравнивается к InjectTarget#NUMBER_TYPE в соответствии с кастом char int, boolean приравнивается к InjectTarget#NUMBER_TYPE в виде 1 и 0.
Примеры для targetInfo(): [InjectTarget.CLASS_TYPE, "java.lang.Class"], [InjectTarget.NUMBER_TYPE, "10"], [InjectTarget.STRING_TYPE, "100000"])

#### targetInfo
Дополнительная информация для инжекта, например класс/метод/дескриптор для REPLACE_INVOKE.
#### methodName
Имя метода для инжекта в DEV-сборке. <init> для конструкторов, <clinit> для статической инициализации.
#### methodNameSRG
SRG имя метода для инжекта (например func_73876_c).
#### methodDesc
Дескриптор метода (например (IILjava/lang/Object;)V).
#### createMethodIfAbsent
Генерировать метод, если его нет.
#### createLocalVarsCount
Сколько слотов под кастомные локальные переменные выделить.
#### fuzzy
Если true, инжект будет применён в любой метод целевого класса.
#### mandatory
Если true, отсутствие возможности инжекта приведёт к падению игры.
#### requiredMods/blacklistedMods
Список модов для обязательного/запрещённого применения инжекта.
#### conditionCallback
Колбек для проверки необходимости инжекта.
#### priority
Приоритет выполнения инжекта (InjectPriority).

## 6. Примеры разных метод с инжектами:

### InjectTarget.HEAD

```java
@HooksContainer(targetClassRef = Minecraft.class)
public class MinecraftHeadHook {

    // Хук в не статический метод. Первым аргументом передается инстанс класса, дальше идут аргументы оригинального метода по порядку.
    @Inject(target = InjectTarget.HEAD)
    public static void refreshResources(Minecraft instance, IHookContext context) {

        // Хук в верх метода Minecraft#refreshResources
        // Тут можно выполнить свой код, например
        System.out.println("[HEAD] Minecraft#refreshResources called!");

        // Если вызвать код ниже, то оригинальный код из метода не будет выполнен никогда.
        context.exit(null);
    }

    // Хук в статический метод. В метод передаются аргументы оригинального метода по порядку. На данном примере метод не принимает никаких аргументов.
    @Inject(target = InjectTarget.HEAD)
    public static void isGuiEnabled(IHookContext context) {

        // Хук в верх метода Minecraft#isGuiEnabled
        // Тут можно выполнить свой код, например
        System.out.println("[HEAD] Minecraft#isGuiEnabled called!");

        // Если вызвать код ниже, то оригинальный код из метода не будет выполнен никогда
        // и метод всегда будет возвращать false
        context.exit(false);
    }
}
```

### InjectTarget.THROW

```
//todo
```

### InjectTarget.RETURN

```java
@HooksContainer(targetClassRef = Minecraft.class)
public class MinecraftReturnHook {

    // Хук в статический метод. В метод передаются аргументы оригинального метода по порядку. На данном примере метод не принимает никаких аргументов.
    @Inject(target = InjectTarget.RETURN)
    public static void isGuiEnabled(IHookContext context) {

        // IHookContext#getRedirectedValue возвращает значение, которое вернул оригинальный метод.
        boolean originalReturn = (boolean) context.getRedirectedValue();

        // Тут можно выполнить свой код, например вывести значение из оригинального метода
        System.out.println("[RETURN] Original return: " + originalReturn);

        // Если вызвать код ниже, то оригинальный метод всегда будет возвращать true
        context.exit(true);
    }

    // Хук в не статический метод. В метод сначала передается инстанс класса, а потом аргументы оригинального метода по порядку.
    @Inject(target = InjectTarget.RETURN)
    public static void loadWorld(Minecraft instance, WorldClient p_71403_1_) {

        // Хук в конец метода Minecraft#loadWorld
        // Тут можно выполнить свой код, например
        System.out.println("[RETURN] Minecraft#loadWorld called!");

        // В данном случае IHookContext ничего не даст, оригинальный метод void, следовательно нельзя подменить значение
        // Также это хук в конец, отмена дальнейшего кода не имеет смысла, его просто уже нет
    }
}
```

### InjectTarget.REPLACE_INVOKE

```java
@HooksContainer(targetClassRef = Minecraft.class)
public class MinecraftReplaceInvokeHook {

    // Метод имеет тип void, потому что Minecraft#checkGLError типа void.
    // Minecraft instanceForCheckGLError - инстанс класса откуда идет вызов Minecraft#checkGLError, тк метод не static
    // String p_71361_1_ - аргумент метода Minecraft#checkGLError
    // Minecraft instanceForRunGameLoop - инстанс класса откуда идет вызов Minecraft#runGameLoop, тк метод не static
    @Inject(
        target = InjectTarget.REPLACE_INVOKE,
        targetInfo = {"net/minecraft/client/Minecraft", "checkGLError", "(Ljava/lang/String)V"}
    )
    public static void runGameLoop(Minecraft instanceForCheckGLError, String p_71361_1_, Minecraft instanceForRunGameLoop, IHookContext context) {

        // Хук в Minecraft#runGameLoop с заменой вызова Minecraft#checkGLError на наш метод
        // Тут можно выполнить свой код, например
        System.out.println("[REPLACE_INVOKE] Minecraft#checkGLError in Minecraft#runGameLoop replaced!");

        // Если вызвать такой метод, то все что идет после Minecraft#checkGLError в Minecraft#runGameLoop вызвано не будет
        context.exit(null);
    }
}

@HooksContainer(targetClassRef = Entity.class)
public class EntityReplaceInvokeHook {

    @Inject(
        target = InjectTarget.REPLACE_INVOKE
        targetInfo = {"java/util/Random", "<init>", "()V"},
        methodName = "<init>"
    )
    public static Random initRandom(Entity entity, World world) {
        // Хук в Entity#<init> с заменой вызова Random#<init> на наш метод
        // Тут можно выполнить свой код, например
        System.out.println("[REPLACE_INVOKE] Random#<init> in Entity#<init> replaced!");

        // Обязательно вернуть что-то, так как мы заменяем вызов new Random на свой!
        return new Random();
    }
}

@HooksContainer(targetClassRef = EntityEnderman.class)
public class EntityEndermanHook {

    // Так как World#setBlock не static, передаем в начале мир - World invokeInstance
    // Потом передаем все параметры World#setBlock по порядку - int invokeX, int invokeY, int invokeZ, Block invokeBlock, int invokeMeta, int invokeFlags
    // Так как EntityEnderman#onLivingUpdate не static, передаем - EntityEnderman enderman
    // EntityEnderman#onLivingUpdate не имеет параметров, иначе бы мы их передали в самом конце метода
    @Inject(
        target = InjectTarget.REPLACE_INVOKE,
        targetInfo = {"net/minecraft/world/World", "setBlock", "(IIILnet/minecraft/block/Block;II)Z"}
    )
    public static boolean onLivingUpdate(World invokeInstance, int invokeX, int invokeY, int invokeZ, Block invokeBlock, int invokeMeta, int invokeFlags, EntityEnderman enderman) {

        // Хук в EntityEnderman#onLivingUpdate с заменой вызова World#setBlock на наш метод
        // Тут можно выполнить свой код, например
        System.out.println("[REPLACE_INVOKE] World#setBlock in EntityEnderman#onLivingUpdate replaced!");

        // Заменяем установку блока эндерменом в мир, при этом из рук он пропадет!
        return false;
    }
}
```

### InjectTarget.FIELD_GET / InjectTarget.FIELD_SET

```java
@HooksContainer(targetClassRef = Minecraft.class)
public class MinecraftFieldHook {

    @Inject(
        target = InjectTarget.FIELD_GET,
        targetInfo: ["net/minecraft/client/Minecraft", "defaultResourcePacks", "java/util/List"]
    )
    public static List refreshResources(Minecraft instance, List defaultResourcePacksRedirect) {

        // Хук перехватывает получение поля defaultResourcePacks в методе Minecraft#refreshResources
        // Тут можно выполнить свой код, например
        System.out.println("[FIELD_GET] Field Minecraft#defaultResourcePacks in Minecraft#refreshResources redirected!");

        // Вернем пустой лист вместо Minecraft#defaultResourcePacks
        return new ArrayList();
    }

    @Inject(
        target = InjectTarget.FIELD_SET,
        targetInfo: ["net/minecraft/client/Minecraft", "gameSettings", "net/minecraft/client/settings/GameSettings"]
    )
    public static GameSettings startGame(Minecraft instance, GameSettings gameSettingsRedirect) {

        // Хук перехватывает установку поля gameSettings в методе Minecraft#startGame
        // Тут можно выполнить свой код, например
        System.out.println("[FIELD_SET] Field Minecraft#gameSettings in Minecraft#startGame redirected!");

        // Вернем то же самое, что и в оригинале
        return gameSettingsRedirect;
    }
}
```

### InjectTarget.BEFORE_INVOKE / InjectTarget.AFTER_INVOKE

```//todo```

### InjectTarget.CONSTANT_LOAD

```//todo```
