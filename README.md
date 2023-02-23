# Парсинг сайтов

# Содержание
[Содержание](#содержание)

[1. Введение](#1-введение)

[2. Виды парсинга](#2-виды-парсинга)  

[2.1 HTML](#21-html)  

[3. Алгоритм](#3-алгоритм) 


# 1. Введение

Парсинг веб-страницы это процесс получения информации о содержании веб страницы по определенным категориям.  <br>
<br>
К примеру есть сайт книжек, на котором нам необходимо обработать какие-то определенные поля, к примеру: автор, название, количество страниц и прочие.

# 2. Виды парсинга
Примеры будут  на языке C# с плагином Angle Sharp.

<br>
Важная часть - получение документа, то есть html страницы.


```C#
public IDocument GetDocument( string url )
{
    var config = Configuration.Default.WithDefaultLoader().WithDefaultCookies();
    var context = BrowsingContext.New(config);
    return context.OpenAsync( url ).Result;
}
```
На вход подается url адрес и происходит скачивание документа. После этого для объекта типа IDocumnt доступны методы: GetElementsByClassName, GetElementById, GetElementsByTag.


## 2.1 HTML
Существует несколько категорий сайтов:

1. Каждому полю соответсвует уникальный id либо класс, который возможно выцепить среди других.

    Примеры сайтов: <br>
    
    1. https://igraslov.store
    2. https://tochka24.com
    3. https://www.gamepark.ru
     
    Пример получения полей:
    
    ```HTML
        <h2 class="single-post-title product_title entry-title" itemprop="name">Ясперс К. Вопрос о виновности. </h2>
    ```
    
    ```C#
        var BookInfo = GetDocument(refToBook);
        BookInfo.GetElementsByClassName("single-post-title product_title entry-title")[0].TextContent;
    ```
    
    
    
    ```HTML
        <h1 class="product_name" itemprop="name">FIFA 23 (PS5)</h1>
    ```
    
    ```C#
        var BookInfo = GetDocument(refToBook);
         Name = BookInfo.GetElementsByClassName("product_name")[0].TextContent;
    ```
    
    
    
2. Полю не соответствует ничего, но существует родительский класс, который возможно получить.

    Примеры сайтов: <br>
    
    1. https://speclit.su
    
    
    Пример получения полей:
    ```HTML
        <div class="specifications">
            <div class="title_small">Характеристики</div>
            <div class="feature">
                   <div class="item flex">
                        <div class="name">Жанр</div>
                        <div class="val"><a href="/playstation5/games/cat-122/">Спортивные </a></div>
                    </div>
                    <div class="item flex">
                        <div class="name">Язык</div>
                        <div class="val">Английский</div>
                    </div>
                    <div class="item flex">
                        <div class="name">Дата выхода</div>
                        <div class="val">06.10.2022</div>
                    </div>
                    <div class="item flex">
                        <div class="name"> Издатель</div>
                        <div class="val"><a href="/playstation5/games/?PROPERTY_PUBLISHER=4606">Electronic Arts</a></div>
                    </div>
                     <div class="item flex">
                        <div class="name">Возраст</div>
                        <div class="val">с 6 лет</div>
                    </div>
                     <div class="item flex">
                        <div class="name">Артикул</div>
                        <div class="val">190214</div>
                    </div>
              </div>
        </div>
    ```
    ```C#
        var BookInfo = GetDocument(refToBook);
        Author = BookInfo.GetElementsByClassName("specifications")[0].GetElementsByClassName("item")[3].GetElementsByClassName("val")[0].TextContent;
    ```
    где в квадратных скобках находится номер эелемента (отсчет с 0).
    
    
    
3. Сайты не пригодные для парсинга. Это могут быть сайты, которые не используют ни классы, ни id, либо же имеют запутанную систему контента, которую невозможно спарсить, либо другие причины.

    Примеры сайтов: <br>

    1. http://chitai-gorod.ru
    2. http://www.belygorod.ru

Работа с самими элементами:
1. С  поялми можно работать как со строкой:

    ```HTML
        <div class="text_block">
        <p style="margin-bottom:15px;"><b>Обратите внимание: игра поддерживает только английский язык!</b></p>
        <p style="margin-bottom:15px;">EA Sports FIFA 23 выводит мировую игру на поле благодаря технологии HyperMotion2 на PlayStation 5, которая обеспечивает еще большую реалистичность игрового процесса, наряду с мужским и женским Чемпионатом мира по футболу FIFA , добавлением женских клубных команд, функциями перекрестной игры и многим другим.</p>
        <p style="margin-bottom:15px;">Войдите в свою футбольную мечту в роли тренера или игрока в режиме карьеры. В FIFA 23 вы сможете определить свою индивидуальность как игрока, управлять знаменитыми футболистами и начать сезон в новом режиме с яркими моментами в самой реалистичной на сегодняшний день карьере в FIFA.</p>
        <p style="margin-bottom:15px;">От повторов трансляций с дополненной реальностью до гиперреалистичных поверхностей поля и улучшенной атмосферы игры на стадионах — теперь игра в футбол на платформах нового поколения будет ещё интереснее!</p>
        <p style="margin-bottom:15px;">Играйте женскими клубными командами впервые в истории EA SPORTS FIFA — сразу после выхода будут доступны Женская суперлига Футбольной ассоциации Англии и Чемпионат Франции по футболу среди женщин.</p>
        <p style="margin-bottom:25px;">Также в FIFA 23 появился «Ричмонд»! Это значит, что Тед Лассо, тренер Борода и все главные звёзды состава «Ричмонда» — Джейми Тартт, Дани Рохас, Сэм Обисанья, Рой Кент и Айзек МакАду, — а также домашняя арена клуба, стадион «Нельсон Роуд» теперь доступны всем фанатам сериала.</p>                        </div>
    ```
    ```C#
        var BookInfo = GetDocument(refToBook);
         Description = BookInfo.GetElementsByClassName("text_block")[0].TextContent
                .Replace("\t", "")
                .Replace("\n", "");
    ```
    
   2.Если поля с одинаковыми названиями можно взять определенный элемент: 
    ```HTML
        <div class="specifications">
            <div class="title_small">Характеристики</div>
            <div class="feature">
                   <div class="item flex">
                        <div class="name">Жанр</div>
                        <div class="val"><a href="/playstation5/games/cat-122/">Спортивные </a></div>
                    </div>
                    <div class="item flex">
                        <div class="name">Язык</div>
                        <div class="val">Английский</div>
                    </div>
                    <div class="item flex">
                        <div class="name">Дата выхода</div>
                        <div class="val">06.10.2022</div>
                    </div>
                    <div class="item flex">
                        <div class="name"> Издатель</div>
                        <div class="val"><a href="/playstation5/games/?PROPERTY_PUBLISHER=4606">Electronic Arts</a></div>
                    </div>
                     <div class="item flex">
                        <div class="name">Возраст</div>
                        <div class="val">с 6 лет</div>
                    </div>
                     <div class="item flex">
                        <div class="name">Артикул</div>
                        <div class="val">190214</div>
                    </div>
              </div>
        </div>
    ```
     ```C#
        var BookInfo = GetDocument(refToBook);
        Author = BookInfo.GetElementsByClassName("specifications")[0].GetElementsByClassName("item")[3].GetElementsByClassName("val")[0].TextContent;
    ```
    где в квадратных скобках находится номер эелемента (отсчет с 0).
    3. Если полей много или их количество меняется, можно брать поле с конца:

    ```C#
        var BookInfo = GetDocument(refToBook);
         PublisherName = BookInfo.GetElementsByClassName("specifications")[0].GetElementsByClassName("item")[^4].GetElementsByClassName("val")[0].TextContent;
    ```
    где в квадратных скобках находится номер эелемента с конца перед знаком ^ (отсчет с 1).
    
   4.Можно брать какие-нибудь атрибуты класса, например (атрибут data-q = 1):
    ```HTML
        <label class="label_radio">
       <input class="used" type="radio" name="chekbox_423342_434740" data-bonus="200" data-href="/playstation5/games/GodofWarRagnarokPS5GameReplay/" data-add_href="/?add_to_cart=434740" data-price="6699.00" data-format_price="6 699" data-id="434740" data-name="God of War – Ragnarok (PS5) (GameReplay)" data-q="1">
       </label>
    ```
        element.GetElementsByClassName("label_radio")[0].GetElementsByTagName("input")[0].Attributes["data-q"].Value
   6.Можно и формировать из них ссылки и использовать по несколько раз, чтобы добраться до дочерних элементов, например на картинку книги:
   
    ```HTML
        <a href="/upload/resize_cache/iblock/15c/1000_1000_1/kupit_god_of_war_ragnarok_ps4.jpg" data-fancybox="gallery" class="img fancy_img">
        <img src="/upload/resize_cache/iblock/15c/372_470_1/kupit_god_of_war_ragnarok_ps4.jpg" alt="" itemprop="image">
         </a>
    ```
    ```C#
        var BookInfo = GetDocument(refToBook);
          Image = "https://www.gamepark.ru/comics" + element.GetElementsByClassName("img")[0].GetElementsByTagName("img")[0].Attributes["src"].Value;
    ```
# 3. Алгоритм парсинга
