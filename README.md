FAQ по Derby 0.6 (на русском)
=====================

В этом репозитории я буду собирать часто задаваемые вопросы, а так же всевозможные тонкости по [derbyjs](http://derbyjs.com)

###Как я могу добавить что-то в FAQ?

Напишите мне @zag2art сообщаение через гитхаб или на почту (zag2art@gmail.com), а лучше сделайте форк, добавьте информацию и посылайте пулл-реквест.

## Запросы

### Как сделать реактинвый запрос к количеству элементов в коллекции (сами элементы мне не нужны)?


  var topicsCount = model.query('topics', {
    $count: true,
    $query: {
      active: true
    }
  });
  
  model.subscribe(tipicsCount, function(){
    topicsCount.refExtra('_page.topicsCount');
    
    // ...
  });
  

### Как подписаться на определенные элементы коллекции (у меня уже есть реактивный список id, нужных элементов)?

### Как получить не сами элементы коллекции, а только их id?
  var query = model.query('topics');
  
  model.subscribe(query, function(){
    query.refIds('_page.topicIds');
  });
  
  Но необходимо учитывать, что сама коллекция topics в данном случае будет копироваться в браузер, чтобы этого избежать используйте проекции. В серверной части derby, в server.js определите проекцию для коллекции topics:
  
  store.shareClient.backend.addProjection("topicIds", "topics", "json0", {id: true});
  
  Далее с проекцией можно работать, как с обычной коллекцией.
  
  var query = model.query('topicsIds');
  
  model.subscribe(query, function(){
    query.refIds('_page.topicIds');
  });
  

## Компоненты

### Как в шаблонах компонент получить доступ к корневой области видимости модели?

Как известно, в компонентах своя, изолированная область видимости, поэтому для доступа к корню необходимо использовать префикс #root, например:

<ul>
  {{each #root._page.topics as #topic}}
    <!-- ... -->
  {{/}}
<ul>

### Как в коде компонент получить доступ к корневой области видимости модели?

Как известно, в компонентах своя, изолированная область видимости, поэтому, чтобы обратиться к корневой модели, вместо model, здесь необходимо использовать model.root. Например:

function MyComponent() {}

MyComponent.prototype.init = function(model){
  // model.get('_page.topics') работать не будет
  var topics = model.root.get('_page.topics');
  // ...
  
}

MyComponent.prototype.onClick = function(event, element){
  var topics = this.model.root.get('_page.topics');
  // ...
  
}


## Модель

### Мне не нужны все поля коллекции в браузере, как получать только определенные поля (проекцию коллекции)?

В серверной части derby-приложения прописываются все проекции:

store.shareClient.backend.addProjection("topic_headers", "topics", "json0", {id: true, header: true, autor: true, createAt: true});
store.shareClient.backend.addProjection("users", "auth", "json0", {id: true, username: true, email: true});

Далее с проекциями users и topic_headers в derby-приложении можно работать, как с обычными коллекциями.

model.subscribe('users' function(){
  model.ref('_page.users', 'users');
  // ...
});

При создании проекций обратите внимание: поле id обязательно, пока поддерживается только белый список полей (перечисляем только поля, которые должны попасть в проекцию), так же поддерживется пока только возможность задавать поля первого уровня вложенности.

## База данных

### Говорят появилась возможность обходится без redis-а, используя только mongodb, как это сделать?

## Въюхи

### Как вставить в шаблон неэкранированный html или текст?

Необходимо использовать unescaped модификатор, например:

<header>
  {{topic.header}}
<header>

<!-- topic.unescapedTitle сделал только для примера, не знаю зачем такое может понадобиться -->
<article title="{{unesqaped topic.unescapedTitle}}">
  {{unesqaped topic.html}}
</article>

Учитывайте то, что это - потенциальная дыра в безопасности. Ваши данные должны быть полностью отчищены от опасных тегов, данные для атрибутов должны быть экранированы. В общем, прежде чем использовать такое, убедитесь, что вы понимаете что делаете.

### Как в шаблоне определенный блок сделать нереактивным (чтобы он не обновлялся сразу при изменении данных в модели)?

### Как в шаблоне сделать так, чтобы в определенный момент обновился нереактивный блок?

### Как привязать реактивную переменную к элементу select?

### Как привязать реактивную переменную к элементу input type=radio?

### Как привязать реактивную переменную к элементу textarea?

## Модули

### Как к derby подключить шаблонизатор jade?

### Какой модуль использовать для авторизации в derby?

### Как подключать клиентские скрипты к derby-приложение, например jquery?

## Написание модулей

### Как проверить, что код в derby-приложении выполняется на клиенте/на сервере?

### Как сделать require модуля в derby-приложении, код которого будет выполняться только на сервере?
