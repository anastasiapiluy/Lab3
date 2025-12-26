# Отчет по лабораторной работе №3
#### № группы: `ПМ-2502`
#### Выполнила: `Пилуй Анастасия Даниловна`
#### Вариант: `14`

- [Постановка задачи](#1-постановка-задачи)
- [Описание функционала](#2-описание-функционала)
- [Программа](#3-программа)
- [Анализ правильности решения](#4-анализ-правильности-решения)

### 1. Постановка задачи

> Разработать программу для реализации системы контроля версий, предназначенной
для работы со стихотворениями. Реализовать функционал добавления, удаления и изменения строк, сохранения состояний, отката к предыдущим версиям и сравнения изменений между ними.

### 2. Описание функционала


#### 1. Создание объекта гита

Создание объекта гита по названию стихотворения, автору и максимальному количеству сохраняемых версий.

#### 2. Вывод стихотворения (переопределение toString)

Отображение текущей версии стихотворения в формате: «НазваниеСтиха - ИмяПоэта» и строки построчно.

#### 3. Добавление строки

Добавление новой строки в конец текущего стихотворения.

#### 4. Удаление строки по номеру

Удаление строки с указанным номером (нумерация начинается с 1).

#### 5. Вставка строки по номеру

Вставка новой строки в указанное место, сдвигая последующие строки вниз.

#### 6. Сохранение версии

Сохранение текущей версии стихотворения. Если количество сохранений превышает максимум, самое старое сохранение удаляется. Обратите внимание: для корректного сохранения массив строк копируется, а не передаётся по ссылке.

#### 7. Просмотр сохранений

Вывод сохранённой версии. Нумерация версий: 0 — текущая версия, 1 — последнее
сохранение, и так далее.

#### 8. Удаление сохранения

Удаление указанного сохранения. Более ранние сохранения, если были удалены,
не восстанавливаются.

#### 9. Откат к сохранению

Откат текущей версии к указанному сохранению. При этом текущая версия и все
более поздние сохранения стираются.

#### 10. Сравнение версий

Сравнение двух версий или текущей версии с одной из сохранённых:

• Если передана одна версия, она сравнивается с текущей.

• Если переданы две версии, они сравниваются между собой.

#### 11. Общие строки между версиями

Вывод строк, которые есть в обеих версиях, в порядке, в котором они встречаются
в более новой версии.

#### 12. Различные строки между версиями

Вывод строк, которые встречаются только в одной из версий:

• Строки из старой версии, которых нет в новой, помечаются знаком «-» и
выводятся в порядке их появления в старой версии.

• Строки из новой версии, которых нет в старой, помечаются знаком «+» и
выводятся в порядке их появления в новой версии.


### 3. Программа

#### 3.1 Класс Version

> Класс хранит одну определенную версию стихотворения

```java
public class Version {
    private String[] str;
    public Version(String[] add) {
        str = new String[add.length];
        for (int i = 0; i < add.length; i++) {
            str[i]=add[i];
        }
    }
    public String[] getter(){
        String[] newStr = new String[str.length];
        for (int i = 0; i < newStr.length; i++) {
            newStr[i]=str[i];
        }
        return newStr;
    }
}
```

#### 3.2 Класс PoemGit

> Класс для работы со стихотворением, содержащий в себе все методы, требуемые по заданию. Хранит текущую версию и массив сохраненных версий

```java
import java.util.Objects;

public class PoemGit {
    private String poemName;
    private String author;
    private int max;
    private String[] currentVersion;
    private Version[] allVersions;

    public PoemGit(String poemName, String author, int max){
        this.poemName = poemName;
        this.author = author;
        this.max = max;
        this.currentVersion = new String[0];
        this.allVersions = new Version[0];
    }

    public String[] getCurrentVersion() {
        return this.currentVersion;
    }

    public Version[] getAllVersions() {
        Version[] newGit = new Version[allVersions.length];
        for (int i = 0; i < allVersions.length; i++) {
            newGit[i] = allVersions[i];
        }
        return newGit;
    }

    public void addStr(String str) {
        String[] newStr = new String[currentVersion.length + 1];
        for (int i = 0; i < currentVersion.length; i++) {
            newStr[i]=currentVersion[i];
        }
        newStr[currentVersion.length] = str;
        this.currentVersion = newStr;
    }

    public void deleteStr(int ind){
        String[] newStr = new String[currentVersion.length-1];
        int k=0;
        for (int i = 0; i < currentVersion.length; i++) {
            if (i!=ind-1) {
                newStr[k] = currentVersion[i];
                k++;
            }
        }
        this.currentVersion=newStr;
    }

    public void insertStr(int ind, String str){
        if (ind < 0) ind = 0;
        if (ind > currentVersion.length) ind = currentVersion.length;
        String[] newStr = new String[currentVersion.length + 1];
        for (int i = 0; i < ind; i++) {
            newStr[i] = currentVersion[i];
        }
        newStr[ind] = str;
        for (int i = ind; i < currentVersion.length; i++) {
            newStr[i + 1] = currentVersion[i];
        }
        this.currentVersion=newStr;
    }

    public void saving() {
        Version[] newVersions;
        if (allVersions.length>=max){
            newVersions = new Version[max];
        }
        else {
            newVersions = new Version[allVersions.length+1];
        }
        newVersions[0]=new Version(currentVersion);
        for (int i = 1; i < newVersions.length; i++) {
            newVersions[i]=allVersions[i-1];
        }
        this.allVersions = newVersions;
    }

    public Version getVersion(int ind) {
        if (ind==0) {
            return new Version (currentVersion);
        }
        else{
            return allVersions[ind-1];
        }
    }

    public void deleteVersion (int ind){
        Version[] newVersions = new Version[allVersions.length-1];
        int k=0;
        if (ind<1 || ind> allVersions.length){
            return;
        }
        for (int i = 0; i < allVersions.length; i++) {
            if (i!=ind-1){
                newVersions[k]=allVersions[i];
                k++;
            }
        }
        this.allVersions=newVersions;
    }

    public void goBack(int ind){
        if (ind < 1 || ind > allVersions.length){
            return;
        }
        Version last = allVersions[ind-1];
        currentVersion=last.getter();
        Version[] newVersions = new Version[allVersions.length-ind];
        for (int i = ind; i < allVersions.length; i++) {
            newVersions[i-ind] = allVersions[i];
        }
        allVersions=newVersions;
    }

    private static boolean contain (String[] ls, String s){
        for (int i = 0; i < ls.length; i++) {
            if (Objects.equals(ls[i], s)){
                return true;
            }
        }
        return false;
    }

    public static String[] commonStr(Version neew, Version old){
        String[] n = neew.getter();
        String[] o = old.getter();
        int count =0;
        int k= 0;
        for (int i = 0; i < n.length; i++) {
            if (contain(o, n[i])) {
                count++;
            }
        }
        String[] res = new String[count];
        for (int i = 0; i < n.length; i++) {
            if(contain(o,n[i])){
                res[k]=n[i];
                k++;
            }
        }
        return res;
    }

    public static String[] difference(Version neew, Version old){
        String[] n = neew.getter();
        String[] o = old.getter();
        int count = 0;
        int k=0;
        for (int i = 0; i < n.length; i++) {
            if(!contain(o,n[i])){
                count++;
            }
        }
        for (int i = 0; i < o.length; i++) {
            if(!contain(n,o[i])){
                count++;
            }
        }
        String[] res = new String[count];
        for (int i = 0; i < o.length; i++) {
            if (!contain(n,o[i])){
                res[k]="- " + o[i];
                k++;
            }
        }
        for (int i = 0; i < n.length; i++) {
            if(!contain(o,n[i])){
                res[k]="+ " + n[i];
                k++;
            }
        }
        return res;
    }

    @Override
    public String toString(){
        String res = poemName +" - " +author + "\n";
        for (int i = 0; i < currentVersion.length; i++) {
            res+=currentVersion[i]+"\n";
        }
        return res;
    }
}
```

#### 3.3 Класс Test

> Отдельный класс для тестирования всех методов класса PoemGit

```java
import java.util.Scanner;

public class Test {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.println("Введите название стихотворения:");
        String name = sc.nextLine();
        System.out.println("Введите автора:");
        String author = sc.nextLine();
        System.out.println("Введите максимальное количество сохраняемых версий:");
        int max = sc.nextInt();
        sc.nextLine();

        PoemGit git = new PoemGit(name, author, max);

        boolean exit = false;
        while (!exit) {
            System.out.println("\n---------- Выберите действие ----------");
            System.out.println("1. Добавить строку");
            System.out.println("2. Удалить строку по номеру");
            System.out.println("3. Вставить строку по номеру");
            System.out.println("4. Сохранить версию");
            System.out.println("5. Показать версию по номеру");
            System.out.println("6. Показать текущую версию");
            System.out.println("7. Показать все версии");
            System.out.println("8. Откат к сохранению");
            System.out.println("9. Показать различные строки версий");
            System.out.println("10. Показать общие строки версий");
            System.out.println("11. Удалить версию по номеру");
            System.out.println("12. Показать текущее стихотворение");
            System.out.println("0. Выход");

            int choice = sc.nextInt();
            sc.nextLine();

            if (choice == 1) {
                System.out.println("Введите строку для добавления:");
                String str = sc.nextLine();
                git.addStr(str);
                System.out.println("----- Строка добавлена -----");

            } else if (choice == 2) {
                System.out.println("Введите номер строки для удаления:");
                int deleteInd = sc.nextInt();
                sc.nextLine();
                git.deleteStr(deleteInd);
                System.out.println("----- Строка удалена -----");

            } else if (choice == 3) {
                System.out.println("Введите индекс для вставки строки:");
                int insertInd = sc.nextInt();
                sc.nextLine();
                System.out.println("Введите строку для вставки:");
                String insLine = sc.nextLine();
                git.insertStr(insertInd, insLine);
                System.out.println("----- Строка вставлена -----");
                System.out.println(git);

            } else if (choice == 4) {
                git.saving();
                System.out.println("----- Версия успешно сохранена -----");

            } else if (choice == 5) {
                System.out.println("Введите индекс версии для просмотра:");
                int ind = sc.nextInt();
                sc.nextLine();
                Version newVersion = git.getVersion(ind);
                System.out.println("Версия " + ind + ":");
                for (String s : newVersion.getter()){
                    System.out.println(s);
                }

            } else if (choice == 6) {
                System.out.println("----- Текущая версия -----");
                for (String s : git.getCurrentVersion()){
                    System.out.println(s);
                }

            } else if (choice == 7) {
                System.out.println("----- Все сохраненные Вами версии -----");
                Version[] all = git.getAllVersions();
                for (int i = 0; i < all.length; i++) {
                    System.out.println("Версия " + (i + 1) + ":");
                    for (String s : all[i].getter()){
                        System.out.println(s);
                    }
                }

            } else if (choice == 8) {
                System.out.println("Введите индекс версии для отката:");
                int back = sc.nextInt();
                System.out.println();
                git.goBack(back);
                System.out.println("----- Откат к заданной версии выполнен -----");

            } else if (choice == 9) {
                System.out.println("Введите индекс версии для сравнения с текущей:");
                int diffIndex = sc.nextInt();
                sc.nextLine();
                Version differentVersion = git.getVersion(diffIndex);
                System.out.println("----- Различия в строках -----");
                for (String s : PoemGit.difference(git.getVersion(0), differentVersion)){
                    System.out.println(s);
                }

            } else if (choice == 10) {
                System.out.println("Введите индекс версии для поиска общих строк с текущей:");
                int commonInd = sc.nextInt();
                sc.nextLine();
                Version common = git.getVersion(commonInd);
                System.out.println("----- Общие строки -----");
                for (String s : PoemGit.commonStr(git.getVersion(0), common)) {
                    System.out.println(s);
                }

            } else if (choice == 11) {
                System.out.println("Введите индекс версии для удаления:");
                int deleteVer = sc.nextInt();
                sc.nextLine();
                git.deleteVersion(deleteVer);
                System.out.println("----- Версия успешно удалена -----");

            } else if (choice == 12) {
                System.out.println("----- Текущее стихотворение -----");
                System.out.println(git);

            } else if (choice == 0) {
                exit = true;
                System.out.println("Выход из программы.");

            } else {
                System.out.println("Введите число от 0 до 12!");
            }
        }
    }
}
```
### 4. Анализ правильности решения

#### После завершения каждого действия выводится меню с выбором следующего действия

- **Output**:
  ```
  Введите название стихотворения:
  ```
- **Input**:
  ```
  Зимнее утро
  ```
- **Output**:
  ```
  Введите автора:
  ```
- **Input**:
  ```
  А.С.Пушкин
  ```
- **Output**:
  ```
  Введите максимальное количество сохраняемых версий:
  ```
- **Input**:
  ```
  3
  ```
- **Output**:
  ```
  ---------- Выберите действие ----------
  1. Добавить строку
  2. Удалить строку по номеру
  3. Вставить строку по номеру
  4. Сохранить версию
  5. Показать версию по номеру
  6. Показать текущую версию
  7. Показать все версии
  8. Откат к сохранению
  9. Показать различные строки версий
  10. Показать общие строки версий
  11. Удалить версию по номеру
  12. Показать текущее стихотворение
  0. Выход
  ```
- **Input**:
  ```
  1
  ```
- **Output**:
  ```
  Введите строку для добавления:
  ```
- **Input**:
  ```
  Мороз и солнце, день чудесный
  ```
- **Output**:
  ```
  ----- Строка добавлена -----
  ```
- **Input**:
  ```
  4
  ```
- **Output**:
  ```
  ----- Версия успешно сохранена -----
  ```
- **Input**:
  ```
  1
  ```
- **Output**:
  ```
  Введите строку для добавления:
  ```
- **Input**:
  ```
  Ещё ты дремлешь, друг прелестный
  ```
- **Output**:
  ```
  ----- Строка доабвлена -----
  ```
- **Input**:
  ```
  2
  ```
- **Output**:
  ```
  Введите номер строки для удаления:
  ```
- **Input**:
  ```
  1
  ```
- **Output**:
  ```
  ----- Строка удалена -----
  ```
- **Input**:
  ```
  3
  ```
- **Output**:
  ```
  Введите индекс для вставки строки:
  ```
- **Input**:
  ```
  0
  ```
- **Output**:
  ```
  Введите строку для вставки:
  ```
- **Input**:
  ```
  Мороз и солнце
  ```
- **Output**:
  ```
  ----- Строка вставлена -----
  ```
- **Input**:
  ```
  4
  ```
- **Output**:
  ```
  ----- Версия успешно сохранена -----
  ```
- **Input**:
  ```
  5
  ```
- **Output**:
  ```
  Введите индекс версии для просмотра:
  ```
- **Input**:
  ```
  1
  ```
- **Output**:
  ```
  Версия 1:
  Мороз и солнце
  День чудесный
  Ещё ты дремлешь, друг прелестный
  ```
- **Input**:
  ```
  6
  ```
- **Output**:
  ```
  ----- Текущая версия -----
  Мороз и солнце
  День чудесный
  Ещё ты дремлешь, друг прелестный
  ```
- **Input**:
  ```
  7
  ```
- **Output**:
  ```
  ----- Все сохраненные Вами версии -----
  Версия 1:
  Мороз и солнце
  День чудесный
  Ещё ты дремлешь, друг прелестный
  Версия 2:
  Мороз и солнце, день чудесный
  ```
- **Input**:
  ```
  8
  ```
- **Output**:
  ```
  Введите индекс версии для отката:
  ```
- **Input**:
  ```
  1
  ```
- **Output**:
  ```
  ----- Откат к заданной версии выполнен -----
  ```
- **Input**:
  ```
  7
  ```
- **Output**:
  ```
  ----- Все сохраненные Вами версии -----
  Версия 1:
  Мороз и солнце
  День чудесный
  Ещё ты дремлешь, друг прелестный
  ```
Добавим еще одну версию:

  Зимнее утро - А.С.Пушкин
Мороз и солнце
День чудесный
Ещё ты дремлешь, друг прелестный
Мороз и солнце
Пушкин

- **Input**:
  ```
  9
  ```
- **Output**:
  ```
  Введите индекс версии для сравнения с текущей:
  ```
- **Input**:
  ```
  2
  ```
- **Output**:
  ```
  + Пушкин
  ```

- **Input**:
  ```
  10
  ```
- **Output**:
  ```
  Введите индекс версии для поиска общих строк с текущей:
  ```
- **Input**:
  ```
  2
  ```
- **Output**:
  ```
  ----- Общие строки -----
  Мороз и солнце
  День чудесный
  Ещё ты дремлешь, друг прелестный
  Мороз и солнце
  ```
- **Input**:
  ```
  11
  ```
- **Output**:
  ```
  Введите индекс версии для удаления:
  ```
- **Input**:
  ```
  1
  ```
- **Output**:
  ```
  ----- Версия успешно удалена -----
  ```
- **Input**:
  ```
  7
  ```
- **Output**:
  ```
  ----- Все сохраненные Вами версии -----
  Версия 1:
  Мороз и солнце
  День чудесный
  Ещё ты дремлешь, друг прелестный
  Мороз и солнце
  Версия 2:
  Мороз и солнце
  День чудесный
  Ещё ты дремлешь, друг прелестный
  ```
- **Input**:
  ```
  12
  ```
- **Output**:
  ```
  ----- Текущее стихотворение -----
  Зимнее утро - А.С.Пушкин
  Мороз и солнце
  День чудесный
  Ещё ты дремлешь, друг прелестный
  Мороз и солнце
  Пушкин
  ```
- **Input**:
  ```
  0
  ```
- **Output**:
  ```
  Выход из программы.

  Process finished with exit code 0
  ```
