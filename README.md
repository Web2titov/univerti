int LS13(int argc, char **argv) {
  FILE *file;
  size_t size = 0;
  regmatch_t match;
  regex_t preg_storage;
  regex_t *preg = &preg_storage;

  char *line = 0;
  char **pattern = &argv[1];  // указатель на адрес аргумена
  char **end = &argv[argc];

  for (; pattern != end && pattern[0][0] == '-';
       ++pattern)  // пока патерн не равен концу и первый агрумент равен -
                   // переберать патерны
    ;

  if (pattern == end) {  // если паттерн равен последнему аргументу
    fprintf(stderr, "no pattern\n");  // выдаем ошибку
    exit(1);                          // выходим из программы
  }

  if (regcomp(preg, *pattern, 0)) {  // компилирует регулярное выражение.
                                     // принимаем куда записать регулярные
    fprintf(stderr, "failed to cimpile regex\n");  // fprint выдает ошибку
    exit(1);  // выходит из программы
  }

  for (char **filename = pattern + 1; filename != end;
       ++filename) {  // для массива строк равному следущему после паттерна
                      // значению, переберать до конца
    if (**filename == '-') continue;
    file = fopen(*filename, "rb");
    if (errno) {  // вызывается только если возвращаемое значение указывает на
                  // ошибку
      fprintf(stderr, "%s",
              argv[0]);  // выдает ошибку с названием первого аргумента
      perror(*filename);  // выводит описательное сообщение об ошибке в stderr
      continue;           // повторяет итерацию
    }

    while (getline(&line, &size, file)) {  // пока сканируем строки
      if (!regexec(preg, line, 1, &match, 0)) {  // до отсутсвия совпадений
        printf("%s", line);
        exit(1);  // печатаем линию
      }
    }
  }
  free(line);
}

int main(int argc, char **argv) {
  int opt = 0;
  while (opt = getopt(argc, argv, "abc")) {
    switch (opt) {
      case 'a':
        LS13(argc, argv);
        break;
    }
  }

  typedef struct {
    int a;
    int b;
    int c;
    int regex_flag;
    bool invert;
    bool count;
    bool filesMatch;
    bool numberLine;
  } Flags;
  // FILE *file;
  Flags flags = {0, false, false, false, false};

  (void)flags;  // обнуление флагов
}
