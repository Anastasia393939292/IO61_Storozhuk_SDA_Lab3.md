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
