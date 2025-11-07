#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_KEY 50
#define MAX_VALUE 100

struct Record {
    char key[MAX_KEY];
    char value[MAX_VALUE];
};

void addRecord(const char *filename, const char *key, const char *value) {
    FILE *fp = fopen(filename, "a");
    if (!fp) { perror("Open file"); exit(1); }
    fprintf(fp, "%s=%s\n", key, value);
    fclose(fp);
}

void getRecord(const char *filename, const char *key) {
    FILE *fp = fopen(filename, "r");
    if (!fp) { perror("Open file"); exit(1); }
    char line[200];
    while (fgets(line, sizeof(line), fp)) {
        char k[MAX_KEY], v[MAX_VALUE];
        sscanf(line, "%[^=]=%s", k, v);
        if (strcmp(k, key) == 0) {
            printf("Value: %s\n", v);
            fclose(fp);
            return;
        }
    }
    printf("Key not found.\n");
    fclose(fp);
}

int main() {
    int choice;
    char key[MAX_KEY], value[MAX_VALUE];
    const char *file = "kvdb.txt";

    printf("1. Add Record\n2. Get Record\n> ");
    scanf("%d", &choice);
    getchar();

    if (choice == 1) {
        printf("Enter key: "); fgets(key, sizeof(key), stdin);
        printf("Enter value: "); fgets(value, sizeof(value), stdin);
        key[strcspn(key, "\n")] = 0; value[strcspn(value, "\n")] = 0;
        addRecord(file, key, value);
    } else if (choice == 2) {
        printf("Enter key: "); fgets(key, sizeof(key), stdin);
        key[strcspn(key, "\n")] = 0;
        getRecord(file, key);
    }
    return 0;
}
