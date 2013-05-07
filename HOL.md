<a name="HOLTop"></a>

# Создание службы кэширования для облачной службы Windows Azure

* * *

<a name="Overview"></a>

## Общие сведения

Служба кэширования Windows Azure обеспечивает с&nbsp;наименьшими затратами распределенное кэширование в оперативной памяти для облачных служб. Служба кэширования, активированная для ролей облачных служб, позволяет использовать свободную память узлов службы для высокопроизводительного кэширования. Это уменьшает время отклика службы и увеличивает пропускную способность системы. Узлы кэша связаны с ролями облачных служб. Это позволяет оптимизировать время доступа и сократить количество вызовов внешних служб. В этом занятии вы научитесь активировать службу кэширования для ролей облачных служб и использовать ее для высокопроизводительного кэширования в оперативной памяти облачных служб.

<a name="Objectives"></a>

### Цели

В рамках этого практического занятия вы научитесь:

*   Легко и быстро активировать службу кэширования.
*   Кэшировать состояние сеанса Asp.Net с помощью службы кэширования.
*   Кэшировать ссылочные данные базы данных Windows Azure SQL Database с помощью службы кэширования.
*   Создавать расширяемый слой кэширования для облачных служб с возможностью его повторного использования.

При выполнении практического занятия вы изучите эти функции на примере простого приложения Asp.Net MVC4.

<a name="Prerequisites"></a>

### Необходимые требования

Для данного практического занятия потребуются:

*   [Microsoft Visual Studio 2012 Express для Web](http://www.microsoft.com/visualstudio/) или более новая версия.
*   [Windows Azure Tools для Microsoft Visual Studio, версия 1.8.](http://www.windowsazure.com/en-us/develop/downloads/)
*   Подписка на Windows Azure. [Зарегистрируйтесь для получения бесплатной пробной версии.](http://aka.ms/WATK-FreeTrial)
> **Примечание.** Для выполнения этого практического занятия необходимо использовать операционную систему Windows&nbsp;8.

<a name="Setup"></a>

### Установка

Прежде чем приступать к выполнению заданий этого практического занятия, настройте свою среду.

1.  Откройте окно Windows Explorer (Проводник Windows) и перейдите в **корневой каталог** практического занятия.
2.  Чтобы настроить рабочую среду и установить фрагменты кода Visual Studio, необходимые для выполнения заданий этого практического занятия, щелкните правой кнопкой мыши файл **Setup.cmd** и запустите его от имени администратора.
3.  При появлении диалогового окна User Account Control (Управление учетными записями пользователей) подтвердите действие, чтобы продолжить процесс установки.
> **Примечание.** В ходе этого практического занятия, прежде чем приступать к установке, необходимо проверить все зависимости.
> 
> Для выполнения этого практического занятия необходимо использовать базу данных Windows Azure SQL. В ходе автоматического построения базы данных Northwind2 файл **Setup.cmd** запросит данные о вашей учетной записи базы данных Windows Azure SQL. Обновите строку подключения NorthwingEntities в конфигурационном файле, чтобы создать ссылку на вашу базу данных для каждого решения.
> 
> Настройте брандмауэр, чтобы обеспечить доступ к серверу базы данных Windows Azure SQL для списка IP-адресов, указанных в настройках учетной записи базы данных Windows Azure SQL. По умолчанию брандмауэр запрещает все подключения. Чтобы разрешить подключение к базе данных, **настройте белый список**. Изменения, внесенные в настройки брандмауэра, вступят в силу через несколько секунд. Для получения дополнительных сведений о настройке учетной записи базы данных Windows Azure SQL см.&nbsp;упражнение&nbsp;1 практического занятия &laquo;Общие сведения о базе данных Windows Azure SQL&raquo; этого учебного комплекта.
> 
> ![Настройка базы данных SQL](Images/sql-database-setup.png?raw=true "Настройка базы данных Windows Azure SQL")
> 
> _Настройка базы данных Windows Azure SQL_

<a name="CodeSnippets"></a>

### Использование фрагментов кода

Этот документ содержит фрагменты кода, которые вы будете использовать в процессе выполнения заданий. Код необязательно вводить вручную. Для вашего удобства мы подготовили фрагменты кода Visual Studio, которые можно использовать непосредственно в Visual Studio&nbsp;2012. 

> **Примечание.** Для каждого упражнения предоставляется исходное решение, расположенное в папке Begin. Это позволяет выполнять каждое упражнение независимо от других. Не забывайте о том, что фрагменты кода, которые добавляются в процессе выполнения заданий, в исходных решениях отсутствуют. Такие решения могут находиться в нерабочем состоянии до тех пор, пока упражнение не завершено. В папке End содержится решение Visual Studio с кодом, который представляет собой результат выполнения соответствующего упражнения. Вы можете использовать эти решения в качестве дополнительной помощи для данного практического занятия.

* * *

<a name="Exercises"></a>

## Упражнения

Это практическое занятие содержит следующие упражнения:

1.  [Активация службы кэширования для состояния сеанса.](#Exercise1)
2.  [Кэширование данных с помощью службы кэширования.](#Exercise2)
3.  [Создание расширяемого слоя кэширования с возможностью его повторного использования.](#Exercise3)

Ориентировочная продолжительность занятия: **60 минут**.

> **Примечание.** При первом запуске Visual Studio необходимо выбрать одну из предварительно созданных коллекций параметров. Каждая из этих коллекций поддерживает определенный стиль разработки и определяет макеты интерфейса, поведение редактора, фрагменты кода IntelliSense и элементы диалогового окна. В этом документе описаны действия, необходимые для выполнения определенной задачи в Visual Studio при использовании коллекции **General Development Settings** (Общие параметры разработки). Обратите внимание: процедуры могут отличаться, если вы выберете другую коллекцию параметров для вашей среды разработки.

<a name="Exercise1"></a>

### Упражнение 1. Активация службы кэширования для состояния сеанса

В этом упражнении вы научитесь использовать поставщика состояния сеанса для службы кэширования в качестве механизма хранения данных о состоянии сеанса вне процесса. Для этого мы рассмотрим работу приложения Azure Store (образец корзины интернет-магазина, созданный с помощью Asp.Net MVC4). Мы запустим приложение в эмуляторе вычислений, а затем изменим его, чтобы использовать службу Windows Azure Cache в качестве хранилища для состояния сеанса Asp.Net. Мы начнем со стартового решения и изучим его с помощью принятого по умолчанию внутрипроцессного поставщика состояния сеанса Asp.Net. Затем мы добавим ссылки на сборки кэша и настроим поставщика состояния сеанса так, чтобы содержимое корзины хранилось в распределенном кластере кэша, доступ к которому предоставляется службой кэширования.

<a name="Ex1Task1"></a>

#### Задание 1. Запуск образца сайта Azure Store в эмуляторе вычислений

В этом задании вы запустите приложение Azure Store в эмуляторе вычислений, используя заданного по умолчанию поставщика состояний сеанса. Затем вы измените поставщика, чтобы использовать преимущества службы кэширования Windows Azure.

1.  Запустите **Microsoft Visual Studio 2012 Express for Web** от имени администратора.
2.  Откройте решение **Begin** из каталога **Source\Ex1-CacheSessionState\Begin**.

    > **Важно!**     Перед запуском решения убедитесь, что начальный проект установлен. Для проектов MVC начальная страница должна быть пустой.
> 
> Чтобы указать начальный проект, откройте **обозреватель решений**, щелкните правой кнопкой мыши проект **CloudShop.Azure** и выберите **Set as StartUp Project** (Использовать в качестве начального проекта).
> 
> Чтобы настроить начальную страницу, откройте **обозреватель решений**, щелкните правой кнопкой мыши проект **CloudShop** и выберите **Properties** (Свойства). В окне **Properties** выберите вкладку **Web** (Интернет). В списке **Start Action** (Начальное действие) выберите **Specific Page** (Определенная страница). Оставьте это поле пустым.
3.  Откройте файл **Web.config** и обновите строку подключения _NorthwindEntities_, создав ссылку на базу данных. Замените строки **[YOUR-SQL-DATABASE-SERVER-ADDRESS]**, **[SQL-DATABASE-USERNAME]** и **[SQL-DATABASE-PASSWORD]** в разделе connectionStrings на имя сервера Windows Azure SQL Database, имя пользователя с правами администратора и пароль администратора, который вы использовали для регистрации на портале и создания базы данных при начальной установке.

    > **Примечание.** Следуя инструкции по установке, необходимо создать копию базы данных Northwind2 для вашей учетной записи базы данных Windows Azure SQL, а также настроить брандмауэр базы данных Windows Azure SQL.
4.  Нажмите клавиши **CTRL**+**F5**, чтобы создать и запустить приложение в эмуляторе вычислений без отладки.
    > **Примечание.** Убедитесь, что приложение запущено в режиме без отладки. В режиме отладки вы не сможете перезапустить веб-роль.
5.  Изучите страницу **Products** (Продукты)&nbsp;&mdash; основную страницу приложения. На ней отображается список продуктов, полученный из базы данных Windows Azure SQL.

![Страница продуктов Azure Store](Images/azure-store-products-page.png?raw=true "Страница продуктов Azure Store")

    _Страница продуктов Azure Store_

6.  Выберите продукт из списка и щелкните **Add item to cart** (Добавить в корзину). Таким образом в корзину можно добавить несколько объектов.

7.  Щелкните ссылку **Checkout** (Извлечь), чтобы просмотреть содержимое корзины. Проверьте, содержит ли список выбранные вами элементы. Эти элементы хранятся в текущем сеансе.

![На странице Checkout отображается содержимое корзины](Images/checkout-page-showing-the-contents-of-the-sho.png?raw=true "На странице Checkout отображается содержимое корзины")

    _На странице Checkout отображается содержимое корзины_

8.  Перейдите обратно на страницу **Products**.

9.  Щелкните ссылку **Recycle** (Перезапустить). Эта ссылка инициирует перезапуск веб-роли. Щелкнув эту ссылку, вы удалите содержимое страницы Products.

10.  Откройте окно **Compute Emulator** (Эмулятор вычислений) и изучите процесс перезапуска веб-роли эмулятором.

![Приостановка экземпляра роли службы](Images/suspending-the-service-role-instance.png?raw=true "Приостановка экземпляра роли службы")

    _Веб-роль перезапущена_

11.  Снова откройте браузер, удалите _/Home/Recycle_ из адресной строки и нажмите клавишу ВВОД, чтобы перезапустить сайт. Через некоторое время откроется пустая страница **Products**.

12.  Перейдите на страницу **Checkout**. Обратите внимание, что поле заказа теперь пусто.

    > **Примечание.** Сейчас приложение использует состояние сеанса внутри процесса, которое хранится в оперативной памяти. Остановка экземпляра службы приведет к удалению всей информации о состоянии сеанса, включая содержимое корзины. В следующем задании мы настроим приложение так, чтобы состояние сеанса хранилось службой кэширования Windows Azure. Это позволит приложению сохранять состояние сеанса при перезапусках и использовать его для нескольких экземпляров ролей, размещающих приложение.
13.  Закройте окно браузера, чтобы остановить приложение.

<a name="Ex1Task2"></a>

#### Задание 2. Добавление выделенной роли кэширования

В этом задании вы создадите новую рабочую роль для использования в качестве выделенного узла кэширования. Все остальные веб-роли и рабочие роли облачной службы смогут обращаться к службе кэширования, которую размещает эта роль. В облачной службе можно использовать несколько таких выделенных рабочих ролей. Кроме того, для любой из существующих ролей можно включить службу кэширования и выделить часть памяти виртуальной машины для использования в качестве кэша. 

1.  В обозревателе решений разверните узел **CloudShop.Azure** и щелкните правой кнопкой мыши пункт **Roles** (Роли). Выберите **Add**-&gt;**New Worker Role Project...*** (Добавить&nbsp;&mdash; Создать проект рабочей роли).
2.  В диалоговом окне **Add New Role Project** (Добавить новый проект роли) выберите шаблон **Cache Worker Role** (Рабочая роль кэширования). Присвойте роли имя **CacheWorkerRole** и щелкните **Add** (Добавить).
> **Примечание.** Все узлы кэширования облачной службы совместно используют состояния среды выполнения с&nbsp;помощью хранилища BLOB-объектов Windows Azure. По умолчанию рабочая роль кэширования настроена на использование хранилища разработки. Эти настройки можно изменить на вкладке **Caching** (Кэширование) страницы свойств роли.

<a name="Ex1Task3"></a>

#### Задание 3. Изменение состояния сеанса с помощью службы кэширования Windows Azure

В этом задании мы настроим поставщика состояния сеанса так, чтобы в качестве механизма хранения использовалась служба Windows Azure. Для этого необходимо добавить некоторые сборки в проект **CloudShop** и обновить соответствующие настройки в файле **Web.config**. 

1.  В среде Visual Studio&nbsp;2012 Express for Web откройте **Package manager Console** (Консоль диспетчера пакетов) в меню **Tools**-&gt;**Library package Manager**-&gt;**Package Manager Console** (Инструменты&nbsp;&mdash; Диспетчер пакетов библиотеки&nbsp;&mdash; Консоль диспетчера пакетов).

2.  Убедитесь, что в раскрывающемся списке **Default project** (Проект по умолчанию) выбран пункт **CloudShop**. Чтобы установить пакет Nuget для службы кэширования, введите следующую команду:
<span class="codelanguage">PowerShell</span>

        Install-package Microsoft.WindowsAzure.Caching    `</pre>
3.  Откройте файл **Web.config**, расположенный в корневом каталоге проекта **CloudShop**.
4.  Замените строку **[cache cluster role name]** (имя роли кластера кэша) на **CacheWorkerRole**.
    <!--mark: 4   -->
    <span class="codelanguage">XML</span><pre>`<span style="color:#0000FF">&lt;</span><span style="color:#800000">dataCacheClients</span><span style="color:#0000FF">&gt;</span>
     <span style="color:#0000FF">&lt;</span><span style="color:#800000">tracing</span> <span style="color:#FF0000">sinkType</span>=<span style="color:#0000FF">&quot;DiagnosticSink&quot;</span> <span style="color:#FF0000">traceLevel</span>=<span style="color:#0000FF">&quot;Error&quot;</span> <span style="color:#0000FF">/&gt;</span>
      <span style="color:#0000FF">&lt;</span><span style="color:#800000">dataCacheClient</span> <span style="color:#FF0000">name</span>=<span style="color:#0000FF">&quot;default&quot;</span><span style="color:#0000FF">&gt;</span>
    **   <span style="color:#0000FF">&lt;</span><span style="color:#800000">autoDiscover</span> <span style="color:#FF0000">isEnabled</span>=<span style="color:#0000FF">&quot;true&quot;</span> <span style="color:#FF0000">identifier</span>=<span style="color:#0000FF">&quot;CacheWorkerRole&quot;</span> <span style="color:#0000FF">/&gt;</span>**
      <span style="color:#008000">&lt;!--&lt;localCache isEnabled=&quot;true&quot; sync=&quot;TimeoutBased&quot; objectCount=&quot;100000&quot; ttlValue=&quot;300&quot; /&gt;--&gt;</span>
      <span style="color:#0000FF">&lt;/</span><span style="color:#800000">dataCacheClient</span><span style="color:#0000FF">&gt;</span>
    <span style="color:#0000FF">&lt;/</span><span style="color:#800000">dataCacheClients</span><span style="color:#0000FF">&gt;</span>
      ...
    `</pre>
5.  Добавьте настройки нового поставщика состояния сеанса в раздел, обозначенный тегом System.Web.    <span class="codelanguage">XML</span><pre>`<span style="color:#0000FF">&lt;</span><span style="color:#800000">system.Web</span><span style="color:#0000FF">&gt;</span>
    ...
    <span style="color:#0000FF">&lt;</span><span style="color:#800000">sessionState</span> <span style="color:#FF0000">mode</span>=<span style="color:#0000FF">&quot;Custom&quot;</span> <span style="color:#FF0000">customProvider</span>=<span style="color:#0000FF">&quot;NamedCacheBProvider&quot;</span><span style="color:#0000FF">&gt;</span>
      <span style="color:#0000FF">&lt;</span><span style="color:#800000">providers</span><span style="color:#0000FF">&gt;</span>
        <span style="color:#0000FF">&lt;</span><span style="color:#800000">add</span> <span style="color:#FF0000">cacheName</span>=<span style="color:#0000FF">&quot;default&quot;</span> <span style="color:#FF0000">name</span>=<span style="color:#0000FF">&quot;NamedCacheBProvider&quot;</span>             <span style="color:#FF0000">dataCacheClientName</span>=<span style="color:#0000FF">&quot;default&quot;</span> <span style="color:#FF0000">applicationName</span>=<span style="color:#0000FF">&quot;MyApp&quot;</span>             <span style="color:#FF0000">type</span>=<span style="color:#0000FF">&quot;Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider, Microsoft.Web.DistributedCache&quot;</span> <span style="color:#0000FF">/&gt;</span>
      <span style="color:#0000FF">&lt;/</span><span style="color:#800000">providers</span><span style="color:#0000FF">&gt;</span>
    <span style="color:#0000FF">&lt;/</span><span style="color:#800000">sessionState</span><span style="color:#0000FF">&gt;</span>
    ...
    <span style="color:#0000FF">&lt;/</span><span style="color:#800000">system.web</span><span style="color:#0000FF">&gt;</span>
    `</pre>
6.  Нажмите клавиши **CTRL+S**, чтобы сохранить изменения, внесенные в файл **Web.config**.

    <a name="Ex1Task4"></a>

    #### Задание 4. Проверка

1.  Нажмите клавиши **CTRL+F5**, чтобы создать и запустить приложение. Дождитесь открытия страницы **Products** в браузере.2.  Выберите продукт из списка и щелкните Add item to cart (Добавить в корзину). Повторите действие и добавьте в корзину несколько элементов.
3.  Щелкните ссылку **Checkout**, чтобы просмотреть содержимое корзины. Проверьте, содержит ли список выбранные вами элементы.
4.  Перейдите обратно на страницу **Products** и щелкните ссылку &quot;Recycle&quot;.
5.  Выбрав пункт меню **Show Compute Emulator UI** (Отобразить интерфейс эмулятора вычислений), вы можете наблюдать перезапуск веб-роли.6.  Снова откройте браузер, удалите _/Home/Recycle_ из адресной строки и нажмите клавишу ВВОД, чтобы перезапустить сайт.
7.  **Страница Products** должна отобразиться корректно. Перейдите на страницу **Checkout**. Обратите внимание, что содержимое заказа не изменилось. При использовании поставщика кэша Windows Azure состояние сеанса хранится вне экземпляра роли и сохраняется при перезапуске приложения.
    > **Примечание.** Для приложения, размещенного на нескольких серверах или в нескольких экземплярах роли Windows Azure (в этом случае обращенные к приложению запросы распределяются балансировщиком нагрузки), данные о сеансе клиента сохранятся вне зависимости от того, какой экземпляр отвечает на запрос.
8.  Закройте окно браузера, чтобы остановить приложение.

    <a name="Exercise2"></a>

    ### Упражнение 2. Кэширование данных с помощью службы кэширования Windows Azure

    В этом упражнении мы рассмотрим использование службы кэширования Windows Azure для кэширования результатов запросов к базе данных Windows Azure SQL. Мы продолжим работу с решением из предыдущего упражнения. Единственное отличие состоит в изменении домашней страницы&nbsp;&mdash; теперь она отображает время, затраченное на получение списка продуктов из каталога, и содержит ссылку, позволяющую включить и отключить функцию кэширования. При выполнении упражнения мы дополним программный код доступа к данным простой реализацией функции кэширования. Кэширование происходит по стандартной схеме. Сначала программа пытается получить данные из кэша. При отсутствии запрашиваемых данных в кэше программа обращается к базе данных и кэширует результаты запроса.

    <a name="Ex2Task1"></a>

    #### Задание 1. Кэширование данных, предоставленных службой SQL Reporting

    Чтобы использовать службу кэширования Windows Azure, необходимо создать объект **DataCacheFactory**. В этом объекте хранятся данные о подключении к кластеру кэша. Настройки объекта задаются программно или считываются из файла конфигурации. Обычно в приложении создается экземпляр класса фабрики, который используется на протяжении всего времени существования приложения. Чтобы сохранить данные в кэше, необходимо запросить экземпляр **DataCache** у фабрики **DataCacheFactory** и использовать его для добавления данных в кэш и получения данных из кэша. В этом задании мы обновим программный код доступа к данным и обеспечим кэширование результатов запросов к базе данных Windows Azure SQL с помощью службы кэширования Windows Azure. 

1.  Запустите **Microsoft Visual Studio 2012 Express for Web** от имени администратора.
2.  Откройте решение **Begin** из каталога **Source\Ex2-CachingData\Begin**.
    > **Важно!** Перед запуском решения убедитесь, что начальный проект установлен. Для проектов MVC начальная страница должна быть пустой. Чтобы указать начальный проект, откройте **обозреватель решений**, щелкните правой кнопкой мыши проект **CloudShop.Azure** и выберите **Set as StartUp Project** (Использовать в качестве начального проекта). Чтобы настроить начальную страницу, откройте **обозреватель решений**, щелкните правой кнопкой мыши проект **CloudShop** и выберите **Properties** (Свойства). В окне **Properties** выберите вкладку **Web** (Интернет). В списке **Start Action** (Начальное действие) выберите **Specific Page** (Определенная страница). Оставьте это поле пустым.
3.  Откройте файл **Web.config** и обновите строку подключения _NorthwindEntities_, создав ссылку на базу данных. Замените строки **[YOUR-SQL-DATABASE-SERVER-ADDRESS]**, **[SQL-DATABASE-USERNAME]** и **[SQL-DATABASE-PASSWORD]** на имя сервера Windows Azure SQL Database, имя пользователя с правами администратора и пароль администратора, который вы использовали для регистрации на портале и создания базы данных при начальной установке.
    > **Примечание.**     Следуя инструкции по установке, необходимо создать копию базы данных Northwind2 для вашей учетной записи базы данных Windows Azure SQL, а также настроить брандмауэр базы данных Windows Azure SQL.
4.  Откройте файл **ProductsRepository.cs**, расположенный в каталоге **Services** проекта **CloudShop**.
5.  Добавьте директиву пространства имен для **Microsoft.ApplicationServer.Caching**.
    <!--mark: 5   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">using</span> System;
    <span style="color:#0000FF">using</span> System.Collections.Generic;
    <span style="color:#0000FF">using</span> System.Linq;
    <span style="color:#0000FF">using</span> CloudShop.Models;
    **<span style="color:#0000FF">using</span> Microsoft.ApplicationServer.Caching;**
    ...
    `</pre>
6.  Добавьте выделенный код в класс **ProductsRepository**. Этот код определяет конструктор и объявляет статическую переменную для экземпляра объекта **DataCacheFactory** в дополнение к логической переменной экземпляра, обеспечивая включение и выключение функции кэширования.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex2-ProductsRepository constructor-CS_)
    <!--mark: 3-9   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> ProductsRepository : IProductRepository    {
    **  <span style="color:#0000FF">private</span> <span style="color:#0000FF">static</span> DataCacheFactory cacheFactory = <span style="color:#0000FF">new</span> DataCacheFactory();**
    **  <span style="color:#0000FF">private</span> <span style="color:#0000FF">bool</span> enableCache = <span style="color:#0000FF">false</span>;**
    **  <span style="color:#0000FF">public</span> ProductsRepository(<span style="color:#0000FF">bool</span> enableCache)**
    **  {**
    **    <span style="color:#0000FF">this</span>.enableCache = enableCache;**
    **  }**
      <span style="color:#0000FF">public</span> List&lt;<span style="color:#0000FF">string</span>&gt; GetProducts()
      {
        ...
      }
    }
    `</pre>
    > **Примечание.** Объект **DataCacheFactory** объявлен как статический и используется на протяжении всего времени существования приложения.
7.  Найдите метод **GetProducts** и добавьте в него следующий (выделенный) код сразу после строки, объявляющей локальную переменную **products**.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex2-GetProducts read cache-CS_)
    <!--mark: 8-30   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> ProductsRepository : IProductRepository
    {
      ...
      <span style="color:#0000FF">public</span> List&lt;<span style="color:#0000FF">string</span>&gt; GetProducts()
      {
        List&lt;<span style="color:#0000FF">string</span>&gt; products = <span style="color:#0000FF">null</span>;
    **    DataCache dataCache = <span style="color:#0000FF">null</span>;**
    **    <span style="color:#0000FF">if</span> (<span style="color:#0000FF">this</span>.enableCache)**
    **    {**
    **      <span style="color:#0000FF">try</span>**
    **      {**
    **        dataCache = cacheFactory.GetDefaultCache();**
    **        products = dataCache.Get(<span style="color:#8B0000">&quot;products&quot;</span>) <span style="color:#0000FF">as</span> List&lt;<span style="color:#0000FF">string</span>&gt;;**
    **        <span style="color:#0000FF">if</span> (products != <span style="color:#0000FF">null</span>)**
    **        {**
    **          products[0] = <span style="color:#8B0000">&quot;(from cache)&quot;</span>;**
    **          <span style="color:#0000FF">return</span> products;**
    **        }**
    **      }**
    **      <span style="color:#0000FF">catch</span> (DataCacheException ex)**
    **      {**
    **        <span style="color:#0000FF">if</span> (ex.ErrorCode != DataCacheErrorCode.RetryLater)**
    **        {**
    **          <span style="color:#0000FF">throw</span>;**
    **        }**
    **        <span style="color:#008000">// игнорировать временные сбои</span>**
    **      }**
    **    }**
        NorthwindEntities context = <span style="color:#0000FF">new</span> NorthwindEntities();
        <span style="color:#0000FF">try</span>
        {
          <span style="color:#0000FF">var</span> query = from product <span style="color:#0000FF">in</span> context.Products
                      select product.ProductName;
          products = query.ToList();
        }
        <span style="color:#0000FF">finally</span>
        {
          <span style="color:#0000FF">if</span> (context != <span style="color:#0000FF">null</span>)
          {
            context.Dispose();
          }
        }
        <span style="color:#0000FF">return</span> products;
      }
    }
    `</pre>
    > **Примечание.** Добавленный фрагмент кода использует объект **DataCacheFactory** для получения экземпляра объекта кэширования, принятого по умолчанию. Затем производится попытка получения данных из этого кэша с&nbsp;помощью ключа со значением &quot;_products_&quot;. Если в кэше содержится объект с запрошенным ключом, то список возвращается. При этом текст первой записи будет содержать сведения о том, что список получен из кэша. Временные сбои службы кэширования Windows Azure обрабатываются как промахи кэша. В этом случае данные будут извлечены не из кэша, а из источника данных.
8.  Теперь добавьте выделенный блок кода в метод **GetProducts** непосредственно перед расположенной в конце метода строкой, возвращающей список **products**.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex2-GetProducts write cache-CS_)
    <!--mark: 30-35   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> ProductsRepository : IProductRepository
    {
        ...
        <span style="color:#0000FF">public</span> List&lt;<span style="color:#0000FF">string</span>&gt; GetProducts()
        {
            List&lt;<span style="color:#0000FF">string</span>&gt; products = <span style="color:#0000FF">null</span>;
            DataCache dataCache = <span style="color:#0000FF">null</span>;
            <span style="color:#0000FF">if</span> (<span style="color:#0000FF">this</span>.enableCache)
            {
              ...
            }
            NorthwindEntities context = <span style="color:#0000FF">new</span> NorthwindEntities();
            <span style="color:#0000FF">try</span>
            {
              <span style="color:#0000FF">var</span> query = from product <span style="color:#0000FF">in</span> context.Products
                         select product.ProductName;
              products = query.ToList();
            }
            <span style="color:#0000FF">finally</span>
            {
              <span style="color:#0000FF">if</span> (context != <span style="color:#0000FF">null</span>)
              {
                context.Dispose();
              }
            }
    **        products.Insert(0, <span style="color:#8B0000">&quot;(from data source)&quot;</span>);**
    **        <span style="color:#0000FF">if</span> (<span style="color:#0000FF">this</span>.enableCache &amp;&amp; dataCache != <span style="color:#0000FF">null</span>)**
    **        {**
    **          dataCache.Add(<span style="color:#8B0000">&quot;products&quot;</span>, products, TimeSpan.FromSeconds(30));**
    **        }**
            <span style="color:#0000FF">return</span> products;
        }
    }
    `</pre>
    > **Примечание.** Добавленный код сохраняет в кэше результаты запроса к источнику данных и определяет политику окончания срока действия, обеспечивая удаление данных из кэша через 30&nbsp;секунд.

    <a name="Ex2Task2"></a>

    #### Задание 2. Измерение задержки доступа к данным

    В этом задании мы обновим приложение и создадим пользовательский интерфейс для включения и выключения функции кэширования, а также для отображения времени, затраченного на получение данных каталога. Это позволит сравнить задержку получения данных из кэша и источника данных.

1.  Откройте файл **HomeController.cs** из каталога **Controllers** и добавьте **System.Diagnostics**, используя директиву в начале файла.
    <!-- mark:1    -->
    <span class="codelanguage">C#</span><pre>`**<span style="color:#0000FF">using</span> System.Diagnostics;**
    `</pre>
2.  Найдите действие **Index**. Перейдите к строкам, создающим экземпляр **ProductsRepository** и вызывающим его метод **GetProducts**. Замените эти строки выделенным фрагментом кода, как показано ниже.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex2-GetProducts latency-CS_)
    <!--mark: 9-17; strike:6-8   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> HomeController : Controller
    {
      ...      <span style="color:#0000FF">public</span> ActionResult Index()
      {
    <span class="strikeLine" style="text-decoration:line-through;">    Services.IProductRepository productRepository =</span>
    <span class="strikeLine" style="text-decoration:line-through;">        <span style="color:#0000FF">new</span> Services.ProductsRepository();</span>
    <span class="strikeLine" style="text-decoration:line-through;">    <span style="color:#0000FF">var</span> products = productRepository.GetProducts();</span>
    **    <span style="color:#0000FF">bool</span> enableCache = (<span style="color:#0000FF">bool</span>)<span style="color:#0000FF">this</span>.Session[<span style="color:#8B0000">&quot;EnableCache&quot;</span>];**
    **    <span style="color:#008000">// получить каталог продуктов из репозитория и измерить затраченное время</span>**
    **    Services.IProductRepository productRepository =**
    **        <span style="color:#0000FF">new</span> Services.ProductsRepository(enableCache);**
    **    Stopwatch stopWatch = <span style="color:#0000FF">new</span> Stopwatch();**
    **    stopWatch.Start();**
    **    <span style="color:#0000FF">var</span> products = productRepository.GetProducts();**
    **    stopWatch.Stop();**
        <span style="color:#008000">// добавить все продукты, не сохраненные в сеансе</span>
        <span style="color:#0000FF">var</span> itemsInSession = <span style="color:#0000FF">this</span>.Session[<span style="color:#8B0000">&quot;Cart&quot;</span>] <span style="color:#0000FF">as</span> List&lt;<span style="color:#0000FF">string</span>&gt; ?? <span style="color:#0000FF">new</span> List&lt;<span style="color:#0000FF">string</span>&gt;();
        <span style="color:#0000FF">var</span> filteredProducts = products.Where(item =&gt; !itemsInSession.Contains(item));
        IndexViewModel model = <span style="color:#0000FF">new</span> IndexViewModel()
        {
          Products = filteredProducts
        };
        <span style="color:#0000FF">return</span> View(model);
      }
      ...
    }
    `</pre>
3.  В том же методе найдите код, создающий экземпляр **IndexViewModel**, и замените его инициализацию следующим (выделенным) блоком.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex2-IndexViewModel initialization-CS_)
    <!--mark: 22-25   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> HomeController : Controller
    {
      ...      <span style="color:#0000FF">public</span> ActionResult Index()
      {
        <span style="color:#0000FF">bool</span> enableCache = (<span style="color:#0000FF">bool</span>)<span style="color:#0000FF">this</span>.Session[<span style="color:#8B0000">&quot;EnableCache&quot;</span>];
        <span style="color:#008000">// получить каталог продуктов из репозитория и измерить затраченное время</span>
        Services.IProductRepository productRepository =
            <span style="color:#0000FF">new</span> Services.ProductsRepository(enableCache);
        Stopwatch stopWatch = <span style="color:#0000FF">new</span> Stopwatch();
        stopWatch.Start();
        <span style="color:#0000FF">var</span> products = productRepository.GetProducts();
        stopWatch.Stop();
        <span style="color:#008000">// добавить все продукты, не сохраненные в сеансе</span>
        <span style="color:#0000FF">var</span> itemsInSession = <span style="color:#0000FF">this</span>.Session[<span style="color:#8B0000">&quot;Cart&quot;</span>] <span style="color:#0000FF">as</span> List&lt;<span style="color:#0000FF">string</span>&gt; ?? <span style="color:#0000FF">new</span> List&lt;<span style="color:#0000FF">string</span>&gt;();
        <span style="color:#0000FF">var</span> filteredProducts = products.Where(item =&gt; !itemsInSession.Contains(item));
        IndexViewModel model = <span style="color:#0000FF">new</span> IndexViewModel()
        {
    **      Products = filteredProducts,**
    **      ElapsedTime = stopWatch.ElapsedMilliseconds,**
    **      IsCacheEnabled = enableCache,**
    **      ObjectId = products.GetHashCode().ToString()**
        };
        <span style="color:#0000FF">return</span> View(model);
      }
      ...
    }
    `</pre>
    > **Примечание.** Добавленные в модель представления элементы позволяют выводить на экран время, затраченное на загрузку каталога продуктов из репозитория, флаг, отображающий включение функции кэширования, и идентификатор объекта каталога, возвращаемый при вызове **GetProducts**. Представление отображает идентификатор объекта. Это дает возможность определить, изменились ли данные, полученные при обращении к каталогу. Эта функция будет использована далее при включении локального кэша.
4.  Чтобы включать и отключать функцию кэширование через интерфейс приложения, добавим к объекту **HomeController** новый метод действия.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex2-EnableCache method-CS_)
    <!--mark: 4-8   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> HomeController : Controller
    {
      ...
    **  <span style="color:#0000FF">public</span> ActionResult EnableCache(<span style="color:#0000FF">bool</span> enabled)**
    **  {**
    **    <span style="color:#0000FF">this</span>.Session[<span style="color:#8B0000">&quot;EnableCache&quot;</span>] = enabled;**
    **    <span style="color:#0000FF">return</span> RedirectToAction(<span style="color:#8B0000">&quot;Index&quot;</span>);**
    **  }**
     }
    `</pre>
5.  Нажмите клавишу **F5**, чтобы выполнить построение и запустить приложение в эмуляторе вычислений.
    > **Примечание.** Рекомендуется протестировать программный код в среде Windows Azure. При запуске приложения в эмуляторе вычислений, для обращения к базе данных Windows Azure SQL и службе кэширования Windows Azure, необходимо выполнение запросов к ресурсам, расположенным за пределами вашей локальной сети. При определенном географическом расположении вашей локальной сети выполнение запросов к этим службам может занять много времени. В этом случае задержка не позволит наглядно продемонстрировать преимущества использования кэша. При развертывании приложения в среде Windows Azure оно будет размещено в том же центре обработки данных, что и служба кэширования базы данных Windows Azure SQL. Задержка при выполнении запроса в этом случае будет намного меньше. Это позволит получить более наглядные результаты.
6.  При запуске приложения функция кэширования первоначально отключена. Обновите страницу. Обратите внимание, что внизу страницы отображается время, затраченное на получение каталога продуктов. Первый элемент списка содержит сведения о том, что приложение получило данные каталога из источника данных.
    > **Примечание.** Для получения корректных данных может потребоваться многократное обновление страницы. При обработке первого запроса может отображаться завышенное значение задержки, так как ASP.NET необходимо скомпилировать страницу.
    ![Запуск приложения с отключенной функцией кэширования](Images/running-the-application-without-the-cache.png?raw=true "Запуск приложения с отключенной функцией кэширования")
    _Запуск приложения с отключенной функцией кэширования_
7.  Обратите внимание на индикатор **Object ID** (Идентификатор объекта), отображаемый в верхней части каталога продуктов. Этот идентификатор меняется при каждом обновлении страницы, так как при каждом вызове репозиторий возвращает новый объект.
8.  Щелкните **Yes** (Да) в поле **Enable Cache** (Включить кэш) и дождитесь обновления страницы. Обратите внимание: первый элемент списка по-прежнему содержит сведения о том, что приложение получило данные каталога из источника данных, так как этих данных еще не было в кэше.
9.  Щелкните ссылку **Products** или обновите страницу в браузере. На этот раз приложение получит данные от службы кэширования Windows Azure и время задержки должно уменьшиться. Теперь первый элемент списка содержит сведения о том, что приложение получило данные каталога из кэша.
    ![Запуск приложения с включенной функцией кэширования](Images/running-the-application-with-the-cache-enable.png?raw=true "Запуск приложения с включенной функцией кэширования")
    _Запуск приложения с включенной функцией кэширования_
10.  Закройте окно браузера.

    <a name="Ex2Task3"></a>

    #### Задание 3. Включение локального кэша

    Служба кэширования Windows Azure позволяет использовать локальный кэш. При этом объекты хранятся не только в кластере кэша, но и в оперативной памяти клиента. Выполняя это задание, вы включите локальный кэш и сравните время доступа к нему со временем доступа к удаленному кэшу.

1.  Откройте файл **ProductsRepository.cs**, расположенный в каталоге **Services** проекта **CloudShop**.
    > **Примечание.** Перед редактированием файлов убедитесь, что решение не запущено.
2.  Добавьте логику управления конфигурацией локального кэша. Для этого перейдите к классу **ProductsRepository** и замените текущие поля членов и код конструктора следующим кодом.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex2-ProductsRepository with local cache-CS_)
    <!--mark: 2-34   -->
    <span class="codelanguage">C#</span><pre>`...    **<span style="color:#0000FF">private</span> <span style="color:#0000FF">static</span> DataCacheFactory cacheFactory;**
    **<span style="color:#0000FF">private</span> <span style="color:#0000FF">static</span> DataCacheFactoryConfiguration factoryConfig;**
    **<span style="color:#0000FF">private</span> <span style="color:#0000FF">bool</span> enableCache = <span style="color:#0000FF">false</span>;**
    **<span style="color:#0000FF">private</span> <span style="color:#0000FF">bool</span> enableLocalCache = <span style="color:#0000FF">false</span>;**
    **<span style="color:#0000FF">public</span> ProductsRepository(<span style="color:#0000FF">bool</span> enableCache, <span style="color:#0000FF">bool</span> enableLocalCache)**
    **{**
    **    <span style="color:#0000FF">this</span>.enableCache = enableCache;**
    **    <span style="color:#0000FF">this</span>.enableLocalCache = enableLocalCache;**
    **    <span style="color:#0000FF">if</span> (enableCache)**
    **    {**
    **        <span style="color:#0000FF">if</span> (enableLocalCache &amp;&amp; (factoryConfig == <span style="color:#0000FF">null</span> || !factoryConfig.LocalCacheProperties.IsEnabled))**
    **        {**
    **            TimeSpan localTimeout = <span style="color:#0000FF">new</span> TimeSpan(0, 0, 30);**
    **            DataCacheLocalCacheProperties localCacheConfig = <span style="color:#0000FF">new</span> DataCacheLocalCacheProperties(10000, localTimeout, DataCacheLocalCacheInvalidationPolicy.TimeoutBased);**
    **            factoryConfig = <span style="color:#0000FF">new</span> DataCacheFactoryConfiguration();**
    **            factoryConfig.LocalCacheProperties = localCacheConfig;**
    **            cacheFactory = <span style="color:#0000FF">new</span> DataCacheFactory(factoryConfig);**
    **        }**
    **        <span style="color:#0000FF">else</span> <span style="color:#0000FF">if</span> (!enableLocalCache &amp;&amp; (factoryConfig == <span style="color:#0000FF">null</span> || factoryConfig.LocalCacheProperties.IsEnabled))**
    **        {**
    **            cacheFactory = <span style="color:#0000FF">null</span>;**
    **        }**
    **    }**
    **    <span style="color:#0000FF">if</span> (cacheFactory == <span style="color:#0000FF">null</span>)**
    **    {**
    **        factoryConfig = <span style="color:#0000FF">new</span> DataCacheFactoryConfiguration();**
    **        cacheFactory = <span style="color:#0000FF">new</span> DataCacheFactory(factoryConfig);**
    **    }**
    **} **
    ...
    `</pre>
3.  Откройте файл **HomeController.cs**, расположенный в каталоге **Controllers** и перейдите к действию **Index**. Найдите строки, создающие новый экземпляр **ProductsRepository**, и замените их выделенным фрагментом кода:
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex2-GetProducts LocalCache-CS_)
    <!--mark: 7-10; strike: 11-13   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> HomeController : Controller
    {
      ...      <span style="color:#0000FF">public</span> ActionResult Index()
      {
        <span style="color:#0000FF">bool</span> enableCache = (<span style="color:#0000FF">bool</span>)<span style="color:#0000FF">this</span>.Session[<span style="color:#8B0000">&quot;EnableCache&quot;</span>];
    **    <span style="color:#0000FF">bool</span> enableLocalCache = (<span style="color:#0000FF">bool</span>)<span style="color:#0000FF">this</span>.Session[<span style="color:#8B0000">&quot;EnableLocalCache&quot;</span>];**
    **    <span style="color:#008000">// получить каталог продуктов из репозитория и измерить затраченное время</span>**
    **    Services.IProductRepository productRepository = <span style="color:#0000FF">new</span> Services.ProductsRepository(enableCache, enableLocalCache);**
    <span class="strikeLine" style="text-decoration:line-through;">    <span style="color:#008000">// получить каталог продуктов из репозитория и измерить затраченное время</span></span>
    <span class="strikeLine" style="text-decoration:line-through;">    Services.IProductRepository productRepository =</span>
    <span class="strikeLine" style="text-decoration:line-through;">    <span style="color:#0000FF">new</span> Services.ProductsRepository(enableCache);</span>
        Stopwatch stopwatch = <span style="color:#0000FF">new</span> Stopwatch();
        stopWatch.Start();
        <span style="color:#0000FF">var</span> products = productRepository.GetProducts();
        ...
    }
    `</pre>
4.  В том же методе найдите код, создающий экземпляр **IndexViewModel**, и добавьте следующее выделенное свойство.
    <!--mark: 25   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> HomeController : Controller
    {
      ...      <span style="color:#0000FF">public</span> ActionResult Index()
      {
          <span style="color:#0000FF">bool</span> enableCache = (<span style="color:#0000FF">bool</span>)<span style="color:#0000FF">this</span>.Session[<span style="color:#8B0000">&quot;EnableCache&quot;</span>];
          <span style="color:#0000FF">bool</span> enableLocalCache = (<span style="color:#0000FF">bool</span>)<span style="color:#0000FF">this</span>.Session[<span style="color:#8B0000">&quot;EnableLocalCache&quot;</span>];
          <span style="color:#008000">// получить каталог продуктов из репозитория и измерить затраченное время</span>
          Services.IProductRepository productRepository =
          <span style="color:#0000FF">new</span> Services.ProductsRepository(enableCache, enableLocalCache);
          Stopwatch stopwatch = <span style="color:#0000FF">new</span> Stopwatch();
          stopWatch.Start();
          <span style="color:#0000FF">var</span> products = productRepository.GetProducts();
          stopWatch.Stop();
          <span style="color:#008000">// добавить все продукты, не сохраненные в сеансе</span>
          <span style="color:#0000FF">var</span> itemsInSession = <span style="color:#0000FF">this</span>.Session[<span style="color:#8B0000">&quot;Cart&quot;</span>] <span style="color:#0000FF">as</span> List&lt;<span style="color:#0000FF">string</span>&gt; ?? <span style="color:#0000FF">new</span> List&lt;<span style="color:#0000FF">string</span>&gt;();
          <span style="color:#0000FF">var</span> filteredProducts = products.Where(item =&gt; !itemsInSession.Contains(item));
          IndexViewModel model = <span style="color:#0000FF">new</span> IndexViewModel()
          {
              Products = filteredProducts,
              ElapsedTime = stopWatch.ElapsedMilliseconds,
              IsCacheEnabled = enableCache,
    **          IsLocalCacheEnabled = enableLocalCache,**
              ObjectId = products.GetHashCode().ToString()
          };
          <span style="color:#0000FF">return</span> View(model);
      }
    }
    `</pre>
5.  Чтобы включать и отключать функцию локального кэширования через интерфейс приложения, добавим к объекту **HomeController** новый метод действия.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex2-EnableLocalCache method-CS_)
    <!--mark: 4-8   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> HomeController : Controller
    {
      ...
    **  <span style="color:#0000FF">public</span> ActionResult EnableLocalCache(<span style="color:#0000FF">bool</span> enabled)**
    **  {**
    **    <span style="color:#0000FF">this</span>.Session[<span style="color:#8B0000">&quot;EnableLocalCache&quot;</span>] = enabled;**
    **    <span style="color:#0000FF">return</span> RedirectToAction(<span style="color:#8B0000">&quot;Index&quot;</span>);**
    **  }**
    }
    `</pre>
6.  Откройте файл **Index.cshtml** из каталога **Views\Home** и добавьте следующий выделенный код перед тегами div раздела **elapsedTime**.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex2-EnableLocalCache Option-HTML_)
    <!--mark: 12-23   -->
    <span class="codelanguage">HTML</span><pre>`<span style="color:#0000FF">&lt;</span><span style="color:#800000">fieldset</span><span style="color:#0000FF">&gt;</span>
         <span style="color:#0000FF">&lt;</span><span style="color:#800000">legend</span><span style="color:#0000FF">&gt;</span>Cache settings for product data<span style="color:#0000FF">&lt;/</span><span style="color:#800000">legend</span><span style="color:#0000FF">&gt;</span>Enable Cache:
         @if (Model.IsCacheEnabled)
         {
              <span style="color:#0000FF">&lt;</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span>Yes |<span style="color:#0000FF">&lt;/</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span><span style="color:#0000FF">&lt;</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span>@Html.ActionLink(&quot;No&quot;, &quot;EnableCache&quot;, new { enabled = false })<span style="color:#0000FF">&lt;/</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span>
         }
         else
         {
              <span style="color:#0000FF">&lt;</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span>@Html.ActionLink(&quot;Yes&quot;, &quot;EnableCache&quot;, new { enabled = true })<span style="color:#0000FF">&lt;/</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span><span style="color:#0000FF">&lt;</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span> | No<span style="color:#0000FF">&lt;/</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span>
         }
         <span style="color:#0000FF">&lt;</span><span style="color:#800000">br</span> <span style="color:#0000FF">/&gt;</span>
    **     @if(Model.IsCacheEnabled)**
    **     {**
    **          <span style="color:#0000FF">&lt;</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span>Use Local Cache:<span style="color:#0000FF">&lt;/</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span>**
    **          if (Model.IsLocalCacheEnabled)**
    **          {**
    **                <span style="color:#0000FF">&lt;</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span>Yes |<span style="color:#0000FF">&lt;/</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span><span style="color:#0000FF">&lt;</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span>@Html.ActionLink(&quot;No&quot;, &quot;EnableLocalCache&quot;, new { enabled = false })<span style="color:#0000FF">&lt;/</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span>**
    **          }**
    **          else**
    **          {**
    **                <span style="color:#0000FF">&lt;</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span>@Html.ActionLink(&quot;Yes&quot;, &quot;EnableLocalCache&quot;, new { enabled = true })<span style="color:#0000FF">&lt;/</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span><span style="color:#0000FF">&lt;</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span> | No<span style="color:#0000FF">&lt;/</span><span style="color:#800000">span</span><span style="color:#0000FF">&gt;</span>**
    **          }**
    **     }**
         <span style="color:#0000FF">&lt;</span><span style="color:#800000">div</span> <span style="color:#FF0000">id</span>=<span style="color:#0000FF">&quot;elapsedTime&quot;</span><span style="color:#0000FF">&gt;</span>Elapsed time: @Model.ElapsedTime.ToString() milliseconds.<span style="color:#0000FF">&lt;/</span><span style="color:#800000">div</span><span style="color:#0000FF">&gt;</span>
    <span style="color:#0000FF">&lt;/</span><span style="color:#800000">fieldset</span><span style="color:#0000FF">&gt;</span>
    `</pre>
7.  Нажмите клавишу **F5**, чтобы выполнить построение и запустить приложение в эмуляторе вычислений.
8.  При запуске приложения функция кэширования первоначально отключена, а параметры локального кэша скрыты (они становятся доступными при включении функции кэширования). Включите функцию кэширования, затем включите локальный кэш.9.  Обновите страницу несколько раз, пока отображаемое время обращения не стабилизируется. Обратите внимание, что время доступа значительно сократилось и составляет менее миллисекунды. Это означает, что приложение получает данные из локального кэша в оперативной памяти.    ![Использование локального кэша](Images/using-the-local-cache.png?raw=true "Использование локального кэша")
    _Использование локального кэша_
10.  Обратите внимание, что при обновлении страницы идентификатор **Object ID**, отображаемый в верхней части каталога продуктов, не меняется. Это означает, что репозиторий каждый раз возвращает один и тот же объект.
    > **Примечание.**  Это важный момент для понимания. Раньше, когда мы работали с отключенным локальным кэшем, изменение полученного из кэша объекта никак не влияло на кэшированные данные, и последующие запросы возвращали новую копию. Локальный кэш хранит ссылки на объекты, находящиеся в оперативной памяти; при изменении объекта меняются и кэшированные данные. Необходимо иметь в виду эту особенность при использовании кэша для работы приложения. Если кэшированный объект был изменен и приложение запросило его из кэша, то наличие изменений в объекте будет зависеть от того, используется локальный или удаленный кэш.
11.  Подождите не менее 30&nbsp;секунд и обновите страницу еще раз. Обратите внимание, что время обработки запроса приняло прежнее значение, а идентификатор объекта изменился. Это означает, что время хранения кэшированного объекта истекло и он был удален из памяти в соответствии с политикой окончания срока действия, определенной при размещении объекта в кэше.

    <a name="Exercise3"></a>

    ### Упражнение 3. Создание расширяемого слоя кэширования с возможностью его повторного использования

    В предыдущем упражнении вы изучили основные аспекты применения службы кэширования Windows Azure. Вы непосредственно обновляли методы для класса доступа к данным и обеспечивали кэширование получаемых из репозитория данных. Такой подход может принести ощутимую пользу, однако для его применения необходимо обновить все методы доступа к данным. Есть и другой подход, не требующий внесения изменений в классы доступа к данным. Он может оказаться более удобным. 

    В этом упражнении вы создадите слой кэширования поверх имеющихся классов доступа к данным. Такой слой кэширования позволит подключать различных поставщиков кэша или вовсе обходиться без них. Для этого достаточно внести незначительные изменения в настройки.

    Чтобы выполнить построение этого слоя, вы создадите абстрактный класс кэширования **CachedDataSource**, позволяющий помещать данные в кэш и удалять их оттуда. С&nbsp;помощью этого класса вы создадите эквивалент кэширования для любого источника данных, используемого вашим приложением. Единственное предварительное требование&nbsp;&mdash; создание источником данных контракта, определяющего операции доступа к данным. Класс кэширования инкапсулирует поставщика кэша, которого необходимо задать в конструкторе класса, а также предоставляет методы получения и удаления данных из кэша.

    Метод извлечения данных в классе кэширования получает ключ кэша, который однозначно идентифицирует: кэшированный объект; делегата, получающего данные из источника; политику окончания срока действия, определяющую время удаления информации из кэша. Этот метод основан на стандартной модели кэширования: вначале он пытается получить данные из кэша, а затем, если копию обнаружить не удается, получает данные из источника с&nbsp;помощью делегата и помещает их в кэш.

    Реализация класса **CachedDataSource** является универсальной и позволяет использовать любого поставщика кэша, удовлетворяющего предъявляемым требованиям. Чтобы определить поставщика кэша, необходимо передать его конструктору экземпляр [ObjectCache](http://msdn.microsoft.com/en-us/library/system.runtime.caching.objectcache.aspx). Класс **ObjectCache**, являющийся частью пространства имен **System.Runtime.Caching**, был реализован в платформе .NET Framework&nbsp;4 и предоставил возможность кэширования данных для всех приложений. Этот абстрактный класс представляет собой кэш объектов и включает основные методы доступа к поставщику кэширования, расположенному уровнем ниже. Платформа .NET Framework предоставляет реализацию этого класса&nbsp;&mdash; [MemoryCache](http://msdn.microsoft.com/en-us/library/system.runtime.caching.memorycache.aspx), которая позволяет кэшировать объекты в оперативной памяти. 

    Чтобы использовать эту службу кэширования в сочетании с производным классом **CachedDataSource**, необходимо создать реализацию класса **ObjectCache** для работы с конкретным поставщиком кэша. Вы можете создать фабрику источников данных, которая позволит выбирать подходящую для конкретного случая реализацию кэша. В этом случае для замены поставщика кэша достаточно поменять настройку в конфигурационном файле.

    В настоящее время служба кэширования Windows Azure не предоставляет собственной реализации класса **ObjectCache**. Однако вы можете создать класс-оболочку, который позволит работать с этой службой. Пример такой реализации&nbsp;&mdash; класс **AzureCacheProvider**, расположенный в каталоге **BuildingAppsWithCacheService\Source\Assets**. Этот класс является производным от **ObjectCache** и позволяет работать со службой кэширования Windows Azure.

    Чтобы использовать преимущества этой реализации для приложения Azure Store, создадим копию кода для доступа к кэшу в классе **ProductsRepository**. Приложение использует этот класс, реализующий контракт **IProductsRepository** с единственной операцией **GetProducts**, для получения данных каталога из базы данных Windows Azure SQL. Чтобы создать кэшируемый источник данных каталога, выполните следующие действия:

*   Создайте класс **CachingProductsReposity**, наследующий от **CachedDataSource**.
*   Добавьте в новый класс конструктора, принимающего два параметра&nbsp;&mdash; **IProductRepository** (Экземпляр класса некэшируемого источника данных) и **ObjectCache** (Экземпляр поставщика кэша, который будет использоваться в приложении).
*   Создайте методы работы с **IProductRepository**, вызывающие метод **RetrievedCachedData** базового класса и передающие делегат, вызывающий исходный класс источника данных.

    <a name="Ex3Task1"></a>

    #### Задание 1. Реализация базового класса кэшируемого источника данных

    В этом задании мы создадим абстрактный класс, который будет использоваться в качестве базового для классов кэшируемых источников данных. Этот класс общего назначения можно использовать и в других приложениях, в которых необходим слой кэширования.

1.  Запустите **Microsoft Visual Studio 2012 Express for Web** от имени администратора.
2.  Откройте решение **Begin** из каталога **Source\Ex3-ReusableCachingImplementation**.
    > **Важно!**     Перед запуском решения убедитесь, что начальный проект установлен. Для проектов MVC начальная страница должна быть пустой.    Чтобы указать начальный проект, откройте **обозреватель решений**, щелкните правой кнопкой мыши проект **CloudShop.Azure** и выберите **Set as StartUp Project** (Использовать в качестве начального проекта).    Чтобы настроить начальную страницу, откройте **обозреватель решений**, щелкните правой кнопкой мыши проект **CloudShop** и выберите **Properties** (Свойства). В окне **Properties** выберите вкладку **Web** (Интернет). В списке **Start Action** (Начальное действие) выберите **Specific Page** (Определенная страница). Оставьте это поле пустым.
3.  Откройте файл **Web.config** и обновите строку подключения _NorthwindEntities_, создав ссылку на базу данных. Замените строки **[YOUR-SQL-DATABASE-SERVER-ADDRESS]**, **[SQL-DATABASE-USERNAME]** и **[SQL-DATABASE-PASSWORD]** на имя сервера Windows Azure SQL Database, имя пользователя с правами администратора и пароль администратора, который вы использовали для регистрации на портале и создания базы данных при начальной установке.
    > **Примечание.**  Следуя инструкции по установке, необходимо создать копию базы данных Northwind2 для вашей учетной записи базы данных Windows Azure SQL, а также настроить брандмауэр базы данных Windows Azure SQL.
4.  Добавьте ссылку на сборку **System.Runtime.Caching** в проекте **CloudShop**.
5.  В каталоге **Services** (Службы) проекта **CloudShop** создайте папку **Caching** (Кэширование).
6.  В папке **Caching** создайте файл для нового класса, **CachedDataSource.cs**.
7.  В этот файл добавьте директиву пространства имен для **System.Runtime.Caching**.
    <!--mark: 5   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">using</span> System;
    <span style="color:#0000FF">using</span> System.Collections.Generic;
    <span style="color:#0000FF">using</span> System.Linq;
    <span style="color:#0000FF">using</span> System.Web;
    **<span style="color:#0000FF">using</span> System.Runtime.Caching;**
    ...
    `</pre>
8.  Задайте модификатор **abstract** для класса **CachedDataSource**.
    <!--mark: 1-3   -->
    <span class="codelanguage">C#</span><pre>`**<span style="color:#0000FF">public</span> <span style="color:#0000FF">abstract</span> <span style="color:#0000FF">class</span> CachedDataSource**
    **{**
    **}**
    `</pre>
9.  Добавьте в класс следующие (выделенные) поля членов.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex3-CachedDataSource member fields-CS_)
    <!--mark: 3,4   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">abstract</span> <span style="color:#0000FF">class</span> CachedDataSource
    {
    **  <span style="color:#0000FF">private</span> <span style="color:#0000FF">readonly</span> ObjectCache cacheProvider;**
    **  <span style="color:#0000FF">private</span> <span style="color:#0000FF">readonly</span> <span style="color:#0000FF">string</span> regionName;**
    }
    `</pre>
10.  Теперь добавьте конструктор, который принимает в качестве параметров кэш объекта и имя области. Необходимые строки выделены ниже.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex3-CachedDataSource constructor-CS_)
    <!--mark: 4-18   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">abstract</span> <span style="color:#0000FF">class</span> CachedDataSource
    {
      ...
    **  <span style="color:#0000FF">public</span> CachedDataSource(ObjectCache cacheProvider, <span style="color:#0000FF">string</span> regionName)**
    **  {**
    **    <span style="color:#0000FF">if</span> (cacheProvider == <span style="color:#0000FF">null</span>)**
    **    {**
    **      <span style="color:#0000FF">throw</span> <span style="color:#0000FF">new</span> ArgumentNullException(<span style="color:#8B0000">&quot;cacheProvider&quot;</span>);**
    **    }**
    **    <span style="color:#0000FF">if</span> (cacheProvider <span style="color:#0000FF">is</span> MemoryCache)**
    **    {**
    **      regionName = <span style="color:#0000FF">null</span>;**
    **    }**
    **    <span style="color:#0000FF">this</span>.cacheProvider = cacheProvider;**
    **    <span style="color:#0000FF">this</span>.regionName = regionName;**
    **  }**
    }
    `</pre>
    > **Примечание.** Конструктор **CachedDataSource** принимает два параметра: экземпляр объекта ObjectCache ([<a href="http://msdn.microsoft.com/en-us/library/system.runtime.caching.objectcache.aspx">http://msdn.microsoft.com/en-us/library/system.runtime.caching.objectcache.aspx](http://msdn.microsoft.com/en-us/library/system.runtime.caching.objectcache.aspx)</a>), который содержит методы и свойства доступа к кэшу объектов, а также имя области.  Область кэша используется для упорядочивания кэшируемых объектов.
11.  Добавьте в код следующий (выделенный) метод для получения данных из кэша.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex3-RetrieveCachedData method-CS_)
    <!--mark: 4-19   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">abstract</span> <span style="color:#0000FF">class</span> CachedDataSource
    {
      ...
    **  <span style="color:#0000FF">protected</span> T RetrieveCachedData&lt;T&gt;(<span style="color:#0000FF">string</span> cacheKey, Func&lt;T&gt; fallbackFunction, CacheItemPolicy cachePolicy) where T : <span style="color:#0000FF">class</span>**
    **  {**
    **    <span style="color:#0000FF">var</span> data = <span style="color:#0000FF">this</span>.cacheProvider.Get(cacheKey, <span style="color:#0000FF">this</span>.regionName) <span style="color:#0000FF">as</span> T;**
    **    <span style="color:#0000FF">if</span> (data != <span style="color:#0000FF">null</span>)**
    **    {**
    **      <span style="color:#0000FF">return</span> data;**
    **    }**
    **    data = fallbackFunction();**
    **    <span style="color:#0000FF">if</span> (data != <span style="color:#0000FF">null</span>)**
    **    {**
    **      <span style="color:#0000FF">this</span>.cacheProvider.Add(<span style="color:#0000FF">new</span> CacheItem(cacheKey, data, <span style="color:#0000FF">this</span>.regionName), cachePolicy);**
    **    }**
    **    <span style="color:#0000FF">return</span> data;**
    **  }**
    }
    `</pre>
    > **Примечание.** Метод **RetrieveCachedData** использует предоставленный ключ для получения из кэша копии запрошенных данных. Если данные доступны, метод возвращает их. В противном случае данные копируются из источника с&nbsp;помощью делегата и кэшируются в соответствии с определенной политикой окончания срока действия.
12.  Добавьте в код метод, удаляющий данные из кэша.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex3-RemoveCachedData method-CS_)
    <!--mark: 4-7   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">abstract</span> <span style="color:#0000FF">class</span> CachedDataSource
    {
      ...
    **  <span style="color:#0000FF">protected</span> <span style="color:#0000FF">void</span> RemoveCachedData(<span style="color:#0000FF">string</span> cacheKey)**
    **  {**
    **    <span style="color:#0000FF">this</span>.cacheProvider.Remove(cacheKey, <span style="color:#0000FF">this</span>.regionName);**
    **  }**
    }
    `</pre>
13.  Сохраните файл **CachedDataSource.cs**.

    <a name="Ex3Task2"></a>

    #### Задание 2. Создание кэшируемого репозитория данных для каталога продуктов

    Абстрактный базовый класс для кэширования источников данных создан. Теперь мы добавим реализацию этого класса, которую можно использовать вместо класса **ProductsRepository**. При выполнении этого задания мы будем использовать те же шаги, что и при создании слоя кэширования для кода доступа к данным с помощью класса **CachedDataSource**.

1.  В каталоге **Services\Caching** проекта **CloudShop** создайте файл для нового класса **CachedProductsRepository.cs**.
2.  Добавьте в этот файл директиву пространства имен для **System.Runtime.Caching** и **CloudShop.Services**.
    <!-- mark:5-6    -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">using</span> System;
    <span style="color:#0000FF">using</span> System.Collections.Generic;
    <span style="color:#0000FF">using</span> System.Linq;
    <span style="color:#0000FF">using</span> System.Web;
    **<span style="color:#0000FF">using</span> CloudShop.Services;**
    **<span style="color:#0000FF">using</span> System.Runtime.Caching;**
    ...
    `</pre>
3.  Измените объявление класса **CachedProductsRepository** так, чтобы он наследовал от двух классов: **CachedDataSource** и **IProductRepository**. Этот код приведен ниже.
    <!--mark: 2   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> CachedProductsRepository    **  : CachedDataSource, IProductRepository**
    {
    }
    `</pre>
    > **Примечание.** Наследование класса кэширования источника данных от **CachedDataSource** необходимо для того, чтобы новый класс обеспечивал те же возможности кэширования и реализовывал тот же контракт источника данных, что и исходный класс.
4.  Добавьте следующий (выделенный) код, определяющий конструктора и объявляющий поле члена, содержащее ссылку на источник данных, который расположен уровнем ниже.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex3-CachedProductsRepository constructor-CS_)
    <!--mark: 3-9   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> CachedProductsRepository : CachedDataSource, IProductRepository
    {
    **  <span style="color:#0000FF">private</span> <span style="color:#0000FF">readonly</span> IProductRepository repository;**
    **  <span style="color:#0000FF">public</span> CachedProductsRepository(IProductRepository repository, ObjectCache cacheProvider) :**
    **    <span style="color:#0000FF">base</span>(cacheProvider, <span style="color:#8B0000">&quot;Products&quot;</span>)**
    **  {**
    **    <span style="color:#0000FF">this</span>.repository = repository;**
    **  }**
    }
    `</pre>
    > **Примечание.** Конструктор **CachedProductsRepository** инициализирует базовый класс с&nbsp;помощью поставщика кэша и сохраняет в поле члена ссылку на источник данных, расположенный уровнем ниже. Класс определяет область кэша &quot;_Products_&quot;.
5.  Теперь обеспечим выполнение контракта **IProductRepository** для реализации метода **GetProducts**. Соответствующий код показан ниже.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex3-GetProducts method -CS_)
    <!--mark: 4-10   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> CachedProductsRepository : CachedDataSource, IProductRepository
    {
      ...
    **  <span style="color:#0000FF">public</span> List&lt;<span style="color:#0000FF">string</span>&gt; GetProducts()**
    **  {**
    **    <span style="color:#0000FF">return</span> RetrieveCachedData(**
    **    <span style="color:#8B0000">&quot;allproducts&quot;</span>,**
    **    () =&gt; <span style="color:#0000FF">this</span>.repository.GetProducts(),**
    **    <span style="color:#0000FF">new</span> CacheItemPolicy { AbsoluteExpiration = DateTime.UtcNow.AddMinutes(1) });**
    **  }**
    }
    `</pre>
    > **Примечание.** Метод **GetProducts** вызывает метод **RetrieveCachedData** базового класса и передает ему ключ, который однозначно определяет кэшированный элемент; в нашем случае это &quot;_allproducts_&quot;. Если данные в кэше не найдены, в форме лямбда-выражения вызывается делегат, который, в свою очередь, вызывает метод **GetProducts** для исходного источника данных и сохраняет данные в кэше. В соответствии с политикой [CacheItemPolicy](http://msdn.microsoft.com/en-us/library/system.runtime.caching.cacheitempolicy.aspx), время хранения объекта в кэше составляет одну&nbsp;минуту.
    Благодаря простоте контракта **IProductRepository**, для реализации кэширования больше ничего не требуется. Обычно источники данных содержат более чем один метод. В этом случае также можно использовать базовый подход и реализовать каждый метод по приведенному образцу.

    <a name="Ex3Task3"></a>

    #### Задание 2. Создание класса фабрики источника данных

    В этом задании мы создадим класс фабрики, который будет возвращать экземпляры источника данных. Фабрика использует поставщика кэша, указанного в настройках конфигурации приложения, и возвращает источник данных, настроенный для работы с выбранным поставщиком.

1.  Скопируйте файл **AzureCacheProvider.cs** из каталога **\Source\Assets** в проект **CloudShop** и поместите его в каталог **Services\Caching**.
    > **Примечание.** Класс **AzureCacheProvider** реализует класс **ObjectCache**, который служит оболочкой для службы кэширования Windows Azure.
2.  В каталоге **Services** проекта **CloudShop** создайте файл для нового класса, **DataSourceFactory.cs**.
3.  В созданный файл класса добавьте директивы пространства имен для **System.Configuration**, **System.Runtime.Caching**, **CloudShop.Services** и **CloudShop.Services.Caching**.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex3-DataSourceFactory namespaces-CS_)
    <!--mark: 5-8   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">using</span> System;
    <span style="color:#0000FF">using</span> System.Collections.Generic;
    <span style="color:#0000FF">using</span> System.Linq;
    <span style="color:#0000FF">using</span> System.Web;
    **<span style="color:#0000FF">using</span> System.Configuration;**
    **<span style="color:#0000FF">using</span> System.Runtime.Caching;**
    **<span style="color:#0000FF">using</span> CloudShop.Services;**
    **<span style="color:#0000FF">using</span> CloudShop.Services.Caching;**
    `</pre>
4.  Добавьте следующий (выделенный) код, который определяет тип конструктора для класса **DataSourceFactory** и объявляет статическое поле, содержащее ссылку на указанного в настройках поставщика службы кэширования.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex3-DataSourceFactory class constructor-CS_)
    <!--mark: 3-20   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> DataSourceFactory
    {
    **  <span style="color:#0000FF">private</span> <span style="color:#0000FF">static</span> <span style="color:#0000FF">readonly</span> ObjectCache cacheProvider;**
    **  <span style="color:#0000FF">static</span> DataSourceFactory()**
    **  {**
    **    <span style="color:#0000FF">string</span> provider = ConfigurationManager.AppSettings[<span style="color:#8B0000">&quot;CacheService.Provider&quot;</span>];**
    **    <span style="color:#0000FF">if</span> (provider != <span style="color:#0000FF">null</span>)**
    **    {**
    **      <span style="color:#0000FF">switch</span> (ConfigurationManager.AppSettings[<span style="color:#8B0000">&quot;CacheService.Provider&quot;</span>].ToUpperInvariant())**
    **      {**
    **        <span style="color:#0000FF">case</span> <span style="color:#8B0000">&quot;AZURE&quot;</span>:**
    **          cacheProvider = <span style="color:#0000FF">new</span> AzureCacheProvider();**
    **          <span style="color:#0000FF">break</span>;**
    **        <span style="color:#0000FF">case</span> <span style="color:#8B0000">&quot;INMEMORY&quot;</span>:**
    **          cacheProvider = MemoryCache.Default;**
    **          <span style="color:#0000FF">break</span>;**
    **      }**
    **    }**
    **  }**
    }
    `</pre>
    > **Примечание.** Конструктор класса считывает параметр _CacheService.Provider_ из файла конфигурации и инициализирует поставщика кэша, который будет использоваться в приложении. В этом примере конструктор распознает два возможных значения параметра. Одно из них соответствует службе кэширования Windows Azure, другое&nbsp;&mdash; поставщику кэша в оперативной памяти для платформы .NET Framework&nbsp;4.
5.  Добавьте в код следующее свойство, возвращающее данные настроенного поставщика кэша.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex3-CacheProvider property-CS_)
    <!--mark: 4-7   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> DataSourceFactory
    {
      ...
    **  <span style="color:#0000FF">public</span> <span style="color:#0000FF">static</span> ObjectCache CacheProvider**
    **  {**
    **    <span style="color:#0000FF">get</span> { <span style="color:#0000FF">return</span> cacheProvider; }**
    **  }**
    }
    `</pre>
6.  И наконец, добавьте метод для возврата экземпляра источника данных **IProductRepository**, инициализированного выбранным поставщиком службы кэширования.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex3-GetProductsRepository method-CS_)
    <!--mark: 4-13   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> DataSourceFactory
    {
      ...
    **  <span style="color:#0000FF">public</span> <span style="color:#0000FF">static</span> IProductRepository GetProductsRepository(<span style="color:#0000FF">bool</span> enableCache)**
    **  {**
    **    <span style="color:#0000FF">var</span> dataSource = <span style="color:#0000FF">new</span> ProductsRepository();**
    **    <span style="color:#0000FF">if</span> (enableCache &amp;&amp; CacheProvider != <span style="color:#0000FF">null</span>)**
    **    {**
    **      <span style="color:#0000FF">return</span> <span style="color:#0000FF">new</span> CachedProductsRepository(dataSource, cacheProvider);**
    **    }**
    **    <span style="color:#0000FF">return</span> dataSource;**
    **  }**
    }
    `</pre>

    <a name="Ex3Task4"></a>

    #### Задание 4. Настройка приложении для использования кэша

    В этом задании мы обновим приложение и будем использовать фабрику источника данных для создания экземпляра источника данных о каталоге продуктов. В завершение настройки уровня кэширования мы укажем параметры, необходимые для выбора поставщика кэша.

1.  Откройте файл **HomeController.cs**, расположенный в каталоге **Controllers**, и перейдите к методу **Index**. В этом методе замените строку инициализации локальной переменной **productRepository** выделенным ниже кодом. Теперь для получения экземпляра **IProductRepository** будет использоваться фабрика **DataSourceFactory**.
    <!--mark: 10   -->
    <span class="codelanguage">C#</span><pre>`<span style="color:#0000FF">public</span> <span style="color:#0000FF">class</span> HomeController : Controller
    {
      ...
      <span style="color:#0000FF">public</span> ActionResult Index()
      {
        <span style="color:#0000FF">bool</span> enableCache = (<span style="color:#0000FF">bool</span>)<span style="color:#0000FF">this</span>.Session[<span style="color:#8B0000">&quot;EnableCache&quot;</span>];
        <span style="color:#008000">// получить каталог продуктов из репозитория и измерить затраченное время</span>
        Services.IProductRepository productRepository =
    **    CloudShop.Services.DataSourceFactory.GetProductsRepository(enableCache);**
        Stopwatch stopWatch = <span style="color:#0000FF">new</span> Stopwatch();
        stopWatch.Start();
        ...
      }
      ...
    }
    `</pre>
2.  Чтобы настроить фабрику **DataSourceFactory**, откройте файл **Web.config** и добавьте в раздел **appSettings** следующие (выделенные) строки.
    (Фрагмент кода&nbsp;&mdash; _BuildingAppsWithCachingService-Ex3-Web.config appSettings section-CS_)
    <!--mark: 3   -->
    <span class="codelanguage">XML</span><pre>`  <span style="color:#0000FF">&lt;</span><span style="color:#800000">appSettings</span><span style="color:#0000FF">&gt;</span>
         ...
    **    <span style="color:#0000FF">&lt;</span><span style="color:#800000">add</span> <span style="color:#FF0000">key</span>=<span style="color:#0000FF">&quot;CacheService.Provider&quot;</span> <span style="color:#FF0000">value</span>=<span style="color:#0000FF">&quot;InMemory&quot;</span> <span style="color:#0000FF">/&gt;</span>**
      <span style="color:#0000FF">&lt;/</span><span style="color:#800000">appSettings</span><span style="color:#0000FF">&gt;</span>
    `</pre>
    > **Примечание.** Для приложений, размещаемых на одиночном узле, рекомендуется выбирать поставщика, который обеспечивает кэширование в оперативной памяти.
3.  Нажмите клавиши **CTRL+F5**, чтобы построить и протестировать улучшенную реализацию функции кэширования в эмуляторе вычислений.
4.  При запуске приложения функция кэширования первоначально отключена. Щелкните **Yes** (Да) в поле **Enable Cache** (Включить кэш) и дождитесь обновления страницы. Не забывайте, что при выполнении первого запроса после включения функции кэширования приложению необходимо обратиться к источнику данных, чтобы получить данные и поместить их в кэш.
5.  Щелкните ссылку **Products** или обновите страницу в браузере еще раз. Теперь приложение получает данные о продуктах из кэша. При использовании платформы .NET Framework для кэширования в оперативной памяти время доступа значительно снизится и составит менее одной миллисекунды.
6.  Теперь откройте файл **Web.config**, перейдите к разделу **appSettings** и установите для поля **CacheService.Provider** значение _Azure_.
    <!--mark: 3   -->
    <span class="codelanguage">XML</span><pre>`  <span style="color:#0000FF">&lt;</span><span style="color:#800000">appSettings</span><span style="color:#0000FF">&gt;</span>
              ...
    **        <span style="color:#0000FF">&lt;</span><span style="color:#800000">add</span> <span style="color:#FF0000">key</span>=<span style="color:#0000FF">&quot;CacheService.Provider&quot;</span> <span style="color:#FF0000">value</span>=<span style="color:#0000FF">&quot;Azure&quot;</span> <span style="color:#0000FF">/&gt;</span>**
      <span style="color:#0000FF">&lt;/</span><span style="color:#800000">appSettings</span><span style="color:#0000FF">&gt;</span>

    > **Примечание.** Для приложений, размещаемых на множестве узлов, не рекомендуется выбирать поставщика, который обеспечивает кэширование в оперативной памяти. В этом случае лучше использовать распределенное кэширование Windows Azure.
7.  Сохраните файл **Web.config**.

8.  Щелкните ссылку **Recycle** (Перезапустить), чтобы перезапустить роль и перезагрузить конфигурацию. Щелкнув эту ссылку, вы удалите содержимое страницы Products.

9.  Снова откройте браузер, удалите _/Home/Recycle_ из адресной строки и нажмите клавишу ВВОД, чтобы перезапустить сайт. Через некоторое время откроется пустая страница **Products**.

10.  Убедитесь, что функция кэширования по-прежнему включена. Обновите страницу в браузере **дважды**, чтобы поместить данные в кэш. Обратите внимание: время, затраченное на обработку запроса, возросло. Это означает, что теперь используется не поставщик услуг кэширования в оперативной памяти, а служба кэширования Windows Azure.

* * *

<a name="Summary"></a>

## Сводка

На этом практическом занятии вы научились использовать службу кэширования Windows Azure. Вы узнали, как использовать кластер кэша для сохранения состояния сеанса при перезагрузках и размещении приложения в нескольких экземплярах роли. Кроме того, вы изучили основы кэширования данных с помощью Windows Azure и научились кэшировать результаты запросов к базе данных Windows Azure SQL. И наконец, вы научились создавать пригодный для многократного использования уровень кэширования и добавлять его к собственным приложениям.

</span>
		</div>

[к началу страницы](#top)
