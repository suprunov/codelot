Скрипт принимает два параметра: путь к файлу и операцию (add или remove). В зависимости от операции, он либо добавляет "### >>> " в начало каждой строки файла, либо удаляет "### >>> " из начала каждой строки файла.

```bash
#!/bin/bash

# Проверяем наличие двух аргументов
if [ $# -ne 2 ]; then
    echo "Usage: $0 <file_path> <add|remove>"
    exit 1
fi

file_path="$1"
operation="$2"

# Проверяем существование файла
if [ ! -f "$file_path" ]; then
    echo "File not found: $file_path"
    exit 1
fi

# Сохраняем текущие права доступа к файлу
file_permissions=$(stat -c %a "$file_path")

# Функция для добавления подстроки в начало строк
add_prefix() {
    local input_file="$1"
    local output_file="${input_file}.new"
    
    sed 's/^/### >>> /' "$input_file" > "$output_file"
    
    mv "$output_file" "$input_file"
}

# Функция для удаления подстроки из начала строк
remove_prefix() {
    local input_file="$1"
    local output_file="${input_file}.new"
    
    sed 's/^### >>> //' "$input_file" > "$output_file"
    
    mv "$output_file" "$input_file"
}

if [ "$operation" = "add" ]; then
    add_prefix "$file_path"
    echo "Added '### >>> ' prefix to each line in $file_path."
elif [ "$operation" = "remove" ]; then
    remove_prefix "$file_path"
    echo "Removed '### >>> ' prefix from each line in $file_path."
else
    echo "Invalid operation. Use 'add' or 'remove'."
    exit 1
fi

# Восстанавливаем права доступа к файлу
chmod "$file_permissions" "$file_path"
```