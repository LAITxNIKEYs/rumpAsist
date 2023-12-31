# Голосовой асистент Румпель

## Здесь вы можете увидить создание голосового помощника  "Румпель".

________________________________________________________________________________________________________________________________________
<p align="center">
  <img src="https://github.com/LAITxNIKEYs/rumpAsist/assets/104034823/ded3b850-353a-4216-a3a0-c0b30b09a5b7" />
</p>
________________________________________________________________________________________________________________________________________

Данный код я выкладываю в открытый доступ, для того, чтобы вы могли так-же исправлять и что-то добовлять, ведь чем больше людей заинтересованы этим делом, тем эффективнее будет сам голосовой помошник.

## Итак, давайте разберем изначальный код, чтобы было понимания как я решил реализовывать голосового ассистента "Румпель":

```Python
import openai
import speech_recognition as sr
import pyttsx3
import random
import os

engine = pyttsx3.init()
openai.api_key = ""

def listen_command():
    
    
    try:
        r = sr.Recognizer()
        with sr.Microphone() as source:
            print("Слушаю...")
            audio = r.listen(source, timeout=5)

        text = r.recognize_google(audio, language="ru-RU")
        print(text)
        
        return text.lower()
    
    except sr.UnknownValueError:
        t = "Не понял что ты сказал"
        engine.say(t)
        engine.runAndWait()

openai.api_key = ''

def generate_response(command):
    response = openai.Completion.create(
        engine='davinci',
        prompt=command,
        max_tokens=50,
        temperature=0.7,
        n=1,
        stop=None,
        )
    return response.choices[0].text.strip()
    
    # Извлечение сгенерированного ответа

    answer = response.choices[0].text.strip()
    
    return answer

def greeting():
    
    t = "Здравствуйте!"
    engine.say(t)
    engine.runAndWait()


def play_music():
    
    
    files = os.listdir('music')
    random_file = f'music/{random.choice(files)}'
    
    t = f"Вот {random_file}"
    engine.say(t)
    engine.runAndWait()
    os.system(f'start {random_file}')

def create_task():
    
    
    t = "Что добавим в список дел?"
    engine.say(t)
    engine.runAndWait()
    
    query = listen_command()
        
    with open('todo-list.txt', 'a') as file:
        file.write(f'! {query}\n')
        
    t = f"Задача {query} добавлена"
    engine.say(t)
    engine.runAndWait()

commands_dict = {
    'greeting': ['привет', 'приветствую', 'хай'],
    'create_task': ['добавить задачу', 'создать задачу', 'заметка'],
    'play_music': ['включить музыку', 'дискотека', 'музыка']
}

key = ['start', 'румпель', 'румпельштильцхен', 'сука']

try:
    while True:
        try:
            audio = listen_command()
            
            if audio == 'пока':
                print('Выключаюсь...')
                t = "Как скажите"
                engine.say(t)
                engine.runAndWait()
                break
            
            for k, v in commands_dict.items():
                if audio in v:
                    globals()[k]()
                    break
            else:
                response = generate_response(audio)
                print(response)
                
        except sr.RequestError as e:
            print(f"Не удалось получить результаты от службы распознавания речи Google; {e}")
        except KeyboardInterrupt:
            print("Программа завершена пользователем")

except KeyboardInterrupt:
    print("Программа завершена пользователем")
```

## Что это такое? 

________________________________________________________________________________________________________________________________________

Данный код реализует собой программу, которая использует голосовое распознование для отклика с пользователем. Данный голосовой ассистент "Румпель" выполняет различные команды, такие как приветствие, создание задачи(заметки), воспроизведение скаченной музыки в рандомном порядке и ответы на вопросы.

### 1. Библиотеки которые используются:

* Библиотека OpenAI - является набором инструментов и API для работы с искусственным интеллектом. Она предоставляет возможность генерировать текст, выполнять машинное обучение и другие задачи. В данном коде, библиотека OpenAI используется для генерации ответов на команды пользователя.

```Python
import openai
```
 
* Библиотека speech_recognition - это инструмент, который позволяет программам распознавать и интерпретировать речь с помощью микрофона. Она предоставляет возможность преобразования аудиофайлов или потока звука в текстовую информацию, которую можно использовать для различных целей, таких как управление голосовыми командами или анализ речи. Библиотека speech_recognition поддерживает несколько популярных сервисов распознавания речи, таких как Google Speech Recognition, IBM Speech to Text и другие.

```Python
import speech_recognition as sr
```

* Библиотека pyttsx3 - это инструмент, который позволяет программам синтезировать речь из текста. Она предоставляет возможность преобразования текстовой информации в аудиофайл или поток звука, который можно воспроизвести через динамики или наушники. Библиотека pyttsx3 поддерживает несколько популярных движков речи, таких как SAPI5 (для Windows) и NSSpeechSynthesizer (для Mac). Она также предоставляет различные настройки и возможности для управления голосом и произношением, такие как выбор языка, скорость речи и тон голоса.

```Python
import pyttsx3
```

* Библиотека random - это модуль в языке программирования Python, который предоставляет функции для генерации случайных чисел и выбора случайных элементов из последовательностей. Она может использоваться для различных задач, таких как генерация случайных чисел, перемешивание элементов списка, выбор случайного элемента из списка и других операций, связанных с случайностью. Библиотека random предоставляет различные функции, такие как randint(), choice(), shuffle() и другие, которые позволяют генерировать случайные значения и выполнять операции с ними.

```Python
import random
```

* Библиотека operating system -предоставляет функции для работы с операционной системой. Она позволяет выполнять различные операции с файлами и директориями, управлять процессами, работать с переменными окружения, получать информацию о системе и многое другое. Библиотека os является частью стандартной библиотеки Python, поэтому ее использование не требует дополнительной установки.

```Python
import os
```


### 2. Методы которые использовались: 

* listen_command(): Этот метод использует библиотеку speech_recognition для записи аудио с микрофона и распознавания речи с помощью сервиса Google. Он возвращает распознанный текст команды.

* generate_response(command): Этот метод использует API OpenAI для генерации ответа на заданную команду. Он отправляет команду в виде промпта API и получает сгенерированный ответ.

* greeting(): Этот метод произносит приветствие для пользователя.

* play_music(): Этот метод выбирает случайный файл из папки "music" и воспроизводит его. Он также произносит имя выбранного файла.

* create_task(): Этот метод запрашивает у пользователя текст задачи, добавляет его в файл "todo-list.txt" и произносит подтверждение.

________________________________________________________________________________________________________________________________________

Основной частью кода является словарь commands_dict:

```Python
commands_dict = {
    'greeting': ['привет', 'приветствую', 'хай'],
    'create_task': ['добавить задачу', 'создать задачу', 'заметка'],
    'play_music': ['включить музыку', 'дискотека', 'музыка']
}
```

commands_dict содержит ключи команд и соответствующие им фразы, которые при произношение пользователем выполняют команду. После выполнения команды, программа в бесконечном чикле цикле слушает команды пользователя, распознает их и выполняет соответствующую команду или генерирует ответ на неизвестную команду.

________________________________________________________________________________________________________________________________________

# Внимание!

Если у вас возникает такая ошибка: resp, got_stream = self._interpret_response(result, stream), то необходимо проверить сам код, который вызывает эту ошибку, а так же убедиться, что он правильно обрабатывает ответ от API OpenAI. Так же убедитесь в том, что вы ввели api-ключ для openai:

```Python
openai.api_key = "Ваш ключ"
```
________________________________________________________________________________________________________________________________________

# Задачи которые планируется сделаться:

* Улучшить взаимодействие "Румпеля" с пользователем.

* Добавить голосовую настройку ключа активации голосового помощника.

* Добавить возможность запуска базовых сценариев, по типу: запустить браузер и открыть социальную сеть.

* Сделать команду которая будет устанавливать напоминания для пользователя.

________________________________________________________________________________________________________________________________________


## Контакты для связи со мной:

* Телеграмм: https://t.me/rumpel_ovs

* Вк: https://vk.com/whyislait
