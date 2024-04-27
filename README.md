import telebot
import requests

# Initialize your Telegram Bot using the token
BOT_TOKEN = '6993561533:AAFTXSYwcwk9di5CT-hd32Jtkm1SFGtN-2k
bot = telebot.TeleBot(BOT_TOKEN)

@bot.message_handler(commands=['start', 'help'])
def send_welcome(message):
    welcome_message = "👋 Welcome to the TikTok Downloader Bot! Send me a TikTok video link, and I'll help you download it. Just paste the TikTok URL, and I'll do the rest. MADE BY @ProsSith 😊"
    bot.reply_to(message, welcome_message)

@bot.message_handler(func=lambda message: True)
def handle_tiktok_link(message):
    user_id = message.from_user.id
    message_text = message.text.strip()

    if message_text.startswith("https://vm.tiktok.com/") or message_text.startswith("https://www.tiktok.com/"):
        bot.reply_to(message, "📥 TikTok video link received. Processing...")

        api_url = f"https://tiktokvideodownloader.apinepdev.workers.dev/?url={message_text}"
        response = requests.get(api_url)

        if response.status_code == 200:
            # MADE BY @ProsSith NEPCODER
            data = response.json()

         # MADE BY @DEVSNP NEPCODER
            download_url = data['data']['download']['video']['NoWM']['url']

       # MADE BY @ProsSith NEPCODER
            caption = create_stylish_caption(data)

                 # MADE BY @ProsSith NEPCODER
            video_response = requests.get(download_url)

            if video_response.status_code == 200:
                video_content = video_response.content
                with open("video.mp4", "wb") as video_file:
                    video_file.write(video_content)
                with open("video.mp4", "rb") as video_file:
                    bot.send_video(user_id, video_file, caption=caption)
            else:
                bot.reply_to(message, "❌ Sorry, I couldn't retrieve the TikTok video. Please check the URL and try again.")
        else:
            bot.reply_to(message, "❌ Sorry, there was an issue with the TikTok downloader API. Please try again later.")
    else:
        bot.reply_to(message, "❌ Please provide a valid TikTok video link (e.g., https://vm.tiktok.com/... or https://www.tiktok.com/...).")

def create_stylish_caption(data):
    caption = "Downloaded by @tiktokvideodownloader bot\n\n"

    video_description = data['data'].get('VideoDescription')
    if video_description:
        caption += f"Description: {video_description}\n"

    stats = data['data'].get('stats')
    if stats:
        for key, value in stats.items():
            caption += f"{key}: {value}\n"

    return caption

bot.polling()
