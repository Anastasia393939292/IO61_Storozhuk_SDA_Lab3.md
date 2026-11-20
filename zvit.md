# Лабораторна робота № 3

### Виконав
Студент групи: ІО-61

Cторожук Анастасія Василівна 

номер у списку групи: 25

### Перевірив

Русінов Володимир Володимирович


## Стеки та черги

### Дисципліна:Структури даних та алгоритми (СДА)

### Мова програмування: C

# 1. Мета лабораторної роботи

Засвоєння теоретичного матеріалу та набуття практичних навичок реалізації й застосування абстрактних структур даних стек (LIFO — «останнім прийшов, першим вийшов») та черга (FIFO — «першим прийшов, першим вийшов»), а також порівняння двох способів їхньої реалізації — на основі масиву фіксованого розміру та на основі однозв'язного списку.

### Завдання варіанта 25

Одночасно використовуючи стек і чергу, перевірити, чи є вхідне слово паліндромом: символи слова одночасно додаються в стек і в чергу; далі елементи виймаються по одному з обох структур і порівнюються (стек видає символи у зворотному порядку, черга — у прямому). Вивести «так»/«ні».

# Код виконання 

```c

#include <stdio.h>
#include <string.h>

#define MAX 100

struct ArrayStack {
    char items[MAX];
    int top;
};

void init_stack(struct ArrayStack *s) {
    s->top = -1;
}

int is_empty_stack(struct ArrayStack *s) {
    return s->top == -1;
}

int is_full_stack(struct ArrayStack *s) {
    return s->top == MAX - 1;
}

int push(struct ArrayStack *s, char c) {
    if (is_full_stack(s)) {
        printf("Стек повний, не можу додати '%c'\n", c);
        return 0;
    }
    s->top++;
    s->items[s->top] = c;
    return 1;
}

int pop(struct ArrayStack *s, char *out) {
    if (is_empty_stack(s)) {
        printf("Стек порожній, дістати нічого\n");
        return 0;
    }
    *out = s->items[s->top];
    s->top--;
    return 1;
}

int peek(struct ArrayStack *s, char *out) {
    if (is_empty_stack(s)) {
        return 0;
    }
    *out = s->items[s->top];
    return 1;
}

struct ArrayQueue {
    char items[MAX];
    int front;
    int rear;
    int size;
};

void init_queue(struct ArrayQueue *q) {
    q->front = 0;
    q->rear = -1;
    q->size = 0;
}

int is_empty_queue(struct ArrayQueue *q) {
    return q->size == 0;
}

int is_full_queue(struct ArrayQueue *q) {
    return q->size == MAX;
}

int enqueue(struct ArrayQueue *q, char c) {
    if (is_full_queue(q)) {
        printf("Черга повна, не можу додати '%c'\n", c);
        return 0;
    }
    q->rear = (q->rear + 1) % MAX;
    q->items[q->rear] = c;
    q->size++;
    return 1;
}

int dequeue(struct ArrayQueue *q, char *out) {
    if (is_empty_queue(q)) {
        printf("Черга порожня, дістати нічого\n");
        return 0;
    }
    *out = q->items[q->front];
    q->front = (q->front + 1) % MAX;
    q->size--;
    return 1;
}

int peek_front(struct ArrayQueue *q, char *out) {
    if (is_empty_queue(q)) {
        return 0;
    }
    *out = q->items[q->front];
    return 1;
}

int check_palindrome(char word[]) {
    struct ArrayStack s;
    struct ArrayQueue q;
    init_stack(&s);
    init_queue(&q);

    int len = (int)strlen(word);
    for (int i = 0; i < len; i++) {
        if (!push(&s, word[i]) || !enqueue(&q, word[i])) {
            return 0;
        }
    }

    int is_pal = 1;
    while (!is_empty_stack(&s) && !is_empty_queue(&q)) {
        char from_s, from_q;
        pop(&s, &from_s);
        dequeue(&q, &from_q);
        if (from_s != from_q) {
            is_pal = 0;
        }
    }
    return is_pal;
}

int main() {
    char word[200];

    printf("Введи слово: ");
    if (scanf("%199s", word) != 1) {
        return 1;
    }

    if (check_palindrome(word)) {
        printf("так\n");
    } else {
        printf("ні\n");
    }

    struct ArrayStack empty_s;
    struct ArrayQueue empty_q;
    char tmp;
    init_stack(&empty_s);
    init_queue(&empty_q);
    pop(&empty_s, &tmp);
    dequeue(&empty_q, &tmp);

    return 0;
}

```

# 2

```c

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Node {
    char data;
    struct Node *next;
};

struct ListStack {
    struct Node *top;
};

void init_stack(struct ListStack *s) {
    s->top = NULL;
}

int is_empty_stack(struct ListStack *s) {
    return s->top == NULL;
}

int is_full_stack(struct ListStack *s) {
    (void)s;
    return 0;
}

int push(struct ListStack *s, char c) {
    struct Node *node = (struct Node *)malloc(sizeof(struct Node));
    if (node == NULL) {
        printf("Не вистачило пам'яті для стека\n");
        return 0;
    }
    node->data = c;
    node->next = s->top;
    s->top = node;
    return 1;
}

int pop(struct ListStack *s, char *out) {
    if (is_empty_stack(s)) {
        printf("Стек порожній, дістати нічого\n");
        return 0;
    }
    struct Node *temp = s->top;
    *out = temp->data;
    s->top = temp->next;
    free(temp);
    return 1;
}

int peek(struct ListStack *s, char *out) {
    if (is_empty_stack(s)) {
        return 0;
    }
    *out = s->top->data;
    return 1;
}

void clear_stack(struct ListStack *s) {
    char tmp;
    while (!is_empty_stack(s)) {
        pop(s, &tmp);
    }
}

struct ListQueue {
    struct Node *front;
    struct Node *rear;
};

void init_queue(struct ListQueue *q) {
    q->front = NULL;
    q->rear = NULL;
}

int is_empty_queue(struct ListQueue *q) {
    return q->front == NULL;
}

int is_full_queue(struct ListQueue *q) {
    (void)q;
    return 0;
}

int enqueue(struct ListQueue *q, char c) {
    struct Node *node = (struct Node *)malloc(sizeof(struct Node));
    if (node == NULL) {
        printf("Не вистачило пам'яті для черги\n");
        return 0;
    }
    node->data = c;
    node->next = NULL;

    if (is_empty_queue(q)) {
        q->front = node;
        q->rear = node;
    } else {
        q->rear->next = node;
        q->rear = node;
    }
    return 1;
}

int dequeue(struct ListQueue *q, char *out) {
    if (is_empty_queue(q)) {
        printf("Черга порожня, дістати нічого\n");
        return 0;
    }
    struct Node *temp = q->front;
    *out = temp->data;
    q->front = temp->next;
    if (q->front == NULL) {
        q->rear = NULL;
    }
    free(temp);
    return 1;
}

int peek_front(struct ListQueue *q, char *out) {
    if (is_empty_queue(q)) {
        return 0;
    }
    *out = q->front->data;
    return 1;
}

void clear_queue(struct ListQueue *q) {
    char tmp;
    while (!is_empty_queue(q)) {
        dequeue(q, &tmp);
    }
}

int check_palindrome(char word[]) {
    struct ListStack s;
    struct ListQueue q;
    init_stack(&s);
    init_queue(&q);

    int len = (int)strlen(word);
    for (int i = 0; i < len; i++) {
        if (!push(&s, word[i]) || !enqueue(&q, word[i])) {
            clear_stack(&s);
            clear_queue(&q);
            return 0;
        }
    }

    int is_pal = 1;
    while (!is_empty_stack(&s) && !is_empty_queue(&q)) {
        char from_s, from_q;
        pop(&s, &from_s);
        dequeue(&q, &from_q);
        if (from_s != from_q) {
            is_pal = 0;
        }
    }

    clear_stack(&s);
    clear_queue(&q);
    return is_pal;
}

int main() {
    char word[200];

    printf("Введи слово: ");
    if (scanf("%199s", word) != 1) {
        return 1;
    }

    if (check_palindrome(word)) {
        printf("так\n");
    } else {
        printf("ні\n");
    }

    struct ListStack empty_s;
    struct ListQueue empty_q;
    char tmp;
    init_stack(&empty_s);
    init_queue(&empty_q);
    pop(&empty_s, &tmp);
    dequeue(&empty_q, &tmp);

    return 0;
}

```

# Cкрін виконання

<img width="1470" height="956" alt="Знімок екрана 2026-11-20 о 11 09 24" src="https://github.com/user-attachments/assets/719bbedc-06bb-4f8c-b2e2-4b2ce18c766c" />
<img width="1470" height="956" alt="Знімок екрана 2026-11-20 о 11 09 18" src="https://github.com/user-attachments/assets/5b4cc884-65a3-41f3-972b-ba3332c3752e" />
<img width="1470" height="956" alt="Знімок екрана 2026-11-20 о 11 08 48" src="https://github.com/user-attachments/assets/e102e787-a264-424c-9d5d-0a5303d523a4" />

# Відповіді на контрольні питання

| № | Контрольне питання | Відповідь |
| :--- | :--- | :--- |
| **1** | Означення структури даних «стек» — принцип LIFO. Приклади застосування. | **Стек** — це лінійна структура даних, яка працює за принципом **LIFO** (*Last In, First Out* — «останнім прийшов, першим вийшов»). Додавання та видалення елементів відбуваються лише з вершини (`top`). **Приклади:** кнопка «Назад» у браузері, `Ctrl + Z`, стек викликів функцій, перевірка дужок, обчислення постфіксних виразів. |
| **2** | Означення структури даних «черга» — принцип FIFO. Приклади застосування. | **Черга** — це лінійна структура даних, яка працює за принципом **FIFO** (*First In, First Out* — «першим прийшов, першим вийшов»). Нові елементи додаються в кінець (`rear`), а видаляються з початку (`front`). **Приклади:** черга на друк, обробка запитів на сервері, буферизація даних. |
| **3** | Реалізація стека на основі масиву: які поля потрібні, як реалізуються `push` / `pop` / `peek`. | Потрібен масив (`items[MAX]`) та індекс вершини `top` (`-1` для порожнього стека). **`push`:** перевіряє, чи стек не повний, збільшує `top` на 1 і записує елемент. **`pop`:** перевіряє, чи стек не порожній, бере елемент із `items[top]` і зменшує `top` на 1. **`peek`:** перевіряє, чи стек не порожній, і повертає `items[top]` без видалення. |
| **4** | Реалізація черги на основі масиву: чому потрібен кільцевий (циклічний) масив; що станеться, якщо не зробити його кільцевим. | Кільцевий масив (`% MAX`) дає повторно використовувати звільнені комірки на початку масиву після `dequeue`. Якщо не зробити чергу кільцевою, доведеться щоразу зсувати елементи вліво (втрачається час O(1)) або хвіст дійде до кінця масиву і черга вважатиметься повною, навіть коли початок уже вільний. |
| **5** | Реалізація стека на основі однозв'язного списку: з якого кінця списку зручніше додавати й видаляти елементи, і чому. | Зручніше **з початку (голови) списку**, щоб `push` і `pop` виконувалися за сталий час O(1) без проходу по всьому списку. Якщо працювати з кінця однозв'язного списку, для видалення останнього елемента довелося б шукати передостанній вузол за час O(n). |
| **6** | Реалізація черги на основі однозв'язного списку: чому для неї зручно тримати одразу два вказівники — на голову і на хвіст. | Вказівник на початок (`front`) дозволяє швидко видаляти елементи (`dequeue`) за O(1), а вказівник на кінець (`rear`) — швидко додавати нові елементи (`enqueue`) також за O(1) без проходу від початку списку до кінця. |
| **7** | Порівняння масивної та спискової реалізацій: обмеження на розмір, використання пам'яті, за яких умов кожна доцільніша. | **Розмір:** у масиві обмежений константою `MAX`, у списку — доступною пам'яттю. **Пам'ять:** масив одразу виділяє пам'ять під `MAX` елементів, а список — динамічно під кожен вузол (`malloc`), але витрачає додаткову пам'ять на вказівник `next` і потребує `free`. **Коли доцільніша:** масив — коли розмір відомий і невеликий; список — коли розмір наперед невідомий або змінюється. |
| **8** | Що станеться при виклику `pop` / `dequeue` на порожній структурі і як це коректно обробити в програмі? | Без перевірки може статися вихід за межі масиву, читання сміття або розіменування `NULL` (аварійне завершення програми). Потрібно спочатку перевірити `is_empty`, вивести повідомлення та повернути код помилки (`0`), щоб програма працювала далі без збоїв. |
| **9** | Яким чином стек застосовується для перевірки збалансованості дужок? | Під час проходу по рядку відкривні дужки кладуться в стек (`push`), а при зустрічі закривної дужки знімається/перевіряється вершина стека на відповідність. Наприкінці стек має бути порожнім; якщо стек порожній або дужки не збігаються — баланс порушено. |
| **10** | Яким чином стек застосовується для обчислення виразу в постфіксній формі? | Вираз читається зліва направо: числа кладуться в стек (`push`), а при зустрічі оператора дістаються два операнди (`pop`), виконується дія і результат кладеться назад у стек. Після завершення виразу у стеку залишається підсумковий результат. |
| **11** | У чому різниця між чергою (`queue`) і двосторонньою чергою (`deque`)? | У звичайній черзі (`queue`) додавання відбувається лише в кінець, а видалення — лише з початку (FIFO). У двосторонній черзі (`deque`) додавати й видаляти елементи можна **з обох кінців** (і з початку, і з кінця). |
| **12** | Яким чином рекурсія пов'язана зі стеком (стек викликів)? | Кожен виклик функції створює кадр зі змінними, параметрами та адресою повернення у **стеку викликів** (*call stack*). Коли рекурсивний виклик завершується, його кадр знімається зі стека і керування повертається до попереднього виклику. |
