#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

struct Student {
    char Name[40];
    char FatherName[30];
    char Class[10];
    int Roll;
    char Address[100];
    char PhoneNumber[15];
    char Date[12];
} s;

struct User {
    char username[30];
    char password[30];
};

FILE *fp;
long sz = sizeof(s);

void input();
void display();
void Search();
void Modify();
void Delete();
void Sort();
void Search_By_Roll();
void Search_By_Name();
void createAccount();
int login();
void forgetPassword();

int main() {
    int ch;
    int loggedIn = 0;

    FILE *userFile = fopen("users.dat", "ab");
    if (userFile == NULL) {
        return 1;
    }
    fclose(userFile);

    while (!loggedIn) {
        system("cls");
        printf("1. Login\n2. Create Account\n3. Forget Password\n0. Exit\nEnter your choice: ");
        scanf("%d", &ch);
        getchar();

        switch (ch) {
            case 0:
                exit(0);
            case 1:
                loggedIn = login();
                break;
            case 2:
                createAccount();
                break;
            case 3:
                forgetPassword();
                break;
        }
        if (!loggedIn) {
            getchar();
        }
    }

    while (1) {
        system("cls");
        printf("1. Take Admission\n2. Student Records\n3. Search\n4. Modify\n5. Delete\n6. Sort By Roll\n0. Exit\nEnter your choice: ");
        scanf("%d", &ch);
        getchar();

        switch (ch) {
            case 0:
                exit(0);
            case 1:
                input();
                break;
            case 2:
                display();
                break;
            case 3:
                Search();
                break;
            case 4:
                Modify();
                break;
            case 5:
                Delete();
                break;
            case 6:
                Sort();
                break;
        }
        getchar();
    }
    return 0;
}

void createAccount() {
    struct User user;
    printf("Enter a username: ");
    scanf("%s", user.username);
    printf("Enter a password: ");
    scanf("%s", user.password);

    FILE *userFile = fopen("users.dat", "ab");
    if (userFile == NULL) {
        return;
    }

    fwrite(&user, sizeof(struct User), 1, userFile);
    fclose(userFile);
}

int login() {
    struct User fileUser;
    char username[30], password[30];
    int found = 0;

    printf("Username: ");
    scanf("%s", username);
    printf("Password: ");
    scanf("%s", password);

    FILE *userFile = fopen("users.dat", "rb");
    if (userFile == NULL) {
        return 0;
    }

    while (fread(&fileUser, sizeof(struct User), 1, userFile) == 1) {
        if (strcmp(username, fileUser.username) == 0) {
            found = 1;
            if (strcmp(password, fileUser.password) == 0) {
                fclose(userFile);
                return 1;
            } else {
                fclose(userFile);
                return 0;
            }
        }
    }

    fclose(userFile);
    return 0;
}

void forgetPassword() {
    struct User fileUser;
    char username[30];
    int found = 0;
    printf("Enter your username: ");
    scanf("%s", username);

    FILE *userFile = fopen("users.dat", "rb");
    if (userFile == NULL) {
        return;
    }

    while (fread(&fileUser, sizeof(struct User), 1, userFile) == 1) {
        if (strcmp(username, fileUser.username) == 0) {
            found = 1;
            printf("Your password is: %s\n", fileUser.password);
            break;
        }
    }
    fclose(userFile);
}

void input() {
    time_t t = time(NULL);
    struct tm tm = *localtime(&t);
    sprintf(s.Date, "%02d/%02d/%d", tm.tm_mday, tm.tm_mon + 1, tm.tm_year + 1900);

    fp = fopen("Student.txt", "ab");
    if (fp == NULL) {
        return;
    }

    printf("Enter Name: ");
    fflush(stdin);
    fgets(s.Name, sizeof(s.Name), stdin);
    s.Name[strcspn(s.Name, "\n")] = 0;

    printf("Enter Father's Name: ");
    fgets(s.FatherName, sizeof(s.FatherName), stdin);
    s.FatherName[strcspn(s.FatherName, "\n")] = 0;

    printf("Enter Class: ");
    fgets(s.Class, sizeof(s.Class), stdin);
    s.Class[strcspn(s.Class, "\n")] = 0;

    printf("Enter Roll Number: ");
    scanf("%d", &s.Roll);
    getchar();

    printf("Enter Address: ");
    fgets(s.Address, sizeof(s.Address), stdin);
    s.Address[strcspn(s.Address, "\n")] = 0;

    printf("Enter Phone Number: ");
    fgets(s.PhoneNumber, sizeof(s.PhoneNumber), stdin);
    s.PhoneNumber[strcspn(s.PhoneNumber, "\n")] = 0;

    fwrite(&s, sz, 1, fp);
    fclose(fp);
}

void display() {
    fp = fopen("Student.txt", "rb");
    if (fp == NULL) {
        return;
    }

    while (fread(&s, sz, 1, fp) == 1) {
        printf("%s %s %s %d %s %s %s\n", s.Name, s.FatherName, s.Class, s.Roll, s.Address, s.PhoneNumber, s.Date);
    }
    fclose(fp);
}
