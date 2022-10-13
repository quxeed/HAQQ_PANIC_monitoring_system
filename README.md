# Система мониторинга PANIC

PANIC - это система для мониторинга и оповещения для блокчейнов на базе Cosmos-SDK, Substrate, Chainlink,  сделанная компанией Simply VC, имеет открытый исходный код.
PANIC имеет много функций, среди которых: 
- установка с помощью Web-UI
- оповещения в Telegram и Slack
- телефонные звонки для критических оповещений
- панель пользовательского интерфейса

![valoper](https://raw.githubusercontent.com/SimplyVC/panic/master/docs/images/IMG_PANIC_DESIGN_10X.png)

# Руководство по установке 

### Шаг 1. Установка Git 

```bash
  # ставим git
  sudo apt install git
  
  # проверяем
  git --version

  ```
### Шаг 2. Установка Docker и Docker Compose


```bash
# install docker and docker-compose
curl -sSL https://get.docker.com/ | sh
sudo apt install docker-compose -y

# check
docker --version
docker-compose --version
```

### Шаг 3. Установка конфигурации

```ash
# клонируем репо и переходим в папку
git clone https://github.com/SimplyVC/panic
cd panic
```

### Шаг 4. В каталоге `panic` редактируем параметры в файле `.env`

- INSTALLER_USERNAME: пользователь
- INSTALLER_PASSWORD: пароль
- UI_ACCESS_IP: адрес хоста на котором установлен PANIC (доступны скрипты scripts/get_ip_linux.sh и scripts/get_ip_mac.sh для определения ip адреса)

```ini
UI_ACCESS_IP=1.1.1.1
INSTALLER_USERNAME=username
INSTALLER_PASSWORD=password
```

### Шаг 5. Запуск

```bash
docker-compose up -d --build
```
Если сборка заканчивается неудачно, удаляем образы docker следующей командой, устараняем причину сбоя и запускаем сборку по новой:

```bash
docker-compose kill
docker system prune -a --volumes
docker-compose up -d --build
```

Чтобы убедиться в том, что система функционирует правильно, можно использовать команды `docker-compose logs -f alerter` и `docker-compose logs -f health-checker`.

Лог файлы находятся в каталоге `panic/alerter/logs`



 Настройка уведомлений Telegram

Для создания Telegram бота, добавляем [@BotFather](https://telegram.me/BotFather) в Telegram, нажимаем Start, и выполняем следующие шаги:

1. Отправляем команду `/newbot` и указываем запрашиваемые данные включая имя бота и имя пользователя
2. Записываем API токен, который выглядит как:`111111:AAA-AAA111111-aaaaa11111`
3. Переходим по ссылке `t.me/<username>` для доступа к созданному боту и нажимаем `Start`.
4. Переходим по ссылке `api.telegram.org/bot<token>/getUpdates`, заменив `<token>` полученным токеном бота. Открывается список активностей бота, включающий сообщения отправленные ему
5. В списке должно быть по меньшей мере одно сообщение, вызванное нажатием кнопки Start. Если его нет, отправьте боту команду `/start`. Фиксируем значение `"id"` в разделе `"chat"`
6. На этом создание бота завершено, для создания еще одного бота, повторяем описанные выше шаги еще раз.
В результате мы должны получить:
- Аккаунт Telegram
- Telegram бота
- API токен Telegram бота
- Идентификатор чата (`chat ID`) 

 Конфигурирование мониторинга и оповещений

Настройка мониторинга узлов и каналов оповещения производится по адресу `https://{UI_ACCESS_IP}:8000`. Для авторизации используем `INSTALLER_USERNAME` и `INSTALLER_PASSWORD` которые мы указывали в файле `.env`. 
После авторизации запускается мастер мониторинга.
