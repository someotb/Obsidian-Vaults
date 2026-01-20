Если запустить  `make`  
то программа попытается найти файл с именем по умолчание `Makefile` в текущем каталоге и выполнить инструкции из него. Если в текущем каталоге есть несколько мейкфайлов, то можно указать на нужный вот таким образом:  `make -f MyMakefile`

##### Самый простой Makefile
В нем должны быть такие части:  

```
цель: зависимости
[tab] команда
```

Пример:
```Makefile
all:
	g++ main.cpp hello.cpp factorial.h -o hello
```

В первом примере цель называется `all`. Это цель по умолчанию для мейкфайла, которая будет выполняться, если никакая другая цель не указана явно. Также у этой цели в этом примере нет никаких зависимостей, так что make сразу приступает к выполнению нужной команды. А команда в свою очередь запускает компилятор.

##### Использование зависимостей
Использовать несколько целей в одном мейкфайле полезно для больших проектов. Это связано с тем, что при изменении одного файла не понадобится пересобирать весь проект, а можно будет обойтись пересборкой только измененной части. Пример:  

```
all: hello

hello: main.o factorial.o hello.o
	g++ main.o factorial.o hello.o -o hello

main.o: main.cpp
	g++ -c main.cpp
	
factorial.o: factorial.cpp
	g++ -c factorial.cpp
	
hello.o: hello.cpp
	g++ -c hello.cpp
	
clean:
	rm -rf *.o hello
```

##### Использование переменных и комментариев
Переменные широко используются в мейкфайлах. Например, это удобный способ учесть возможность того, что проект будут собирать другим компилятором или с другими опциями. 

```
# Это комментарий, который говорит, что переменная CC указывает компилятор, используемый для сборки
CC=g++
#Это еще один комментарий. Он поясняет, что в переменной CFLAGS лежат флаги, которые передаются компилятору
CFLAGS=-c -Wall

all: hello

hello: main.o factorial.o hello.o
	$(CC) main.o factorial.o hello.o -o hello
	
main.o: main.cpp
	$(CC) $(CFLAGS) main.cpp
	
factorial.o: factorial.cpp
	$(CC) $(CFLAGS) factorial.cpp
	
hello.o: hello.cpp
	$(CC) $(CFLAGS) hello.cpp
	
clean:
	rm -rf *.o hello
```

#### Универсальный Makefile
```
CC=g++
CFLAGS=-c -Wall
LDFLAGS=
SOURCES=main.cpp hello.cpp factorial.cpp
OBJECTS=$(SOURCES:.cpp=.o)
EXECUTABLE=hello

all: $(SOURCES) $(EXECUTABLE)

$(EXECUTABLE): $(OBJECTS)
 	$(CC) $(LDFLAGS) $(OBJECTS) -o $@
	 	
.cpp.o:
	$(CC) $(CFLAGS) $< -o $@
```

Пример Makefile, который собирает разные программы в разные .exe:
```Makefile
CC = g++
CFLAGS = -Wall

SRCDIR = src
BINDIR = bin

MAINS = $(shell grep -l "int main" $(SRCDIR)/*.cpp)
TARGETS = $(patsubst $(SRCDIR)/%.cpp,$(BINDIR)/%,$(MAINS))

all: $(TARGETS)

$(BINDIR)/%: $(SRCDIR)/%.cpp | $(BINDIR)
    $(CC) $(CFLAGS) $< -o $@

$(BINDIR):
    mkdir -p $(BINDIR)

clean:
    rm -rf $(BINDIR)/*
```

Удобные функции Makefile:
```Makefile
$(patsubst <шаблон_входа>, <шаблон_выхода>, <список>)
$(patsubst $(SRCDIR)/%.cpp, $(BINDIR)/%, $(MAINS))
$(patsubst src/%.cpp, bin/%, src/a.cpp src/b.cpp src/client.cpp)
```

**src/%.cpp** означает:
- строка должна начинаться с `src/`
- заканчиваться на `.cpp`
- `%` — это “имя файла без расширения”