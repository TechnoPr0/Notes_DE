# Notes_DE
Записки начинающего Инженера данных
___
</br> 

## Парсинг
Для того что-бы работать с данными их нужно откуда-то брать. Один из способов это парсинг.

Стандартный инструмент для составления HTTP-запросов - библиотека ```requests```.
Такие HTTP методы, как GET и POST, определяют, какие действия будут выполнены при создании HTTP запроса.
GET является одним из самых популярных HTTP методов. Метод GET указывает на то, что происходит попытка извлечь данные из определенного ресурса. Для того, чтобы выполнить запрос GET, используется requests.get().

```python
requests.get('https://api.github.com')
#<Response [200]>
```

Далее будет рассмотрен ответ на данный запрос, который можно получить при помощи объекта Response.
Response представляет собой довольно мощный объект для анализа результатов запроса. В качестве примера будет использован предыдущий запрос, только на этот раз результат будет представлен в виде переменной. Таким образом, получится лучше изучить его атрибуты и особенности использования.

```python
response = requests.get('https://api.github.com')
```
___

### Коды состояния
Самыми первыми данными, которые будут получены через Response, будут коды состояния. Коды состояния сообщают о статусе запроса.

```python
response.status_code
#200
```

Например, статус 200 OK значит, что запрос успешно выполнен. А вот статус 404 NOT FOUND говорит о том, что запрашиваемый ресурс не был найден. Существует множество других статусных кодов, которые могут сообщить важную информацию, связанную с запросом.

Используя .status_code, можно увидеть код состояния, который возвращается с сервера.

В некоторых случаях необходимо использовать полученную информацию для написания программного кода.

```Python
if response.status_code == 200:
    print('Success!')
elif response.status_code == 404:
    print('Not Found.')
```
В таком случае, если с сервера будет получен код состояния 200, тогда программа выведет значение Success!. Однако, если от сервера поступит код 404, тогда программа выведет значение Not Found.
requests может значительно упростить весь процесс. Если использовать Response в условных конструкциях, то при получении кода состояния в промежутке от 200 до 400, будет выведено значение True. В противном случае отобразится значение False.

Последний пример можно упростить при помощи использования оператора if.

```Python
if response:
    print('Success!')
else:
    print('An error has occurred.')
```

Стоит иметь в виду, что данный способ не проверяет, имеет ли статусный код точное значение 200. Причина заключается в том, что другие коды в промежутке от 200 до 400, например, 204 NO CONTENT и 304 NOT MODIFIED, также считаются успешными в случае, если они могут предоставить действительный ответ.

Допустим, если при использовании оператора if вы не хотите проверять код состояния, можно расширить диапазон исключений для неудачных результатов запроса. Это можно сделать при помощи использования .raise_for_status().

```Python
import requests
from requests.exceptions import HTTPError
 
for url in ['https://api.github.com', 'https://api.github.com/invalid']:
    try:
        response = requests.get(url)
 
        # если ответ успешен, исключения задействованы не будут
        response.raise_for_status()
    except HTTPError as http_err:
        print(f'HTTP error occurred: {http_err}')  # Python 3.6
    except Exception as err:
        print(f'Other error occurred: {err}')  # Python 3.6
    else:
        print('Success!')
```

В случае вызова исключений через .raise_for_status() к некоторым кодам состояния применяется HTTPError. Когда код состояния показывает, что запрос успешно выполнен, программа продолжает работу без применения политики исключений.
___

### Получить содержимое страницы в Requests
Зачастую ответ на запрос GET содержит весьма ценную информацию. Она находится в теле сообщения и называется пейлоад (payload). Используя атрибуты и методы библиотеки Response, можно получить пейлоад в различных форматах.

Для того, чтобы получить содержимое запроса в байтах, необходимо использовать .content.

```Python
response = requests.get('https://api.github.com')
response.content
#
b'{"current_user_url":"https://api.github.com/user","current_user_authorizations_html_url":"https://github.com/settings/connections/applications{/client_id}","authorizations_url":"https://api.github.com/authorizations","code_search_url":"https://api.github.com/search/code?q={query}{&page,per_page,sort,order}","commit_search_url":"https://api.github.com/search/commits?q={query}{&page,per_page,sort,order}","emails_url":"https://api.github.com/user/emails","emojis_url":"https://api.github.com/emojis","events_url":"https://api.github.com/events","feeds_url":"https://api.github.com/feeds","followers_url":"https://api.github.com/user/followers","following_url":"https://api.github.com/user/following{/target}","gists_url":"https://api.github.com/gists{/gist_id}","hub_url":"https://api.github.com/hub","issue_search_url":"https://api.github.com/search/issues?q={query}{&page,per_page,sort,order}","issues_url":"https://api.github.com/issues","keys_url":"https://api.github.com/user/keys","notifications_url":"https://api.github.com/notifications","organization_repositories_url":"https://api.github.com/orgs/{org}/repos{?type,page,per_page,sort}","organization_url":"https://api.github.com/orgs/{org}","public_gists_url":"https://api.github.com/gists/public","rate_limit_url":"https://api.github.com/rate_limit","repository_url":"https://api.github.com/repos/{owner}/{repo}","repository_search_url":"https://api.github.com/search/repositories?q={query}{&page,per_page,sort,order}","current_user_repositories_url":"https://api.github.com/user/repos{?type,page,per_page,sort}","starred_url":"https://api.github.com/user/starred{/owner}{/repo}","starred_gists_url":"https://api.github.com/gists/starred","team_url":"https://api.github.com/teams","user_url":"https://api.github.com/users/{user}","user_organizations_url":"https://api.github.com/user/orgs","user_repositories_url":"https://api.github.com/users/{user}/repos{?type,page,per_page,sort}","user_search_url":"https://api.github.com/search/users?q={query}{&page,per_page,sort,order}"}'
```
Использование .content обеспечивает доступ к чистым байтам ответного пейлоада, то есть к любым данным в теле запроса. Однако, зачастую требуется конвертировать полученную информацию в строку в кодировке UTF-8. response делает это при помощи .text.

```Python
response.text
#
'{"current_user_url":"https://api.github.com/user","current_user_authorizations_html_url":"https://github.com/settings/connections/applications{/client_id}","authorizations_url":"https://api.github.com/authorizations","code_search_url":"https://api.github.com/search/code?q={query}{&page,per_page,sort,order}","commit_search_url":"https://api.github.com/search/commits?q={query}{&page,per_page,sort,order}","emails_url":"https://api.github.com/user/emails","emojis_url":"https://api.github.com/emojis","events_url":"https://api.github.com/events","feeds_url":"https://api.github.com/feeds","followers_url":"https://api.github.com/user/followers","following_url":"https://api.github.com/user/following{/target}","gists_url":"https://api.github.com/gists{/gist_id}","hub_url":"https://api.github.com/hub","issue_search_url":"https://api.github.com/search/issues?q={query}{&page,per_page,sort,order}","issues_url":"https://api.github.com/issues","keys_url":"https://api.github.com/user/keys","notifications_url":"https://api.github.com/notifications","organization_repositories_url":"https://api.github.com/orgs/{org}/repos{?type,page,per_page,sort}","organization_url":"https://api.github.com/orgs/{org}","public_gists_url":"https://api.github.com/gists/public","rate_limit_url":"https://api.github.com/rate_limit","repository_url":"https://api.github.com/repos/{owner}/{repo}","repository_search_url":"https://api.github.com/search/repositories?q={query}{&page,per_page,sort,order}","current_user_repositories_url":"https://api.github.com/user/repos{?type,page,per_page,sort}","starred_url":"https://api.github.com/user/starred{/owner}{/repo}","starred_gists_url":"https://api.github.com/gists/starred","team_url":"https://api.github.com/teams","user_url":"https://api.github.com/users/{user}","user_organizations_url":"https://api.github.com/user/orgs","user_repositories_url":"https://api.github.com/users/{user}/repos{?type,page,per_page,sort}","user_search_url":"https://api.github.com/search/users?q={query}{&page,per_page,sort,order}"}'
```

Декодирование байтов в строку требует наличия определенной модели кодировки. По умолчанию requests попытается узнать текущую кодировку, ориентируясь по заголовкам HTTP. Указать необходимую кодировку можно при помощи добавления .encoding перед .text.

```Python
response.encoding = 'utf-8' # Optional: requests infers this internally
response.text
#
'{"current_user_url":"https://api.github.com/user","current_user_authorizations_html_url":"https://github.com/settings/connections/applications{/client_id}","authorizations_url":"https://api.github.com/authorizations","code_search_url":"https://api.github.com/search/code?q={query}{&page,per_page,sort,order}","commit_search_url":"https://api.github.com/search/commits?q={query}{&page,per_page,sort,order}","emails_url":"https://api.github.com/user/emails","emojis_url":"https://api.github.com/emojis","events_url":"https://api.github.com/events","feeds_url":"https://api.github.com/feeds","followers_url":"https://api.github.com/user/followers","following_url":"https://api.github.com/user/following{/target}","gists_url":"https://api.github.com/gists{/gist_id}","hub_url":"https://api.github.com/hub","issue_search_url":"https://api.github.com/search/issues?q={query}{&page,per_page,sort,order}","issues_url":"https://api.github.com/issues","keys_url":"https://api.github.com/user/keys","notifications_url":"https://api.github.com/notifications","organization_repositories_url":"https://api.github.com/orgs/{org}/repos{?type,page,per_page,sort}","organization_url":"https://api.github.com/orgs/{org}","public_gists_url":"https://api.github.com/gists/public","rate_limit_url":"https://api.github.com/rate_limit","repository_url":"https://api.github.com/repos/{owner}/{repo}","repository_search_url":"https://api.github.com/search/repositories?q={query}{&page,per_page,sort,order}","current_user_repositories_url":"https://api.github.com/user/repos{?type,page,per_page,sort}","starred_url":"https://api.github.com/user/starred{/owner}{/repo}","starred_gists_url":"https://api.github.com/gists/starred","team_url":"https://api.github.com/teams","user_url":"https://api.github.com/users/{user}","user_organizations_url":"https://api.github.com/user/orgs","user_repositories_url":"https://api.github.com/users/{user}/repos{?type,page,per_page,sort}","user_search_url":"https://api.github.com/search/users?q={query}{&page,per_page,sort,order}"}'
```

Если присмотреться к ответу, можно заметить, что его содержимое является сериализированным JSON контентом. Воспользовавшись словарем, можно взять полученные из .text строки str и провести с ними обратную сериализацию при помощи использования json.loads(). Есть и более простой способ, который требует применения .json().
```Python
response.json()
#
{'current_user_url': 'https://api.github.com/user', 'current_user_authorizations_html_url': 'https://github.com/settings/connections/applications{/client_id}', 'authorizations_url': 'https://api.github.com/authorizations', 'code_search_url': 'https://api.github.com/search/code?q={query}{&page,per_page,sort,order}', 'commit_search_url': 'https://api.github.com/search/commits?q={query}{&page,per_page,sort,order}', 'emails_url': 'https://api.github.com/user/emails', 'emojis_url': 'https://api.github.com/emojis', 'events_url': 'https://api.github.com/events', 'feeds_url': 'https://api.github.com/feeds', 'followers_url': 'https://api.github.com/user/followers', 'following_url': 'https://api.github.com/user/following{/target}', 'gists_url': 'https://api.github.com/gists{/gist_id}', 'hub_url': 'https://api.github.com/hub', 'issue_search_url': 'https://api.github.com/search/issues?q={query}{&page,per_page,sort,order}', 'issues_url': 'https://api.github.com/issues', 'keys_url': 'https://api.github.com/user/keys', 'notifications_url': 'https://api.github.com/notifications', 'organization_repositories_url': 'https://api.github.com/orgs/{org}/repos{?type,page,per_page,sort}', 'organization_url': 'https://api.github.com/orgs/{org}', 'public_gists_url': 'https://api.github.com/gists/public', 'rate_limit_url': 'https://api.github.com/rate_limit', 'repository_url': 'https://api.github.com/repos/{owner}/{repo}', 'repository_search_url': 'https://api.github.com/search/repositories?q={query}{&page,per_page,sort,order}', 'current_user_repositories_url': 'https://api.github.com/user/repos{?type,page,per_page,sort}', 'starred_url': 'https://api.github.com/user/starred{/owner}{/repo}', 'starred_gists_url': 'https://api.github.com/gists/starred', 'team_url': 'https://api.github.com/teams', 'user_url': 'https://api.github.com/users/{user}', 'user_organizations_url': 'https://api.github.com/user/orgs', 'user_repositories_url': 'https://api.github.com/users/{user}/repos{?type,page,per_page,sort}', 'user_search_url': 'https://api.github.com/search/users?q={query}{&page,per_page,sort,order}'}
```
Тип полученного значения из .json(), является словарем. Это значит, что доступ к его содержимому можно получить по ключу.

Коды состояния и тело сообщения предоставляют огромный диапазон возможностей. Однако, для их оптимального использования требуется изучить метаданные и заголовки HTTP.
___

### HTTP заголовки в Requests
HTTP заголовки ответов на запрос могут предоставить определенную полезную информацию. Это может быть тип содержимого ответного пейлоада, а также ограничение по времени для кеширования ответа. Для просмотра HTTP заголовков загляните в атрибут .headers.
```Python
response.headers
#
{'Server': 'GitHub.com', 'Date': 'Thu, 13 Oct 2022 00:38:43 GMT', 'Content-Type': 'application/json; charset=utf-8', 'Cache-Control': 'public, max-age=60, s-maxage=60', 'Vary': 'Accept, Accept-Encoding, Accept, X-Requested-With', 'ETag': 'W/"4f825cc84e1c733059d46e76e6df9db557ae5254f9625dfe8e1b09499c449438"', 'X-GitHub-Media-Type': 'github.v3; format=json', 'Access-Control-Expose-Headers': 'ETag, Link, Location, Retry-After, X-GitHub-OTP, X-RateLimit-Limit, X-RateLimit-Remaining, X-RateLimit-Used, X-RateLimit-Resource, X-RateLimit-Reset, X-OAuth-Scopes, X-Accepted-OAuth-Scopes, X-Poll-Interval, X-GitHub-Media-Type, X-GitHub-SSO, X-GitHub-Request-Id, Deprecation, Sunset', 'Access-Control-Allow-Origin': '*', 'Strict-Transport-Security': 'max-age=31536000; includeSubdomains; preload', 'X-Frame-Options': 'deny', 'X-Content-Type-Options': 'nosniff', 'X-XSS-Protection': '0', 'Referrer-Policy': 'origin-when-cross-origin, strict-origin-when-cross-origin', 'Content-Security-Policy': "default-src 'none'", 'Content-Encoding': 'gzip', 'X-RateLimit-Limit': '60', 'X-RateLimit-Remaining': '57', 'X-RateLimit-Reset': '1665624832', 'X-RateLimit-Resource': 'core', 'X-RateLimit-Used': '3', 'Accept-Ranges': 'bytes', 'Content-Length': '530', 'X-GitHub-Request-Id': 'E556:131D0:1F99751:200E03D:63475E14'}
```
.headers возвращает словарь, что позволяет получить доступ к значению заголовка HTTP по ключу. Например, для просмотра типа содержимого ответного пейлоада, требуется использовать Content-Type.

```Python
response.headers['Content-Type']
#'application/json; charset=utf-8'
```
У объектов словарей в качестве заголовков есть своим особенности. Специфика HTTP предполагает, что заголовки не чувствительны к регистру. Это значит, что при получении доступа к заголовкам можно не беспокоится о том, использованы строчным или прописные буквы.

```Python
response.headers['content-type']
#'application/json; charset=utf-8'
```
При использовании ключей 'content-type' и 'Content-Type' результат будет получен один и тот же.
___

### Python Requests параметры запроса
Наиболее простым способом настроить запрос GET является передача значений через параметры строки запроса в URL. При использовании метода get(), данные передаются в params. Например, для того, чтобы посмотреть на библиотеку requests можно использовать Search API на GitHub.

```Python
import requests

# Поиск местонахождения для запросов на GitHub
response = requests.get(
    'https://api.github.com/search/repositories',
    params={'q': 'requests+language:python'},
)

# Анализ некоторых атрибутов местонахождения запросов
json_response = response.json()
repository = json_response['items'][0]
print(f'Repository name: {repository["name"]}')  # Python 3.6+
print(f'Repository description: {repository["description"]}')  # Python 3.6+
```
Передавая словарь {'q': 'requests+language:python'} в параметр params, который является частью .get(), можно изменить ответ, что был получен при использовании Search API.


Можно передать параметры в get() в форме словаря, как было показано выше. Также можно использовать список кортежей.

```Python
requests.get(
...     'https://api.github.com/search/repositories',
...     params=[('q', 'requests+language:python')],
... )
<Response [200]>
```
Также можно передать значение в байтах.

```Python
>>> requests.get(
...     'https://api.github.com/search/repositories',
...     params=b'q=requests+language:python',
... )
<Response [200]>
```
Строки запроса полезны для уточнения параметров в запросах GET. Также можно настроить запросы при помощи добавления или изменения заголовков отправленных сообщений.
Настройка HTTP заголовка запроса (headers)
Для изменения HTTP заголовка требуется передать словарь данного HTTP заголовка в get() при помощи использования параметра headers. Например, можно изменить предыдущий поисковой запрос, подсветив совпадения в результате. Для этого в заголовке Accept медиа тип уточняется при помощи text-match.

```Python
import requests
 
response = requests.get(
    'https://api.github.com/search/repositories',
    params={'q': 'requests+language:python'},
    headers={'Accept': 'application/vnd.github.v3.text-match+json'},
)
 
# просмотр нового массива `text-matches` с предоставленными данными
# о поиске в пределах результатов
json_response = response.json()
repository = json_response['items'][0]
print(f'Text matches: {repository["text_matches"]}')
```
Заголовок Accept сообщает серверу о типах контента, который можно использовать в рассматриваемом приложении. Здесь подразумевается, что все совпадения будут подсвечены, для чего в заголовке используется значение application/vnd.github.v3.text-match+json. Это уникальный заголовок Accept для GitHub. В данном случае содержимое представлено в специальном JSON формате.

Перед более глубоким изучением способов редактирования запросов, будет не лишним остановиться на некоторых других методах HTTP.
___

### Примеры HTTP методов в Requests
Помимо GET, большой популярностью пользуются такие методы, как POST, PUT, DELETE, HEAD, PATCH и OPTIONS. Для каждого из этих методов существует своя сигнатура, которая очень похожа на метод get().

```Python
requests.post('https://httpbin.org/post', data={'key':'value'})
requests.put('https://httpbin.org/put', data={'key':'value'})
requests.delete('https://httpbin.org/delete')
requests.head('https://httpbin.org/get')
requests.patch('https://httpbin.org/patch', data={'key':'value'})
requests.options('https://httpbin.org/get')
```
Каждая функция создает запрос к httpbin сервису, используя при этом ответный HTTP метод. Результат каждого метода можно изучить способом, который был использован в предыдущих примерах.

```Python
response = requests.head('https://httpbin.org/get')
response.headers['Content-Type']
'application/json'
 
response = requests.delete('https://httpbin.org/delete')
json_response = response.json()
json_response['args']
{}
```

При использовании каждого из данных методов в Response могут быть возвращены заголовки, тело запроса, коды состояния и многие другие аспекты. Методы POST, PUT и PATCH в дальнейшем будут описаны более подробно.
___
</br>

### Python Requests тело сообщения
В соответствии со спецификацией HTTP запросы POST, PUT и PATCH передают информацию через тело сообщения, а не через параметры строки запроса. Используя requests, можно передать данные в параметр data.

В свою очередь data использует словарь, список кортежей, байтов или объект файла. Это особенно важно, так как может возникнуть необходимость адаптации отправляемых с запросом данных в соответствии с определенными параметрами сервера.

К примеру, если тип содержимого запроса application/x-www-form-urlencoded, можно отправить данные формы в виде словаря.

```Python
requests.post('https://httpbin.org/post', data={'key':'value'})
#<Response [200]>
```
Ту же самую информацию также можно отправить в виде списка кортежей.

```Python
requests.post('https://httpbin.org/post', data=[('key', 'value')])
#<Response [200]>
```
В том случае, если требуется отравить данные JSON, можно использовать параметр json. При передачи данных JSON через json, requests произведет сериализацию данных и добавит правильный Content-Type заголовок.

Стоит взять на заметку сайт httpbin.org. Это чрезвычайно полезный ресурс, созданный человеком, который внедрил использование requests – Кеннетом Рейтцом. Данный сервис предназначен для тестовых запросов. Здесь можно составить пробный запрос и получить ответ с требуемой информацией. В качестве примера рассмотрим базовый запрос с использованием POST.

```Python
response = requests.post('https://httpbin.org/post', json={'key':'value'})
json_response = response.json()
json_response['data']
'{"key": "value"}'
json_response['headers']['Content-Type']
'application/json'
```
Здесь видно, что сервер получил данные и HTTP заголовки, отправленные вместе с запросом. requests также предоставляет информацию в форме PreparedRequest.

</br>

___

### Библиотеки для парсинга.
В Python на данный момент существует 3 основных библиотеки для сбора данных.

+ BeautifulSoup4 - Легка в освоении, но достаточно медленная т.к. не особо оптимизированна в многопоточности.
+ Scrapy - Более оптимизированна и работает быстрее, но знания для неё нужны более обширные
  
Обе эти библиотеки используют один подход - сначало собираем данные со страницы, скачиваем страницу в формате lxml или html и парсим теги из нашей страницы.

+ Selenium - в отличии от остальных двух эмулирует работу пользователя, что позволяет обойти защиту от парсинга на различных сайтах, но из-за этого она работает гораздо медленнее. Данная библиотека используется как для парсинга, так и для тестинга данных.
  

</br>

___

## Библиотека NumPy
NumPy (Numeric Python) - предоставляет базовые методы для работы с большими массивами и матрицами. 
SciPy (Scientific Python) - расширяет функционал NumPy огромной коллекцией полезных алгоритмов.

Установка Numpy:
```bash
pip install numpy
```

установка SciPy:
```bash
pip install scipy
```
Импортировать библиотеку NumPy:
```python
import numpy as np
```
Дописывать as np не обязательно, это нужно для облегчения доступа к numpy объектам.  Вместо ```numpy.x``` нужно будет написать ```np.x```
___
</br>

### Массивы
Главный объект NumPy - это однородный многомерный массив. Чаще всего это одномерная последовательность или двумерная таблица, заполненные элементами одного типа, как правило числами, которые проиндексированы кортежем положительных целых чисел. В NumPy, элементы этого кортежа называются осями, а число осей рангом.

Самый простой способ создать массив в ручную его заполнить:

```python
a = np.array([11, 22, 33, 44, 55, 66, 77, 88, 99])
a
#array([11, 22, 33, 44, 55, 66, 77, 88, 99])
```
Для доступа к элементу можно воспользоваться привычным способом:
```python
a[2]
#33
```

Получить несколько значений с помощью индексов можно таким образом:

```python
a[[7, 0, 3, 3, 3, 0, 7]]
#array([88, 11, 44, 44, 44, 11, 88])
```

А вот еще пример, вместо индекса укажем логическое выражение:
```python
a[a > 50]
#array([55, 66, 77, 88, 99])
```
Векторизованные вычисления:

```python
2*a + 10
#array([ 32,  54,  76,  98, 120, 142, 164, 186, 208])
 
np.sin(a)**2 + np.cos(a)**2
#array([1., 1., 1., 1., 1., 1., 1., 1., 1.])
```

Векторизованные - означает, что все арифметические операции и математические функции выполняются сразу над всеми элементами массивов. А это в свою очередь означает, что нет никакой необходимости выполнять вычисления в цикле. В случае одномерного массива, можно подумать, что это не такой уж бонус, ведь есть генераторы. Но давайте перейдем к двумерным массивам:

```python
a = np.arange(12)
a
#array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11])

a = a.reshape(3, 4)
a
#array([[ 0,  1,  2,  3],
#       [ 4,  5,  6,  7],
#       [ 8,  9, 10, 11]])
```

Сейчас мы создали массив с помощью функции np.arange(), которая во многом аналогична функции range() языка Python. Затем, мы изменили форму массива с помощью метода reshape(), т.е. на самом деле создать этот массив мы могли бы и одной командой:
```python
a = np.arange(12).reshape(3, 4)
a
#array([[ 0,  1,  2,  3],
#       [ 4,  5,  6,  7],
#       [ 8,  9, 10, 11]])
```

Элемент из двумерного массива можно получить так:
```python
a[2][1]    #  равносильно команде a[2, 1]
#9
```
Транслирование массивов:
```python
b = [2, 3, 4, 5]

a*b
#array([[ 0,  3,  8, 15],
#       [ 8, 15, 24, 35],
#       [16, 27, 40, 55]])
```
Мы умножили каждый столбец из массива a на соответствующий элемент из массива b. Т.е. мы как бы транслировали (в какой-то степени можно сказать - растянули) массив b по массиву a.

Так-же мы можем сложить массивы:
```python
c = [[10], [20], [30]]

a + c
#array([[10, 11, 12, 13],
#       [24, 25, 26, 27],
#       [38, 39, 40, 41]])
```

В numpy есть много полезных функций:

```python
#Создадим массив со случайными числами от 0 до 15 c округлением до целого,
#размером 4*6
a = np.random.randint(0, 15, size = (4, 6))
a
#array([[ 9, 12,  5,  3,  1,  7],
#       [ 2, 12, 10, 11, 14,  9],
#       [ 4,  4,  9, 11,  5,  2],
#       [12,  8,  6,  8,  9,  3]])

#Возврат минимального элемента
a.min()
#1

a.min(axis = 0)    #  минимальные элементы по столбцам
#array([2, 4, 5, 3, 1, 2])

a.min(axis = 1)    #  минимальные элементы по строкам
#array([1, 2, 2, 3])



a.mean(axis = 0)    #  среднее по столбцам
#array([6.75, 9.  , 7.5 , 8.25, 7.25, 5.25])
 
np.std(a, axis = 1)    #  стандартное отклонение по строкам
#array([3.67045259, 3.77123617, 3.13138237, 2.74873708])

```


А вот пример создания трехмерных массивов:
```python
a = np.arange(48).reshape(4, 3, 4)
a
#array([[[ 0,  1,  2,  3],
#        [ 4,  5,  6,  7],
#        [ 8,  9, 10, 11]],

#       [[12, 13, 14, 15],
#        [16, 17, 18, 19],
#        [20, 21, 22, 23]],

#       [[24, 25, 26, 27],
#        [28, 29, 30, 31],
#        [32, 33, 34, 35]],

#       [[36, 37, 38, 39],
#        [40, 41, 42, 43],
#        [44, 45, 46, 47]]])
```
Визуализация аоможет догадаться, как устроена индексация трехмерных массивов. Например, если нам нужно вытащить из данного массива число 31, то достаточно выполнить:
```python
a[2][1][3]    #  или a[2, 1, 3]
#31
```

Размерность массива можно узнать командой:

```python
a.ndim
#3
```

Помочь с выяснением количеством элементов в каждой оси может команда:
```python
a.shape()
#(4,3,4)
```

Метод ndarray.size просто возвращает общее количество элементов массива:
```python
a.size
#48
```

Еще может встать такой вопрос - сколько памяти занимает наш массив? Иногда даже возникает такой вопрос - влезет ли результирующий массив после всех вычислений в оперативную память? Что бы на него ответить надо знать, сколько "весит" один элемент массива:

```python
a.itemsize    #  эквивалентно ndarray.dtype.itemsize
#4
```

ndarray.itemsize возвращает размер элемента в байтах. Теперь мы можем узнать сколько "весит" наш массив:

```python
a.size*a.itemsize
#192
```

Итого - 192 байта. На самом деле, размер занимаемой массивом памяти, зависит не только от количества элементов в нем, но и от испльзуемого типа данных:

```python
a.dtype
dtype('int32')
```

dtype('int32') - означает, что используется целочисленный тип данных, в котором для хранения одного числа выделяется 32 бита памяти. Но если мы выполним какие-нибудь вычисления с массивом, то тип данных может измениться:
```python
b = a/3.14

b
#array([[[ 0.        ,  0.31847134,  0.63694268,  0.95541401],
#        [ 1.27388535,  1.59235669,  1.91082803,  2.22929936],
#        [ 2.5477707 ,  2.86624204,  3.18471338,  3.50318471]],

#       [[ 3.82165605,  4.14012739,  4.45859873,  4.77707006],
#        [ 5.0955414 ,  5.41401274,  5.73248408,  6.05095541],
#        [ 6.36942675,  6.68789809,  7.00636943,  7.32484076]],

#       [[ 7.6433121 ,  7.96178344,  8.28025478,  8.59872611],
#        [ 8.91719745,  9.23566879,  9.55414013,  9.87261146],
#        [10.1910828 , 10.50955414, 10.82802548, 11.14649682]],

#       [[11.46496815, 11.78343949, 12.10191083, 12.42038217],
#        [12.7388535 , 13.05732484, 13.37579618, 13.69426752],
#        [14.01273885, 14.33121019, 14.64968153, 14.96815287]]])
 
b.dtype
dtype('float64')
```

Теперь у нас есть еще один массив - массив b и его тип данных 'float64' - вещественные числа (числа с плавающей точкой) длинной 64 бита. А его размер:
``` python
b.size*b.itemsize
#384
```
Тогда массив a - 192 байта, массив b - 384 байта. А в общем, получается, 576 байт - что очень мало для современных объемов оперативной памяти, но и реальные объемы данных, которые сейчас приходится обрабатывать совсем немаленькие.



___
</br>

## ***Библиотека Pandas.***
Библиотека Pandas содержит высокоуровневые структуры данных и средства управления ими, спроэктированные так, что-бы обеспечить простоту и высокую скорость анализа данных на Python

Библиотека построенна поверх NumPy, поэтому ей легко пользоваться в приложениях ориентируемых на NumPy
Для устоановки библиотеки:

```Python
import pandas as pd
from pandas import Series, DataFrame
```
- в коде строка pd - ссылка на pandas
- Объекты Series и DataFrame используются настолько часто, что полезно импортировать их в локальное пространство имён
- Чтобы начать работу с pandas, необходимо освоить две основные структуры данных: Series и DataFrame. Они конечно не являются универсальным решением, но всё же образуют основу большинста приложений.

___
</br>

### Series

Series - одномерный объект  - контейнер, содержащий набор данных(любого типа поддерживаемого NumPy) и ассоциированный с ним массив меток, который называется индексом

</br>

### DataFrame
Объект DataFrame представляет собою табличную структуру данных, состоящую из упорядоченной коллекции столбцов, причем типы значений (числовой, строковый, булевый и.д.) в разных столбцах могут различаться.
	В объекте DataFrame хранятся два индекса:
по строкам.
по столбцам.
Можно считать, что это словарь объектов Series.

Есть много способов сконструировать объект DataFrame, один из самых распространённых  – на основе словаря списков одинаковой длины или массивов NumPy

```python
data = {‘state’: [‘Ohio’, ‘Ohio’, ‘Ohio’, ‘Nevada’, ‘Nevada’, ‘Nevada’],
		‘year’: [2000, 2001, 2002, 2001, 2002, 2003],
		‘pop’: [1.5, 1.7, 3.6, 2.4, 2.9, 3.2]}
frame = pd.DataFrame(data)
```
Выглядеть это будет следующим образом:
```python
    state  year  pop
0    Ohio  2000  1.5
1    Ohio  2001  1.7
2    Ohio  2002  3.6
3  Nevada  2001  2.4
4  Nevada  2002  2.9
5  Nevada  2003  3.2
```

Если задать последовательность столбцов, то столбцы DataFrame расположатся в строго указанном порядке:

```python
frame = pd.DataFrame(data, columns = ['year', 'state', 'pop'])
   year   state  pop
0  2000    Ohio  1.5
1  2001    Ohio  1.7
2  2002    Ohio  3.6
3  2001  Nevada  2.4
4  2002  Nevada  2.9
5  2003  Nevada  3.2
```

Если запросить столбец которого нет, то он будет заполнятся значениями NaN:

```python
frame2 = pd.DataFrame(data, columns = ['year', 'state', 'pop', 'debt'],
                      index = ['one', 'two', 'three', 'four', 'five', 'six'])
```
```python
       year	state	pop	debt
one    2000	Ohio	1.5	NaN
two    2001	Ohio	1.7	NaN
three  2002	Ohio	3.6	NaN
four   2001	Nevada	2.4	NaN
five   2002	Nevada	2.9	NaN
six    2003	Nevada	3.2	NaN
```

Столбец DataFrame можно извлечь как объект Series, воспользовавшись нотацией словарей или с помощью атрибута.

Строки тоже можно извлечь, для этого есть метод loc:
```python
print (frame2.loc['three'])

year     2002
state    Ohio
pop       3.6
debt      NaN
Name: three, dtype: object
```
Столбцы можно модифицировать путём присваивания. Например, пустому столбцу можно ‘debt’ можно было бы присвоить скалярное значение или массив значений:

```python
frame2['debt']=16.5
print (frame2, '\n')
frame2['debt'] = [i for i in range(1,7)]
print (frame2)

      year   state  pop  debt
one    2000    Ohio  1.5  16.5
two    2001    Ohio  1.7  16.5
three  2002    Ohio  3.6  16.5
four   2001  Nevada  2.4  16.5
five   2002  Nevada  2.9  16.5
six    2003  Nevada  3.2  16.5 

       year   state  pop  debt
one    2000    Ohio  1.5     1
two    2001    Ohio  1.7     2
three  2002    Ohio  3.6     3
four   2001  Nevada  2.4     4
five   2002  Nevada  2.9     5
six    2003  Nevada  3.2     6
```

Когда столбцу присваивается список или массив, длина значения должна совпадать с длиной DataFrame

Если же присваивается объект Series, то он будет точно согласован с индексом DataFrame, а в пустые места будут вставлены значения NaN:

```python
val = pd.Series([-1.2, -1.5, -1.7], index = ['two', 'four', 'five'])
frame2['debt'] = val
print(frame2)


      year   state  pop  debt
one    2000    Ohio  1.5   NaN
two    2001    Ohio  1.7  -1.2
three  2002    Ohio  3.6   NaN
four   2001  Nevada  2.4  -1.5
five   2002  Nevada  2.9  -1.7
six    2003  Nevada  3.2   NaN
```

Присваивание несуществующему столбцу приводит к созданию нового столбца. Для удаления столбцов  служит ключевое слово del, как и в обычном словаре:

```python
frame2['eastern'] = frame2.state == 'Ohio'
print(frame2)

      year   state  pop  debt  eastern
one    2000    Ohio  1.5   NaN     True
two    2001    Ohio  1.7  -1.2     True
three  2002    Ohio  3.6   NaN     True
four   2001  Nevada  2.4  -1.5    False
five   2002  Nevada  2.9  -1.7    False
six    2003  Nevada  3.2   NaN    False

del frame2['eastern']
print(frame2.columns, '\n')
print(frame2)



Index(['year', 'state', 'pop', 'debt'], dtype='object') 



       year   state  pop  debt
one    2000    Ohio  1.5   NaN
two    2001    Ohio  1.7  -1.2
three  2002    Ohio  3.6   NaN
four   2001  Nevada  2.4  -1.5
five   2002  Nevada  2.9  -1.7
six    2003  Nevada  3.2   NaN
```


Ещё одна  распространённая форма данных – словарь словарей. Если передать его конструктору, то ключи внешнего словаря будут интерпретированы как столбцы, а ключи внутреннего словаря как индексы строк:

```python
pop = {'Nevada':{2001:2.4,2002:2.9}, 
       'Ohio':{2001:1.7, 2002:3.6, 2000:1.5}
       }
frame3 = pd.DataFrame(pop)
frame3


     Nevada  Ohio
2001     2.4   1.7
2002     2.9   3.6
2000     NaN   1.5
```

Словари объектов Series интерпретируются очень похоже:

```python
pdata = {'Ohio':frame3['Ohio'][:-1],
         'Nevada':frame3['Nevada'][:2]}
pd.DataFrame(pdata)



     Ohio  Nevada
2001   1.7     2.4
2002   3.6     2.9
```


Если у объектов, возвращаемых при обращении к атрибутам index и columns объекта DataFrame, устанавливается атрибут name, то он также выводится:
```python
frame3.index.name = 'year'; frame3.columns.name = 'state'
print(frame3)


state  Nevada  Ohio
year               
2001      2.4   1.7
2002      2.9   3.6
2000      NaN   1.5
```

Как и в случае с Series, атрибут values возвращает данные, хранящиеся в DataFrame, в виде двумерного массива ndarray:

```python
frame3.values

array([[2.4, 1.7],
       [2.9, 3.6],
       [nan, 1.5]])
```


Удаление нескольких строк с фильтром contains.
Для удаления строк можно воспользоваться методом .drop .
Довольно долго искал информацию как удалить строки с определённым условием, нашёл такой вариант:
```python
#В фильтре можно указывать несколько значений, разделять знаком “|”.
df_drop = df.loc[df["title"].str.contains("Фильтр" , case=False)]
prof = prof.drop(prof_drop.index)
```


Индексные объекты

В индексных объектах pandas хранятся метки вдоль осей и прочие метаданные (например имена осей)

Любой массив или иная последовательность меток, указанная при конструировании Series или DataFrame, преобразуется в объект index:

```python
obj = pd.Series(range(3), index=['a', 'b', 'c'])
index = obj.index
print(index)
print(index[:1])


Index(['a', 'b', 'c'], dtype='object')
Index(['a'], dtype='object')
```

Индексные объекты неизменяемы т.е. пользователь не может их модифицировать. Неизменяемость нужна для того что-бы несколько структур данных могли совместно использовать один и тот же индексный объект  не боясь его  повредить:

```python
labels = pd.Index(np.arange(3))
print(labels, '\n')
obj2 = pd.Series([1.5, -2.5, 0], index=labels)
print(obj2, '\n')
print(obj2.index == labels, '\n')
print(obj.index is labels)


Int64Index([0, 1, 2], dtype='int64') 

0    1.5
1   -2.5
2    0.0
dtype: float64 

[ True  True  True] 

False
```

Фундаментальные основы взаимодействия  с данными, хранящимися в объектах Series и DataFrame:
- Переиндексация
- Удаление элементов из оси
- Доступ по индексу, выборка и фильтрация
- Арифметические операции и выравнивание данных
- Операции между DataFrame и Series

Методы объектов pandas:
- Сортировка и ранжирование
- Индексы по осям с повторяющимися значениями
- Редукция и вычисление описательных статистик
- Уникальные значения, счётчики значений и членство



# Ссылки
* Библиотека Requests - https://python-scripts.com/requests
* NumPy в Python - https://habr.com/ru/post/352678/
* Массивы numpy - https://pyprog.pro/introduction.html
* Основные компоненты matplotlib - https://pyprog.pro/mpl/mpl_main_components.html
* 
