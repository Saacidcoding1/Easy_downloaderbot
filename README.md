import os
from telegram.ext import Updater, MessageHandler, Filters
from yt_dlp import YoutubeDL

TOKEN = os.environ['BOT_TOKEN']

def start(update, context):
    update.message.reply_text("📥 Send YouTube/TikTok links!")

def handle_message(update, context):
    url = update.message.text
    try:
        with YoutubeDL({'format': 'best'}) as ydl:
            info = ydl.extract_info(url, download=True)
            filename = ydl.prepare_filename(info)
            update.message.reply_video(video=open(filename, 'rb'))
        os.remove(filename)
    except Exception as e:
        update.message.reply_text(f"❌ Error: {str(e)}")

if __name__ == '__main__':
    updater = Updater(TOKEN)
    dp = updater.dispatcher
    dp.add_handler(MessageHandler(Filters.text, handle_message))
    updater.start_polling()
    updater.idle()