description: Работа со словарём руд.

# Словарь руд

OreDictionary существует в первую очередь для совместимости между модами.

Предметы, зарегистрированные в OreDictionary, становятся взаимозаменяемыми с другими предметами, имеющими то же имя OreDictionary. 
Это позволяет рецептам использовать любой предметы для получения одного и того же результата.

Несмотря на свое название, OreDictionary используется не только для руд. Любой предмет, похожий на другой предмет (например, красители), 
может быть зарегистрирован в OreDictionary и использоваться с ним.

## Условное обозначение имён

!!! note "Примечание"
    Старайтесь использовать имена существующих [групп словаря](oredict-table.md), прежде чем выдумывать свою, 
    иначе смысл от использования словаря стремится к нулю.

Forge не требует, чтобы имена были в каком-либо определенном формате, но следующее стало популярным стандартом для имен OreDictionary:

Полное название OreDictionary обычно использует camelCase (составные слова, начинающиеся со строчной буквы, 
где каждое последующее слово начинается с заглавной буквы) и избегает пробелов или подчеркиваний.

Первое слово в названии OreDictionary указывает на тип элемента. Для уникальных предметов (таких, как `record`, `dirt`, `egg` и `vine`) достаточно одного конкретного слова.

Последняя часть названия указывает на материал предмета. Это отличает, например, `ingotIron` от `ingotGold`.

Когда два слова недостаточно конкретны, можно добавить третье слово. Например, кирпич зарегистрировано как `ingotBrick`, в то время как адский кирпич зарегистрировано как `ingotBrickNether`.

## Регистрация предметов в словаре руд

Существует три метода используемые для регистрации предмета в словаре руд:

1. `OreDictionary#registerOre(String, Item)` - регистрация предмета, когда `itemDamage` неважен.
2. `OreDictionary#registerOre(String, Block)` - регистрация блока, когда `metadata` неважна.
3. `OreDictionary#registerOre(String, ItemStack)` - регистрирует предмет/блок с конкретным значением `itemDamage`.

!!! tip "Подсказка"
    `WILDCARD_VALUE` значение используется для указания на то, что `itemDamage` ItemStack`a не важен.

Зарегистрируем рубин, рубиновый блок и воздушные шарики в словаре руд.

```java
package ru.mcmodding.tutorial.common.handler;

import net.minecraft.item.ItemStack;
import net.minecraftforge.oredict.OreDictionary;

public class ModRecipes {
    public static void registerRecipes() {
        // ...
    }

    public static void registerOres() {
        OreDictionary.registerOre("blockRuby", ModBlocks.RUBY);
        OreDictionary.registerOre("gemRuby", ModItems.RUBY);
        OreDictionary.registerOre("balloon", new ItemStack(ModItems.BALLOON, 1, OreDictionary.WILDCARD_VALUE));
    }
}
```

Затем необходимо вызвать метод `ModRecipes#registerOres` в `CommonProxy#postInit`, до регистрации основных рецептов.

```java hl_lines="9"
package ru.mcmodding.tutorial.common;

import cpw.mods.fml.common.event.FMLPostInitializationEvent;
import ru.mcmodding.tutorial.common.handler.*;

public class CommonProxy implements IGuiHandler {

    public void postInit(FMLPostInitializationEvent event) {
        ModRecipes.registerOres();
        ModRecipes.registerRecipes();
    }
}
```

## Остальное

В классе `OreDictionary` существуют вспомогательные методы:

1. `OreDictionary#getOreID(String)` - возвращает идентификатор для указанного имени руды. Если у имени нет идентификатора, оно присваивает ему новый.
2. `OreDictionary#getOreName(Integer)` - возвращает имя для указанного идентификатора руды.
3. `OreDictionary#getOreID(ItemStack)` - возвращает идентификатор по переданному стеку предмета. Если стек предмета не привязан к имени, метод вернёт -1.
4. `OreDictionary#getOreIDs(ItemStack)` - возвращает все идентификаторы по преданному стеку предмета. Если стек предмета не привязан к имени, метод вернёт пустой массив.
5. `OreDictionary#getOres(String)` - возвращает список стеков предметов зарегистрированные для указанного имени руды. Создаёт пустой список, если списка изначально нет. Возвращает неизменяемый список, но может быть обновлён при регистрации руды используя `OreDictionary#registerOre`.
6. `OreDictionary#getOres(String, Boolean)` - возвращает список предметов, которые зарегистрированы для этого типа руды. Если флаг имеет значение `true`, то он создаст список как пустой, если бы он не существовал. Эта опция должна использоваться модмейкерами, которые выполняют полное сканирование в `postInit`. Это значительно уменьшает беспорядок в словаре руд и является правильным способом использования словаря в большинстве случаев. Другая функция, описанная выше, используется в OreRecipe и требуется для работы этого кода.
7. `OreDictionary#doesOreNameExist(String)` - проверяет наличие имени руды в словаре. Эта функция может быть использована для безопасного запроса словаря руд без добавления ненужного беспорядка в базовую структуру карты. Пожалуйста, используйте этот метод, когда это возможно и уместно.
8. `OreDictionary#getOreNames()` - возвращает массив имён руд зарегистрированных в словаре.
9. `OreDictionary#getOres(Integer)` - возвращает список стеков предметов зарегистрированные для указанного идентификатора руды. Создаёт пустой список, если списка изначально нет.
10. `OreDictionary#itemMatches(ItemStack, ItemStack, Boolean)` - проверяет совпадение входящего предмета с целевым. Имеет параметр `strict`, который отвечает за строгую проверку `itemDamage` предмета.
