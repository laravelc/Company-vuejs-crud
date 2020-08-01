# Описание кода проекта
1. При выполнении команды `yarn serve` согласно назначению в `package.json` выполняется команда `vue-cli-service serve`.
Команда `vue-cli-service serve` запускает сервер для разработки. Настройки сервера разработки находятся в файлн
`vue.config.js`.

2. Входным точкой в программу является скрипт `main.js`. Что делается в данном скрипте:
    1. Импортиуется хранилище `vuex` для реализации `FLAX`-паттерна в строке `import store from './store'`
    2. Импортруются вложенные многоуровневые маршруты для переходов по ссылкам и загрузки компонентов в строке `import router from './router'`
    3. Импортируется объект локализации для перевода на другие языки в строке `import i18n from './locales/index'`
    4. Подключается плагин `vuetify`, содержащий широкий набор компонентов для отображения и управления
    5. Регистриуется `Service Woker` для кеширования и отлова событий с сетью
    6. Регистрируется интерцепотры для axios, чтобы для выходящих запросов обеспечивать присуствие токена авторизации,
    а в случае ответов сервера с кодами 400, 401, 403 перенаправлять на страницу авторизации `/login`
    7. В конце выполнения скрипта создается объект `Vue`, связывающий загруженные контейнеры и показывающий 1-ю страницу

3. Организация хранилищ `vuex` в проекте имеет модульный тип. В проекте хранилища расположены в нескольких папках.
    Общая сборка всех хранилищ в одну модульную-древовидную структуру происходит в файле `src/store/index.js`. В ходе такой сборки 
    `const mainModules = { crud, auth, app }` все части хранилищ `actions`, `getters`, `mutations`, `state` попадают в модульную структуру.
    И обращения к ним происходит с указанием модуля-пространства имен. Например, `mapState`, `mapGetters`, `mapActions`,
    `mapMutations` имеют первым аргументом пространства имен.    
    1. Что характерно для экшенов, то в них происходят почти все обращения к API для обновления и получения данных. В экшенах
    широко использованы промисы, асинхронные запросы с помощью `Axios`. Кофигурации обращений к Api импортируются из файлов
    `/config/api`, `/config/auth`. В случае возникновения необходимости сообщения об ошибках, удачных завершениях
    во всех экшенах вызывается экшен `openAlertBox` вывода сообщений и корневого немодульного уровня.
    2. По состояниям, мутациям, геттерам можно сказать что они работают только в пределах своего модуля. И их использование
    в компонентах программы строго используется декларированием `mapState`, `mapGetters`, `mapActions`, `mapMutations` в
    методах программы для упрощения обращений, а также понимания какое хранилище используется компонент на этапе чтения 
    `methods` программистом

4. Маршруты в проекте расположены в нескольких папках. Сборка роутов происходит в файле `src/router.js`.
    1. В корне маршрутов находятся 2 маршрута: основной маршрут приложения `/` и маршрут авторизации`/login`. Для маршрута `/` в
    коллбэке `beforeEnter: ` имеется проверка наличия токена в Local Storage. В случае наличия токена выполяется запрос 
    для загрузки пользователя `auth/user`. Если запрос успешен, то загрузка роута продолжается.
    2. Имеется навигационный хук `beforeEach` перед переключением на каждый роут. Данный хук проверяет роут, на который переключаемся
    на наличие переменной `guard`, в которой заполяются уровни защиты. Если переменной нет то пропускаем дальше. Если `guard` есть,
    то делаем проверку в хранилище модуля `auth` на предмет, имеется ли права доступа на данный роут.
    3. Вложенность маршрутов имеет 3 уровня. Они связаны переменной `children` в родительских объектах. Связано такое усложение 
    вложенности маршрутов с тем, что необходимо обновление компонентов в проекте с одними и теми же родительскими контейнерами.
    Маршруты имеют форму дерева как и полагается в таких случаях.

5.   
  
         

# Разворачивание проекта
1. Склонировать из репозитория
2. Выполнить команду  `yarn`
3. Убедитесь что запущен бекенд. При необходимости нужно указать API бекенда `src/config/api.js`
4. Запистите сервер фронтенда командой `yarn serve`