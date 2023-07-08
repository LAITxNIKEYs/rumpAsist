# Голосовой асистент Румпель

## Здесь вы можете увидить создание голосового помощника  "Румпель".

________________________________________________________________________________________________________________________________________
<p align="center">
  <img src="https://github.com/LAITxNIKEYs/rumpAsist/assets/104034823/ded3b850-353a-4216-a3a0-c0b30b09a5b7" />
</p>
________________________________________________________________________________________________________________________________________

Данный код я выкладываю в открытый доступ, для того, чтобы вы могли так-же исправлять и что-то добовлять, ведь чем больше людей заинтересованы этим делом, тем эффективнее будет сам голосовой помошник.

# Изначальная основа
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
