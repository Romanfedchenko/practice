<h1 align="center">Task-4</h1>

<h3 align="left">Viewing statistics on covid-19 in Telegram</h3>
<p align="left">Implement a window application that will provide statistics for 5+ countries, statistics can be taken any total_cases, and so on.</p>

<p align="left">An example of working:</p>
<kbd>
    <img src="../Images/Task-4_1.png" width="700px" alt="Task-4">
</kbd>

<br>
<br>

### Install pyTelegramBotAPI

```shell
$ pip3 install pyTelegramBotAPI
```

<br>

<h3 align="left">An example of working with the program.</h3>
<img width="700px" src="https://drive.google.com/uc?id=1LyWxFSA6jPX1Qam04z8iOKaLsxKjB2nn" alt="Task-4">

<br>
<br>

### Example

```python
import telebot
import os.path
import numpy as np 
from datetime import datetime
from telebot import types
import webbrowser
import requests
import io

url = "https://vaccovid-coronavirus-vaccine-and-treatment-tracker.p.rapidapi.com/api/npm-covid-data/europe"

headers = {
    'x-rapidapi-key': "01f8a8cfd9mshbca1824dd2b941dp1526f5jsn4e6523312a12",
    'x-rapidapi-host': "vaccovid-coronavirus-vaccine-and-treatment-tracker.p.rapidapi.com"
}

response = requests.request("GET", url, headers=headers)

bot = telebot.TeleBot('TOP SECRET')
print('Start:')

mainHarr = []
for i in response.json():
    mainHarr.append(i['Country'])

@bot.message_handler(commands=['start'])
def welcome(message):
    # keyboard
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)

    btn1 = types.KeyboardButton('Send me the statistics file')
    btn2 = types.KeyboardButton('Refresh')
    markup.add(btn1, btn2)
    
    bot.send_photo(message.chat.id, photo=open('images/covid.png', 'rb'))
    bot.send_message(message.chat.id, '<b>Welcome.</b> Here you can get the latest information on coronavirus statistics in different countries.\nFor more information, enter the command ( /help ). '.format(message.from_user, bot.get_me()), parse_mode='html', reply_markup=markup)
 
@bot.message_handler(content_types=['text'])
def lalala(message):
    global response
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)

    if message.chat.type == 'private':
        msg = message.text

        if msg == '/help':
            bot.send_message(message.chat.id, '<b>All available commands:</b>\n1. /Visit_the_site\n2. /Refresh\n3. /Send_me_the_statistics_file'.format(message.from_user, bot.get_me()), parse_mode='html', reply_markup=markup)
        elif msg == '/Visit_the_site':
            webbrowser.open('https://github.com/sergden2021/python-practice', new=2)
        elif msg == '/Send_me_the_statistics_file' or msg == 'Send me the statistics file':
            file_obj = io.BytesIO(bytearray(str(response.json()).encode()))
            file_obj.name = "Сoronavirus-statistics.txt"
            bot.send_document(message.chat.id, file_obj)
        elif msg == '/Refresh' or msg == 'Refresh':
            response = requests.request("GET", url, headers=headers)
            bot.send_message(message.chat.id, 'Done')
        elif msg in mainHarr:
            index = mainHarr.index(msg)
            print(response.json()[index])
            bot.send_message(message.chat.id, ('Rank: ' + str(response.json()[index]['rank']) + ', Infection_Risk: ' + str(response.json()[index]['Infection_Risk']) + ', Total cases: ' + str(response.json()[index]['TotalCases'])))

@bot.callback_query_handler(func=lambda call: True)
def callback_inline(call):
    try:
        if call.message:
            pass

    except Exception as e:
        print(repr(e))
 
bot.polling(none_stop=True)
```
