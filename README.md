# gns3-telnet-wrapper
fix error like: xdgopen cant open gns3+telnet://192.168.0.14:5003

# Create gns3-telnet.desktop
```
vim ~/.local/share/applications/gns3-telnet.desktop
```
# Paste text
```
[Desktop Entry]
Version=1.0
Name=GNS3 Telnet Handler
Exec=/path/to/your/script %u
Type=Application
Terminal=false
MimeType=x-scheme-handler/gns3+telnet;
```
# Create and register wrapper for xdg-utils in /path/to/your/script
```
chmod +x /path/to/your/script
```
```/path/to/your/script
#!/usr/bin/env python
import sys
import urllib.parse
import subprocess


def main(url):
    # Парсим URL
    parsed_url = urllib.parse.urlparse(url)

    # Проверяем, что схема правильная
    if parsed_url.scheme != 'gns3+telnet':
        print("Неправильный протокол. Ожидался 'gns3+telnet'.")
        return

    # Извлекаем хост и порт
    host = parsed_url.hostname
    port = parsed_url.port

    # Если порт не указан, используем стандартный для Telnet
    if port is None:
        port = 23

    # Извлекаем параметры
    params = urllib.parse.parse_qs(parsed_url.query)
    name = params.get('name', [None])[0]
    project_id = params.get('project_id', [None])[0]
    node_id = params.get('node_id', [None])[0]

    # Выводим информацию
    print(f"Подключение к {host}:{port} с именем {
          name}, project_id {project_id}, node_id {node_id}")

    # Команда для открытия терминала и выполнения Telnet
    command = f"telnet {host} {port}"

    # Открываем новый терминал и выполняем команду
    try:
        # Замените 'konsole' на ваш терминал, если необходимо
        subprocess.run(['konsole', '-e', command])
    except Exception as e:
        print(f"Ошибка при открытии терминала: {e}")


if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Использование: python script.py 'gns3+telnet://<IP>:<PORT>?name=<NAME>&project_id=<PROJECT_ID>&node_id=<NODE_ID>'")
        sys.exit(1)

    url = sys.argv[1]
    main(url)
```
registration of wrapper
```
xdg-mime default gns3-telnet.desktop x-scheme-handler/gns3+telnet
```
