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
