import telebot  # type: ignore
import threading
import time
import ssl

TOKEN = '7936252603:AAHWsuClse5qgNMJd3J9vP__X6ATFUS-o8k'
CHAT_ID = 1395142576  # مقدار عددی چت آی‌دی خود را وارد کنید

# اطمینان از در دسترس بودن SSL
try:
    ssl.create_default_context()
except AttributeError:
    print("Error: SSL module is missing or not configured correctly.")
    exit(1)

bot = telebot.TeleBot(TOKEN)

# تابع اضافه کردن شماره به انتهای کپشن پیام
def add_number_to_caption(message):
    try:
        chat_id = message.chat.id
        message_id = message.message_id
        caption = message.caption if message.caption else ""

        caption_with_number = f"{caption}\n02166718315\n02166742954"

        if message.photo:
            bot.send_photo(chat_id, message.photo[-1].file_id, caption=caption_with_number, reply_to_message_id=message_id)
        elif message.video:
            bot.send_video(chat_id, message.video.file_id, caption=caption_with_number, reply_to_message_id=message_id)
        elif message.document:
            bot.send_document(chat_id, message.document.file_id, caption=caption_with_number, reply_to_message_id=message_id)
        elif caption:
            bot.send_message(chat_id, caption_with_number, reply_to_message_id=message_id)

    except Exception as e:
        print(f"Error: {e}")

# هندل کردن پیام‌ها
@bot.message_handler(content_types=['text', 'photo', 'video', 'document'])
def handle_message(message):
    add_number_to_caption(message)

# تابع پینگ برای بررسی وضعیت آنلاین بودن ربات
def keep_alive():
    while True:
        try:
            bot.get_me()
            print("Bot is alive!")
        except Exception as e:
            print(f"Ping Error: {e}")
        time.sleep(600)  # هر ۱۰ دقیقه یکبار

# اجرای پینگ در یک ترد جداگانه
ping_thread = threading.Thread(target=keep_alive, daemon=True)
ping_thread.start()

# اجرای ربات
bot.polling()
