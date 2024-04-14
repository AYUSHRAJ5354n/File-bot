import telegram
from telegram.error import TelegramError

# Replace YOUR_BOT_TOKEN with the token you received from BotFather
bot = telegram.Bot(token='YOUR_BOT_TOKEN')

def send_file(chat_id, file_path):
    try:
        bot.send_chat_action(chat_id=chat_id, action=telegram.ChatAction.TYPING)
        bot.send_document(chat_id=chat_id, document=open(file_path, 'rb'))
    except TelegramError as e:
        print(e)

def force_subscribe(chat_id, channel_username):
    try:
        bot.send_chat_action(chat_id=chat_id, action=telegram.ChatAction.TYPING)
        bot.send_message(chat_id=chat_id, text=f"To get the file, please join our channel: {channel_username}")
        bot.send_chat_action(chat_id=chat_id, action=telegram.ChatAction.RECORD_VIDEO_NOTE)
        bot.send_message(chat_id=chat_id, text="Once you've joined, send us a message to confirm.")
    except TelegramError as e:
        print(e)

def handle_message(update, context):
    chat_id = update.message.chat_id
    text = update.message.text

    if text.lower() == '/start':
        force_subscribe(chat_id, 'your_channel_username')
    elif text.lower() == 'i have joined':
        send_file(chat_id, 'path_to_your_file')

def main():
    updater = Updater(token='YOUR_BOT_TOKEN', use_context=True)
    dp = updater.dispatcher

    dp.add_handler(CommandHandler('start', handle_message))
    dp.add_handler(MessageHandler(Filters.text & (~Filters.command), handle_message))

    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
