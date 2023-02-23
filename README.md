# Парсинг сайтов

# Содержание
[Содержание](#содержание)

[1. Введение](#1-введение)

[2. Виды парсинга](#2-виды-парсинга)  

[2.1 HTML](#21-html)  


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
    
    ```C#
        var BookInfo = GetDocument(refToBook);
        BookInfo.GetElementsByClassName("single-post-title product_title entry-title")[0].TextContent;
    ```
    
    ```C#
        var BookInfo = GetDocument(refToBook);
         Name = BookInfo.GetElementsByClassName("product_name")[0].TextContent;
    ```
    
    
    
2. Полю не соответствует ничего, но существует родительский класс, который возможно получить.

    Примеры сайтов: <br>
    
    1. https://speclit.su
    
    
    Пример получения полей:
    
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
    ```C#
        var BookInfo = GetDocument(refToBook);
         Description = BookInfo.GetElementsByClassName("text_block")[0].TextContent
                .Replace("\t", "")
                .Replace("\n", "");
    ```
    
   2.Если поля с одинаковыми названиями можно взять определенный элемент: 

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
    
   4.Можно брать какие-нибудь атрибуты класса, например (атрибут data-q = 10):
        element.GetElementsByClassName("label_radio")[0].GetElementsByTagName("input")[0].Attributes["data-q"].Value
   6.Можно и формировать из них ссылки и использовать по несколько раз, чтобы добраться до дочерних элементов, например на картинку книги:
    ```C#
        var BookInfo = GetDocument(refToBook);
          Image = "https://www.gamepark.ru/comics" + element.GetElementsByClassName("img")[0].GetElementsByTagName("img")[0].Attributes["src"].Value;
    ```
## 3 Алгоритм парсинга
