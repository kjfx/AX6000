# Xiaomi AX6000: разблокировка SSH, прошивка OpenWrt и установка ShellClash

**Languages:** [简体中文](README.md) | [English](README.en.md) | [Русский](README.ru.md)

Это руководство показывает, как разблокировать SSH на Xiaomi AX6000 без использования дополнительного soft router. Процесс довольно длинный, поэтому лучше внимательно идти по видеоинструкции шаг за шагом.

Видео по разблокировке SSH на AX6000: https://youtu.be/cLSEmZ6ufE4

## Загрузки

Пакет с инструментами: [Скачать](https://github.com/aews/lyq/releases/download/AX6000/AX6000-SSH.zip)

## Шаг 1. Настройка мобильной точки доступа

Задайте параметры точки доступа:

- Имя сети: `OpenWrt`
- Пароль: `12345678`
- Диапазон: `2.4GHz`

Также проверьте канал Wi-Fi, обычно это `1`. Его можно посмотреть с другого компьютера или через приложение на телефоне, подключившись к этой точке доступа.

## Шаг 2. Прошивка и даунгрейд AX6000

Для даунгрейда и прошивки следуйте видеоинструкции по ссылке выше.

## Шаг 3. Установка VMware и OpenWrt

Стандартные данные для входа в OpenWrt:

- Адрес: `192.168.5.1`
- Логин: `root`
- Пароль: `password`

## Шаг 4. Загрузка `xqsystem.lua` через WinSCP

Загрузите файл `xqsystem.lua` в каталог OpenWrt:

```text
/usr/lib/lua/luci/controller/admin/
```

## Шаг 5. Изменение IPv4-адреса LAN в OpenWrt

Установите для LAN в OpenWrt следующий IPv4-адрес:

```text
169.254.31.1
```

## Шаг 6. Снова включите мобильную точку доступа и измените настройки адаптера

Включите точку доступа снова, откройте настройки сетевого подключения этой точки и отключите `Internet Protocol Version 4 (IPv4)`.

## Шаг 7. Настройка bridged network в VMware

Откройте:

```text
Edit > Virtual Network Editor > Change Settings
```

## Шаг 8. Войдите в Xiaomi AX6000

После получения актуального `stok` из панели управления роутером откройте этот URL:

```text
http://192.168.31.1/cgi-bin/luci/;stok=XXXXXX/api/xqsystem/extendwifi_connect_inited_router?ssid=OpenWrt&password=12345678&encryption=WPA2PSKenctype=CCMP&channel=1&band=2g&admin_username=root&admin_password=admin&admin_nonce=xxx
```

Замените `XXXXXX` и `xxx` на реальные значения из своей сессии.

## Шаг 9. Получение пароля root по серийному номеру устройства

Серийный номер можно найти на наклейке снизу роутера или в веб-интерфейсе по адресу `192.168.31.1`.

Генератор пароля:

```text
https://miwifi.dev/ssh
```

Если сайт генерации пароля недоступен, используйте офлайн-пакет: [Скачать](https://github.com/aews/lyq/releases/download/AX6000/SN.zip)

Пример формата SN:

```text
30212/F1ZD22032
```

## Частые проблемы

Важные замечания:

1. Значение `stok` меняется при каждом новом входе в админку Xiaomi. Всегда используйте самое свежее значение.
2. После успешной разблокировки SSH может не появиться никакого уведомления. Проверьте вход через PuTTY. Если видите `ARE U OK`, значит всё прошло успешно.
3. Если с первого раза не получилось, заново войдите в админку Xiaomi, скопируйте новый `stok` и повторите попытку.

### Не получается открыть OpenWrt по адресу `192.168.5.1`

1. Перезапустите OpenWrt вручную или выполните `reboot`.
2. Убедитесь, что IP OpenWrt действительно изменён на `169.254.31.1`. Если да, входить нужно уже по нему.
3. Измените IP-диапазон локального сетевого адаптера так, чтобы он соответствовал подсети OpenWrt.
4. В VMware выключите работающую виртуальную машину OpenWrt и откройте `Edit > Virtual Network Editor > Change Settings > Restore Defaults`.
5. Откройте текущий IP OpenWrt в браузере.

### Если появляется ошибка `{"msg":"一键换机过程中请求对端接口失败","code":1643}`

Внутри виртуальной машины выполните:

```bash
vim /etc/config/dhcp
```

Далее:

1. Зажмите `d`, чтобы удалить всё содержимое.
2. Нажмите `Esc`.
3. Введите `:wq`.
4. Нажмите `Enter`.
5. Перезагрузите виртуальную машину командой `reboot`.

## Установка ShellClash

Видео по настройке ShellClash на Xiaomi AX6000: https://youtu.be/vn5QdyDlTtI

Основная команда установки:

```bash
export url='https://testingcf.jsdelivr.net/gh/juewuy/ShellCrash@master' && sh -c "$(curl -kfsSl $url/install.sh)" && . /etc/profile &> /dev/null
```

Если установка не проходит, попробуйте один из резервных источников:

```bash
# 1
export url='https://raw.githubusercontent.com/juewuy/ShellCrash/master' && sh -c "$(curl -kfsSl $url/install.sh)" && . /etc/profile &> /dev/null

# 2
export url='https://fastly.jsdelivr.net/gh/juewuy/ShellCrash@master' && sh -c "$(curl -kfsSl $url/install.sh)" && source /etc/profile &> /dev/null

# 3
export url='https://gh.jwsc.eu.org/master' && sh -c "$(curl -kfsSl $url/install.sh)" && . /etc/profile &> /dev/null

# 4
export url='https://testingcf.jsdelivr.net/gh/juewuy/ShellCrash@master' && wget -q --no-check-certificate -O /tmp/install.sh $url/install.sh && sh /tmp/install.sh && . /etc/profile &> /dev/null

# 5
export url='http://t.jwsc.eu.org' && wget -q -O /tmp/install.sh $url/install.sh && sh /tmp/install.sh && . /etc/profile &> /dev/null

# 6
export url='https://testingcf.jsdelivr.net/gh/juewuy/ShellCrash@master' \
&& sh -c "$(curl -kfsSl $url/install.sh)" \
&& . /etc/profile &> /dev/null
```

## Дополнительные ссылки

- [Скачать PuTTY](https://github.com/aews/lyq/releases/download/AX6000/putty.zip)
- [Веб-интерфейс ShellClash](http://192.168.31.1:9999/ui)
- Рекомендуемый провайдер: http://www.txyun.xyz/
- Telegram: https://t.me/kjfxlyq
