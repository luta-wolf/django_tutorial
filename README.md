## Часть II Связи. Ввод данных. Статические файлы.

### 2.1. Связи между моделями
- `Связь "один-со-многими"` - связь при которой одна запись модели Rubric (рубрика) будет связана с произвольным количеством записей модели Bb (объявлений).
- Для создания связи нужно добавить в модель Bb поле внешнего ключа
- Класс `ForeignKey` представляет поле внешнего ключа, в котором фактически будет храниться ключ записи из первичной модели. Первым параметром конструктору этого класса передается класс первичной модели в виде:
	- ссылки на класс - если код, объявляющий класс первичной модели, располага­ется перед кодом класса вторичной модели;
	- строки с именем класса - если вторичная модель объявлена раньше первичной (как у нас).
- `null=True` - делает поле необязательное, только после этого поле будет успешно добавлено в модель.
- `on_delete` - этот параметр управляет каскадными удалениями записей вторичной модели после удаления записи первичной модели, с которой они бьmи связаны.
- `PROTECT` - Значение этого параметра запрещает каскадные удаления (чтобы какой-нибудь несообразительный администратор не удалил разом сразу уйму объявлений).

- Модель необходими сразуже регестрировать на административном сайте, добавив в мо­ дуль `admiп.py` пакета приложения такие два выражения:
	- `from .models import Rubric`
	- `admin.site.register(Rubric)`

### 2.2. Строковое представление модели
- `__str__` - переопределив этот метод мы будем возвращать строковое предстваление класса

### 2.3. URL-параметры и параметризованные запросы
Указываем маршрутизатору, какую часть пути считать URL- параметром, каков тип значения этого параметра, и какое имя должно быть у пара­метра контроллера, которому будет присвоено значение этого URL-параметра.
- В начало набора маршрутов добавляем еще один `path('<int:rubric_id>/', by_rubric)`
	- `<  >`угловые скобки обозначают описание URL-пара­метра
	- языковая конструкция `int` - целочисленный тип этого параметра
	- `rubric_id` - имя параметра контроллера, которому будет присвоено значение этого URL-параметра.
	- `by_rubric` - контроллер-функция для созданного маршрута
- маршруты, содержащие URL-параметры, носят название `параметризо­ванных`.

### 2.4 Обратное разрешение интернет-адресов
- Обратное разрешение -  мы указываем маршрут, формирующий нужный нам интернет-адрес, и, если это параметризован­ный маршрут, значения URL-параметров, а Django сам генерирует на основе всего этого правильный интернет-адрес. Для этого следует выполнить два действия:
	- Первое - дать нужным маршрутам имена, создав тем самым именованные маршру­ты.
	- Второе - использование для создания интернет-адресов в гиперссьmках тегов шаблонизатора url.

### 2.5. Формы, связанные с моделями
Для ввода данных (наример для добавления в базу данных новых объ­явлений) язык HTML предлагает так называемые веб-формы. Джанго умеет это делать для этого в файле `forms.py` создаем класс наследуясь от `ModelForm` в котором указываем параметры нашей формы: класс модели, с которой она связана (атри­бут класса model), и последовательность из имен полей модели, которые должны присутствовать в форме (атрибут класса fields)

### 2.6. Контроллеры-классы
Базовый класс реализует функциональность по созданию формы, выводу ее на экран с применением указанного шаблона, получению занесенных в форму данных, проверке и на корректность, сохранению их в новой записи модели и перенапралению в случае успеха на интернет-адресс, который мы зададим
- `template_name` - путь к файлу шаблона, что будет использован для вывода стра­ницы с формой;
- `form_class` - сам класс формы, связанной с моделью;
- `success_url` - интернет-адрес, по которому будет выполнено перенаправление после успешного сохранения данных (в нашем случае это адрес главной стра­ницы).
- `get_context_data` - этот Метод, этого класса формирует контекст шаблона

Создание веб-формы
- форма в контексте шаблона хранится в переменной `form`. Эту переменную созда­ет базовый класс `CreateView`;
- для вывода формы, чьи элементы управления находятся на отдельных абзацах, применяется метод `as_p()` класса `ModelForm`;
- метод `as_p()` генерирует только код, создающий элементы управления. Тег `<form>`, необходимый для создания самой формы, и тег `<input>`, формирующий кнопку отправки данных, нам придется вставить самостоятельно.
- в теге `<form> `мы поместили тег шаблонизатора `csrf_token`. Он создает в форме скрытое поле, хранящее цифровой жетон, получив который, контроллер «пой­мет», что данные бьли отправлены с текущего сайта, и им можно доверять. Это часть подсистемы обеспечения безопасности Django.
- Функция `reverse_lazy()` из модуля `django.urls` в качестве параметров принимает имя маршруrа и значения всех входящих в маршруr URL-параметров (если они там есть). Результатом станет готовый интернет-адрес.
<img width="508" alt="image" src="https://user-images.githubusercontent.com/58044383/211330084-33452378-516d-46df-a843-84abf49680cc.png">

### 2.7. Наследование wаблонов
Шаблон, являющийся базовым, объявляет в составе своего содержимого так назы­ ваемые блоки. Они определяют место в шаблоне, куда будет вставлено содержимое, извлеченное из шаблонов, которые стануn производными по отношению к базово­му. Каждый из блоков имеет уникальное в пределах шаблона имя.
- `title` - будет помещаться в теге `<title>` и использоваться для создания уни­ кального названия для каждой страницы;
- `content` - будет использоваться для размещения уникального содержимого страниц.

- Начало объявляемого блока помечается тегом шаблонизатора `blосk`, за которым должно следовать имя блока. Завершается блок тегом `endblock`.
- Объявленный в базовом шаблоне блок может быть как пустым:
	{% blосk content %}
	{% endblock %}
- так и иметь какое-либо изначальное содержимое:
	(% blосk title %}Главная{% endblock %}
- В самом начале кода mобого производного шаблона должен стоять тег шаблониза­тора `extends`, после котuрого записывается путь к базовому шаблону.

### 2.8. Статические файлы
Статические файлы - файлы, содержимое которых не обрабатывается программно, а пересьmается клиенrу как есть. К таким файлам относятся, например, таблицы стилей и графические изображения, поме­щаемые на страницы.
- По умолчанию Django требует, чтобы статические файлы, относящиеся к опреде­ ленному приложению, помещались в папке `static` пакета этого приложения
- Чтобы сформировать интернет-адрес статического файла, нужно использовать тег шаблонизатора `static`.
<img width="766" alt="image" src="https://user-images.githubusercontent.com/58044383/211375298-23f4ad69-cf1a-4e77-a0cd-e11968d63e0d.png">
