# Домашнее задание к работе 19

## Условие задачи
Разработать программу, которая записывает в файл поля структуры данных для управления задачами. Каждая задача должна содержать информацию о названии, дате создания, исполнителе, статусе, оценке времени и фактически затраченных часах

## 1. Реализация программы

```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <time.h>
#include <string.h>
#include <locale.h>

typedef enum {
    NEW,
    IN_PROGRESS,
    COMPLETED,
    CANCELLED
} Status;

struct Task {
    char name[100];
    time_t creation_date;
    char assignee[50];
    enum Status status;
    int estimated_hours;
    int actual_hours;
};

int write_tasks_to_file(char* filename, struct Task* tasks, int size) {
    FILE* out;

    if ((out = fopen(filename, "wt")) == NULL) {
        printf("Ошибка открытия файла для записи\n");
        return 0;
    }

    for (int i = 0; i < size; i++) {
        struct tm* timeinfo = localtime(&tasks[i].creation_date);

        char status_str[20];
        switch (tasks[i].status) {
            case NEW: strcpy(status_str, "Новая"); break;
            case IN_PROGRESS: strcpy(status_str, "В работе"); break;
            case COMPLETED: strcpy(status_str, "Завершена"); break;
            case CANCELLED: strcpy(status_str, "Отменена"); break;
        default: strcpy(status_str, "Неизвестно"); break;
        }

        fprintf(out, "Имя:%s; Дата_создания:%02d:%02d:%04d; Исполнитель:%s; Статус:%s; Оценка_времени:%d; Фактические_часы:%d\n",
            tasks[i].name,
            timeinfo->tm_mday,
            timeinfo->tm_mon + 1,  // месяц от 0 до 11
            timeinfo->tm_year + 1900,  // год с 1900
            tasks[i].assignee,
            status_str,
            tasks[i].estimated_hours,
            tasks[i].actual_hours);
    }

    fclose(out);
    return 1; 
}


int main() {
    setlocale(LC_ALL, "");

    // Тестовые задачи
    struct Task tasks[3];

    // Задача 1
    strcpy(tasks[0].name, "Разработать_приложение");
    tasks[0].creation_date = time(NULL);  // текущее время
    strcpy(tasks[0].assignee, "Иван_Иванов");
    tasks[0].status = IN_PROGRESS;
    tasks[0].estimated_hours = 40;
    tasks[0].actual_hours = 25;

    // Задача 2
    strcpy(tasks[1].name, "Написать_отчет");
    tasks[1].creation_date = time(NULL) - 86400;  // вчера
    strcpy(tasks[1].assignee, "Петр_Петров");
    tasks[1].status = NEW;
    tasks[1].estimated_hours = 20;
    tasks[1].actual_hours = 0;

    // Задача 3
    strcpy(tasks[2].name, "Сдать_отчет");
    tasks[2].creation_date = time(NULL) - 172800;  // позавчера
    strcpy(tasks[2].assignee, "Анна_Сидорова");
    tasks[2].status = COMPLETED;
    tasks[2].estimated_hours = 15;
    tasks[2].actual_hours = 18;

    // Запись
    if (write_tasks_to_file("tasks.txt", tasks, 3)) {
        printf("Задачи успешно записаны в файл tasks.txt\n");
    }
    else {
        printf("Ошибка записи в файл\n");
    }

    return 0;
}
```

## 3. Результаты работы программы

### Вывод в консоль:
```
Задачи успешно записаны в файл tasks.txt
```

### Содержимое файла `tasks.txt`:
```
Имя:Разработать_приложение; Дата:16:12:2025; Исполнитель:Иван_Иванов; Статус:В работе; Планируемые_часы:40; Фактические_часы:25
Имя:Написать_отчет; Дата:15:12:2025; Исполнитель:Петр_Петров; Статус:Новая; Планируемые_часы:20; Фактические_часы:0
Имя:Сдать_отчет; Дата:14:12:2025; Исполнитель:Анна_Сидорова; Статус:Завершена; Планируемые_часы:15; Фактические_часы:18
```

### Пояснение формата записи:
Каждая строка файла содержит информацию об одной задаче в следующем формате:
```
Имя:[название]; Дата_создания:[дд:мм:гггг]; Исполнитель:[ФИО]; Статус:[статус]; Оценка_времени:[часы]; Фактические_часы:[часы]
```

## 4. Информация о разработчике

Маркина Елена, бТИИ-251
